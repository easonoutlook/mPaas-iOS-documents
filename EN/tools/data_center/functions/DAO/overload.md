@cnName 2.3.6 Function overloading
@priority 6

### 2.3.6 Function overloading

DAO interfaces (@protocol) match the methods in the XML configuration file using the method name and argument count. The argument name has nothing to do with the matching.  Argument names in the XML configuration file can be different from the selector argument names of the DAO interface, as long as they correspond to each other in order. 

For example, two interfaces are defined in XML, with the same method name, but different argument counts. 
```xml
<select id="getMessage" arguments="id" result="[{}]">
    select * from ${T} where id=#{id}
</select>
<select id="getMessage" arguments="id, time, state" result="{}">
    select * from ${T} where id=#{id} and time=#{time} and state=#{state}
</select>
```

* Definition in DAO interfaces. 
```C
@protocol DemoProtocol <APDAOProtocol>
- (NSArray*)getMessage:(NSString*)msgId;
- (NSDictionary*)getMessage:(NSString*)msgId msgTime:(NSNumber*)msgTime msgState:(NSNumber*)msgState;
@end
```

We can see that the selector argument name in the second method in the DAO interface is inconsistent with the argument name in XML file. This is allowed and the call will not be affected. 

