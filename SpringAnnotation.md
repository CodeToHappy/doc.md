# - Annotation - 
	开发中我们可以使用注解 取代xml配置文件
	Link: https://zhuanlan.zhihu.com/p/137507309
## @SpringBootApplication
	Spring Boot的项目一般都会有XxxApplication的入口类，入口类中会有main方法，这是一个标准的Java应用程序的入口方法。
	这个入口类都会有@SpringBootApplication注解，它让Spring Boot自动给程序进行必要的配置，该注解是SpringBoot特有的。
### 举例
```java
@SpringBootApplication
public class DemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}
```
	这个配置等同于以下几个注解之和：@SpringBootConfiguration，@EnableAutoConfiguration， @ComponentScan
----
## @SpringBootConfiguration
	表示Application作为配置文件存在
	@SpringBootConfiguration继承自@Configuration，二者功能也一致，标注当前类是配置类，
	并会将当前类内声明的一个或多个以@Bean注解标记的方法的实例纳入到spring容器中，并且实例名就是方法名。
```java
//定义了一个配置类
package com.lhkj.pluto.config;

import java.util.HashMap;
import java.util.Map;

import org.springframework.boot.SpringBootConfiguration;
import org.springframework.context.annotation.Bean;

@SpringBootConfiguration
public class Config {
    @Bean
    public Map createMap(){
        Map map = new HashMap();
        map.put("username","gxz");
        map.put("age",27);
        return map;
    }
}
```
----
```java
在main方法中，可以直接这样使用:
package com.lhkj.pluto;

import java.util.Map;
import java.util.concurrent.TimeUnit;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.embedded.EmbeddedServletContainerFactory;
import org.springframework.boot.context.embedded.tomcat.TomcatEmbeddedServletContainerFactory;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.lhkj.pluto.user.entity.User;

/*
 * 发现@SpringBootApplication是一个复合注解，
 * 包括@ComponentScan，和@SpringBootConfiguration，@EnableAutoConfiguration
 * 
 */

@RestController
@SpringBootApplication
public class App 
{     
    @RequestMapping(value="/hello")
    public String Hello(){
        return "hello";
    }  
    
    @Bean
    public Runnable createRunnable() {
        return () -> System.out.println("spring boot is running");
    }

    public static void main( String[] args )
    {
        System.out.println( "Hello World!" );
        ConfigurableApplicationContext context = SpringApplication.run(App.class, args);
        context.getBean(Runnable.class).run();
        System.out.println(context.getBean(User.class));
        Map map = (Map) context.getBean("createMap");   //注意这里直接获取到这个方法bean
        int age = (int) map.get("age");
        System.out.println("age=="+age);
    }
     
    @Bean
    public EmbeddedServletContainerFactory servletFactory(){
        TomcatEmbeddedServletContainerFactory tomcatFactory = 
                new TomcatEmbeddedServletContainerFactory();
        //tomcatFactory.setPort(8011);
        tomcatFactory.setSessionTimeout(10,TimeUnit.SECONDS);
        return tomcatFactory;
        
    }
}

```
----
## @EnableAutoConfiguration
	表示启用SpringBoot内置的自动配置功能
----
## @ComponentScan 
	自动扫描和装配一些bean，路径为Application类所在package以及package下的子路径，在spring boot中bean都放置在该路径以及子路径下
----
## @Configuration
	等同于spring的XML配置文件；使用Java代码可以检查类型安全。
	@Component可配合CommandLineRunner使用，在程序启动后执行一些基础任务。
----
### CommandLineRunner
	在使用SpringBoot构建项目时，我们通常有一些预先数据的加载。
	那么SpringBoot提供了一个简单的方式来实现–CommandLineRunner。
	CommandLineRunner是一个接口，我们需要时，只需实现该接口就行。
	如果存在多个加载的数据，我们也可以使用@Order注解来排序。
----
#### @Order	
```java
@Component
@Order(value = 2)
public class MyStartupRunner1 implements CommandLineRunner{
@Override
public void run(String... strings) throws Exception {
    System.out.println(">>>>>>>>>>>>>>>服务启动执行，执行加载数据等操作 MyStartupRunner1 order 2 <<<<<<<<<<<<<");
    }
}

@Component
@Order(value = 1)
public class MyStartupRunner2 implements CommandLineRunner {
@Override
public void run(String... strings) throws Exception {
    System.out.println(">>>>>>>>>>>>>>>服务启动执行，执行加载数据等操作 MyStartupRunner2 order 1 <<<<<<<<<<<<<");
    }
}
```
输出：（图片插入 ./SpringBoot_CommandLineRunner.png）
----
### ApplicationRunner
	ApplicationRunner中run方法的参数为ApplicationArguments，而CommandLineRunner接口中run方法的参数为String数组。
	想要更详细地获取命令行参数，那就使用ApplicationRunner接口.
----
#### CommandLineRunner
``` java
@Component
@Order(value = 11)
public class AgentApplicationRun implements CommandLineRunner {

	@Override
	public void run(String... strings) throws Exception {

	}
}
```
#### ApplicationRunner
```java
@Component
@Order(value = 10)
public class AgentApplicationRun2 implements ApplicationRunner {
	@Override
	public void run(ApplicationArguments applicationArguments) throws Exception {

	}
}
```
----
## @RequestMapping
	@RequestMapping注解是用来映射请求的，即指明处理器可以处理哪些URL请求，该注解既可以用在类上，也可以用在方法上。
	当使用@RequestMapping标记控制器类时，方法的请求地址是相对类的请求地址而言的；
	当没有使用@RequestMapping标记类时，方法的请求地址是绝对路径。
	@RequestMapping的地址可以是uri变量，并且通过@PathVariable注解获取作为方法的参数。也可以是通配符来筛选请求地址。
  
	1. value:指定请求的实际地址，指定的地址可以是URI Template 模式	
	2. method:指定请求的method类型， GET、POST、PUT、DELETE等
		value的uri值为以下三类：
			A） 可以指定为普通的具体值；如@RequestMapping(value ="/testValid")
			B)  可以指定为含有某变量的一类值;如@RequestMapping(value="/{day}")
			C) 可以指定为含正则表达式的一类值;如@RequestMapping(value="/{textualPart:[a-z-]+}.{numericPart:[\\d]+}")  可以匹配../chenyuan122912请求。
	3. params:指定request中必须包含某些参数值是，才让该方法处理。
### params举例
```java
//仅处理请求中包含了名为“name”，值为“chenyuan”的请求.
@RequestMapping(value = "/test", method = RequestMethod.GET, params="name=chenyuan")  
  public void findOrd(String name) {      
    // implementation omitted  
  }
```	
	4. headers:指定request中必须包含某些指定的header值，才能让该方法处理请求。
### headers举例
```java
//仅处理request的header中包含了指定“Refer”请求头和对应值为“www.baidu.com”的请求
@RequestMapping(value = "/test", method = RequestMethod.GET, headers="Referer=www.baidu.com")  
  public void findOrd(String name) {      
    // implementation omitted  
  }
```
	5. consumes:指定处理请求的提交内容类型（Content-Type）
		例如，@RequestMapping(value = "/test", consumes="application/json")处理application/json内容类型
	6. produces:指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回
	
	@RequestParam：用在方法的参数前面
```java	

	@RequestParam
	  String a =request.getParameter(“a”)。
```
	@PathVariable:路径变量。如参数与大括号里的名字一样要相同。
----
### 6种基本运用
	1. 方法级别上的运用：
```java
@RequestMapping(value="/departments")  
public String simplePattern(){  
  
  System.out.println("simplePattern method was called");  
  return "someResult";  
}  
```
	访问....../departments的时候，就调用 simplePattern方法了
	
   2. 参数绑定
```java
@GetMapping("/add")
public void addUser(@RequestParam("name")String name){
	//code...
}
```
	将请求参数绑定到你控制器的方法参数上，是springmvc中接收普通参数的注解。
	请求中的参数名和处理器中的形参名不一致时用 @RequestParam。
	语法：@RequestParam(value=”参数名”,required=”true/false”,defaultValue=””)
	value：参数名
	required：是否包含该参数，默认为true，表示该请求路径中必须包含该参数，如果不包含就报错。
	defaultValue：默认参数值，如果设置了该值，required=true将失效，自动为false,如果没有传该参数，就使用默认值。
	
```java
@RequestMapping(value="/departments")  
public String findDepatment(  
  @RequestParam("departmentId") String departmentId){  
    
    System.out.println("Find department with ID: " + departmentId);  
    return "someResult";  
}  
```
	 访问/departments?departmentId=23就可以触发访问findDepatment方法了
	 
   3. REST风格的参数绑定1
```java
@RequestMapping(value="/departments/{departmentId}")  
public String findDepatment(@PathVariable String departmentId){  
  
  System.out.println("Find department with ID: " + departmentId);  
  return "someResult";  
} 
```
	形如REST风格的地址访问，比如：/departments/23，其中用(@PathVariable接收rest风格的参数
	
   4. REST风格的参数绑定2
```java
@RequestMapping(value="/departments/{departmentId}")  
public String findDepatmentAlternative(  
  @PathVariable("departmentId") String someDepartmentId){  
  
    System.out.println("Find department with ID: " + someDepartmentId);  
    return "someResult"; 
}  
```
    这个有点不同，就是接收形如/departments/23的URL访问，把23作为传入的departmetnId,，但是在实际的方法findDepatmentAlternative中，使用
@PathVariable("departmentId") String someDepartmentId，将其绑定为 someDepartmentId,所以这里someDepartmentId为23

   5. url中同时绑定多个id
```java
@RequestMapping(value="/departments/{departmentId}/employees/{employeeId}")  
public String findEmployee(  
  @PathVariable String departmentId,  
  @PathVariable String employeeId){  
  
    System.out.println("Find employee with ID: " + employeeId +   
      " from department: " + departmentId);  
    return "someResult";   
}
```
	（待解决）
	请求的值是to_upload，返回的视图是background/fileOperate/upload
	
   6. 支持正则表达式,可以用于处理动态的URI，将URI中的一个或者多个值作为参数
```java
@RequestMapping(value="/{textualPart:[a-z-]+}.{numericPart:[\\d]+}")  
public String regularExpression(  
  @PathVariable String textualPart,  
  @PathVariable String numericPart){  
  
    System.out.println("Textual part: " + textualPart +   
      ", numeric part: " + numericPart);  
    return "someResult";  
}  
```
	URL：....../sometext.123，则输出：Textual part: sometext, numeric part: 123
----
### @RequestParam vs @PathVariable
	后端想要获取前端的参数，传统来讲，肯定是两种方式为主，一种是 GET ，一种是 POST ，
	这两种方式都是向一个 URL 传参 GET 方式体现到了地址栏里，POST 方式将内容放在了 body 里。
	
	@RequestParam 和 @PathVariable 注解是用于从 request 中接收请求的，两个都可以接收参数，
	关键点不同的是@RequestParam 是从 request 里面拿取值，而 @PathVariable 是从一个URI模板里面来填充。
	
#### @RequestParam
	获取 request 里的值
```java
@RequestMapping(value = "/getbyid", method = RequestMethod.GET)
	@ResponseBody
	private Map<String, Object> getbyid( HttpServletRequest request,@RequestParam(value="id", required=true)int idnum){
		Map<String, Object> modelMap = new HashMap<String, Object>();
		modelMap.put("idnum", idnum);
		return modelMap;
	}
```
	访问：....../getbyid?id=21
	只能获取 ?id 的值，如果是 POST 方式，我们也可以获取指定值

#### @PathVariable
	处理requet uri部分,当使用@RequestMapping URI template 样式映射时，
	即someUrl/{paramId}, 这时的paramId可通过 @Pathvariable注解绑定它传过来的值到方法的参数上
##### 举例
```java
	@RequestMapping(value = "/getbyid/{id}", method = RequestMethod.GET)
	@ResponseBody
	private Map<String, Object> getbyid( HttpServletRequest request,@PathVariable("id") int idnum){
		Map<String, Object> modelMap = new HashMap<String, Object>();
		modelMap.put("idnum", idnum);
		return modelMap;
	}
```
```java
@Controller 
@RequestMapping("/owners/{a}") 
public class RelativePathUriTemplateController { 
  @RequestMapping("/pets/{b}") 
  public void findPet(@PathVariable("a") String a,@PathVariable String b, Model model) {     
    // implementation omitted 
  } 
```
	访问：....../getbyid/19
	可以直接获取地址里的19

	优缺点
	直接获取 URI 模板里的值是很方便的，不用去获取 request 里的固定参数，比较直接，
	藏在 request 里的使用 POST 方式会优雅一点，如果只是 ID 这种单个或者多个数字字母，
	使用 @PathVariable 会好很多，这里借鉴的是 thinkphp5 里的优点加以利用。

	总结
	在单个参数提交 API 获取信息的时候，直接放在 URL 地址里，
	也就是使用 URI 模板的方式是非常方便的，而不使用 @PathVariable 还需要从 request 里提取指定参数，
	多一步操作，如果提取的是多个参数，而且是多个不同类型的参数，应该使用 @PathParam。
----
#### @RequestHeader vs @CookieValue 
	处理request header部分的注解
##### 举例
	将头部信息绑定到方法参数上
```java
@RequestMapping("/test") 
public void displayHeaderInfo(@RequestHeader("Accept-Encoding") String encoding, 
                              @RequestHeader("Keep-Alive")long keepAlive)  { 
								//code  
}
```
	将cookie里JSESSIONID绑定到方法参数上
```java
@RequestMapping("/test")  
public void displayHeaderInfo(@CookieValue("JSESSIONID") String cookie)  {   
								//code
}
```
----
### @GetMapping
	@GetMapping是一个组合注解，是@RequestMapping(method = RequestMethod.GET)的缩写; 
	举例：@GetMapping("/get/{id}") 等于 @RequestMapping(value = "/get/{id}", method = RequestMethod.GET)；
	限定了请求只能为get，否则报错
### @PostMapping
	同理，@PostMapping是一个组合注解，是@RequestMapping(method = RequestMethod.POST)的缩写；非post请求报错
----
## @RestController = @Controller + @ResponseBody
	注解是@Controller和@ResponseBody的合集,表示这是个控制器bean,并且是将函数的返回值直接填入HTTP响应体中,是REST风格的控制器。

### @Controller + @ResponseBody
  Spring的基于注释的MVC框架简化了创建RESTful Web服务的过程。传统的Spring MVC控制器和RESTful Web服务控制器之间的关键区别在于: 
  创建HTTP响应主体的方式。
  虽然传统的MVC控制器依赖于View技术，但RESTful Web服务控制器只返回对象，对象数据作为JSON / XML直接写入HTTP响应。
 
 
  以下步骤描述了典型的Spring MVC REST工作流：
	1. 客户端以URI形式向Web服务发送请求。
	2. 该请求被DispatcherServlet拦截，该服务器查找Handler Mappings及其类型。
			应用程序上下文文件中定义的Handler Mappings部分告诉DispatcherServlet使用哪种策略根据传入请求查找控制器。
			Spring MVC支持三种不同类型的映射请求URI到控制器：注释，名称约定和显式映射。
	请求由Controller处理，响应返回到DispatcherServlet，然后DispatcherServlet将调度到视图。

  Spring 3.x 或使用@Controller情况下，在方法上使用@ResponseBody注释时，Spring会转换返回值并自动将其写入HTTP响应。
  Controller类中的每个方法都必须使用@ResponseBody进行注释。

  Spring有一个在后台注册的HttpMessageConverters列表。HTTPMessageConverter的职责是将请求主体转换为特定类并再次返回响应主体，
  具体取决于预定义的mime类型。每次发出请求命中@ResponseBody时，Spring都会遍历所有已注册的HTTPMessageConverters，
  寻找符合给定mime类型和类的第一个，然后将其用于实际转换。
 
### @ResponseBody
	将java对象转为json格式的数据。
	@responseBody注解的作用是将controller的方法返回的对象通过适当的转换器转换为指定的格式之后，写入到response对象的body区，
	通常用来返回JSON数据或者是XML数据。
	注意：在使用此注解之后不会再走视图处理器，而是直接将数据写入到输入流中，他的效果等同于通过response对象输出指定格式的数据。
	@ResponseBody 表示该方法的返回结果直接写入 HTTP response body 中，一般在异步获取数据时使用，如AJAX。
 
 ``` java
@Controller
@RequestMapping("employees")
public class EmployeeController {
    Employee employee = new Employee();
    @RequestMapping(value = "/{name}", method = RequestMethod.GET, produces = "application/json")
    public @ResponseBody Employee getEmployeeInJSON(@PathVariable String name) {
       employee.setName(name);
       employee.setEmail("employee1@genuitec.com");
    return employee; 
    }

    @RequestMapping(value = "/{name}.xml", method = RequestMethod.GET, produces = "application/xml")
    public @ResponseBody Employee getEmployeeInXML(@PathVariable String name) {
       employee.setName(name);
     employee.setEmail("employee1@genuitec.com");
       return employee; 
    }
}
```
  注意@ResponseBody添加到返回值中的每个@RequestMapping方法，Spring将做两件事：
	 1. 将<context:component-scan> 和  <mvc:annotation-driven /> 标记添加  到Spring配置文件中。
			<context:component-scan> 激活注释并扫描包以在应用程序上下文中查找和注册bean。 
			<mvc:annotation-driven/> 如果Jackson / JAXB库在类路径上，则添加对读写JSON / XML的支持。 
			对于JSON格式，包括jackson-databind jar，对于XML，包括项目类路径的jaxb-api-osgi jar。
	 2. 可在任何服务器（例如，Tomcat）上部署并运行应用程序。
			....../employees/Bob  并显示输出JSON.
			....../employees/Bob.xml 输出XML
			
### @RestController

	Spring 4.0引入了@RestController，这是一个控制器的专用版本，它是一个方便的注释，
	除了自动添加@Controller和@ResponseBody注释之外没有其他新魔法。
	通过使用@RestController批注对控制器类进行注释，您不再需要将@ResponseBody添加到所有请求映射方法中。
	@ResponseBody注释默认处于活动状态。
	要在我们的示例中使用@RestController，需要做的就是将@Controller修改为@RestController并从每个方法中删除@ResponseBody。
	结果类应如下所示：
```java
@RestController
public class EmployeeController {

    Employee employee = new Employee();

    @GetMapping("/employees/{name}")
    public Employee getEmployeeInJSON(@PathVariable("name") String name) {
       employee.setName(name);
       employee.setEmail("employee1@genuitec.com");
       return employee;
    }
}
```
----			
## @RepositoryRestResourcepublic （待解决）
Link: https://www.jianshu.com/p/3423fa97d185
@JsonBackReference vs @JsonManagedReference vs @JsoIgnore
	Jackson中的这三个注解都是为了解决对象中存在双向引用导致的无限递归（infinite recursion）问题。
	这些标注均可用在属性或对应的get、set方法中。 
----	
### @JsonBackReference和@JsonManagedReference
	这两个标注通常配对使用，通常用在父子关系中。
	@JsonBackReference标注的属性在序列化（serialization，即将对象转换为json数据）时，
	会被忽略（即结果中的json数据不包含该属性的内容）。@JsonManagedReference标注的属性则会被序列化。
	在序列化时，@JsonBackReference的作用相当于@JsonIgnore，此时可以没有@JsonManagedReference。
	但在反序列化（deserialization，即json数据转换为对象）时，如果没有@JsonManagedReference，
	则不会自动注入@JsonBackReference标注的属性（被忽略的父或子）；如果有@JsonManagedReference，
	则会自动注入自动注入@JsonBackReference标注的属性。
----	
### @JsonIgnore
	直接忽略某个属性，以断开无限递归，序列化或反序列化均忽略。
	当然如果标注在get、set方法中，则可以分开控制，序列化对应的是get方法，反序列化对应的是set方法。
	在父子关系中，当反序列化时，@JsonIgnore不会自动注入被忽略的属性值（父或子），
	这是它跟@JsonBackReference和@JsonManagedReference最大的区别。   

举例（注意反序列化后的TreeNode[readValue]的children里的parent）：
TreeNode.java     
```java
import java.util.ArrayList;  
import java.util.List;  
  
import org.codehaus.jackson.annotate.JsonBackReference;  
import org.codehaus.jackson.annotate.JsonManagedReference;  
  
public class TreeNode {  
    String name;  
    @JsonBackReference  
//  @JsonIgnore  
    TreeNode parent;  
    @JsonManagedReference  
    List<TreeNode> children;  
  
    public TreeNode() {  
    }  
  
    public TreeNode(String name) {  
        this.name = name;  
    }  
  
    public String getName() {  
        return name;  
    }  
  
    public void setName(String name) {  
        this.name = name;  
    }  
  
    public TreeNode getParent() {  
        return parent;  
    }  
  
    public void setParent(TreeNode parent) {  
        this.parent = parent;  
    }  
  
    public List<TreeNode> getChildren() {  
        return children;  
    }  
  
    public void setChildren(List<TreeNode> children) {  
        this.children = children;  
    }  
  
    public void addChild(TreeNode child) {  
        if (children == null)  
            children = new ArrayList<TreeNode>();  
        children.add(child);  
    }  
}  
``` 
JsonTest.java  
```java
import java.io.IOException;  
  
import org.codehaus.jackson.JsonGenerationException;  
import org.codehaus.jackson.map.JsonMappingException;  
import org.codehaus.jackson.map.ObjectMapper;  
import org.junit.AfterClass;  
import org.junit.BeforeClass;  
import org.junit.Test;  
  
public class JsonTest {  
    static TreeNode node;  
  
    @BeforeClass  
    public static void setUp() {  
        TreeNode node1 = new TreeNode("node1");  
        TreeNode node2 = new TreeNode("node2");  
        TreeNode node3 = new TreeNode("node3");  
        TreeNode node4 = new TreeNode("node4");  
        TreeNode node5 = new TreeNode("node5");  
        TreeNode node6 = new TreeNode("node6");  
  
        node1.addChild(node2);  
        node2.setParent(node1);  
        node2.addChild(node3);  
        node3.setParent(node2);  
        node2.addChild(node4);  
        node4.setParent(node2);  
        node3.addChild(node5);  
        node5.setParent(node3);  
        node5.addChild(node6);  
        node6.setParent(node5);  
  
        node = node3;  
    }  
  
    @Test   
    public void test() throws JsonGenerationException, JsonMappingException, IOException {  
        ObjectMapper mapper = new ObjectMapper();  
        String json = mapper.writeValueAsString(node);  
        System.out.println(json);  
        TreeNode readValue = mapper.readValue(json, TreeNode.class);  
        System.out.println(readValue.getName());  
    }  
  
    @AfterClass  
    public static void tearDown() {  
        node = null;  
    }  
}  

```
----
*JPA
## @Entity
```java
	@Entity
	@Table(name="")
```
	表明这是一个实体类。一般用于jpa这两个注解一般一块使用
	如果表名和实体类名相同的话，@Table可以省略
----
## @MappSuperClass
	用在确定是父类的entity上。父类的属性子类可以继承
----
## @NoRepositoryBean
	一般用作父类的repository，有这个注解，spring不会去实例化该repository。
----
## @Column
	如果字段名与列名相同，则可以省略
----
## @Id
	表示该属性为主键
----
## @GeneratedValue
```java	
	@GeneratedValue(strategy = GenerationType.SEQUENCE,generator = “repair_seq”)
```
	表示主键生成策略是sequence（可以为Auto、IDENTITY、native等，Auto表示可在多个数据库间切换），
	指定sequence的名字是repair_seq。
----
## @SequenceGeneretor
```java	
	@SequenceGeneretor(name = “repair_seq”, sequenceName = “seq_repair”, allocationSize = 1)
```
	name为sequence的名称，以便使用，sequenceName为数据库的sequence名称，两个名称可以一致
----
## @Transient vs Basic

### @Transient
		表示该属性并非一个到数据库表的字段的映射,ORM框架将忽略该属性。
		如果一个属性并非数据库表的字段映射,就务必将其标示为@Transient,否则,ORM框架默认其注解为@Basic
### @Basic
```java
@Basic(fetch=FetchType.LAZY)
```
		标记可以指定实体属性的加载方式
----
## @JsonIgnore
	作用是json序列化时将Java bean中的一些属性忽略掉,序列化和反序列化都受影响。
----
## @JoinColumn
	@JoinColumn（name=”loginId”）:一对一：本表中指向另一个表的外键。一对多：另一个表指向本表的外键。
----
## @OneToOne、@OneToMany、@ManyToOne
	对应hibernate配置文件中的一对一，一对多，多对一。
----
*全局异常处理
## @ControllerAdvice
	包含@Component。可以被扫描到。统一处理异常。
----
## @ExceptionHandler（Exception.class）
	用在方法上面表示遇到这个异常就执行以下方法。
----
*项目中具体配置解析和使用环境
## @MappedSuperclass
	1.@MappedSuperclass 注解使用在父类上面，是用来标识父类的
	2.@MappedSuperclass 标识的类表示其不能映射到数据库表，因为其不是一个完整的实体类，但是它所拥有的属性能够映射在其子类对用的数据库表中
	3.@MappedSuperclass 标识的类不能再有@Entity或@Table注解
----
## @Column
	1. 当实体的属性与其映射的数据库表的列不同名时需要使用@Column标注说明，该属性通常置于实体的属性声明语句之前，还可与 @Id 标注一起使用。
	2. @Column 标注的常用属性是name，用于设置映射数据库表的列名。此外，该标注还包含其它多个属性，如：unique、nullable、length、precision等。具体如下：
	
		1 name属性定义了被标注字段在数据库表中所对应字段的名称
		2 unique属性表示该字段是否为唯一标识，默认为false，如果表中有一个字段需要唯一标识，则既可以使用该标记，也可以使用@Table注解中的@UniqueConstraint
		3 nullable属性表示该字段是否可以为null值，默认为true
		4 insertable属性表示在使用”INSERT”语句插入数据时，是否需要插入该字段的值
		5 updateable属性表示在使用”UPDATE”语句插入数据时，是否需要更新该字段的值
		6 insertable和updateable属性：一般多用于只读的属性，例如主键和外键等，这些字段通常是自动生成的
		7 columnDefinition属性表示创建表时，该字段创建的SQL语句，一般用于通过Entity生成表定义时使用，如果数据库中表已经建好，该属性没有必要使用
		8 table属性定义了包含当前字段的表名
		9 length属性表示字段的长度，当字段的类型为varchar时，该属性才有效，默认为255个字符
		10 precision属性和scale属性一起表示精度，当字段类型为double时，precision表示数值的总长度，scale表示小数点所占的位数
			具体如下：
			10_1.double类型将在数据库中映射为double类型，precision和scale属性无效
			10_2.double类型若在columnDefinition属性中指定数字类型为decimal并指定精度，则最终以columnDefinition为准
			10_3.BigDecimal类型在数据库中映射为decimal类型，precision和scale属性有效
			10_4.precision和scale属性只在BigDecimal类型中有效
	3.@Column标注的columnDefinition属性: 表示该字段在数据库中的实际类型.通常 ORM 框架可以根据属性类型自动判断数据库中字段的类型,但是对于Date类型仍无法确定数据库中字段类型究竟是DATE,TIME还是TIMESTAMP.此外,String的默认映射类型为VARCHAR,如果要将 String 类型映射到特定数据库的 BLOB 或TEXT字段类型.
	4.@Column标注也可置于属性的getter方法之前
----
*Lombok
## @Getter
	注解在属性上；为属性提供 getting 方法
## @Setter
	注解在属性上；为属性提供 setting 方法 
## @Data
	注解在类上；提供类所有属性的 getting 和 setting 方法，此外还提供了equals、canEqual、hashCode、toString 方法
## @Log4j2 
	注解在类上；为类提供一个 属性名为log 的 log4j 日志对象，和@Log4j注解类似
## @NoArgsConstructor
	注解在类上；为类提供一个无参的构造方法
## @AllArgsConstructor
	注解在类上；为类提供一个全参的构造方法
## @EqualsAndHashCode
	默认情况下，会使用所有非瞬态(non-transient)和非静态(non-static)字段来生成equals和hascode方法，也可以指定具体使用哪些属性。
## @toString
	生成toString方法，默认情况下，会输出类名、所有属性，属性会按照顺序输出，以逗号分割。
## @NoArgsConstructor, @RequiredArgsConstructor and @AllArgsConstructor
	无参构造器、部分参数构造器、全参构造器，当我们需要重载多个构造器的时候，只能自己手写了
## @NonNull
	注解在属性上，如果注解了，就必须不能为Null
## @val
	注解在属性上，如果注解了，就是设置为final类型，可查看源码的注释知道
----
## 数据库
	当你在执行各种持久化方法的时候，实体的状态会随之改变，状态的改变会引发不同的生命周期事件。这些事件可以使用不同的注释符来指示发生时的回调函数。
		@javax.persistence.PostLoad：加载后。
		@javax.persistence.PrePersist：持久化前。
		@javax.persistence.PostPersist：持久化后。
		@javax.persistence.PreUpdate：更新前。
		@javax.persistence.PostUpdate：更新后。
		@javax.persistence.PreRemove：删除前。
		@javax.persistence.PostRemove：删除后。
### 数据库查询

#### @PostLoad事件在下列情况下触发：
	执行EntityManager.find()或getreference()方法载入一个实体后。
	执行JPQL查询后。
	EntityManager.refresh()方法被调用后。

### 数据库插入

#### @PrePersist vs @PostPersist
	这两个事件在实体对象插入到数据库的过程中发生。
##### @PrePersist
		@PrePersist事件在调用persist()方法后立刻发生，此时的数据还没有真正插入进数据库。
##### @PostPersist
		@PostPersist事件在数据已经插入进数据库后发生。
		
### 数据库更新

#### @PreUpdate vs @PostUpdate
	@PreUpdate和@PostUpdate事件的触发由更新实体引起。
##### @PreUpdate
@PreUpdate事件在实体的状态同步到数据库之前触发，此时的数据还没有真正更新到数据库。
##### @PostUpdate
@PostUpdate事件在实体的状态同步到数据库之后触发，同步在事务提交时发生。

### 数据库删除

#### @PreRemove vs @PostRemov
@PreRemove和@PostRemove事件的触发由删除实体引起。
##### @PreRemove
@PreRemove事件在实体从数据库删除之前触发，即在调用remove()方法删除时发生，此时的数据还没有真正从数据库中删除。
##### @PostRemove
@PostRemove事件在实体从数据库中删除后触发。
----
## @Repository
	用于标注数据访问层，也可以说用于标注数据访问组件，即DAO组件
	具体只需将该注解标注在 DAO类上即可。同时，为了让 Spring 能够扫描类路径中的类并识别出 @Repository 注解，
	需要在 XML 配置文件中启用Bean的自动扫描功能，这可以通过<context:component-scan/>实现。
	(图片插入 ./SpringBoot_@Repository.png)
	
	为什么 @Repository 只能标注在 DAO 类上呢？
		这是因为该注解的作用不只是将类识别为Bean，同时它还能将所标注的类中抛出的数据访问异常封装为
		Spring 的数据访问异常类型。 
		Spring本身提供了一个丰富的并且是与具体的数据访问技术无关的数据访问异常结构，
		用于封装不同的持久层框架抛出的异常，使得异常独立于底层的框架。
		
	 Spring 2.5之后，在@Repository的基础上增加了功能类似的额外三个注解：@Component、@Service、@Constroller	
----	 
## @Component
	@component是spring中的一个注解，它的作用就是实现bean的注入。
	当类不属于各种归类的时候（不属于@Controller、@Services等的时候），我们就可以使用@Component来标注这个类
	@component （把普通pojo实例化到spring容器中，相当于配置文件中的 <bean id="" class=""/>）
	
	虽然我们可以通过 @Autowired 或 @Resource 在 Bean 类中使用自动注入功能，
	但是 Bean 还是在 XML 文件中通过 <bean> 进行定义 —— 也就是说，在 XML 配置文件中定义 Bean，
	通过@Autowired 或 @Resource 为 Bean 的成员变量、方法入参或构造函数入参提供自动注入的功能。
	
	能否也通过注释定义 Bean，从 XML 配置文件中完全移除 Bean 定义的配置呢？
		答案是肯定的，我们通过 Spring 2.5 提供的@Component 注释就可以达到这个目标了。

	为什么 @Repository 只能标注在 DAO 类上呢？
		这是因为该注解的作用不只是将类识别为 Bean，
		同时它还能将所标注的类中抛出的数据访问异常封装为 Spring 的数据访问异常类型。 
		Spring 本身提供了一个丰富的并且是与具体的数据访问技术无关的数据访问异常结构，
		用于封装不同的持久层框架抛出的异常，使得异常独立于底层的框架。

		Spring 2.5 在 @Repository 的基础上增加了功能类似的额外三个注解：@Component、@Service、@Constroller，
		它们分别用于软件系统的不同层次：

		@Component 是一个泛化的概念，仅仅表示一个组件 (Bean) ，可以作用在任何层次。
		@Service 通常作用在业务层，但是目前该功能与 @Component 相同。
		@Constroller 通常作用在控制层，但是目前该功能与 @Component 相同。
		通过在类上使用 @Repository、@Component、@Service 和 @Constroller 注解，
		Spring 会自动创建相应的 BeanDefinition 对象，并注册到 ApplicationContext 中。
		这些类就成了 Spring 受管组件。这三个注解除了作用于不同软件层次的类，
		其使用方式与 @Repository 是完全相同的。
### 举例1
	完全使用注释定义 Bean 并完成 Bean 之间装配
	使用 @Component 注释的 Car.java
```java
package com.baobaotao;

import org.springframework.stereotype.Component;

@Component
public class Car {
    …
}
```
	仅需要在类定义处，使用 @Component 注释就可以将一个类定义了 Spring 容器中的 Bean。
	
### 举例2
将 Office 定义为一个 Bean
使用 @Component 注释的 Office.java
```java
package com.baobaotao;
import org.springframework.stereotype.Component;

@Component
public class Office {
    private String officeNo = "001";
    …
}
```
就可以在 Boss 类中通过 @Autowired 注入前面定义的 Car 和 Office Bean 了。

### 举例3
使用 @Component 注释的 Boss.java
```java
package com.baobaotao;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Required;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component("boss")
public class Boss {
    @Autowired
    private Car car;

    @Autowired
    private Office office;
    …
}
```
	@Component 有一个可选的入参，用于指定 Bean 的名称，在 Boss 中，
	我们就将 Bean 名称定义为“boss”。一般情况下，Bean 都是 singleton 的，
	需要注入 Bean 的地方仅需要通过 byType 策略就可以自动注入了，
	所以大可不必指定 Bean 的名称。

	在使用 @Component 注释后，Spring 容器必须启用类扫描机制以启用注释驱动 Bean 定义和注释驱动 Bean 自动注入的策略。
	Spring 2.5 对 context 命名空间进行了扩展，提供了这一功能.
	
```
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
 http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
 http://www.springframework.org/schema/context 
 http://www.springframework.org/schema/context/spring-context-2.5.xsd">
    <context:component-scan base-package="com.baobaotao"/>
</beans>
```
	所有通过 <bean> 元素定义 Bean 的配置内容已经被移除，
	仅需要添加一行 <context:component-scan/> 配置就解决所有问题了
	——Spring XML 配置文件得到了极致的简化（当然配置元数据还是需要的，只不过以注释形式存在罢了）。
	<context:component-scan/> 的 base-package 属性指定了需要扫描的类包，
	类包及其递归子包中所有的类都会被处理。
	<context:component-scan/> 还允许定义过滤器将基包下的某些类纳入或排除。
	
### Spring 4 种过滤方式
过滤器类型			说明
注释				假如 com.baobaotao.SomeAnnotation 是一个注释类，
					我们可以将使用该注释的类过滤出来。
类名指定			通过全限定类名进行过滤，如您可以指定将 com.baobaotao.Boss 纳入扫描，
					而将 com.baobaotao.Car 排除在外。
正则表达式			通过正则表达式定义过滤的类，如下所示： com\.baobaotao\.Default.*
AspectJ 表达式		通过 AspectJ 表达式定义过滤的类，如下所示： com. baobaotao..*Service+

#### 举例
```
<context:component-scan base-package="com.baobaotao">
    <context:include-filter type="regex" 
        expression="com\.baobaotao\.service\..*"/>
    <context:exclude-filter type="aspectj" 
        expression="com.baobaotao.util..*"/>
</context:component-scan>
```
<context:component-scan/> 配置项不但启用了对类包进行扫描以实施注释驱动 Bean 定义的功能，
同时还启用了注释驱动自动注入的功能（即还隐式地在内部注册了AutowiredAnnotationBeanPostProcessor
 和 CommonAnnotationBeanPostProcessor），因此当使用 <context:component-scan/> 后，
 就可以将 <context:annotation-config/> 移除了。

默认情况下通过 @Component 定义的 Bean 都是 singleton 的，如果需要使用其它作用范围的 Bean，
可以通过@Scope 注释来达到目标.
#### 举例
通过 @Scope 指定 Bean 的作用范围
```java
package com.baobaotao;
import org.springframework.context.annotation.Scope;
…
@Scope("prototype")
@Component("boss")
public class Boss {
    …
}
```
解释：
	当从 Spring 容器中获取 boss Bean 时，每次返回的都是新的实例了。
----
	Spring 2.5 中除了提供 @Component 注释外，还定义了几个拥有特殊语义的注释，
	它们分别是：@Repository、@Service 和@Controller。在目前的 Spring 版本中，
	这 3 个注释和 @Component 是等效的，但是从注释类的命名上，
	很容易看出这 3 个注释分别和持久层、业务层和控制层（Web 层）相对应。
	虽然目前这 3 个注释和@Component 相比没有什么新意，但 Spring 将在以后的版本中为它们添加特殊的功能。
	所以，如果 Web 应用程序采用了经典的三层分层结构的话，
	最好在持久层、业务层和控制层分别采用@Repository、@Service 和 @Controller 对分层中的类进行注释，
	而用@Component 对那些比较中立的类进行注释。
	
	有了这些 IOC 注释，我们就可以完全摒除原来 XML 配置的方式呢？答案是否定的。

		注释配置不一定在先天上优于 XML 配置。如果 Bean 的依赖关系是固定的，
		（如 Service 使用了哪几个 DAO 类），这种配置信息不会在部署时发生调整，
		那么注释配置优于 XML 配置；反之如果这种依赖关系会在部署时发生调整，
		XML 配置显然又优于注释配置，因为注释是对 Java 源代码的调整，
		需要重新改写源代码并重新编译才可以实施调整。
		如果 Bean 不是自己编写的类（如 JdbcTemplate、SessionFactoryBean 等），
		注释配置将无法实施，此时 XML 配置是唯一可用的方式。
		注释配置往往是类级别的，而 XML 配置则可以表现得更加灵活。
		比如相比于 @Transaction 事务注释，使用 aop/tx 命名空间的事务配置更加灵活和简单。
		
总结：
	在实现应用中，我们往往需要同时使用注释配置和 XML 配置，
	对于类级别且不会发生变动的配置可以优先考虑注释配置；
	而对于那些第三方类以及容易发生调整的配置则应优先考虑使用 XML 配置。
	Spring 会在具体实施 Bean 创建和 Bean 注入之前将这两种配置方式的元信息融合在一起。
----
	@Component注解和@Bean注解的作用：
		@Component注解表明一个类会作为组件类，并告知Spring要为这个类创建bean。
		@Bean注解告诉Spring这个方法将会返回一个对象，这个对象要注册为Spring应用上下文中的bean。通常方法体中包含了最终产生bean实例的逻辑。
		两者的目的是一样的，都是注册bean到Spring容器中。

	@Component注解和@Bean注解的区别：	
		@Component（@Controller、@Service、@Repository）通常是通过类路径扫描来自动侦测以及自动装配到Spring容器中。
		@Bean注解通常是我们在标有该注解的方法中定义产生这个bean的逻辑。
		@Component 作用于类，@Bean作用于方法。

	总结：
		@Component和@Bean都是用来注册Bean并装配到Spring容器中，但是Bean比Component的自定义性更强。
		可以实现一些Component实现不了的自定义加载类。
----
## @Primary
	自动装配时当出现多个Bean候选者时，被注解为@Primary的Bean将作为首选者，否则将抛出异常。
### 举例
```java
	@Component  
public class Apple implements Fruit{  
  
    @Override  
    public String hello() {  
        return "我是苹果";  
    }  
}
 
@Component  
@Primary
public class Pear implements Fruit{  
  
    @Override  
    public String hello(String lyrics) {  
        return "梨子";  
    }  
}
 
public class FruitService { 
  
  //Fruit有2个实例子类，因为梨子用@Primary，那么会使用Pear注入
    @Autowired  
    private Fruit fruit;  
  
    public String hello(){  
        return fruit.hello();  
    }  
}

```
----
## @Service
	用于标注服务层，主要用来进行业务的逻辑处理
	 @Service("serviceName")注解相当于applicationContext.xml配置文件中配置的<bean id="serviceName">，
	 表示给当前类命名一个别名，方便注入到其他需要用到的类中。@Service注解也可以不指定serviceName,
	 如果不指定相当于<bean id="com.study.service.serviceName">，com.study.service.ServiceName就是这个类的全限定名,
	 不加的话，默认别名就是当前类名，但是首字母小写。
```java	
	@Service("lwApiCompanyServiceImpl")
	public class LwApiCompanyServiceImpl implements LwCompanyService {}
```

```java
	@Service
	public class LwApiCompanyServiceImpl implements LwCompanyService {}
```
----
## @Inject
	等价于默认的@Autowired，只是没有required属性
	使用@Inject需要引用javax.inject.jar，它与Spring没有关系，是jsr330规范。
	与@Autowired有互换性。
----
## @Autowired
	自动导入依赖的bean
	@Autowired 注释，它可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作。 
	通过 @Autowired的使用来消除 set ，get方法。
	
	@Autowired标注可以放在成员变量上，也可以放在成员变量的set方法上，也可以放在任意方法上,
	表示自动执行当前方法，如果方法有参数，会在IOC容器中自动寻找同类型参数为其传值。
	这里必须明确：@Autowired是根据类型进行自动装配的，如果需要按名称进行装配，则需要配合@Qualifier使用。
	
		1. 当不能确定 Spring 容器中一定拥有某个类的 Bean 时，可以在需要自动注入该类 Bean 的地方可以使用 
			@Autowired(required = false)，这等于告诉 Spring：在找不到匹配 Bean 时也不报错。
			(使用了自动注入而又允许不注入的情况一般仅会在开发期或测试期碰到（如为了快速启动 Spring 容器，
			仅引入一些模块的 Spring 配置文件）)
			例子：
```java
	…
    @Autowired(required = false)
    public void setOffice(Office office) {
        this.office = office;
    }
    …
```
		2. 在Spring容器中配置了两个类型为Office类型的Bean，当对某一office成员变量进行自动注入时，
			Spring 容器将无法确定到底要用哪一个 Bean，因此异常发生了。
			例子：
```java
… 
<bean id="office" class="com.baobaotao.Office">
    <property name="officeNo" value="001"/>
</bean>
<bean id="office2" class="com.baobaotao.Office">
    <property name="officeNo" value="001"/>
</bean>
…
```

Spring 允许我们通过 @Qualifier 注释指定注入 Bean 的名称，这样歧义就消除了
举例：
	使用@Autowired之前，我们对一个bean配置起属性：
``` 
<property name="属性名" value=" 属性值"/>  
```
	通过这种方式来，配置比较繁琐，而且代码比较多。所以在Spring 2.5 引入了 @Autowired 注释
	
UserRepository.java
	定义了一个UserRepository接口，其中定义了一个save方法
``` java
	package com.proc.bean.repository;

public interface UserRepository {

    void save();
}
```
	定义一个UserRepository接口的实现类，并实现save方法，在这里指定了该bean在IoC中标识符名称为userRepository
```java
package com.proc.bean.repository;

import org.springframework.stereotype.Repository;

@Repository("userRepository")
public class UserRepositoryImps implements UserRepository{

    @Override
    public void save() {
        System.out.println("UserRepositoryImps save");
    }
}
```
UserService.java
	需要一个UserRepository类型的属性，通过@Autowired自动装配方式，从IoC容器中去查找到，并返回给该属性
```java
package com.proc.bean.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.proc.bean.repository.UserRepository;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public void save(){
        userRepository.save();
    }
}
```
applicationContext.xml配置
```
<context:component-scan base-package="com.proc.bean" />
```
测试代码：
```
ApplicationContext ctx=new ClassPathXmlApplicationContext("applicationContext.xml");
UserService userService=(UserService) ctx.getBean("userService");
userService.save();
```
输出结果：UserRepositoryImps save

@Autowired原理：
	其实在启动spring IoC时，容器自动装载了一个AutowiredAnnotationBeanPostProcessor后置处理器，
	当容器扫描到@Autowied、@Resource(是CommonAnnotationBeanPostProcessor后置处理器处理的)或@Inject时，
	就会在IoC容器自动查找需要的bean，并装配给该对象的属性。
```
 <bean class="org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor"/> 
```
	注意事项：

	　　在使用@Autowired时，首先在容器中查询对应类型的bean

	　　　　如果查询结果刚好为一个，就将该bean装配给@Autowired指定的数据
	　　　　如果查询的结果不止一个，那么@Autowired会根据名称来查找。
	　　　　如果查询的结果为空，那么会抛出异常。解决方法时，使用required=false

继续上面的例子举例：
	在上面例子中，我们再定义一个类来实现UserRepository接口
```java
package com.proc.bean.repository;

import org.springframework.stereotype.Repository;

@Repository
public class UserJdbcImps implements UserRepository {

    @Override
    public void save() {
        System.out.println("UserJdbcImps save");
    }
}
```
	这时在启动容器后，在容器中有两个UserRepository类型的实例，一个名称为userRepository，另一个为userJdbcImps。
	在UserService.java中
```java
@Autowired
private UserRepository userRepository;
```
	输出结果：UserRepositoryImps save
	这里由于查询到有两个该类型的实例，那么采用名称匹配方式，在容器中查找名称为userRepository的实例，并自动装配给该参数。
	
	如果这里想要装载userJdbcImps的实例，除了将字段userRepository名称改成userJdbcImps外，
	可以提供了一个@Qualifier标记，来指定需要装配bean的名称.
```java
@Autowired
@Qualifier("userJdbcImps")
private UserRepository userRepository;
```
	输出结果：UserJdbcImps save
---
## @Qualifier
	当有多个同一类型的Bean时，可以用@Qualifier(“name”)来指定。与@Autowired配合使用
	@Qualifier限定描述符除了能根据名字进行注入，但能进行更细粒度的控制如何选择候选者，具体使用方式如下：
	@Resource(name=”name”,type=”type”)：没有括号内内容的话，默认byName。与@Autowired干类似的事。
### 举例：	
```java
	@Autowired
	public void setOffice(@Qualifier("office")Office office) {
		this.office = office;
	}
```
	解释：
		@Qualifier("office") 中的 office 是 Bean 的名称，所以 @Autowired 和@Qualifier 结合使用时，
		自动注入的策略就从 byType 转变成 byName 了。@Autowired 可以对成员变量、方法以及构造函数进行注释，
		而@Qualifier 的标注对象是成员变量、方法入参、构造函数入参。正是由于注释对象的不同，
		所以 Spring 不将 @Autowired 和@Qualifier 统一成一个注释类。
	
### 对成员变量使用 @Qualifier 注解
```java
public class Boss {
    @Autowired
    private Car car;
 
    @Autowired
    @Qualifier("office")
    private Office office;
    …
}
```
### 对构造函数变量使用 @Qualifier 注解
```java
public class Boss {
    private Car car;
    private Office office;

    @Autowired
    public Boss(Car car , @Qualifier("office")Office office){
        this.car = car;
        this.office = office ;
	}
}
```
	总结：
		@Qualifier 只能和 @Autowired 结合使用，是对 @Autowired 有益的补充。
		一般来讲，@Qualifier 对方法签名中入参进行注释会降低代码的可读性，而对成员变量注释则相对好一些。
----		
## @Resource		
	可以用来装配bean，可以在字段上或写在setter方法上
	
	@Resource 的作用相当于 @Autowired，只不过 @Autowired 按 byType 自动注入，
	而@Resource 默认按 byName 自动注入罢了。@Resource 有两个属性是比较重要的，
	分别是 name 和 type，Spring 将@Resource 注释的 name 属性解析为 Bean 的名字，
	而 type 属性则解析为 Bean 的类型。所以如果使用 name 属性，则使用 byName 的自动注入策略，
	而使用 type 属性时则使用 byType 自动注入策略。如果既不指定 name 也不指定 type 属性，
	这时将通过反射机制使用 byName 自动注入策略。
	
	Resource 注释类位于 Spring 发布包的 lib/j2ee/common-annotations.jar 类包中，
	因此在使用之前必须将其加入到项目的类库中。
### 举例
	使用 @Resource 注释的 Boss.java
```java
package com.baobaotao;

import javax.annotation.Resource;

public class Boss {
    // 自动注入类型为 Car 的 Bean
    @Resource
    private Car car;

    // 自动注入 bean 名称为 office 的 Bean
    @Resource(name = "office")
    private Office office;
}
```
一般情况下，我们无需使用类似于 @Resource(type=Car.class) 的注释方式，
因为 Bean 的类型信息可以通过 Java 反射从代码中获取。

要让 JSR-250 的注释生效，除了在 Bean 类中标注这些注释外，
还需要在 Spring 容器中注册一个负责处理这些注释的 BeanPostProcessor。

```java
<bean 
  class="org.springframework.context.annotation.CommonAnnotationBeanPostProcessor"/>
```
CommonAnnotationBeanPostProcessor 实现了 BeanPostProcessor 接口，
它负责扫描使用了 JSR-250 注释的 Bean，并对它们进行相应的操作。
----
## @PostConstruct vs @PreDestroy
Spring 容器中的 Bean 是有生命周期的，Spring 允许在 Bean 在初始化完成后以及 Bean 销毁前执行特定的操作，
既可以通过实现 InitializingBean/DisposableBean 接口来定制初始化之后 / 销毁之前的操作方法，
也可以通过 <bean> 元素的 init-method/destroy-method 属性指定初始化之后 / 销毁之前调用的操作方法。

JSR-250 为初始化之后/销毁之前方法的指定定义了两个注释类，分别是 @PostConstruct 和 @PreDestroy，
这两个注释只能应用于方法上。
标注了 @PostConstruct 注释的方法将在类实例化后调用，
而标注了 @PreDestroy 的方法将在类销毁之前调用。

### 举例1
使用 @PostConstruct 和 @PreDestroy 注释的 Boss.java
```java
package com.baobaotao;

import javax.annotation.Resource;
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

public class Boss {
    @Resource
    private Car car;

    @Resource(name = "office")
    private Office office;

    @PostConstruct
    public void postConstruct1(){
        System.out.println("postConstruct1");
    }

    @PreDestroy
    public void preDestroy1(){
        System.out.println("preDestroy1"); 
    }
    …
}
```
解释：
	只需要在方法前标注 @PostConstruct 或 @PreDestroy，
	这些方法就会在 Bean 初始化后或销毁之前被 Spring 容器执行了。

	不管是通过实现 InitializingBean/DisposableBean 接口，
	还是通过 <bean> 元素的init-method/destroy-method 属性进行配置，
	都只能为 Bean 指定一个初始化 / 销毁的方法。
	但是使用 @PostConstruct 和 @PreDestroy 注释却可以指定多个初始化 / 销毁方法，
	那些被标注 @PostConstruct 或 @PreDestroy 注释的方法都会在初始化 / 销毁时被执行。
	
### 举例2
Bean 的初始化 / 销毁方法是如何被执行的
```java
package com.baobaotao;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AnnoIoCTest {
    public static void main(String[] args) {
        String[] locations = {"beans.xml"};
        ClassPathXmlApplicationContext ctx = 
            new ClassPathXmlApplicationContext(locations);
        Boss boss = (Boss) ctx.getBean("boss");
        System.out.println(boss);
        ctx.destroy();// 关闭 Spring 容器，以触发 Bean 销毁方法的执行
    }
}
```
解释：
	看到标注了 @PostConstruct 的 postConstruct1() 方法将在 Spring 容器启动时，
	创建Boss Bean 的时候被触发执行，
	而标注了 @PreDestroy 注释的 preDestroy1() 方法将在 
	Spring 容器关闭前销毁Boss Bean 的时候被触发执行。
----
## @Value
### @Value(“#{}”) 
	表示SpEl表达式通常用来获取bean的属性，或者调用bean的某个方法。当然还有可以表示常量
#### 举例
```java
@RestController  
@RequestMapping("/login")  
@Component  
public class LoginController {  

    @Value("#{1}")  
    private int number; //获取数字 1  

    @Value("#{'Spring Expression Language'}") //获取字符串常量  
    private String str;  

    @Value("#{dataSource.url}") //获取bean的属性  
    private String jdbcUrl;  

    @Autowired  
    private DataSourceTransactionManager transactionManager;  

    @RequestMapping("login")  
    public String login(String name,String password) throws FileNotFoundException{  
        System.out.println(number);  
        System.out.println(str);  
        System.out.println(jdbcUrl);  
        return "login";  
    }  
}  
```
解释：
	当bean通过@Value(#{“”}) 获取其他bean的属性，或者调用其他bean的方法时，
	只要该bean (Beab_A)能够访问到被调用的bean(Beab_B)，即要么Beab_A 和Beab_B在同一个容器中，
	或者Beab_B所在容器是Beab_A所在容器的父容器。(拿我上面贴出来的代码为例在springMvc项目中，
	dataSource这个bean一般是在springContext.xml文件中申明的，
	而loginController这个bean一般是在springMvc.xml文件中申明的，
	虽然这两个bean loginController和dataSource不在一个容器，
	但是loginController所在容器继承了dataSource所在的容器，
	所以在loginController这个bean中通过@Value(“#{dataSource.url}”)能够获取到dataSource的url属性)。
### @Value(“${xxxx}”)
	注解从配置文件读取值的用法

	1. 从配置properties文件中读取init.password 的值
#### 举例
```java
@Value("${init.password}")  
 private String initPwd; 
```
	2. 在spring的配置文件中加载配置文件dbconfig.properties
#### 举例
```
<!-- 加载配置文件 -->  
   <bean id="configProperties" class="org.springframework.beans.factory.config.PropertiesFactoryBean">  
    <property name="fileEncoding" value="UTF-8"/>  
    <property name="locations">  
        <list>  
            <value>classpath:dbconfig.properties</value>  
        </list>  
    </property>  
</bean> 
```
或者这样加载
```
<context:property-placeholder location="classpath:dbconfig.properties" /> 
```
或者这样加载
```
<bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PreferencesPlaceholderConfigurer">  
    <property name="location">  
    <value>dbconfig.properties</value>  
    </property>  
</bean>  
```
dbconfig.properties文件
```
#MD5  
password.algorithmName=md5  
password.hashIterations=2  
#initpwd  
init.password=admin 
```
	通过@Value(“${}”) 可以获取对应属性文件中定义的属性值。
	假如我有一个sys.properties文件 里面规定了一组值： web.view.prefix =/WEB-INF/views/

	在springMvc.xml文件中引入下面的代码既即以在 该容器内通过@Value(“web.view.prefix")获取这个字符串。
	需要指出的是，如果只在springMvc.xml引入下面代码，
	只能在springMvc.xml文件中扫描或者注册的bean中才能通过@Value("{web.view.prefix}”)获取这个字符串，
	其他未在springMvc.xml扫描和定义的bean必须在相应的xml文件中引入下面代码才能使用@Value(“${}”)表达式。
```
<!-- 加载配置属性文件 -->  
    <context:property-placeholder  
        ignore-unresolvable="true" location="classpath:sys.properties" />  
```
然后再controller文件中通过下面代码即可获取“”/WEB-INF/views/“”这个字符串
```java
@Value("${web.view.prefix}")  
private String prefix;
```
----
## @CrossOrigin
	Cross-Origin ResourceSharing（跨域资源共享）
	作用是解决跨域访问的问题，在Spring4.2以上的版本可直接使用。在类上或方法上添加该注解
	如果失效则可能方法没解决是GET还是POST方式，指定即可解决问题。
## 举例	
```java
@CrossOrigin
public class TestController extends BaseController { 
	//code
}
```
----
## @Scope
配置bean的作用域。

	四种常见的 Spring Bean 的作用域：
		singleton : 唯一 bean 实例，Spring 中的 bean 默认都是单例的。
		prototype : 每次请求都会创建一个新的 bean 实例。
		request : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP request 内有效。
		session : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP session 内有效。
### 举例
```java
@Controller
@RequestMapping("/test")
@Scope("prototype")
public class TestController {
		//code
}
```
解释：
	默认是单例模式，即@Scope("singleton"),
	singleton：单例，即容器里只有一个实例对象。
	prototype：多对象，每一次请求都会产生一个新的bean实例，
	Spring不无法对一个prototype bean的整个生命周期负责，
	容器在初始化、配置、装饰或者是装配完一个prototype实例后，
	将它交给客户端，由程序员负责销毁该对象，不管何种作用域，
	容器都会调用所有对象的初始化生命周期回调方法，而对prototype而言，
	任何配置好的析构生命周期回调方法都将不会被调用
	request：对每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前HTTP request内有效
```
<listener>
<listener-class>org.springframework.web.context.request.RequestContextListener</listener-class>
  </listener>
session：该针对每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前HTTP session内有效。也要在web.xml配置如下代码：
<listener>
<listener-class>org.springframework.web.context.request.RequestContextListener</listener-class>
  </listener>
global session：作用不大，可不用管他。
```
web.xml增加如上配置。
----
## @ResponseStatus
@ResponseStatus用于修饰一个类或者一个方法，修饰一个类的时候，
一般修饰的是一个异常类,当处理器的方法被调用时，@ResponseStatus指定的code和reason会被返回给前端。
value属性是http状态码，比如404，500等。reason是错误信息
当修改类或方法时，只要该类得到调用，那么value和reason都会被添加到response里。
### 举例
```java
@ResponseStatus(value=HttpStatus.FORBIDDEN, reason="出现了错误")
public class UserException extends RuntimeException{
	XXXXX
}
```
当某处抛出UserException时，则会把value和reason返回给前端。
```java
@RequestMapping("/testResponseStatus")
    public String testResponseStatus(int i){
        if(i==0)
            throw new UserNotMatchException();
        return "hello";
}
```
修改方法：
```java
@ControllerAdvice
@Component
public class GlobalExceptionHandler {
	@Bean
	public MethodValidationPostProcessor methodValidationPostProcessor() {
		return new MethodValidationPostProcessor();
	}
 
	@ExceptionHandler
	@ResponseBody
	@ResponseStatus(value=HttpStatus.BAD_REQUEST,reason="哈哈")
	public String handle(ValidationException exception) {
		System.out.println("bad request, " + exception.getMessage());
		return "bad request, " + exception.getMessage();
	}
}
```
结果:
(图片插入 ./picture/SpringBoot_@ResponseStatus.png）

	正如上面所说，该方法得到调用，不论是否抛异常，都会把value和reason添加到response里。、

总结：@ResponseStatus是为了在方法或类得到调用时将指定的code和reason添加到response里返前端，
就像服务器常给我们报的404错误一样，我们可以自己指定高逼格错误提示。
----
## 元注解
元注解是指注解的注解
###举例
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface ControllerAdvice {
	XXX
}
```
### @Retention
	@Retention: 定义注解的保留策略：
	@Retention(RetentionPolicy.SOURCE)   //注解仅存在于源码中，在class字节码文件中不包含
	@Retention(RetentionPolicy.CLASS)     //默认的保留策略，注解会在class字节码文件中存在，但运行时无法获得，
	@Retention(RetentionPolicy.RUNTIME)  //注解会在class字节码文件中存在，在运行时可以通过反射获取到
#### 举例
比如@Valid注解定义是
（图片插入 ./picture/SpringBoot_@Retention.png)
	表示该注解只能用在方法，属性，构造函数及方法参数上。该注意会被编译到class里可通过反射得到。
----
### @Target
	@Target：定义注解的作用目标:
	@Target(ElementType.TYPE)   //接口、类、枚举、注解
	@Target(ElementType.FIELD) //字段、枚举的常量
	@Target(ElementType.METHOD) //方法
	@Target(ElementType.PARAMETER) //方法参数
	@Target(ElementType.CONSTRUCTOR)  //构造函数
	@Target(ElementType.LOCAL_VARIABLE)//局部变量
	@Target(ElementType.ANNOTATION_TYPE)//注解
	@Target(ElementType.PACKAGE) ///包   
	由以上的源码可以知道，他的elementType 可以有多个，一个注解可以为类的，方法的，字段的等等
----
### @Document 
	@Document：说明该注解将被包含在javadoc中
----
### @Inherited
	@Inherited：说明子类可以继承父类中的该注解
----
（待解决）
## Restful
----
## @Lazy(true)
	用于指定该Bean是否取消预初始化，用于注解类，延迟初始化。
----
## @Named
----
## @Valid vs @ Valided (待解决）
https://chenyuan.blog.csdn.net/article/details/72786759?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param
----
## @Singleton
	只要在类上加上这个注解，就可以实现一个单例类，不需要自己手动编写单例实现类。
----
## @ImportResource: 用来加载xml配置文件
----
## @ConfigurationProperties
----
## @Profiles
----
## @ControllerAdvice：全局处理异常的包含@Component。可以被扫描到。统一处理异常。
----
### @ExceptionHandler
----
## @Import
	@Import只能用在类上 ，@Import通过快速导入的方式实现把实例加入spring的IOC容器中。
	加入IOC容器的方式有很多种，@Import注解就相对很牛皮了，
	@Import注解可以用于导入第三方包 ，当然@Bean注解也可以，但是@Import注解快速导入的方式更加便捷。

### @Import的三种用法
	1. 直接填class数组方式
			直接填对应的class数组，class数组可以有0到多个。
			```java
			@Import({ 类名.class , 类名.class... })
			public class TestDemo {
				//code
			}
			```
			对应的import的bean都将加入到spring容器中，这些在容器中bean名称是该类的全类名 ，比如com.yc.类名
	2. ImportSelector方式[重点]
			这种方式的前提就是一个类要实现ImportSelector接口，假如我要用这种方法，目标对象是Myclass这个类
			创建Myclass类并实现ImportSelector接口
			```java
			public class Myclass implements ImportSelector {
				//既然是接口肯定要实现这个接口的方法
				@Override
				public String[] selectImports(AnnotationMetadata annotationMetadata) {
					return new String[0];
				}
			}
			```
		分析实现接口的selectImports方法中的：
			1、返回值： 就是我们实际上要导入到容器中的组件全类名[重点]
			2、参数： AnnotationMetadata表示当前被@Import注解给标注的所有注解信息[非重点]
		需要注意的是selectImports方法可以返回空数组但是不能返回null，否则会报空指针异常！
		
### 使用方法举例
	1. 创建Myclass类并实现ImportSelector接口，这里用于演示就添加一个全类名给其返回值
```java
public class Myclass implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata annotationMetadata) {
        return new String[]{"com.yc.Test.TestDemo3"};
    }
}
```
	2. 编写TestDemo 类，并标注上使用ImportSelector方式的Myclass类
```java
@Import({TestDemo2.class,Myclass.class})
public class TestDemo {
        @Bean
        public AccountDao2 accountDao2(){
            return new AccountDao2();
        }

}
```
	可以看出，宜春故意挑了个龙套角色@Bean注解，若对@Bean注解不是很清晰的童鞋可以参考大白话讲解Spring的@bean注解

	3. 编写打印容器中的组件测试类
```java
/**
 * 打印容器中的组件测试
 */
public class AnnotationTestDemo {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext=new AnnotationConfigApplicationContext(TestDemo.class);  //这里的参数代表要做操作的类

        String[] beanDefinitionNames = applicationContext.getBeanDefinitionNames();
        for (String name : beanDefinitionNames){
            System.out.println(name);
        }

    }
}
```
(图片插入）
	3. ImportBeanDefinitionRegistrar方式
	同样是一个接口，类似于第二种ImportSelector用法，相似度80%，只不过这种用法比较自定义化注册，具体如下：

		A. 创建Myclass2类并实现ImportBeanDefinitionRegistrar接口
```java
public class Myclass2 implements ImportBeanDefinitionRegistrar {
//该实现方法默认为空
    @Override
    public void registerBeanDefinitions(AnnotationMetadata annotationMetadata, BeanDefinitionRegistry beanDefinitionRegistry) {
      
    }
}
```
	参数分析：
		第一个参数：annotationMetadata 和之前的ImportSelector参数一样都是表示当前被@Import注解给标注的所有注解信息
		第二个参数表示用于注册定义一个bean
		
		B. 编写代码，自定义注册bean
```java
public class Myclass2 implements ImportBeanDefinitionRegistrar {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata annotationMetadata, BeanDefinitionRegistry beanDefinitionRegistry) {
        //指定bean定义信息（包括bean的类型、作用域...）
        RootBeanDefinition rootBeanDefinition = new RootBeanDefinition(TestDemo4.class);
        //注册一个bean指定bean名字（id）
        beanDefinitionRegistry.registerBeanDefinition("TestDemo4444",rootBeanDefinition);
    }
}
```
		C. 编写TestDemo 类，并标注上使用ImportBeanDefinitionRegistrar方式的Myclass2类
```java
@Import({TestDemo2.class,Myclass.class,Myclass2.class})
public class TestDemo {

        @Bean
        public AccountDao2 accountDao222(){
            return new AccountDao2();
        }

}
```
### @Import总结
	第一种用法：@Import（{ 要导入的容器中的组件 } ）：容器会自动注册这个组件，id默认是全类名
	第二种用法：ImportSelector：返回需要导入的组件的全类名数组，springboot底层用的特别多【重点 】
	第三种用法：ImportBeanDefinitionRegistrar：手动注册bean到容器
	以上三种用法方式皆可混合在一个@Import中使用，特别注意第一种和第二种都是以全类名的方式注册，而第三中可自定义方式。
	@Import注解本身在springboot中用的很多，特别是其中的第二种用法ImportSelector方式在springboot中使用的特别多，尤其要掌握！
----
## @Bean
	相当于XML中的,放在方法的上面，而不是类，意思是产生一个bean,并交给spring管理
----
## @Async(待解决）
    java里使用线程有3种方法：

		1. 继承Thread，重写run方法
		2. 实现Runnable,重写run方法
		3. 使用Callable和Future接口创建线程，并能得到返回值。
		
		4. 使用@Async可视为第4种方法。基于@Async标注的方法，称之为异步方法
			这个注解用于标注某个方法或某个类里面的所有方法都是需要异步处理的。
			被注解的方法被调用的时候，会在新线程中执行，而调用它的方法会在原来的线程中执行。















