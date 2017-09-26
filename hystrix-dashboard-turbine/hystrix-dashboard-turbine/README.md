# 熔断监控

Hystrix-dashboard是一款针对Hystrix进行实时监控的工具，通过Hystrix Dashboard我们可以在直观地看到各Hystrix Command的请求响应时间, 请求成功率等数据。  
但是只使用Hystrix Dashboard的话, 你只能看到单个应用内的服务信息, 这明显不够.   
我们需要一个工具能让我们汇总系统内多个服务的数据并显示到Hystrix Dashboard上, 这个工具就是Turbine.

###Turbine
在复杂的分布式系统中，相同服务的节点经常需要部署上百甚至上千个，很多时候，运维人员希望能够把相同服务的节点状态以一个整体集群的形式展现出来，这样可以更好的把握整个系统的状态。   
为此，Netflix提供了一个开源项目（Turbine）来提供把多个hystrix.stream的内容聚合为一个数据源供Dashboard展示。

1、添加依赖
```
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-turbine</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-netflix-turbine</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-hystrix-dashboard</artifactId>
    </dependency>
</dependencies>
```
2、配置文件
```
spring.application.name=hystrix-dashboard-turbine
server.port=8001
turbine.appConfig=node01,node02
turbine.aggregator.clusterConfig= default
turbine.clusterNameExpression= new String("default")

eureka.client.serviceUrl.defaultZone=http://localhost:8000/eureka/
```
	* turbine.appConfig ：配置Eureka中的serviceId列表，表明监控哪些服务
	* turbine.aggregator.clusterConfig ：指定聚合哪些集群，多个使用”,”分割，默认为default。可使用http://.../turbine.stream?cluster={clusterConfig之一}访问
	* turbine.clusterNameExpression ： 1. clusterNameExpression指定集群名称，默认表达式appName；此时：turbine.aggregator.clusterConfig需要配置想要监控的应用名称；2. 当clusterNameExpression: default时，turbine.aggregator.clusterConfig可以不写，因为默认就是default；3. 当clusterNameExpression: metadata[‘cluster’]时，假设想要监控的应用配置了eureka.instance.metadata-map.cluster: ABC，则需要配置，同时turbine.aggregator.clusterConfig: ABC

3、启动类
启动类添加@EnableTurbine，激活对Turbine的支持
```
@SpringBootApplication@EnableHystrixDashboard@EnableTurbinepublic class DashboardApplication {

public static void main(String[] args) {
SpringApplication.run(DashboardApplication.class, args);
    }
}
```
到此Turbine（hystrix-dashboard-turbine）配置完成
