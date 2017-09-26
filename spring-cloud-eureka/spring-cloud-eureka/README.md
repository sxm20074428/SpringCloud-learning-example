# spring-cloud-eureka
@EnableEurekaServer 

引用actuator模块，此模块在涉及到健康健康，状态信息都需要引用依赖
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```