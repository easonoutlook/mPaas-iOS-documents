@cnName 2.3.3 引用方式
@priority 3

### 2.3.3 引用方式

[TOC]

#### @引用

@{something}，用于方法参数，参数名为 something，在格式化 sql 语句时会把对象内容拼到 sql 语句中；因为参数都为 id 类型，所以默认使用[NSString stirngWithFormat:@"%@", id]来格式化；@{something or ""}这种格式，表示传入的参数如果为 nil，会转成一个空字符串而不是 NULL。

不建议使用@{}的方式来引用参数，效率比较低，有 SQL 注入风险。如果参数对象是个 NSString，拼接进去后，会自动添加引号将字符串括起来，保证 SQL 语句格式的正确性。如果用户在配置文件里自己写了引号，底层不会自动添加引号了。

使用@{something no ""}，这种格式，可以强制不添加引号。

```XML
<select id="getMessage" arguments="id" result="[MessageModel]">
    select * from ${T} where id = @{id}
</select>
```

比如上例，id 参数传进来是个 NSString，上面的写法是正确的，生成的 SQL 会自动把 id 格式化进去，并且在前后添加引号。

#### #引用

\#{something}，用于方法参数，参数名为 something，在格式化 sql 语句时会转为一个'?'，然后将对象绑定给 sqlite；建议书写 sql 时尽量使用这种方式，效率更高。 #{something or ""}这种格式，表示传入的参数如果为 nil，会转成一个空字符串而不是 NULL。

#### $引用

${something}，用来引用配置文件里的内容，比如引用默认表名${T}或${t}、引用配置文件里定义的常量和 sql 代码块。

#### 链式访问

对于@和#引用，可以使用'.'来访问参数对象的属性。比如传入的参数名是 model，并且是一个 MessageModel 类型，它有属性 NSString* content。那么@{model.content}，会取出其 content 属性的值。内部实现为[NSObject valueForKey:]，所以如果参数是一个字典（字典的 valueForKey 等价于 dict[@""]），那么也可以使用#{adict.aaa}引用 adict[@"aaa"]值。