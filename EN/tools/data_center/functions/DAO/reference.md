@cnName 2.3.3 Reference
@priority 3

### 2.3.3 Reference

[TOC]

#### @ reference

@{something}: used for method arguments and the argument name is "something". To format SQL statements, all the object contents will be concatenated into SQL statements. As all the arguments are of the ID type, [NSString stirngWithFormat:@"%@", id] is used by default for formatting. The @{something or ""} format represents that if the incoming argument is nil, it will be converted to an empty string instead of NULL. 

We do not recommend @{} for referencing arguments as it is not efficient and subject to the SQL injection risk.  If the argument object is of the NSString class, the string will be automatically enclosed in quotes after concatenation to ensure the correctness of the SQL statement format.  But if the user has added quotes in the configuration file, the underlying layer will not add the quotes again. 

If the @{something no ""} format is adopted, you can enforce not to add quotes. 

```XML
<select id="getMessage" arguments="id" result="[MessageModel]">
    select * from ${T} where id = @{id}
</select>
```

In the example above, the incoming id argument is of the NSString class and the codes above are correct. The generated SQL will format and concatenate the id and enclose it with quotes. 

#### # reference

\#{something}: used for method arguments and the argument name is "something". To format SQL statements, the argument will be converted to '?' and the object is bound to SQLite. This method is recommended for SQL coding as it is efficient.   The #{something or ""} format represents that if the incoming argument is nil, it will be converted to an empty string instead of NULL. 

#### $ reference

${something}: used for referencing contents in the configuration file, such as the default table name ${T} or ${t}, and constants and SQL statement blocks defined in the configuration file. 

#### Chain access

For @ reference and # reference, you can use '.' to access the property of argument objects.  For example, the incoming argument name is "model" of the MessageModel type. It has a property of NSString*: "content".  With @{model.content}, you will be able to fetch the value of its content property.  The internal implementation is [NSObject valueForKey:]. So if the argument is a dictionary (The dictionary's valueForKey is equivalent to dict[@""]), you can also use #{adict.aaa} to reference the adict[@"aaa"] value. 

