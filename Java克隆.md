[TOC]

## 网络来源

[网络来源1](https://sharember.blog.csdn.net/article/details/53365564)
[网络来源2](https://www.cnblogs.com/javastack/p/12759352.html)  (可参照)
[网络来源3](https://www.jb51.net/article/90547.html) (本文引用)

## 简介
> 世界上第一只克隆羊多莉就是利用细胞核移植技术将哺乳动物的成年体细胞培育出新个体。在Java中也存在克隆的概念，即实现对象的复制。
## Java对于克隆的限制
1. 被克隆的类必须自己实现Cloneable 接口，以指示 Object.clone() 方法可以合法地对该类实例进行按字段复制。Cloneable 接口实际上是个标识接口，没有任何接口方法。
2. 实现Cloneable接口的类应该使用公共方法重写 Object.clone（它是受保护的）。某个对象实现了此接口就克隆它是不可能的。即使 clone 方法是反射性调用的，也无法保证它将获得成功。
3. 再Java.lang.Object类中克隆方法的定义
```java
protected Object clone() throws CloneNotSupportedException
```

创建并返回此对象的一个副本。表明是一个受保护的方法，同一个包中可见。
按照惯例，返回的对象应该通过调用 super.clone 获得。
## Java 中的赋值
在Java中，赋值是很常用的，一个简单的赋值如下
```java
//原始类型
int a = 1;
int b = a;

//引用类型
String[] weekdays = new String[5];
String[] gongzuori = weekdays;//仅拷贝引用
```

在上述代码中。
1. 如果是原始数据类型，赋值传递的为真实的值
2. 如果是引用数据类型，赋值传递的为对象的引用，而不是对象。
了解了数据类型和引用类型的这个区别，便于我们了解clone。
## Clone
在Java中，clone是将已有对象在内存中复制出另一个与之相同的对象的过程。java中的克隆为逐域复制。
在Java中想要支持clone方法，需要首先实现Cloneable接口
Cloneable其实是有点奇怪的，它不同与我们常用到的接口，它内部不包含任何方法，它仅仅是一个标记接口。
其源码如下
```java
public interface Cloneable {
}
```
关于cloneable，需要注意的

      1、如果想要支持clone，就需要实现Cloneable 接口
      2、如果没有实现Cloneable接口的调用clone方法，会抛出CloneNotSupportedException异常。
然后是重写clone方法，并修改成public访问级别
```java
static class CloneableImp implements Cloneable {
 public int count;
 public Child child;
   
   
 @Override
 public Object clone() throws CloneNotSupportedException {
   return super.clone();
 }
}
```

调用clone方法复制对象
```java
CloneableImp imp1 = new CloneableImp();
imp1.child = new Child("Andy");
try {
 Object obj = imp1.clone();
 CloneableImp imp2 = (CloneableImp)obj;
 System.out.println("main imp2.child.name=" + imp2.child.name);
} catch (CloneNotSupportedException e) {
 e.printStackTrace();
}
```

## 浅拷贝
上面的代码实现的clone实际上是属于浅拷贝（Shallow Copy）。
关于浅拷贝，你该了解的

1. 使用默认的clone方法
2. 对于原始数据域进行值拷贝
3. 对于引用类型仅拷贝引用
4. 执行快，效率高
5. 不能做到数据的100%分离。
6. 如果一个对象只包含原始数据域或者不可变对象域，推荐使用浅拷贝。
关于无法做到数据分离，我们可以使用这段代码验证。

```java
CloneableImp imp1 = new CloneableImp();
imp1.child = new Child("Andy");
try {
 Object obj = imp1.clone();
 CloneableImp imp2 = (CloneableImp)obj;
 imp2.child.name = "Bob";
     
 System.out.println("main imp1.child.name=" + imp1.child.name);
} catch (CloneNotSupportedException e) {
 e.printStackTrace();
}
```

上述代码我们使用了imp1的clone方法克隆出imp2,然后修改 imp2.child.name 为 Bob,然后打印imp1.child.name 得到的结果是
```java
main imp1.child.name=Bob
```

原因是浅拷贝并没有做到数据的100%分离，imp1和imp2共享同一个Child对象，所以一个修改会影响到另一个。

## 深拷贝
深拷贝可以解决数据100%分离的问题。只需要对上面代码进行一些修改即可。
1、Child实现Cloneable接口。
```java
public class Child implements Cloneable{

 public String name;

 public Child(String name) {
   this.name = name;
 }

 @Override
 public String toString() {
   return "Child [name=" + name + "]";
 }

 @Override
 protected Object clone() throws CloneNotSupportedException {
   return super.clone();
 }
}
```

2.重写clone方法，调用数据域的clone方法。
```java
static class CloneableImp implements Cloneable {
 public int count;
 public Child child;
   
   
 @Override
 public Object clone() throws CloneNotSupportedException {
   CloneableImp obj = (CloneableImp)super.clone();
   obj.child = (Child) child.clone();
   return obj;
 }
}
```

当我们再次修改imp2.child.name就不会影响到imp1.child.name的值了，因为imp1和imp2各自拥有自己的child对象，因为做到了数据的100%隔离。

关于深拷贝的一些特点

1. 需要重写clone方法，不仅仅只调用父类的方法，还需调用属性的clone方法
2. 做到了原对象与克隆对象之间100%数据分离
3. 如果是对象存在引用类型的属性，建议使用深拷贝
4. 深拷贝比浅拷贝要更加耗时，效率更低

## 为什么使用克隆
- 很重要并且常见的常见就是：某个API需要提供一个List集合，但是又不希望调用者的修改影响到自身的变化，因此需要克隆一份对象，以此达到数据隔离的目的。

## 应当尽量避免克隆
1. 通常情况下，实现接口是为了表明类可以为它的客户做些什么，而Cloneable仅仅是一个标记接口，而且还改变了超类中的手保护的方法的行为，是接口的一种极端非典型的用法，不值得效仿。
2. Clone方法约定及其脆弱 clone方法的Javadoc描述有点暧昧模糊，如下为 Java SE8的约定

    - clone方法创建并返回该对象的一个拷贝。而拷贝的精确含义取决于该对象的类。
    - 一般的含义是，对于任何对象x，表达式 x.clone() != x 为 true x.clone().getClass() == x.getClass() 也返回true，但非必须 x.clone().equals(x) 也返回true，但也不是必须的
	- 上面的第二个和第三个表达式很容易就返回false。因而唯一能保证永久为true的就是表达式一，即两个对象为独立的对象。

3. 可变对象final域 在克隆方法中，如果我们需要对可变对象的final域也进行拷贝，由于final的限制，所以实际上是无法编译通过的。因此为了实现克隆，我们需要考虑舍去该可变对象域的final关键字。

4. 线程安全 如果你决定用线程安全的类实现Cloneable接口，需要保证它的clone方法做好同步工作。默认的Object.clone方法是没有做同步的。

总的来说，java中的clone方法实际上并不是完善的，建议尽量避免使用。如下是一些替代方案。

### Copy Constructors
使用复制构造器也可以实现对象的拷贝。
1. 复制构造器也是构造器的一种
2. 只接受一个参数，参数类型为当前的类
3. 目的是生成一个与参数相同的新对象
4. 复制构造器相比clone方法的优势是简单，易于实现。
一段使用了复制构造器的代码示例

#### 实现浅拷贝
```java
public class Car {
 Wheel wheel;
 String manufacturer;
 
 public Car(Wheel wheel, String manufacturer) {
   this.wheel = wheel;
   this.manufacturer = manufacturer;
 }
 
 //copy constructor
 public Car(Car car) {
   this(car.wheel, car.manufacturer);
 }
 
 public static class Wheel {
   String brand;
 }
}
```

#### 实现深拷贝
```java
//copy constructor
public Car(Car car) {
 Wheel wheel = new Wheel();
 wheel.brand = car.wheel.brand;
   
 this.wheel = wheel;
 this.manufacturer = car.manufacturer;
}
```

为了更加便捷，我们还可以为上述类增加一个静态的方法
```java
public static Car newInstance(Car car) {
 return new Car(car);
}
```

#### 使用Serializable实现深拷贝
利用serializable实现深复制（这个是利用Serializable，利用序列化的方式来实现深复制（深克隆），在其中利用了Io流的方式将这个对象写到IO流里面，然后在从IO流里面读取，这样就实现了一个复制，然后实现序列化的这个会将引用的那个对象也一并进行深复制，这样就实现了这个机制，同时在IO里面读取数据的时候还使用了装饰者模式）
```java
public class CloneTest3
{
	public static void main(String[] args) throws Exception
	{
		Teacher3 teacher3 = new Teacher3();
		teacher3.setAge(23);
		teacher3.setName("niesong");
		
		Student3 student3 = new Student3();
		student3.setAge(50);
		student3.setName("wutao");
		student3.setTeacher3(teacher3);
		
		Student3 ss = (Student3)student3.deepCopt();
		System.out.println(ss.getAge());
		System.out.println(ss.getName());
		
		System.out.println("---------------------");
		System.out.println(ss.getTeacher3().getAge());
		System.out.println(ss.getTeacher3().getName());
		
		System.out.println("-----------------------");
		
		ss.getTeacher3().setAge(7777);
		ss.getTeacher3().setName("hhhhh");
		
		System.out.println(teacher3.getAge());
		System.out.println(teacher3.getName());
		//虽然上面的已经改了，但是改的是那个复制对象后的那个里面的，然后那个原来的那个里面的并没有改，下面验证：：：
		
		System.out.println("-----------------");
		
		System.out.println(ss.getTeacher3().getAge());
		System.out.println(ss.getTeacher3().getName());
		
		
	
		
		
	}
	
 
}
class Teacher3 implements Serializable
{
//  上面的那个警告可以直接消除，除了使用在设置中不显示这个警告，还可以使用下面的这两条语句中的任何一条语句
//	这个serialVersionUID为了让该类别Serializable向后兼容
//	private static final long serialVersionUID = 1L;
//	private static final long serialVersionUID = 8940196742313994740L;
	private int age;
	private String name;
	public int getAge()
	{
		return age;
	}
	public void setAge(int age)
	{
		this.age = age;
	}
	public String getName()
	{
		return name;
	}
	public void setName(String name)
	{
		this.name = name;
	}
}
	class Student3 implements Serializable
	{
		private static final long serialVersionUID = 1L;
		private int age;
		private String name;
		private Teacher3 teacher3;
		public int getAge()
		{
			return age;
		}
		public void setAge(int age)
		{
			this.age = age;
		}
		public String getName()
		{
			return name;
		}
		public void setName(String name)
		{
			this.name = name;
		}
		public Teacher3 getTeacher3()
		{
			return teacher3;
		}
		public void setTeacher3(Teacher3 teacher3)
		{
			this.teacher3 = teacher3;
		}
		//使得序列化student3的时候也会将teacher序列化
		public Object deepCopt()throws Exception
		{
			ByteArrayOutputStream bos = new ByteArrayOutputStream();
			ObjectOutputStream  oos = new ObjectOutputStream(bos);
			oos.writeObject(this);
			//将当前这个对象写到一个输出流当中，，因为这个对象的类实现了Serializable这个接口，所以在这个类中
			//有一个引用，这个引用如果实现了序列化，那么这个也会写到这个输出流当中
			
			ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
			ObjectInputStream ois = new ObjectInputStream(bis);
			return ois.readObject();
			//这个就是将流中的东西读出类，读到一个对象流当中，这样就可以返回这两个对象的东西，实现深克隆
		}
	}
```