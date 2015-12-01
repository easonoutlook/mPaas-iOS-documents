@cnName 2.3.1 概述
@priority 1

### 2.3.1 概述

[TOC]

#### DAO 使用场景

普通 KV 存储只能存储简单数据类型，或封装好的 OC 对象，也不支持搜索。当业务有 sqlite 访问需要时，可由统一存储的 DAO 功能进行简化和封装。

#### DAO 基本工作原理

![DAO](https://os.alipayobjects.com/rmsportal/KtfSCPVZcjFfyNL.png)

* 定义一个 xml 格式的配置文件，描述各 sqlite 操作的函数，返回的数据类型，需要加密的字段等。
* 定义一个 DAO 对象的接口 DAOInterface（@protocol），接口方法名、参数与配置文件里的描述一致。
* 业务将 xml 配置文件传给 APDataCenter 的 daoWithPath 方法，生成 DAO 访问对象。该对象直接强转为 id<DAOInterface>
* 接下来业务就可以直接调用 DAO 对象的方法，统一存储会将该方法转换为配置文件里描述的数据库操作。

#### 一个最简单的例子

* 配置文件第一行定义了默认表名和数据库版本，以及初始化方法。
* insertItem 和 getItem 是插入和读取数据的两个方法，接收参数并格式化到 sql 表达式里。
* createTable 方法会在底层被默认调用一次。
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
* DAO 接口定义
```C
@protocol DemoProtocol <APDAOProtocol>
 - (APDAOResult*)insertItem:(NSString*)content;
 - (NSString*)getItem:(NSNumber*)index;
@end
```
* 创建 DAO 代理对象，假设配置文件叫 demo_config.xml，在 Main Bundle 里。
* 用 insertItem 方法写入一个数据，获取它的索引，再用该索引把写入的数据读出来。
```C
NSString* filePath = [[NSBundle mainBundle] pathForResource:@"demo_config" ofType:@"xml"];
id<DemoProtocol> proxy = [[APDataCenter defaultDataCenter] daoWithPath:filePath userDependent:YES];
[proxy insertItem:@"something"];
long long lastIndex = [proxy lastInsertRowId];
NSString* content = [proxy getItem:[NSNumber numberWithInteger:lastIndex]];
NSLog(@"content %@", content);
```
* 其中 lastInsertRowId 是 APDAOProtocol 的一个方法，用来取最后插入的行的 rowId。想让 DAO 对象支持该方法，只要在声明 DemoProtocol 时继承自 APDAOProtocol 即可。