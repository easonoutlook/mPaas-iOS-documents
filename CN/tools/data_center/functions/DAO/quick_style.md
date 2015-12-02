@cnName 2.3.8 高级语法
@priority 8

### 2.3.8 高级语法

[TOC]

#### 快捷语法

```XML
<insert id="addMessages" arguments="messages" foreach="messages.model">
    insert or replace into ${T} (id, time, content, uin, read, date) values(#{model.msgId}, #{model.time}, #{model.content}, #{model.uin}, #{model.read}, #{model.date})
</insert>
```

比如上面这条插入语句，如果 model 对象有很多属性都需要插入到数据库里，那么 values 里面每条属性都要写一次，费时费力。

再比如

```XML
<update id="updateTemplate" arguments="template">
    update ${T} set data = #{template.data}, tag = #{template.tag} where tplId = #{template.id} and tplVersion = #{template.tplVersion}
</update>
```

set 后面如果很多属性都要进行 update，那么 sql 语句写起来很复杂。

快捷语句用来解决这种问题：

```XML
%{'#{model.*}', msgId, time, content, uin, read, date}

等价于

#{model.msgId}, #{model.time}, #{model.content}, #{model.uin}, #{model.read}, #{model.date}
```
```XML
%{'* = #{model.*}', data, tag, id, version}

等价于

data = #{model.data}, tag = #{model.tag}, id = #{model.id}, version = #{model.version}
```

基本格式为
```XML
%{'format', ...}
```
第一个参数包在单引号里，表示格式，后面接的为需要格式化的数据数组。DAO 会对每个对象执行一次，把 format 里的`*`替换为相应数据，拼接到一起。
不用考虑性能的损耗，这个预处理只会执行一次。

上面两个例子可以修改为
```XML
<insert id="addMessages" arguments="messages" foreach="messages.model">
    insert or replace into ${T} (id, time, content, uin, read, date) values(%{'#{model.*}', msgId, time, content, uin, read, date})
</insert>

<update id="updateTemplate" arguments="template">
    update ${T} set %{'* = #{template.*}', data, tag} where tplId = #{template.id} and tplVersion = #{template.tplVersion}
</update>
```

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
有时，同一个 model 可能多次插入数据库，用 insert or replace 会导致当 model 主键冲突（同主键的 model 已经在数据库存在）时，原先的数据被删除掉，重新 insert。这样会导致同条数据的 rowid 发生变化（通常不用关心 rowid，不过有些情况需要 rowid 在整个数据库文件的生命周期保持不变）。用 try except 语句块可以解决这个问题（当然不仅限于解决这种问题）。try except 只能出现在 DAO Method 定义里，前后不能再有其它语句。try 和 except 里面可以包含其它语句块。

当这条 DAO 方法执行时，如果 try 里面的语句执行失败，会自动尝试执行 except 里的语句。如果都失败，这次 DAO 调用才会返回失败。

#### 非空参数

```XML
<insert id="addMessage" arguments="$model">
    insert or replace into ${T} ${table_columns} values(#{model.msgId}, #{model.time}, #{model.content}, #{model.uin}, #{model.read}, #{model.date})
</insert>
```
业务调用 DAO 接口，是允许传 nil 参数的。但当参数名前面有$符号时，表示该参数不接受 nil 值。当调用者不小心传入了 nil 值，DAO 调用会自动失败，防止发生不可预知的问题。

#### 条件格式化

条件格式化类似 foreach 关键字，它会接收一个字典（NSDictionary*）参数，并把字典里的 k-v 对，格式化为"k1=v1, k2=v2"的格式。

```XML
<insert id="updateMessage" arguments="values, conditions">
    update ${T} <format prefix="set" pairs="values" join=","/><format prefix="where" pairs="conditions" join="and"/>
</insert>
```

如果传入的 values 是@{@"uin":@(12345), @"content":@"12345"}，conditions 为@{@"id":@"100"}

这个方法生成的 SQL 语句如下：
```SQL
update table set uin = ?, content = ? where id = ?
```

并将@(12345)，@"12345"，@"100"绑定给 sqlite

___当某个条件传入的 pairs 为空时，prefix 也不会拼接到 SQL 语句里。___