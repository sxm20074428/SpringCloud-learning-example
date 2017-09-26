# 熔断器
Feign Hystrix

因为熔断只是作用在服务调用这一端，因此我们根据上一篇的示例代码只需要改动spring-cloud-consumer项目相关代码就可以。
因为，Feign中已经依赖了Hystrix，所以在maven配置上不用做任何改动。  

1、配置文件
application.properties添加这一条：
```
feign.hystrix.enabled=true
```
2、创建回调类  
创建HelloRemoteHystrix类继承与HelloRemote实现回调的方法

```
@Component
public class HelloRemoteHystrix implements HelloRemote{

    @Override
    public String hello(@RequestParam(value = "name") String name) {
        return "hello" +name+", this messge send failed ";
    }
}
```

3、添加fallback属性

在HelloRemote类添加指定fallback类，在服务熔断的时候返回fallback类中的内容。
```
@FeignClient(name= "spring-cloud-producer",fallback = HelloRemoteHystrix.class)
public interface HelloRemote {

    @RequestMapping(value = "/hello")
    public String hello(@RequestParam(value = "name") String name);

}
```