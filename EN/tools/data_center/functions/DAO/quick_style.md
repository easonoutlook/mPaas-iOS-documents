@cnName 2.3.8 High-level syntax
@priority 8

### 2.3.8 High-level syntax

[TOC]

#### Shortcut syntax

```XML
<insert id="addMessages" arguments="messages" foreach="messages.model">
    insert or replace into ${T} (id, time, content, uin, read, date) values(#{model.msgId}, #{model.time}, #{model.content}, #{model.uin}, #{model.read}, #{model.date})
</insert>
```

Taking the INSERT statement above for example, if many properties of the model object need to be inserted to the database, each property in the values will be written, which is both time- and effort-consuming. 

Here is another example. 

```XML
<update id="updateTemplate" arguments="template">
    update ${T} set data = #{template.data}, tag = #{template.tag} where tplId = #{template.id} and tplVersion = #{template.tplVersion}
</update>
```

if many properties after the SET statement require to be updated, the SQL statements will be very complicated. 

The shortcut syntax can come in handy: 

```XML
%{'#{model.*}', msgId, time, content, uin, read, date}

is equivalent to

#{model.msgId}, #{model.time}, #{model.content}, #{model.uin}, #{model.read}, #{model.date}
```
```XML
%{'* = #{model.*}', data, tag, id, version}

is equivalent to

data = #{model.data}, tag = #{model.tag}, id = #{model.id}, version = #{model.version}
```

The basic format is: 
```XML
%{'format', ...}
```
The first argument is bracketed by single quotes, indicating the format. Following it is the data to be formatted.  DAO will format each piece of data once and concatenate the data after DAO replaces the “*” in format with corresponding data. 
Do not worry about the comprimising performance as this pre-processing will be executed only once. 

The two examples above can be modifed to: 
```XML
<insert id="addMessages" arguments="messages" foreach="messages.model">
    insert or replace into ${T} (id, time, content, uin, read, date) values(%{'#{model.*}', msgId, time, content, uin, read, date})
</insert>

<update id="updateTemplate" arguments="template">
    update ${T} set %{'* = #{template.*}', data, tag} where tplId = #{template.id} and tplVersion = #{template.tplVersion}
</update>
```

#### TRY EXCEPT

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
Sometimes the same model may be inserted into the database for multiple times. In this case, the INSERT or REPLACE statements will lead to the loss of old data when conflicts occur in the model primary key (Another model of the same primary key already exists in the database), and the data is re-inserted. This will lead to changes in the rowid of the same piece of data. (Usually you don't need to care about the rowid. But sometimes the rowid is required to be persistent throughout the entire life cycle of the database file.) The TRY EXCEPT statement block can solve the problem, among others. TRY EXCEPT statements can only be used in definitions of DAO methods and should have no preceding or following statements. Other statement blocks can be included inside the TRY and EXCEPT statements.

During execution of this DAO method, if execution of the TRY statement fails, the execution will move to the EXCEPT statement automatically. Only when both of them fail will this DAO call return the failure result.

#### Non-nil argument

```XML
<insert id="addMessage" arguments="$ model">
    insert or replace into ${T} ${table_columns} values(#{model.msgId}, #{model.time}, #{model.content}, #{model.uin}, #{model.read}, #{model.date})
</insert>
```
If an argument has a $ symbol at its beginning, it indicates that this argument does not accept the nil value. Calling DAO interfaces from the business layer allows nil arguments. But if an argument has a $ symbol at its beginning and the caller accidentally passes a nil value, the DAO call will fail automatically to prevent unexpected issues from happening.

#### Conditional formatting

Conditional formatting is akin to the foreach keyword. It will receive a dictionary argument, and format the k-v pairs in the dictionary into the format of "k1=v1, k2=v2". 

```XML
<insert id="updateMessage" arguments="values, conditions">
    update ${T} <format prefix="set" pairs="values" join=","/><format prefix="where" pairs="conditions" join="and"/>
</insert>
```

If the incoming values are @{@"uin":@(12345), @"content":@"12345"}, and the conditions are @{@"id":@"100"}, 

SQL statements generated by this method are as follows: 
```SQL
update table set uin = ?, content = ? where id = ?
```

and the @(12345), @"12345" and @"100" are bound to SQLite. 

'[Attention]' When a condition passes an empty value for pairs, the prefix argument will not be concatenated into the SQL statements either. 

