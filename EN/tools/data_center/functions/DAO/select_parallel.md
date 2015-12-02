@cnName 2.3.7 Concurrent SELECT operations
@priority 7

### 2.3.7 Concurrent SELECT operations

Default SQLite distributions are fully serialized. The SQLite that comes with the iOS platform is multi-threaded and can support connections with multiple databases.  DAO has a new feature supporting concurrent SELECT operations, and supporting specifying a SELECT method for connections of the user's database.  In this way, in case of intensive writing operations to the database, the SELECT operation will not be suspended waiting. 

```XML
<select id="getTemplatesById" arguments="id" result="[PPDynamicTemplate]" parallel="true">
    select * from ${T} where id = #{id}
</select>
```

The definition method is as above, by simply adding the argument parallel="true" after the SELECT method. 

Add the prepareParallelConnection method for APDAOProtocol
```C
/**
 *  Create a parallel connection for the database, for the purpose of speeding up the possible concurrent SELECT operations that may follow. This method can be called for multiple times to create several stand-by connections for the database.
 *  The connections created will be automatically closed and the business layer does not to handle them.
 
 *  @param autoclose Automatically close the connections if they are idle for a certain number of seconds. The value "0" indicates that the system value will be used.
 */
- (void)prepareParallelConnection:(NSTimeInterval)autoclose;
```
You can call this method while creating the DAO proxy object.  Data Center will prepare an additional connection for this database to cope with possible intensive operations that may follow.  If you didn't call this method and prepare the database connection in advance, you can create it as needed when calling the SELECT method.  These database connections will be closed automatically in the idle status and the business layer does not need to manage them. 

