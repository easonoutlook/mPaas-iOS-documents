@cnName 2.3.5 Transaction
@priority 5

### 2.3.5 Transaction

Each DAO call is a transaction, even if there are multiple SQL statements for execution in one DAO operation.  E.g.,
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

in this operation for creating a table (DAO operations for table creation will be automatically executed), there are two SQL calls divided by “step”: first, create a table; second, create indexes. The two statements constitute one transaction. 

Here is another example. A foreach argument exists in INSERT operation. 
```xml
<insert id="addMessages" arguments="messages" foreach="messages.model">
    insert or replace into ${T} (id, content, uin, read) values(#{model.msgId}, #{model.content}, #{model.uin}, #{model.read})
</insert>
```
When the business layer calls this DAO method, it will pass the message model list "messages", DAO will execute one SQL INSERT operation for each model (loop in the underlying layer), and the whole operation is also a transaction. 

Sometimes there will be multiple DAO calls. Through each call is a transaction, the overall efficiency is low because of too many calls.  The business layer can start transactions before all the DAO calls, and end the transactions after all the DAO calls. 

```C
NSString* filePath = [[NSBundle mainBundle] pathForResource:@"demo_config" ofType:@"xml"];
id<DemoProtocol> proxy = [[APDataCenter defaultDataCenter] daoWithPath:filePath userDependent:YES];
[proxy daoTransaction:APDAOTransactionBegin];
...// massive DAO invocations
[proxy daoTransaction:APDAOTransactionCommit];
```

Here we directly call the daoTransaction method of the proxy.  This method is actually declared in APDAOProtocol, so the DemoProtocol should be inherited from the APDAOProtocol.  (Actually, all the DAO proxy objects support methods in APDAOProtocol. It is for the business layer to decide whether to declare that its protocols are inherited from the APDAOProtocol.)

Calling the daoTransaction method of the proxy is the most easy and straightforward way.  In fact, as the userDependent argument in the example above is "YES" when the proxy is created,  the database file it operates on is actually APDataCenter.userPreferences.  So the example above is equivalent to the call below: 
```C
NSString* filePath = [[NSBundle mainBundle] pathForResource:@"demo_config" ofType:@"xml"];
id<DemoProtocol> proxy = [[APDataCenter defaultDataCenter] daoWithPath:filePath userDependent:YES];
[APUserPreferences daoTransaction:APDAOTransactionBegin];
...// massive DAO invocations
[APUserPreferences daoTransaction:APDAOTransactionCommit];
```

If the userDependent argument is "NO" when the proxy is created, the daoTransaction method of APCommonPreferences can be called. 

Here is a well-structured calling template for putting a series of DAO operations into the transaction. 
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
