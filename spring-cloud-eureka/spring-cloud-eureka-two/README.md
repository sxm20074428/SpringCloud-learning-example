# 双节点注册中心
Eureka Server除了单点运行之外，还可以通过运行多个实例，并进行互相注册的方式来实现高可用的部署，所以我们只需要将Eureke Server配置其他可用的serviceUrl就能实现高可用部署。

首次我们尝试一下双节点的注册中心的搭建。
1、创建application-peer1.properties，作为peer1服务中心的配置，并将serviceUrl指向peer2
```
spring.application.name=spring-cloud-eureka
server.port=8000
eureka.instance.hostname=peer1
eureka.client.serviceUrl.defaultZone=http://peer2:8001/eureka/
```

2、创建application-peer2.properties，作为peer2服务中心的配置，并将serviceUrl指向peer1
```
spring.application.name=spring-cloud-eureka
server.port=8001
eureka.instance.hostname=peer2
eureka.client.serviceUrl.defaultZone=http://peer1:8000/eureka/
```
3、host转换
在hosts文件中加入如下配置
```
127.0.0.1 peer1
127.0.0.1 peer2
```
4、打包启动
依次执行下面命令
#打包
```
mvn clean package
# 分别以peer1和peeer2 配置信息启动eureka
java -jar spring-cloud-eureka-0.0.1-SNAPSHOT.jar --spring.profiles.active=peer1
java -jar spring-cloud-eureka-0.0.1-SNAPSHOT.jar --spring.profiles.active=peer2
```
