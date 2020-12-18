# Dubbo配置

## API配置

> ### 服务提供者

1. 服务实现

   ```java
   import org.apache.dubbo.rpc.config.ApplicationConfig;
   import org.apache.dubbo.rpc.config.RegistryConfig;
   import org.apache.dubbo.rpc.config.ProviderConfig;
   import org.apache.dubbo.rpc.config.ServiceConfig;
   import com.xxx.XxxService;
   import com.xxx.XxxServiceImpl;
   
   XxxService xxxservice = new XxxServiceImpl();
   ```

2. 当前应用配置

   ```java
   ApplicationConfig application = new ApplicationConfig();
   application.setName("xxx");
   ```

3. 连接注册中心配置

   ```java
   RegistryConfig registry = new RegistryConfig();
   registry.setAddress("10.20.130.230:9090");
   registry.setUsername("aaa");
   registry.setPassword("bbb");
   ```

4. 服务提供者协议配置

   ```java
   ProtocolConfig protocol = new ProtocolConfig();
   protocol.setName("dubbo");
   protocol.setPort(12345);
   protocol.setThreads(200);
   ```

5. 服务提供者暴露服务配置

   ```java
   // 注意：ServiceConfig为重对象，内部封装了与注册中心的连接，以及开启服务端口
   ServiceConfig<XxxService> service = new ServiceConfig<XxxService>(); // 此实例很重，封装了与注册中心的连接，请自行缓存，否则可能造成内存和连接泄漏
   service.setApplication(application);
   service.setRegistry(registry); // 多个注册中心可以用setRegistries()
   service.setProtocol(protocol); // 多个协议可以用setProtocols()
   service.setInterface(XxxService.class);
   service.setRef(xxxService);
   service.setVersion("1.0.0");
   ```

6. 暴露及注册服务

   ```java
   service.export();
   ```

   > ### 服务消费者

1. 当前应用配置

   ```java
   import org.apache.dubbo.rpc.config.ApplicationConfig;
   import org.apache.dubbo.rpc.config.RegistryConfig;
   import org.apache.dubbo.rpc.config.ConsumerConfig;
   import org.apache.dubbo.rpc.config.ReferenceConfig;
   import com.xxx.XxxService;
    
   ApplicationConfig application = new ApplicationConfig();
   application.setName("yyy");
   ```

   2.连接注册中心配置

   ```java
   RegistryConfig registry = new RegistryConfig();
   registry.setAddress("10.20.130.230:9090");
   registry.setUsername("aaa");
   registry.setPassword("bbb");
   ```

   3.引用远程服务

   ```java
   // 注意：ReferenceConfig为重对象，内部封装了与注册中心的连接，以及与服务提供方的连接
   ReferenceConfig<XxxService> reference = new ReferenceConfig<XxxService>(); // 此实例很重，封装了与注册中心的连接以及与提供者的连接，请自行缓存，否则可能造成内存和连接泄漏
   reference.setApplication(application);
   reference.setRegistry(registry); // 多个注册中心可以用setRegistries()
   reference.setInterface(XxxService.class);
   reference.setVersion("1.0.0");
   ```

   4.和本地bean一样使用xxxService

   ```java
   XxxService xxxService = reference.get(); // 注意：此代理对象内部封装了所有通讯细节，对象较重，请缓存复用
   ```

   > ### 特殊场景

   #### 方法级设置

   ```java
   ...
    
   // 方法级配置
   List<MethodConfig> methods = new ArrayList<MethodConfig>();
   MethodConfig method = new MethodConfig();
   method.setName("createXxx");
   method.setTimeout(10000);
   method.setRetries(0);
   methods.add(method);
    
   // 引用远程服务
   ReferenceConfig<XxxService> reference = new ReferenceConfig<XxxService>(); // 此实例很重，封装了与注册中心的连接以及与提供者的连接，请自行缓存，否则可能造成内存和连接泄漏
   ...
   reference.setMethods(methods); // 设置方法级配置
    
   ...
   
   ```

   #### 点对点直连

   ```java
   
   ...
    
   ReferenceConfig<XxxService> reference = new ReferenceConfig<XxxService>(); // 此实例很重，封装了与注册中心的连接以及与提供者的连接，请自行缓存，否则可能造成内存和连接泄漏
   // 如果点对点直连，可以用reference.setUrl()指定目标地址，设置url后将绕过注册中心，
   // 其中，协议对应provider.setProtocol()的值，端口对应provider.setPort()的值，
   // 路径对应service.setPath()的值，如果未设置path，缺省path为接口名
   reference.setUrl("dubbo://10.20.130.230:20880/com.xxx.XxxService"); 
    
   ...
   
   ```

   > API使用范围说明：API 仅用于 OpenAPI, ESB, Test, Mock 等系统集成，普通服务提供方或消费方，请采用[XML 配置](http://dubbo.apache.org/zh/docs/v2.7/user/configuration/xml)方式使用 Dubbo 

## xml配置

### provider.xml 示例

```java
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">
    <dubbo:application name="demo-provider"/>
    <dubbo:registry address="zookeeper://127.0.0.1:2181"/>
    <dubbo:protocol name="dubbo" port="20890"/>
    <bean id="demoService" class="org.apache.dubbo.samples.basic.impl.DemoServiceImpl"/>
    <dubbo:service interface="org.apache.dubbo.samples.basic.api.DemoService" ref="demoService"/>
</beans>
```

### consumer.xml示例

```java
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">
    <dubbo:application name="demo-consumer"/>
    <dubbo:registry group="aaa" address="zookeeper://127.0.0.1:2181"/>
    <dubbo:reference id="demoService" check="false" interface="org.apache.dubbo.samples.basic.api.DemoService"/>
</beans>
```

**所有标签都支持自定义参数，用于不同扩展点实现的特殊配置，如：**

```java
<dubbo:protocol name="jms">
    <dubbo:parameter key="queue" value="your_queue" />
</dubbo:protocol>
```

```java
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">  
    <dubbo:protocol name="jms" p:queue="your_queue" />  
</beans>
```

### 配置之间的关系

![配置关系](dubbo学习笔记.assets/dubbo-config.jpg)

| 标签                                                         | 用途         | 解释                                                         |
| :----------------------------------------------------------- | ------------ | ------------------------------------------------------------ |
| `<dubbo:service/>`                                           | 服务配置     | 用于暴露一个服务，定义服务的元信息，一个服务可以用多个协议暴露，一个服务也可以注册到多个注册中心 |
| `<dubbo:reference/>` [2](http://dubbo.apache.org/zh/docs/v2.7/user/configuration/xml/#fn:2) | 引用配置     | 用于创建一个远程服务代理，一个引用可以指向多个注册中心       |
| `<dubbo:protocol/>`                                          | 协议配置     | 用于配置提供服务的协议信息，协议由提供方指定，消费方被动接受 |
| `<dubbo:application/>`                                       | 应用配置     | 用于配置当前应用信息，不管该应用是提供者还是消费者           |
| `<dubbo:module/>`                                            | 模块配置     | 用于配置当前模块信息，可选                                   |
| `<dubbo:registry/>`                                          | 注册中心配置 | 用于配置连接注册中心相关信息                                 |
| `<dubbo:monitor/>`                                           | 监控中心配置 | 用于配置连接监控中心相关信息，可选                           |
| `<dubbo:provider/>`                                          | 提供方配置   | 当 ProtocolConfig 和 ServiceConfig 某属性没有配置时，采用此缺省值，可选 |
| `<dubbo:consumer/>`                                          | 消费方配置   | 当 ReferenceConfig 某属性没有配置时，采用此缺省值，可选      |
| `<dubbo:method/>`                                            | 方法配置     | 用于 ServiceConfig 和 ReferenceConfig 指定方法级的配置信息   |
| `<dubbo:argument/>`                                          | 参数配置     | 用于指定方法参数配置                                         |

### 不同粒度配置的覆盖关系

以 timeout 为例，下图显示了配置的查找顺序，其它 retries, loadbalance, actives 等类似：

- 方法级优先，接口级次之，全局配置再次之。
- 如果级别一样，则消费方优先，提供方次之。

其中，服务提供方配置，通过 URL 经由注册中心传递给消费方。

![覆盖关系](D:\Typora\md\dubbo-config-override.jpg)

> （建议由服务提供方设置超时，因为一个方法需要执行多长时间，服务提供方更清楚，如果一个消费方同时引用多个服务，就不需要关心每个服务的超时设置）。
>
> 理论上 ReferenceConfig 中除了`interface`这一项，其他所有配置项都可以缺省不配置，框架会自动使用ConsumerConfig，ServiceConfig, ProviderConfig等提供的缺省配置。
>

1. `2.1.0` 开始支持，注意声明：`xmlns:p="http://www.springframework.org/schema/p"` [↩︎](http://dubbo.apache.org/zh/docs/v2.7/user/configuration/xml/#fnref:1)
2. 引用缺省是延迟初始化的，只有引用被注入到其它 Bean，或被 `getBean()` 获取，才会初始化。如果需要饥饿加载，即没有人引用也立即生成动态代理，可以配置：`<dubbo:reference ... init="true" />` 

## 注解配置

>### 服务提供方

#### Service注解暴露服务

```java
@Service
public class AnnotationServiceImpl implements AnnotationService {
    @Override
    public String sayHello(String name) {
        return "annotation: hello, " + name;
    }
}
```

#### 增加应用共享配置

```java
# dubbo-provider.properties
dubbo.application.name=annotation-provider
dubbo.registry.address=zookeeper://127.0.0.1:2181
dubbo.protocol.name=dubbo
dubbo.protocol.port=20880
```

#### 指定Spring扫描路径

```java
@Configuration
@EnableDubbo(scanBasePackages = "org.apache.dubbo.samples.simple.annotation.impl")
@PropertySource("classpath:/spring/dubbo-provider.properties")
static public class ProviderConfiguration {
       
}
```

>### 服务消费方

#### Reference注解引用服务

```java
@Component("annotationAction")
public class AnnotationAction {

    @Reference
    private AnnotationService annotationService;
    
    public String doSayHello(String name) {
        return annotationService.sayHello(name);
    }
}
```

#### 增加应用共享配置

```java
# dubbo-consumer.properties
dubbo.application.name=annotation-consumer
dubbo.registry.address=zookeeper://127.0.0.1:2181
dubbo.consumer.timeout=3000
```

#### 指定Spring扫描路径

```java
@Configuration
@EnableDubbo(scanBasePackages = "org.apache.dubbo.samples.simple.annotation.action")
@PropertySource("classpath:/spring/dubbo-consumer.properties")
@ComponentScan(value = {"org.apache.dubbo.samples.simple.annotation.action"})
static public class ConsumerConfiguration {

}
```

#### 调用服务

```java
public static void main(String[] args) throws Exception {
    AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(ConsumerConfiguration.class);
    context.start();
    final AnnotationAction annotationAction = (AnnotationAction) context.getBean("annotationAction");
    String hello = annotationAction.doSayHello("world");
}
```

## 动态配置中心

> 配置中心（v2.7.0）在 Dubbo 中承担两个职责：
>
> 1. 外部化配置。启动配置的集中式存储 （简单理解为 dubbo.properties 的外部化存储）。
> 2. 服务治理。服务治理规则的存储与通知。
>
> 启用动态配置，以 Zookeeper 为例

```java
<dubbo:config-center address="zookeeper://127.0.0.1:2181"/>
```

或者：

```java
dubbo.config-center.address=zookeeper://127.0.0.1:2181
```

或者：

```java
ConfigCenterConfig configCenter = new ConfigCenterConfig();
configCenter.setAddress("zookeeper://127.0.0.1:2181");
```

> 为了兼容 2.6.x 版本配置，在使用 Zookeeper 作为注册中心，且没有显示配置配置中心的情况下，Dubbo 框架会默认将此 Zookeeper 用作配置中心，但将只作服务治理用途。

### 外部化配置

> 外部化配置目的之一是实现配置的集中式管理，这部分业界已经有很多成熟的专业配置系统如 Apollo, Nacos 等，Dubbo 所做的主要是保证能配合这些系统正常工作。

> 外部化配置和其他本地配置在内容和格式上并无区别，可以简单理解为 `dubbo.properties` 的外部化存储，配置中心更适合将一些公共配置如注册中心、元数据中心配置等抽取以便做集中管理。

```java
# 将注册中心地址、元数据中心地址等配置集中管理，可以做到统一环境、减少开发侧感知。
dubbo.registry.address=zookeeper://127.0.0.1:2181
dubbo.registry.simplified=true

dubbo.metadata-report.address=zookeeper://127.0.0.1:2181

dubbo.protocol.name=dubbo
dubbo.protocol.port=20880

dubbo.application.qos.port=33333
```

* **优先级**

> 外部化配置默认较本地配置有更高的优先级，因此这里配置的内容会覆盖本地配置值，关于 [各配置形式间的覆盖关系](http://dubbo.apache.org/zh/docs/v2.7/user/configuration/configuration-load-process) 有单独一章说明，你也可通过以下选项调整配置中心的优先级：

```fallback
-Ddubbo.config-center.highest-priority=false
```

* **作用域**

> 外部化配置有全局和应用两个级别，全局配置是所有应用共享的，应用级配置是由每个应用自己维护且只对自身可见的。当前已支持的扩展实现有Zookeeper、Apollo

### Zookeeper

```java
<dubbo:config-center address="zookeeper://127.0.0.1:2181"/>
```

默认所有的配置都存储在`/dubbo/config`节点，具体节点结构图如下：

![](Dubbo配置.assets/zk-configcenter.jpg)

* namespace，用于不同配置的环境隔离。

* config，Dubbo约定的固定节点，不可更改，所有配置和服务治理规则都存储在此节点下。

* dubbo/application，分别用来隔离全局配置、应用级别配置：dubbo是默认group值，application对应应用名

* dubbo.properties，此节点的node value存储具体配置内容

  