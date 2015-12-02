@cnName 2.3.2 Keywords
@priority 2

### 2.3.2 Keywords

[TOC]

#### module
```xml
<module name="demo" initializer="createTable" tableName="tableDemo" version="1.5" resetOnUpgrade="true" upgradeMinVersion="1.2">
```
* The initializer parameter is optional. For the update method specified by the initializer, DAO regards it a method for creating database tables and will execute it once by default at the first DAO request. 
* The tableName specifies the table name for the default operation in the method below. It can be replaced by ${T} or ${t} in SQL statements, so that you don't have to input the table name every time.  It is recommended that one configuration file be targeted to one table.  The tableName can be empty, so that the same configuration file can operate on multiple tables sharing the same format.  For example, to process chat messages in tables, the setTableName method of DAO objects can be called for setting the name of the table to be operated on. 
* The version represents the version number of the configuration file, in 'x.x' format. After the table is created, the tableName will serve as the key and the table version will be saved to the TableVersions table in the database file. TableVersions will work in concert with the upgrade block for table updates. 
* resetOnUpgrade. If its value is true or YES, when the version is updated, the old table will be deleted instead of calling the upgrade block.  If this argument does not exist, its value is false by default. 
* upgradeMinVersion. If it is not empty, database files of a version lower than its value will be reset directly. Otherwise the upgrade operation will be performed. 

#### const
```xml
<const table_columns="(id, time, content, uin, read)"/>
```
* It defines a constant of the string type. The table_columns is the name of a constant, and the content after the equal mark is the constant value. The constant can be referenced in the configuration file with ${constant name}. 

#### select
```xml
<select id="find" arguments="id, time" result=":messageModelMap">
  select * from ${T} where id = #{id} and time > @{time}
</select>
```
@arguments
* List of the argument names, separated by ','.  Incoming arguments from the caller are named in turn according to the descriptions in arguments.  Selectors of DAO objects will not carry the argument name in calling, so arguments must be named in sequence here. 
* If an argument has a $ symbol at its beginning, it indicates that this argument does not accept the nil value.  Calls of DAO interfaces from the business layer allow nil arguments. But if an argument has a $ symbol at its beginning and the caller accidentally passes a nil value, the DAO call will fail automatically  to prevent unexpected issues from happening. 
* For ways to reference arguments, see [DAO Reference](./reference.md)。

In the codes above, the corresponding selector is: 

```C
- (MessageModel*)find:(NSNumber*)id time:(NSNumber*)time;
```
If the DAO object calls [daoProxy find:@1234 time:@2014], the ready SQL statement will be: 

```C
select * from tableDemo where id = ? and time > 2014
```
and the NSNumber value @1234 will be handed over to SQLite for binding.  

@result
* The result can be the return value of the DAO method. The use of square brackets [] indicates to return the array type of value and iterations occur for the returned values of the SELECT method until no result is returned from SQLite.  If the square brackets [] are removed, it indicates to return one result only and one iteration is conducted for the SELECT method. It is similar to the next method for FMResultSet in FMDB database. 

Return types: 
* int: only one result, of the [NSNumber numberWithInt] type. 
* long long: only one result, of the [NSNumber numberWithLongLong] type. 
* bool: only one result, of the [NSNumber numberWithBool] type.
* double: only one result, of the [NSNumber numberWithDouble] type. 
* string: only one result, of the NSString* type.  
* binary: only one result, of the NSData* type.
* [int]: an array, with values of the [NSNumber numberWithInt] type in the array. 
* [long long]: an array, with values of the [NSNumber numberWithLongLong] type in the array. 
* [bool]: an array, with values of the [NSNumber numberWithBool] type in the array. 
* [double]: an array, with values of the [NSNumber numberWithDouble] type in the array. 
* [string]: an array, with values of the NSString* type in the array. 
* [binary]: an array, with values of the NSData* type in the array. 
* [{}]: an array, with mapping of column name->column value in the array. 
* [AType]: an array, with filled self-defined classes in the array. 
* {}: only one result, mapping of column name->column value. 
* AType: only one result, with the filled self-defined class. 
* [:AMap]: an array, with XML-defined mapped objects of AMap in the array. 
* :AMap: only one result. The AMap defined in the configuration file is used to describe the object. 

In the example above, the returned type is: ":messageModelMap".  The Objective-C types returned and columns that require special mapping will be defined in messageModelMap.  Refer to the keyword "map".

@foreach
The SELECT method also supports "foreach" field and its usage is similar to that of INSERT, UPDATE and DELETE methods to be introduced later.  The difference is, if the SELECT method specifies the foreach argument, the SELECT operation will be executed for N times, and the results will be returned in an array.  So if the SELECT method in DAO specifies the foreach argument, its return value must be defined as "NSArray*" in the protocol. 

#### insert, update, delete
```xml
<insert id="addMessages" arguments="messages" foreach="messages.model">
    insert or replace into ${T} (id, content, uin, read) values(#{model.msgId}, #{model.content}, #{model.uin}, #{model.read})
</insert>

<update id="createTable">
  <step>
    create table if not exists ${T} (id integer primary key, content text, uin integer, read boolean)
  </step>
  <step>
    create index if not exists uin_idx on ${T} (uin)
  </step>
</update>

<delete id="remove" arguments="msgId">
  delete from ${T} where msgId = #{msgId}
</delete>
```
* The INSERT, UPDATE and DELETE methods share the same format and their arguments concatenation and references are the same with that of the SELECT method. 
* The INSERT and DELETE keywords serve to differentiate purposes of methods.  You can write a "DELETE FROM TABLE" operation in the "UPDATE" function. 
* In DAO interfaces, when the INSERT, UPDATE or DELETE methods are executed, the returned value is APDAOResult*, indicating whether the execution succeeded.  See [DAO A Simple Example](#3)

@foreach
* When a 'foreach' field follows the INSERT, UPDATE or DELETE methods, the method will query every element in the argument array one by one when the method is called. 
* The 'foreach' field must follow the format below:   collectionName.item.  The “collectionName” must correspond to an argument in "arguments" and specify the loop container object. It must be of the NSArray or NSSet type when called. The “item” represents the object fetched from the container and will be used as the loop variable.  The item name cannot be duplicated with that of any argument in the "arguments". The item can be used as a normal argument in the SQL statement.  

For example, the delegate method is: 
```C
- (void)addMessages:(NSArray*)messages;
```
The “messages” is a MessageModel array. For every model in the messages, an SQL call will be executed so that elements in the array will be inserted to the database in one shot while the upper layer does not need to care about the loop call.  The underlying layer will merge the operation into one transaction to improve efficiency. 

@step
* In INSERT, UPDATE and DELETE methods, you may run into such a circumstance: To execute a DAO method, the SQL update operation is called for multiple times to execute multiple SQL statements.  For example, after a table is created, the user wants to create some indexes.  The statement between <step> in the function will be executed independently as one SQL update operation, and the underlying layer will merge all the operations into one transaction,  such as the createTable method in the figure above. 
* If the step clause exists in a function, no texts are allowed outside the step.  The step cannot contain another step. 

#### map
```xml
<map id="messageModelMap" result="MessageModel">
  <result property="msgId" column="id"/>
</map>
```
* It defines a mapping named messageModelMap. The actual Objective-C object generated is of the MessageModel class. 
* The msgId property of the Objective-C object maps the column value of Column id in the table. Properties not listed are regarded consistent with the column name in the table, thus omitted. 

#### upgrade
```xml
<upgrade toVersion="1.1">
  <step>
    alter table...
  </step>
  <step>
    alter table...
  </step>
</upgrade>
```
* With the version updated, the database may have the demand for upgrade. The SQL statements for upgrade are written here.  For example, at the very beginning, the version of the configuration file module is 1.0. After upgrade, the configuration file version is changed to 1.1.  When the new version configuration file module runs DAO methods for the first time, it will check the current table version with the configuration file version. With an inconsistency found, it will execute the upgrade step by step.  This method is called automatically by the underlying layer. The DAO method will be executed after the upgrade is done. 
* The upgrade is executed according to the SQL UPDATE statement. If there are multiple statements, they can be enclosed by <step>, which is similar to the implementation of <update id="createTable">. 
* If the operations defined by the upgrade block are required for upgrading the table, the "resetOnUpgrade" argument in the module must be set to "false". 

#### crypt
```xml
<crypt class="MessageModel" property="content"/>
```
* It describes that the property of the specified class will be encrypted. When data is written to the database, values fetched from this property will be encrypted. When data is read from the database, the generated object will first decrypt the value before assigning values to the property. 

For example, in execution of this DAO method, model is of the MessageModel class.  As the content property of model is fetched, the value will be encrypted before being written into the database. 
```xml
<insert id="insertMessage" arguments="model">
     insert into ${T} (content) values(#{model.content})
</insert>
```
The execution of this SELECT method will return an object of the MessageModel class.  When the underlying layer fetches data from the database and writes the content property to the MessageModel instance, it will first decrypt the data before writting the data,  and then return the ready MessageModel object. 
```xml
<select id="getMessage" arguments="msgId" result="MessageModel">
     select * from ${T} where msgId = #{msgId}
</select>
```

The methods for setting encryption are defined in [APDAOProtocol](#3.3), as follows: 
```C
/**
 *  Set an encryptor for encrypting the data in columns marked for encryption in the table.  During data writing to the table, data in this column will be encrypted. 
 *
 *  @param crypt The encryption struct which will by copied.  If the incoming crypt is created externally, it needs to be freed externally.  If it is APDefaultEncrypt(), no free is required. 
 */
- (void)setEncryptMethod:(APDataCrypt*)crypt;

/**
 *  Set a decryptor for decrypting the data in columns marked for encryption in the table.  During data reading from the table, data in this column will be decrypted. 
 *
 *  @param crypt The decryption struct which will be copied. If the incoming crypt is created externally, it needs to be freed externally. If it is APDefaultDecrypt(), no free is required.
 */
- (void)setDecryptMethod:(APDataCrypt*)crypt;
```
If no setting is made, the default encryption of APDataCenter will apply. See [KVStore](#2). 
If a DAO proxy object is id<DAOProtocol>, and the DAOProtocol is @protocol<APDAOProtocol>, the DAO proxy object can be directly used for calling setEncryptMethod and setDecryptMethod for setting encryption and decryption methods. 

#### if
```xml
<insert id="addMessages" arguments="messages, onlyRead" foreach="messages.model">
    <if true="model.read or (not onlyRead)">
    insert or replace into ${T} (msgId, content, read) values(#{model.msgId}, #{model.content}, #{model.read})
    </if>
</insert>
```
* The IF conditional statements can be nested in INSERT, UPFATE, DELETE and SELECT methods.  When IF conditions are met, the texts in the IF block will be concatenated into the final SQL statement. 
* The IF can be followed by true="expr" or false="expr". The “expr” stands for expression. It can utilize the argument of the method, and "." to list the argument object properties to call. 
* Operators supported by the expression are as follows: 

  (): brackets

  +: positive sign

  -: negative sign

  +: plus sign

  -: minus sign

  *: multiplication sign

  /: division sign

  \: exact division sign

  %: modulus 

  &gt;: greater than

  <: less than

  &gt;=: greater than or equal to 

  <=: less than or equal to 

  ==: equal to

  !=: unequal to

  and: AND, case-insensitive

  or: OR, case-insensitive

  not: NOT, case-insensitive

  xor: exclusive OR, case-insensitive
* The greater-than sign and less-than sign must be escaped.  See http://lidongbest5.com/blog/5/
* The arguments inside are names of the incoming arguments from external calls, but do not enclose the arguments with #{} or @{} like in the case of the SQL block. 
* The meaning of nil here is the same as the nil in Objective-C. 
* Strings in the expression should be started or ended with single quotes. Escape characters are not supported, but "\'" is supported to represent a single quote. 
* When the arguments are an Objective-C object, '.' is supported for accessing its property, such as the model.read in the example above. If the argument is an array or dictionary, 'argument name.count' can be used to get the element count. 

Below is a more complex expression: 
```xml
<if true="(title != nil and year > 2010) or authors.count >= 2">
```
The “title”, “year” and “authors” are all arguments passed from the caller. A nil value is allowed for “title” in the call. 
The expression above means "when the book title is not empty, and the year of publication is later than 2010, or there are more than 2 authors for the book".

#### choose
```xml
<choose>
  <when true="title != nil">
    AND title like #{title}
  </when>
  <when true="author != nil and author.name != nil">
    AND author_name like #{author.name}
  </when>
  <otherwise>
    AND featured = 1
  </otherwise>
</choose>
```
* It implements similar syntax to SWITCH statements, and its expression requirements are similar to the IF statement. The WHEN keyword can also be followed by true="expr" or false="expr". 
* Only the first eligible WHEN or OTHERWISE statement will be executed. The OTHERWISE argument can be void. 

#### foreach
```xml
<foreach item="iterator" collection="list" open="(" separator="," close=")" reverse="yes">
  @{iterator}
</foreach>
```
* The open, separator, close, and reverse arguments can be omitted. 
* The “item” represents the loop variable, and “collection” represents the argument name of the loop array. 

For example, a method receives string array arguments from the outside. The list content is @[@"1", @"2", @"3"]. There is another argument, prefix=@"abc", enclosed by '()', and separated by ','.  The execution result will be: (abc1,abc2,abc3). 
```xml
<update id="proc" arguments="list, prefix">
   <foreach item="iterator" collection="list" open="(" separator="," close=")">
       {prefix}{iterator}
   </foreach>
</update>
```
Foreach statements are usually used for concatenating the “in” blocks in the SELECT statement, for example:  
```xml
select * from ${T} where id in
<foreach item="id" collection="idList" open="(" separator="," close=")">
#{id}
</foreach>
```

#### where, set, trim
```xml
<where onVoid="quit"> 
  <if true="state != nil"> 
    state = #{state}
  </if> 
  <if true="title != nil">
    AND title like #{title}
  </if>
  <if true="author != nil and author.name != nil">
    AND author_name like #{author.name}
  </if>
</where>
```
The WHERE condition will handle superfluous AND, OR (case insensitive) conditions, and it may not return anything when no condition is met, even the WHERE.   It is used to concatenate WHERE clauses with a large number of conditions in SQL.  As shown in the example above, if only the last judgment is tenable, the statement will correctly return "where author_name like XXX", instead of "where AND author_name like XXX".  
```xml
<set>
  <if false="username != nil">username=#{username},</if>
  <if false="password != nil">password=#{password},</if>
  <if true="email != nil">email=#{email},</if>
  <if true="bio != nil">bio=#{bio},</if>
</set>
```
The SET keyword will handle the superfluous ',' signs in the end, and return nothing when no condition is met.  It is similar to the WHERE statement, but only that it handles the suffixal commas. 
```xml
<trim prefix="WHERE" prefixOverrides="AND | OR | and | or " onVoid="ignoreAfter">
</trim>
<!--
  Equivalent to <where>
-->

<trim prefix="SET" suffixOverrides=",">
</trim>
<!--
  Equivalent to <set>
-->
```
* The WHERE and SET statements can be replaced by TRIM statements.  TRIM statements define the overall prefix of the statement, and the list of superfluous prefixes and suffixes that each clause will handle (divided by "|"). 

* The onVoid argument can appear in WHERE, SET and TRIM statements. It has two values: "ignoreAfter" and "quit".  The values imply the logics used when an empty string is generated because no clause in the TRIM statement is tenable.  ignoreAfter means to ignore the following formatting statements and return the current SQL statement for execution, while “quit” means not to execute this SQL statement but to return success. 

#### sql
```xml
<sql id="userColumns"> id,username,password </sql>
<select id="selectUsers" result="{}">
  select ${userColumns}
  from some_table
  where id = #{id}
</select>
```
It defines the reusable SQL code segments and uses ${name} to quote the source code segments from other statements. 
The "name" in ${name} cannot be 'T' or 't', because ${T} and ${t} represents the default table name.  Inside the sql block other sql blocks can be further quoted. 

#### try except
```xml
<insert id="insertTemplates" arguments="templates" foreach="templates.model">
    <try>
        insert into ${T} (tplId, tplVersion, time, data, tag) values(%{'#{model.*}', tplId, tplVersion, time, data, tag})
        <except>
            update ${T} set %{'* = #{model.*}', data, tag} where tplId = #{model.tplId} and tplVersion = #{model.tplVersion}
        </except>
    </try>
</insert>
```
Sometimes the same model may be inserted into the database for multiple times. In this case, the INSERT or REPLACE statements will lead to the loss of old data when conflicts occur in the model primary key (Another model of the same primary key already exists in the database), and the data is re-inserted.  This will lead to changes in the rowid of the same piece of data.  The TRY EXCEPT statement block can solve the problem, among others.  TRY EXCEPT statements can only be used in definitions of DAO methods and should have no preceding or following statements.  Other statement blocks can be included inside the TRY and EXCEPT statements. 

During execution of this DAO method, if execution of the TRY statement fails, the execution will move to the EXCEPT statement automatically.  Only when both of them fail will this DAO call return the failure result.  

