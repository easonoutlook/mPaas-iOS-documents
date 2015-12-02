@cnName 2.3.1 Overview
@priority 1

### 2.3.1 Overview

[TOC]

#### When to use

General KV storage can only store simple data types, or encapsulated OC objects and does not support search.  When the business requires the access to SQLite, the DAO function of Data Center can be utilized for simplification and encapsulation. 

#### Basic working principle

![DAO](https://os.alipayobjects.com/rmsportal/KtfSCPVZcjFfyNL.png)

* Define an XML-format configuration file to describe the functions, returned data types and encrypted fields of various SQLite operations. 
* Define an interface for DAO objects, DAOInterface (@protocol). The interface method names and arguments should stay consistent with those described in the configuration file. 
* The business layer passes the XML configuration file to the daoWithPath method of APDataCenter, and the DAO access object is generated.  This object is enforced to be converted to id<DAOInterface>. 
* Next, the business layer will be able to directly call the DAO object methods, and Data Center will translate the method into the database operations described in the configuration file. 

#### A simple example

* The first line of the configuration file defines the default table name and database version, as well as the initialization method. 
* The insertItem and getItem are two methods for inserting and reading data. They receive arguments and format the arguments into SQL expressions. 
* The createTable method will be called once on the underlying layer by default. 
```xml
<module name="Demo" initializer="createTable" tableName="demoTable" version="1.0">
  <update id="createTable">
    create table if not exists ${T} (index integer primary key, content text)
  </update>

  <insert id="insertItem" arguments="content">
    insert into ${T} (content) values(#{content})
  </insert>

  <select id="getItem" arguments="index" result="string">
    select * from ${T} where index = #{index}
  </select>
</module>
```
* Definition of DAO interfaces. 
```C
@protocol DemoProtocol <APDAOProtocol>
 - (APDAOResult*)insertItem:(NSString*)content;
 - (NSString*)getItem:(NSNumber*)index;
@end
```
* Create a DAO proxy object. Suppose the configuration file is named demo_config.xml and is located in Main Bundle. 
* Write a piece of data with the insertItem method, acquire its index, and then read the written data with the index. 
```C
NSString* filePath = [[NSBundle mainBundle] pathForResource:@"demo_config" ofType:@"xml"];
id<DemoProtocol> proxy = [[APDataCenter defaultDataCenter] daoWithPath:filePath userDependent:YES];
[proxy insertItem:@"something"];
long long lastIndex = [proxy lastInsertRowId];
NSString* content = [proxy getItem:[NSNumber numberWithInteger:lastIndex]];
NSLog(@"content %@", content);
```
* The lastInsertRowId is a method of APDAOProtocol, used for getting the rowId of the row last inserted.  To enable the DAO object to support this method, you only need to make the DemoProtocol inherit from the APDAOProtocol in declaration. 

