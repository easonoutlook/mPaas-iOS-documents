@cnName 2.3.6 函数重载
@priority 6

### 2.3.6 函数重载

DAO 接口（@protocol）与XML 配置文件里的方法，使用方法名和参数个数进行匹配，与参数名无关。XML 里的参数名，可以与 DAO 接口里selector 的参数名不一致，只要保证顺序对应即可。

比如，xml 里定义两个接口，方法名相同，参数数量不一样
```xml
<select id="getMessage" arguments="id" result="[{}]">
    select * from ${T} where id=#{id}
</select>
<select id="getMessage" arguments="id, time, state" result="{}">
    select * from ${T} where id=#{id} and time=#{time} and state=#{state}
</select>
```

DAO 接口里的定义
```C
@protocol DemoProtocol <APDAOProtocol>
- (NSArray*)getMessage:(NSString*)msgId;
- (NSDictionary*)getMessage:(NSString*)msgId msgTime:(NSNumber*)msgTime msgState:(NSNumber*)msgState;
@end
```

可以看到，DAO 接口中第二个方法里 selector 的参数名与 xml 里的参数名不一致，这是允许的，同样可以成功调用。