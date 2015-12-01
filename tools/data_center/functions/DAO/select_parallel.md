@cnName 2.3.7 并行 select
@priority 7

### 2.3.7 并行 select

默认发行版 sqlite 为完全串行，iOS 自带的 sqlite 为多线程模式，可以支持多个数据库连接。DAO 功能新增并行 select 功能，支持指定某 select 方法使用自己的数据库连接。这样在其它密集的写入操作时，select 操作可以不被挂起等待。

```XML
<select id="getTemplatesById" arguments="id" result="[PPDynamicTemplate]" parallel="true">
    select * from ${T} where id = #{id}
</select>
```

定义方法如上，在 select 方法后添加参数 parallel="true"即可。

APDAOProtocol 接口新增方法 prepareParallelConnection
```C
/**
 *  创建一个数据库副连接，为接下来可能发生的并发select 操作加速使用。可以调用多次，创建多个数据库连接待用。
 *  这些创建的链接会自动关闭，业务层无须处理。
 
 *  @param autoclose 在空闲状态多少秒后自动关闭，0 表示使用系统值
 */
- (void)prepareParallelConnection:(NSTimeInterval)autoclose;
```
你可以在创建 DAO 代理对象时，调用一次这个方法。统一存储会为这个数据库额外准备一份数据库连接，用于接下来可能发生的密集操作。如果不调用这个方法预先准备数据库连接，则会在调用 select 时，有需要再创建。这些数据库连接空闲时会自动关闭，业务不需要管理。