@cnName 2.3.2 关键字
@priority 2

### 2.3.2 关键字

[TOC]

#### module
```xml
<module name="demo" initializer="createTable" tableName="tableDemo" version="1.5" resetOnUpgrade="true" upgradeMinVersion="1.2">
```
* initializer 参数可选，对于 initializer 指定的 update 方法，DAO 认为是数据库建表方法，会在第一次 DAO 请求时默认执行一次；
* tableName 指定下面方法里默认操作的表名，在 sql 语句里可以用${T}或${t}代替，不用每次都写表名了。建议每个配置文件只操作一张表。tableName 可空，应对同一配置文件操作相同格式的多张表的情况。比如聊天消息分表处理，可以调用 DAO 对象的 setTableName 方法设置需要操作的表名。
* version 是配置文件的版本号，请使用'x.x'的格式，创建 table 后，会以 tableName 作为 key，把表的版本存到数据库文件的 TableVersions 表里，配合 upgrade 块进行表的更新。
* resetOnUpgrade，如果为 true 或 YES，当 version 更新后，会删除原表，而不是调用 ungrade 块。无此参数为默认为 false。
* upgradeMinVersion，如果不为空，对于小于这个版本的数据库文件，直接重置，否则执行升级操作。

#### const
```xml
<const table_columns="(id, time, content, uin, read)"/>
```
* 定义一个字符串类型的常量，table_columns 是常量的名字，等号后面的是常量值，在配置文件里可以使用${常量名}来引用。

#### select
```xml
<select id="find" arguments="id, time" result=":messageModelMap">
  select * from ${T} where id = #{id} and time > @{time}
</select>
```
@arguments
* 参数名的列表，用','分隔。调用者传进来的参数依赖 arguments 里的描述依次命名。DAO 对象的 selector 调用时不会携带参数名，所以必须在这里按顺序命名。
* 参数名前面有$符号时，表示这个参数不接受 nil 值。业务的调用 DAO 接口，是允许传 nil 参数的，如果某个参数前面有$符号，当调用者不小心传入了 nil 值，DAO 调用会自动失败。防止发生不可预知的问题。
* 关于如何引用参数，参考[DAO 引用方式](./reference.md)。

比如上面这句，对应的 selector 为：

```C
- (MessageModel*)find:(NSNumber*)id time:(NSNumber*)time;
```
如果 DAO 对象调用[daoProxy find:@1234 time:@2014]，那么 sql 语句拼好后是：

```C
select * from tableDemo where id = ? and time > 2014
```
并且@1234 这个 NSNumber 会交给 sqlite 绑定。 

@result
* result 里可以填写 DAO 方法的返回值，用[]括起来表示返回数组类型，会对 select 执行的返回一直进行迭代，直到 sqlite 无结果返回。如果不用[]括起来，表示只返回一个结果，对 select 执行的返回只迭代一次，类似 FMDB 库里 FMResultSet 只调用一次 next 方法。

返回类型
* int：只有一个结果，返回[NSNumber numberWithInt]类型
* long long：只有一个结果，返回[NSNumber numberWithLongLong]类型
* bool：只有一个结果，返回[NSNumber numberWithBool]类型
* double：只有一个结果，返回[NSNumber numberWithDouble]类型
* string：只有一个结果，返回 NSString*类型
* binary：只有一个结果，返回 NSData*类型
* [int]：返回数组，数组里为[NSNumber numberWithInt]
* [long long]：返回数组，数组里为[NSNumber numberWithLongLong]
* [bool]：返回数组，数组里为[NSNumber numberWithBool]
* [double]：返回数组，数组里为[NSNumber numberWithDouble]
* [string]：返回数组，数组里为 NSString*
* [binary]：返回数组，数组里为 NSData*
* [{}]：返回数组，数组里是列名->列值的 map
* [AType]：返回数组，数组里是填好的自定义类
* {}：只有一个结果，返回列名->列值的 map
* AType：只有一个结果，返回填好的自定义类
* [:AMap]：返回数组，数组里是使用 xml 里定义的 AMap 映射出来的对象
* :AMap：只有一个结果，使用配置文件里定义的 AMap 来描述对象

比如上面的例子，返回类型为":messageModelMap"。具体返回的 Objective-C 类型，以及需要特殊映射的列都会在 messageModelMap 里定义。参考 map 关键字。

@foreach
select 也支持 foreach 字段，用法下文介绍的 insert、update、delete 里的相似。不同的是，select 方法如果指定了 foreach 参数，那么会执行 N 次 SQL 的 select 操作，并把结果放到数组里返回。所以如果 DAO 的 select 方法是 foreach 的，它的返回值在 protocol 里一定要定义成 NSArray*

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
* insert、update、delete 方法格式相同，参数的拼接与引用和 select 方法相同。
* insert 和 delete 关键字是为了更好的区分方法用途。你完全可以把一个 delete from table 的操作写在 update 函数中。
* 在 DAO 接口中，需要执行 insert、update 或 delete 的方法，返回值为 APDAOResult*，标识执行是否成功。参考[DAO 最简单例子](#3)

@foreach
* 当 insert、update、delete 方法后面有'foreach'字段时，这个方法被调用时，会依次对参数数组里的每个元素执行一次 sql。
* 要求 foreach 字段遵循格式： collectionName.item。其中 collectionName 必须对应 arguments 里的一个参数，指定循环的容器对象，并且调用时必须为一个 NSArray 或 NSSet 类型； item 表示从容器里取出的对象，用作循环变量。不能和 arguments 里的任何参数重名，这个 item 可以当作一个普通参数用在 sql 语句里。 

比如代理方法为
```C
- (void)addMessages:(NSArray*)messages;
```
messages 为 MessageModel 数组，那么对于 messages 里的每个 model，都会执行一次 sql 调用，这样就能实现把数组里的元素一次性插入数据库而上层不需要关心循环的调用。底层会把这次操作合并为一个事务而提升效率。

@step
* 在 insert, update, delete 方法里，可能遇到这种情况：执行一个 DAO 方法，但是需要调用多次 sql update 操作执行多条 sql 语句。比如用户建表后，又要创建一些索引。在函数里<step>包裹的语句，都会当作一次 sql update 被单独执行，底层会把所有操作合并为一次事务。比如上图的 createTable 方法。
* 如果一个函数里面有 step 了，那么在 step 外面不允许有文本了。step 内不能再含其它 step。

#### map
```xml
<map id="messageModelMap" result="MessageModel">
  <result property="msgId" column="id"/>
</map>
```
* 定义一个名为 messageModelMap 的映射，实际生成的 Objective-C 对象是 MessageModel 类。
* Objective-C 对象的 msgId 属性，对应表里名为 id 的列值；未列的属性认为和表的列名相同，可以省略。

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
* 随着版本升级，数据库可能有升级需求；处理升级的 sql 语句写在这里。比如最初，配置文件 module 的版本是 1.0，升级后，配置文件的版本为 1.1。那么新版本第一次运行 DAO 方法时，会检测当前表的版本与配置文件的版本，当发现不一致时，会依次执行升级方法。这个方法会由底层自动调用，并在升级完成后再执行 DAO 方法。
* upgrade 按照 sql update 来执行，如果 sql 不止一个，可以用<step>括起来，类似<update id="createTable">的实现。
* 如果需要使用 upgrade 块定义的操作来升级表，那么 module 里的 resetOnUpgrade 必须设置为 false。

#### crypt
```xml
<crypt class="MessageModel" property="content"/>
```
* 描述 class 这个类的 property 属性进行加密处理，当向数据库写入时从这个属性里取出的值会进行加密；当数据库读出时，生成对象向这个属性里赋值会先解密再赋值。

比如执行 DAO 方法，model 是 MessageModel 类。因为取了 model 的 content 属性，所以会加密后再写入数据库。
```xml
<insert id="insertMessage" arguments="model">
     insert into ${T} (content) values(#{model.content})
</insert>
```
执行这个 select 方法时，返回对象是 MessageModel 类。底层从数据库里取出数据向 MessageModel 的实例写入 content 属性时，会将数据先解密再写入。最后返回处理好的 MessageModel 对象。
```xml
<select id="getMessage" arguments="msgId" result="MessageModel">
     select * from ${T} where msgId = #{msgId}
</select>
```

加密方式的设置方法定义在[APDAOProtocol](#3.3)中，如下
```C
/**
 *  设置加密器，用于加密表里标记为需要加密的列的数据。向表里写数据时，碰到这个列，会调用进行加密。
 *
 *  @param crypt 加密结构体，会被拷贝一份。如果传入的 crypt 是外部创建的，需要外部进行 free。如果是 APDefaultEncrypt()，不需要进行释放。
 */
- (void)setEncryptMethod:(APDataCrypt*)crypt;

/**
 *  设置解密器，用于解密表里标记为需要加密的列的数据。从表里读数据时，碰到这个列，会调用进行解密。
 *
 *  @param crypt 解密结构体，会被拷贝一份。如果传入的 crypt 是外部创建的，需要外部进行 free。如果是 APDefaultDecrypt()，不需要进行释放。
 */
- (void)setDecryptMethod:(APDataCrypt*)crypt;
```
如果不进行设置，会使用 APDataCenter 的默认加密，见[KV 存储](#2)。
如果一个 DAO 代理对象是 id<DAOProtocol>，并且 DAOProtocol 是@protocol<APDAOProtocol>，那么可以直接用 DAO 代理对象调用 setEncryptMethod 和 setDecryptMethod 来设置加密、解密方法。

#### if
```xml
<insert id="addMessages" arguments="messages, onlyRead" foreach="messages.model">
    <if true="model.read or (not onlyRead)">
    insert or replace into ${T} (msgId, content, read) values(#{model.msgId}, #{model.content}, #{model.read})
    </if>
</insert>
```
* 在 insert、update、delete、select 方法里，可以嵌套使用 if 条件判断语句。当 if 条件满足时，会把 if 块内的文本拼接到最终的 sql 语句里。
* if 后可以接 true="expr"，也可以接 flase="expr"； expr 为表达式，可以使用方法的参数，并且可以使用"."来链式访问参数对象的属性。
* 表达式支持的运算符如下：

  ()：括号

  +：正号

  -：负号

  +：加号

  -：减号

  *：乘号

  /：除号

  \：整除

  %：取模

  &gt;：大于

  <：小于

  &gt;=：大于等于

  <=：小于等于

  ==：等于

  !=：不等于

  and：逻辑与，不区分大小写

  or：逻辑或，不区分大小写

  not：逻辑非，不区分大小写

  xor：异或，不区分大小写
* 大于号、小于号字符需要使用转义。参考 http://lidongbest5.com/blog/5/
* 里面的参数就是外部调用传入的参数名，但是不要像 sql 块里使用#{}或@{}包裹；
* nil 含义同Objective-C 里的 nil；
* 表达式里的字符串使用单引号起止，不支持转义字符，但是支持\'代表一个单引号；
* 当参数为 Objective-C 对象时，支持用'.'来访问它的属性，比如上面例子 model.read，如果参数是数组或字典，可以用'参数名.count'取元素数。

一个较复杂的表达式如下：
```xml
<if true="(title != nil and year > 2010) or authors.count >= 2">
```
title，year，authors 都是调用者传来的参数，调用时 title 是可以传 nil 的；
那么上面含义为"当书名不为空，并且出版年份大于 2010 年，或作者数大于 2"

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
* 实现类似 switch 的语法，表达式要求类似 if 语句； when 后面也可以接 true="expr"或 false="expr"。
* 只会执行第一个符合条件的 when 或者 otherwise；可以没有 otherwise。

#### foreach
```xml
<foreach item="iterator" collection="list" open="(" separator="," close=")" reverse="yes">
  @{iterator}
</foreach>
```
* open，separator，close，reverse 可以省略。
* item 表示循环变量，collection 表示循环数组参数名。

比如一个方法从外部接收字符串数组参数，list 内容为@[@"1", @"2", @"3"]，有另一个参数是 prefix=@"abc"，使用'()'包裹，','分隔。那么执行结果为：(abc1,abc2,abc3)
```xml
<update id="proc" arguments="list, prefix">
   <foreach item="iterator" collection="list" open="(" separator="," close=")">
       {prefix}{iterator}
   </foreach>
</update>
```
foreach 语句通常用于拼接 select 语句里的 in 块，比如：
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
where 会处理多余的 AND、OR（大小写无所谓），并在任何条件都不符合时连where 都不返回。用于在 sql 语句里拼接有大量判断条件的 where 子句。比如上例，如果只有最后一个判断成立，该语句会正确返回 where author_name like XXX，而不是 where AND author_name like XXX。
```xml
<set>
  <if false="username != nil">username=#{username},</if>
  <if false="password != nil">password=#{password},</if>
  <if true="email != nil">email=#{email},</if>
  <if true="bio != nil">bio=#{bio},</if>
</set>
```
set 会处理结尾多余的','，并在任何条件都不符合时什么都不返回。与 where 语句类似，只是它处理的是后缀的逗号。
```xml
<trim prefix="WHERE" prefixOverrides="AND | OR | and | or " onVoid="ignoreAfter">
</trim>
<!--
  等价于<where>
-->

<trim prefix="SET" suffixOverrides=",">
</trim>
<!--
  等价于<set>
-->
```
* where 和 set 语句可以使用 trim 替换。Trim 语句定义了语句的整体前缀，以及对每个子句需要处理的多余的前缀与后缀列表（用|划分）。

* onVoid 参数可以出现在 where、set、trim 里，有两个取值"ignoreAfter"和"quit"。分别代表当这个 trim 语句里任何子句都不成立，导致生成一个空串时，采取什么逻辑。ignoreAfter 代码忽略下面的格式化语句，直接返回当前生成的 sql 语句执行，quit 代表不再执行这条 sql 语句，但会返回成功。

#### sql
```xml
<sql id="userColumns"> id,username,password </sql>
<select id="selectUsers" result="{}">
  select ${userColumns}
  from some_table
  where id = #{id}
</select>
```
定义可重用的 sql 代码段，在其它语句中使用${name}来原文引入进来。
name 不能为'T'或't'，因为${T}和${t}代表默认的表名了。sql 块里面可以再引用别的 sql 块。

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
有时，同一个 model 可能多次插入数据库，用 insert or replace 会导致当 model 主键冲突（同主键的 model 已经在数据库存在）时，原先的数据被删除掉，重新 insert。这样会导致同条数据的 rowid 发生变化。用 try except 语句块可以解决这个问题（当然不仅限于解决这种问题）。try except 只能出现在 DAO Method 定义里，前后不能再有其它语句。try 和 except 里面可以包含其它语句块。

当这条 DAO 方法执行时，如果 try 里面的语句执行失败，会自动尝试执行 except 里的语句。如果都失败，这次 DAO 调用才会返回失败。