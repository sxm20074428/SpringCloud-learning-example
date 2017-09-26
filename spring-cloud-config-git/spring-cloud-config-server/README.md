# Spring Cloud Config
Spring Cloud Config项目是一个解决分布式系统的配置管理方案。  
它包含了Client和Server两个部分，server提供配置文件的存储、以接口的形式将配置文件的内容提供出去，client通过接口获取数据、并依据此数据初始化自己的应用。  
Spring cloud使用git或svn存放配置文件，默认情况下使用git，我们先以git为例做一套示例。  
首先在github上面创建了一个文件夹config-repo用来存放配置文件，为了模拟生产环境，我们创建以下三个配置文件：
```
// 开发环境
    neo-config-dev.properties
// 测试环境
    neo-config-test.properties
// 生产环境
    neo-config-pro.properties
```
每个配置文件中都写一个属性neo.hello,属性值分别是 hello im dev/test/pro 。下面我们开始配置server端
### server 端
1、添加依赖
```
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-config-server</artifactId>
    </dependency>
</dependencies>
```
只需要加入spring-cloud-config-server包引用既可。

2、配置文件
```
server:
port: 8040spring:
application:
name: spring-cloud-config-server
cloud:
config:
server:
git:
uri: https://github.com/ityouknow/spring-cloud-starter/ # 配置git仓库的地址
search-paths: config-repo # git仓库地址下的相对地址，可以配置多个，用,分割。
username: # git仓库的账号
password: # git仓库的密码
```
Spring Cloud Config也提供本地存储配置的方式。我们只需要设置属性spring.profiles.active=native，Config Server会默认从应用的src/main/resource目录下检索配置文件。也可以通过spring.cloud.config.server.native.searchLocations=file:E:/properties/属性来指定配置文件的位置。虽然Spring Cloud Config提供了这样的功能，但是为了支持更好的管理内容和版本控制的功能，还是推荐使用git的方式。

3、启动类

启动类添加@EnableConfigServer，激活对配置中心的支持
```
@EnableConfigServer@SpringBootApplicationpublic class ConfigServerApplication {

public static void main(String[] args) {
SpringApplication.run(ConfigServerApplication.class, args);
}}
```
到此server端相关配置已经完成

4、测试

首先我们先要测试server端是否可以读取到github上面的配置信息，直接访问：http://localhost:8001/neo-config/dev
