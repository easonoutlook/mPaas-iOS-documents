@cnName 2.3.5 事务
@priority 5

### 2.3.5 事务

每个 DAO 调用都是自成一个事务的，如果一个 DAO 操作，含有多条 SQL 语句执行，也是自成一个事务。比如
```xml
<update id="createTable">
   <step>
      create table if not exists ${T} (localID long, clientMsgID char(64) primary key, msgID long, userID char(20), userType text, side integer, templateCode text, templateData text, bizMemo text, bizType text, appId text, link text, createTime long, messageState integer, extendData text, mediaState text, contextData text, egg text)
   </step>
   <step>
      create index if not exists messageState_idx on ${T} (messageState)
   </step>
</update>
```

这个创建表的操作（创建表的 DAO 操作会自动执行），里面用 step 分为两条 SQL 调用，先创建表，再创建索引，这两条语句整体是一个事务。

再比如 insert 操作里有 foreach 参数
```xml
<insert id="addMessages" arguments="messages" foreach="messages.model">
    insert or replace into ${T} (id, content, uin, read) values(#{model.msgId}, #{model.content}, #{model.uin}, #{model.read})
</insert>
```
业务调用这条 DAO 时，传入消息模型列表 messages，DAO 会对每个 model 执行一次插入操作的 SQL（底层的循环处理），整个操作也是一个事务。

有些时候，可能有很多次 DAO 调用，虽然每条自成一个事务，但是因为 DAO 调用次数过多，导致效率仍然不高。业务可以在所有 DAO 调用前开启事务，在所有 DAO 调用后结束事务。

```C
NSString* filePath = [[NSBundle mainBundle] pathForResource:@"demo_config" ofType:@"xml"];
id<DemoProtocol> proxy = [[APDataCenter defaultDataCenter] daoWithPath:filePath userDependent:YES];
[proxy daoTransaction:APDAOTransactionBegin];
...// massive DAO invocations
[proxy daoTransaction:APDAOTransactionCommit];
```

这里直接调用了 proxy 的 daoTransaction 方法。这个方法实际是声明在 APDAOProtocol 里的，所以要求 DemoProtocol 继承自 APDAOProtocol。(实际，所有 DAO proxy 对象都支持 APDAOProtocol 里的方法，是否显式声明它的 Protocol 继承自 APDAOProtocol 由业务自行决定。)

调用 proxy 的 daoTransaction 方法是最简单直接的。实际上由于上个例子中的 proxy 创建时，userDependent 为 YES。所以它操作的数据库文件实际为 APDataCenter.userPreferences。所以上面的例子等价于下面的调用：
```C
NSString* filePath = [[NSBundle mainBundle] pathForResource:@"demo_config" ofType:@"xml"];
id<DemoProtocol> proxy = [[APDataCenter defaultDataCenter] daoWithPath:filePath userDependent:YES];
[APUserPreferences daoTransaction:APDAOTransactionBegin];
...// massive DAO invocations
[APUserPreferences daoTransaction:APDAOTransactionCommit];
```

如果 proxy 创建时 userDependent 为 NO，可以调用 APCommonPreferences 的 daoTransaction 方法。

一个比较合理的把一系列 DAO 操作放到事务里的调用模板
```C
- (BOOL)multipleDAOInvocations
{
    APDAOResult* transactionResult = [proxy daoTransaction:APDAOTransactionBegin];
    if ([transactionResult failed])
        return NO;
    
    BOOL daoResult = YES;
    @try
    {
        // massive DAO invocations
        daoResult = [[proxy daoMethod1] succeeded];
        if (!daoResult)
            return NO;
        
        daoResult = [[proxy daoMethod2] succeeded];
        if (!daoResult)
            return NO;
        
        return daoResult;
    }
    @finally
    {
        [proxy daoTransaction:daoResult ? APDAOTransactionCommit : APDAOTransactionRollback];
    }
}
```