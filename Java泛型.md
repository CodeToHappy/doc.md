# 泛型

[TOC]

----

## -网络来源-

[泛型网络来源1](https://www.jianshu.com/p/986f732ed2f1)
[泛型网络来源2](https://blog.csdn.net/s10461/article/details/53941091)
[泛型网络来源3](https://segmentfault.com/a/1190000014120746)

## 简介
>泛型，即“参数化类型”。 一提到参数，最熟悉的就是定义方法时有形参，然后调用此方法时传递实参。那么参数化类型怎么理解呢？顾名思义，就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。

- 泛型的本质是为了参数化类型（在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型）。也就是说在泛型使用过程中，操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。

Java泛型设计原则：只要在编译时期没有出现警告，那么运行时期就不会出现ClassCastException异常。
泛型：把类型明确的工作推迟到创建对象或调用方法的时候才去明确的特殊的类型。
参数化类型：

    - 把类型当作是参数一样传递
	- <数据类型>只能是引用类型

相关术语：
	- ArrayList<E> 中的E称为类型参数变量
	- ArrayList<Integer> 中的Integer称为实际类型参数
	- 整个称为ArrayList<E>泛型类型
	- 整个ArrayList<Integer>称为参数化的类型ParameterizedType

## 为什么需要泛型
1. 
```java
List arrayList = new ArrayList();
arrayList.add("aaaa");
arrayList.add(100);

for(int i = 0; i< arrayList.size();i++){
    String item = (String)arrayList.get(i);
    Log.d("泛型测试","item = " + item);
}
//程序的运行结果会以崩溃结束：java.lang.ClassCastException: java.lang.Integer cannot be cast to java.lang.String
//ArrayList可以存放任意类型，例子中添加了一个String类型，添加了一个Integer类型，再使用时都以String的方式使用，因此程序崩溃了。为了解决类似这样的问题（在编译阶段就可以解决），泛型应运而生。

//我们将第一行声明初始化list的代码更改一下，编译器会在编译阶段就能够帮我们发现类似这样的问题。
List<String> arrayList = new ArrayList<String>();
...
//arrayList.add(100); 在编译阶段，编译器就会报错
```

2. 
早期Java是使用Object来代表任意类型的，但是向下转型有强转的问题，这样程序就不太安全。
没有泛型，集合会怎么样：
	- Collection、Map集合对元素的类型是没有任何限制的。 本来我的Collection集合装载的是全部的Dog对象，但是外边把Cat对象存储到集合中，是没有任何语法错误的。
	- 把对象扔进集合中，集合是不知道元素的类型是什么的，仅仅知道是Object。因此在get()的时候，返回的是Object。外边获取该对象，还需要强制转换。
有了泛型后：
	- 代码更加简洁[不用强制转换]
	- 程序更加健壮[只要编译时期没有警告，那么运行时期就不会出现ClassCastException异常]
	- 可读性和稳定性[在编写集合的时候，就限定了类型]
有了泛型后使用增强for遍历集合：
	- 在创建集合的时候，我们明确了集合的类型了，所以我们可以使用增强for来遍历集合！
```java
//创建集合对象
ArrayList&lt;String&gt; list = new ArrayList&lt;&gt;();

    list.add("hello");
    list.add("world");
    list.add("java");

    //遍历,由于明确了类型.我们可以增强for
    for (String s : list) {
        System.out.println(s);
    }
```

## 泛型的特性
泛型只在编译阶段有效。
```java
List<String> stringArrayList = new ArrayList<String>();
List<Integer> integerArrayList = new ArrayList<Integer>();

Class classStringArrayList = stringArrayList.getClass();
Class classIntegerArrayList = integerArrayList.getClass();

if(classStringArrayList.equals(classIntegerArrayList)){
    Log.d("泛型测试","类型相同");
}
```
```
输出结果：D/泛型测试: 类型相同。
```

- 通过上面的例子可以证明，在编译之后程序会采取去泛型化的措施。也就是说Java中的泛型，只在编译阶段有效。在编译过程中，正确检验泛型结果后，会将泛型的相关信息擦出，并且在对象进入和离开方法的边界处添加类型检查和类型转换的方法。也就是说，泛型信息不会进入到运行时阶段。

- 对此总结成一句话：泛型类型在逻辑上看以看成是多个不同的类型，实际上都是相同的基本类型。

## 泛型的使用与实现

### 1. 泛型类

```java
class 类名称 <泛型标识：可以随便写任意标识号，标识指定的泛型的类型>{
  private 泛型标识 /*（成员变量类型）*/ var; 
  .....

  }
}
```
```java
//基本的泛型类： 此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
//在实例化泛型类时，必须指定T的具体类型
public class Generic<T>{ 
    //key这个成员变量的类型为T,T的类型由外部指定  
    private T key;

    public Generic(T key) { //泛型构造方法形参key的类型也为T，T的类型由外部指定
        this.key = key;
    }

    public T getKey(){ //泛型方法getKey的返回值类型为T，T的类型由外部指定
        return key;
    }
}


//测试
//泛型的类型参数只能是类类型（包括自定义类），不能是简单类型
//传入的实参类型需与泛型的类型参数类型相同，即为Integer.
Generic<Integer> genericInteger = new Generic<Integer>(123456);

//传入的实参类型需与泛型的类型参数类型相同，即为String.
Generic<String> genericString = new Generic<String>("key_vlaue");
Log.d("泛型测试","key is " + genericInteger.getKey());
Log.d("泛型测试","key is " + genericString.getKey());

//结果
//12-27 09:20:04.432 13063-13063/? D/泛型测试: key is 123456
//12-27 09:20:04.432 13063-13063/? D/泛型测试: key is key_vlaue
```

定义的泛型类，就一定要传入泛型类型实参么？

- 并不是这样，在使用泛型的时候如果传入泛型实参，则会根据传入的泛型实参做相应的限制，此时泛型才会起到本应起到的限制作用。如果不传入泛型类型实参的话，在泛型类中使用泛型的方法或成员变量定义的类型可以为任何的类型。

```java
Generic generic = new Generic("111111");
Generic generic1 = new Generic(4444);
Generic generic2 = new Generic(55.55);
Generic generic3 = new Generic(false);

Log.d("泛型测试","key is " + generic.getKey());
Log.d("泛型测试","key is " + generic1.getKey());
Log.d("泛型测试","key is " + generic2.getKey());
Log.d("泛型测试","key is " + generic3.getKey());

/*
 * 结果：
 * D/泛型测试: key is 111111
 * D/泛型测试: key is 4444
 * D/泛型测试: key is 55.55
 * D/泛型测试: key is false
 */
```

注意：
	- 泛型的类型参数只能是类类型，不能是简单类型。
	- 不能对确切的泛型类型使用instanceof操作。 如下面的操作是非法的，编译时会报错。

```java
if（ex_num instanceof Geeric<Number>){
	//code
}
```

### 2. 泛型接口

- 泛型接口与泛型类的定义及使用基本相同。泛型接口常被用在各种类的生产器中。

```java
//定义一个泛型接口
public interface Generator<T> {
    public T next();
}
```
当实现泛型接口的类，未传入泛型实参时：
```java
/**
 * 未传入泛型实参时，与泛型类的定义相同，在声明类的时候，需将泛型的声明也一起加到类中
 * 即：class FruitGenerator<T> implements Generator<T>{
 * 如果不声明泛型，如：class FruitGenerator implements Generator<T>，编译器会报错："Unknown class"
 */
class FruitGenerator<T> implements Generator<T>{
    @Override
    public T next() {
        return null;
    }
}
```
当实现泛型接口的类，传入泛型实参时：
```java
/**
 * 传入泛型实参时：
 * 定义一个生产器实现这个接口,虽然我们只创建了一个泛型接口Generator<T>
 * 但是我们可以为T传入无数个实参，形成无数种类型的Generator接口。
 * 在实现类实现泛型接口时，如已将泛型类型传入实参类型，则所有使用泛型的地方都要替换成传入的实参类型
 * 即：Generator<T>，public T next();中的的T都要替换成传入的String类型。
 */
public class FruitGenerator implements Generator<String> {

    private String[] fruits = new String[]{"Apple", "Banana", "Pear"};

    @Override
    public String next() {
        Random rand = new Random();
        return fruits[rand.nextInt(3)];
    }
}
```

### 3. 泛型方法
在Java中,泛型类的定义非常简单，但是泛型方法就比较复杂了。
尤其是我们见到的大多数泛型类中的成员方法也都使用了泛型，有的甚至泛型类中也包含着泛型方法，这样在初学者中非常容易将泛型方法理解错了。
泛型类，是在实例化类的时候指明泛型的具体类型；泛型方法，是在调用方法的时候指明泛型的具体类型 。

```java
/**
 * 泛型方法的基本介绍
 * @param tClass 传入的泛型实参
 * @return T 返回值为T类型
 * 说明：
 *     1）public 与 返回值中间<T>非常重要，可以理解为声明此方法为泛型方法。
 *     2）只有声明了<T>的方法才是泛型方法，泛型类中的使用了泛型的成员方法并不是泛型方法。
 *     3）<T>表明该方法将使用泛型类型T，此时才可以在方法中使用泛型类型T。
 *     4）与泛型类的定义一样，此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型。
 */
public <T> T genericMethod(Class<T> tClass)throws InstantiationException ,
  IllegalAccessException{
        T instance = tClass.newInstance();
        return instance;
}
```
```java
Object obj = genericMethod(Class.forName("com.test.test"));
```

### 限定泛型类型变量
1. 对方法的限定
```java
//对方法的限定：public static<T extends Comparable<T>>T getMin(T a, T b) {}
/**
 * Author：Jay On 2019/5/10 16:38
 * <p>
 * Description: 类型变量的限定-方法
 */
public class TypeLimitForMethod {

    /**
     * 计算最小值
     * 如果要实现这样的功能就需要对泛型方法的类型做出限定
     */
//    private static <T> T getMin(T a, T b) {
//        return (a.compareTo(b) > 0) ? a : b;
//    }

    /**
     * 限定类型使用extends关键字指定
     * 可以使类，接口，类放在前面接口放在后面用&符号分割
     * 例如：<T extends ArrayList & Comparable<T> & Serializable>
     */
    public static <T extends Comparable<T>> T getMin(T a, T b) {
        return (a.compareTo(b) < 0) ? a : b;
    }

    public static void main(String[] args) {
        System.out.println(TypeLimitForMethod.getMin(2, 4));
        System.out.println(TypeLimitForMethod.getMin("a", "r"));
    }
}
```

2. 对类的限定
```java
//对类的限定：public class TypeLimitForClass<T extends List & Serializable>{}
/**
 * Author：Jay On 2019/5/10 17:02
 * <p>
 * Description: 类型变量的限定-类
 */
public class TypeLimitForClass<T extends List & Serializable> {
    private T data;

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }

    public static void main(String[] args) {
        ArrayList<String> stringArrayList = new ArrayList<>();
        stringArrayList.add("A");
        stringArrayList.add("B");
        ArrayList<Integer> integerArrayList = new ArrayList<>();
        integerArrayList.add(1);
        integerArrayList.add(2);
        integerArrayList.add(3);
        TypeLimitForClass<ArrayList> typeLimitForClass01 = new TypeLimitForClass<>();
        typeLimitForClass01.setData(stringArrayList);
        TypeLimitForClass<ArrayList> typeLimitForClass02 = new TypeLimitForClass<>();
        typeLimitForClass02.setData(integerArrayList);

        System.out.println(getMinListSize(typeLimitForClass01.getData().size(), typeLimitForClass02.getData().size()));

    }

    public static <T extends Comparable<T>> T getMinListSize(T a, T b) {
        return (a.compareTo(b) < 0) ? a : b;
    }
```

### 泛型中的约束和局限性
1. 不能实例化泛型类
2. 静态变量或方法不能引用泛型类型变量，但是静态泛型方法是可以的
3. 基本类型无法作为泛型类型
4. 无法使用instanceof关键字或==判断泛型类的类型
5. 泛型类的原生类型与所传递的泛型无关，无论传递什么类型，原生类是一样的
6. 泛型数组可以声明但无法实例化
7. 泛型类不能继承Exception或者Throwable
8. 不能捕获泛型类型限定的异常但可以将泛型限定的异常抛出
```java
/**
 * Author：Jay On 2019/5/10 17:41
 * <p>
 * Description: 泛型的约束和局限性
 */
public class GenericRestrict1<T> {
    static class NormalClass {

    }

    private T data;

    /**
     * 不能实例化泛型类
     * Type parameter 'T' cannot be instantiated directly
     */
    public void setData() {
        //this.data = new T();
    }

    /**
     * 静态变量或方法不能引用泛型类型变量
     * 'com.jay.java.泛型.restrict.GenericRestrict1.this' cannot be referenced from a static context
     */
//    private static T result;

//    private static T getResult() {
//        return result;
//    }

    /**
     * 静态泛型方法是可以的
     */
    private static <K> K getKey(K k) {
        return k;
    }

    public static void main(String[] args) {
        NormalClass normalClassA = new NormalClass();
        NormalClass normalClassB = new NormalClass();
        /**
         * 基本类型无法作为泛型类型
         */
//        GenericRestrict1<int> genericRestrictInt = new GenericRestrict1<>();
        GenericRestrict1<Integer> genericRestrictInteger = new GenericRestrict1<>();
        GenericRestrict1<String> genericRestrictString = new GenericRestrict1<>();
        /**
         * 无法使用instanceof关键字判断泛型类的类型
         * Illegal generic type for instanceof
         */
//        if(genericRestrictInteger instanceof GenericRestrict1<Integer>){
//            return;
//        }

        /**
         * 无法使用“==”判断两个泛型类的实例
         * Operator '==' cannot be applied to this two instance
         */
//        if (genericRestrictInteger == genericRestrictString) {
//            return;
//        }

        /**
         * 泛型类的原生类型与所传递的泛型无关，无论传递什么类型，原生类是一样的
         */
        System.out.println(normalClassA == normalClassB);//false
        System.out.println(genericRestrictInteger == genericRestrictInteger);//
        System.out.println(genericRestrictInteger.getClass() == genericRestrictString.getClass()); //true
        System.out.println(genericRestrictInteger.getClass());//com.jay.java.泛型.restrict.GenericRestrict1
        System.out.println(genericRestrictString.getClass());//com.jay.java.泛型.restrict.GenericRestrict1

        /**
         * 泛型数组可以声明但无法实例化
         * Generic array creation
         */
        GenericRestrict1<String>[] genericRestrict1s;
//        genericRestrict1s = new GenericRestrict1<String>[10];
        genericRestrict1s = new GenericRestrict1[10];
        genericRestrict1s[0]=genericRestrictString;
    }

}
```
```java
/**
 * Author：Jay On 2019/5/10 18:45
 * <p>
 * Description: 泛型和异常
 */
public class GenericRestrict2 {

    private class MyException extends Exception {
    }

    /**
     * 泛型类不能继承Exception或者Throwable
     * Generic class may not extend 'java.lang.Throwable'
     */
//    private class MyGenericException<T> extends Exception {
//    }
//
//    private class MyGenericThrowable<T> extends Throwable {
//    }

    /**
     * 不能捕获泛型类型限定的异常
     * Cannot catch type parameters
     */
    public <T extends Exception> void getException(T t) {
//        try {
//
//        } catch (T e) {
//
//        }
    }

    /**
     *可以将泛型限定的异常抛出
     */
    public <T extends Throwable> void getException(T t) throws T {
        try {

        } catch (Exception e) {
            throw t;
        }
    }
}
```

### 泛型类型继承规则
1. 对于泛型参数是继承关系的泛型类之间是没有继承关系的
2. 泛型类可以继承其它泛型类
    例如: public class ArrayList<E> extends AbstractList<E>
3. 泛型类的继承关系在使用中同样会受到泛型类型的影响
```java
/**
 * Author：Jay On 2019/5/10 19:13
 * <p>
 * Description: 泛型继承规则测试类
 */
public class GenericInherit<T> {
    private T data1;
    private T data2;

    public T getData1() {
        return data1;
    }

    public void setData1(T data1) {
        this.data1 = data1;
    }

    public T getData2() {
        return data2;
    }

    public void setData2(T data2) {
        this.data2 = data2;
    }

    public static <V> void setData2(GenericInherit<Father> data2) {

    }

    public static void main(String[] args) {
//        Son 继承自 Father
        Father father = new Father();
        Son son = new Son();
        GenericInherit<Father> fatherGenericInherit = new GenericInherit<>();
        GenericInherit<Son> sonGenericInherit = new GenericInherit<>();
        SubGenericInherit<Father> fatherSubGenericInherit = new SubGenericInherit<>();
        SubGenericInherit<Son> sonSubGenericInherit = new SubGenericInherit<>();

        /**
         * 对于传递的泛型类型是继承关系的泛型类之间是没有继承关系的
         * GenericInherit<Father> 与GenericInherit<Son> 没有继承关系
         * Incompatible types.
         */
        father = new Son();
//        fatherGenericInherit=new GenericInherit<Son>();

        /**
         * 泛型类可以继承其它泛型类，例如: public class ArrayList<E> extends AbstractList<E>
         */
        fatherGenericInherit=new SubGenericInherit<Father>();

        /**
         *泛型类的继承关系在使用中同样会受到泛型类型的影响
         */
        setData2(fatherGenericInherit);
//        setData2(sonGenericInherit);
        setData2(fatherSubGenericInherit);
//        setData2(sonSubGenericInherit);

    }

    private static class SubGenericInherit<T> extends GenericInherit<T> {

    }
```

### 通配符类型
1. <? extends Parent> 指定了泛型类型的上届
2. <? super Child> 指定了泛型类型的下届
3.  <?> 指定了没有限制的泛型类型
![泛型](https://gitee.com/lemonsoldout/images/raw/master/泛型2.png)
```java
/**
 * Author：Jay On 2019/5/10 19:51
 * <p>
 * Description: 泛型通配符测试类
 */
public class GenericByWildcard {
    private static void print(GenericClass<Fruit> fruitGenericClass) {
        System.out.println(fruitGenericClass.getData().getColor());
    }

    private static void use() {
        GenericClass<Fruit> fruitGenericClass = new GenericClass<>();
        print(fruitGenericClass);
        GenericClass<Orange> orangeGenericClass = new GenericClass<>();
        //类型不匹配,可以使用<? extends Parent> 来解决
//        print(orangeGenericClass);
    }

    /**
     * <? extends Parent> 指定了泛型类型的上届
     */
    private static void printExtends(GenericClass<? extends Fruit> genericClass) {
        System.out.println(genericClass.getData().getColor());
    }

    public static void useExtend() {
        GenericClass<Fruit> fruitGenericClass = new GenericClass<>();
        printExtends(fruitGenericClass);
        GenericClass<Orange> orangeGenericClass = new GenericClass<>();
        printExtends(orangeGenericClass);

        GenericClass<Food> foodGenericClass = new GenericClass<>();
        //Food是Fruit的父类，超过了泛型上届范围，类型不匹配
//        printExtends(foodGenericClass);

        //表示GenericClass的类型参数的上届是Fruit
        GenericClass<? extends Fruit> extendFruitGenericClass = new GenericClass<>();
        Apple apple = new Apple();
        Fruit fruit = new Fruit();
        /*
         * 道理很简单，？ extends X  表示类型的上界，类型参数是X的子类，那么可以肯定的说，
         * get方法返回的一定是个X（不管是X或者X的子类）编译器是可以确定知道的。
         * 但是set方法只知道传入的是个X，至于具体是X的那个子类，不知道。
         * 总结：主要用于安全地访问数据，可以访问X及其子类型，并且不能写入非null的数据。
         */
//        extendFruitGenericClass.setData(apple);
//        extendFruitGenericClass.setData(fruit);

        fruit = extendFruitGenericClass.getData();

    }

    /**
     * <? super Child> 指定了泛型类型的下届
     */
    public static void printSuper(GenericClass<? super Apple> genericClass) {
        System.out.println(genericClass.getData());
    }

    public static void useSuper() {
        GenericClass<Food> foodGenericClass = new GenericClass<>();
        printSuper(foodGenericClass);

        GenericClass<Fruit> fruitGenericClass = new GenericClass<>();
        printSuper(fruitGenericClass);

        GenericClass<Apple> appleGenericClass = new GenericClass<>();
        printSuper(appleGenericClass);

        GenericClass<HongFuShiApple> hongFuShiAppleGenericClass = new GenericClass<>();
        // HongFuShiApple 是Apple的子类，达不到泛型下届，类型不匹配
//        printSuper(hongFuShiAppleGenericClass);

        GenericClass<Orange> orangeGenericClass = new GenericClass<>();
        // Orange和Apple是兄弟关系，没有继承关系，类型不匹配
//        printSuper(orangeGenericClass);

        //表示GenericClass的类型参数的下界是Apple
        GenericClass<? super Apple> supperAppleGenericClass = new GenericClass<>();
        supperAppleGenericClass.setData(new Apple());
        supperAppleGenericClass.setData(new HongFuShiApple());
        /*
         * ？ super  X  表示类型的下界，类型参数是X的超类（包括X本身），
         * 那么可以肯定的说，get方法返回的一定是个X的超类，那么到底是哪个超类？不知道，
         * 但是可以肯定的说，Object一定是它的超类，所以get方法返回Object。
         * 编译器是可以确定知道的。对于set方法来说，编译器不知道它需要的确切类型，但是X和X的子类可以安全的转型为X。
         * 总结：主要用于安全地写入数据，可以写入X及其子类型。
         */
//        supperAppleGenericClass.setData(new Fruit());

        //get方法只会返回一个Object类型的值。
        Object data = supperAppleGenericClass.getData();
    }

    /**
     * <?> 指定了没有限定的通配符
     */
    public static void printNonLimit(GenericClass<?> genericClass) {
        System.out.println(genericClass.getData());
    }

    public static void useNonLimit() {
        GenericClass<Food> foodGenericClass = new GenericClass<>();
        printNonLimit(foodGenericClass);
        GenericClass<Fruit> fruitGenericClass = new GenericClass<>();
        printNonLimit(fruitGenericClass);
        GenericClass<Apple> appleGenericClass = new GenericClass<>();
        printNonLimit(appleGenericClass);

        GenericClass<?> genericClass = new GenericClass<>();
        //setData 方法不能被调用， 甚至不能用 Object 调用；
//        genericClass.setData(foodGenericClass);
//        genericClass.setData(new Object());
        //返回值只能赋给 Object
        Object object = genericClass.getData();

    }

}
```

### 获取泛型的参数类型
这里的Type指java.lang.reflect.Type, 是Java中所有类型的公共高级接口, 代表了Java中的所有类型. Type体系中类型的包括：数组类型(GenericArrayType)、参数化类型(ParameterizedType)、类型变量(TypeVariable)、通配符类型(WildcardType)、原始类型(Class)、基本类型(Class), 以上这些类型都实现Type接口.

参数化类型,就是我们平常所用到的泛型List、Map；
数组类型,并不是我们工作中所使用的数组String[] 、byte[]，而是带有泛型的数组，即T[] ；
通配符类型, 指的是<?>, <? extends T>等等
原始类型, 不仅仅包含我们平常所指的类，还包括枚举、数组、注解等；
基本类型, 也就是我们所说的java的基本类型，即int,float,double等

```java
public interface ParameterizedType extends Type {
    // 返回确切的泛型参数, 如Map<String, Integer>返回[String, Integer]
    Type[] getActualTypeArguments();
    
    //返回当前class或interface声明的类型, 如List<?>返回List
    Type getRawType();
    
    //返回所属类型. 如,当前类型为O<T>.I<S>, 则返回O<T>. 顶级类型将返回null 
    Type getOwnerType();
}
```
```java
/**
 * Author：Jay On 2019/5/11 22:41
 * <p>
 * Description: 获取泛型类型测试类
 */
public class GenericType<T> {
    private T data;

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }

    public static void main(String[] args) {
        GenericType<String> genericType = new GenericType<String>() {};
        Type superclass = genericType.getClass().getGenericSuperclass();
        //getActualTypeArguments 返回确切的泛型参数, 如Map<String, Integer>返回[String, Integer]
        Type type = ((ParameterizedType) superclass).getActualTypeArguments()[0]; 
        System.out.println(type);//class java.lang.String
    }
}
```

### 虚拟机是如何实现泛型的
Java泛型是Java1.5之后才引入的，为了向下兼容。Java采用了C++完全不同的实现思想。Java中的泛型更多的看起来像是编译期用的
Java中泛型在运行期是不可见的，会被擦除为它的上级类型。如果是没有限定的泛型参数类型，就会被替换为Object.
```java
GenericClass<String> stringGenericClass=new GenericClass<>();
GenericClass<Integer> integerGenericClass=new GenericClass<>();
```

C++中GenericClass<String>和GenericClass<Integer>是两个不同的类型
Java进行了类型擦除之后统一改为GenericClass<Object>
```java
/**
 * Author：Jay On 2019/5/11 16:11
 * <p>
 * Description:泛型原理测试类
 */
public class GenericTheory {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        map.put("Key", "Value");
        System.out.println(map.get("Key"));
        GenericClass<String, String> genericClass = new GenericClass<>();
        genericClass.put("Key", "Value");
        System.out.println(genericClass.get("Key"));
    }

    public static class GenericClass<K, V> {
        private K key;
        private V value;

        public void put(K key, V value) {
            this.key = key;
            this.value = value;
        }

        public V get(V key) {
            return value;
        }
    }

    /**
     * 类型擦除后GenericClass2<Object>
     * @param <T>
     */
    private class GenericClass2<T> {

    }

    /**
     * 类型擦除后GenericClass3<ArrayList>
     * 当使用到Serializable时会将相应代码强制转换为Serializable
     * @param <T>
     */
    private class GenericClass3<T extends ArrayList & Serializable> {

    }
}
```

对应的字节码文件
```java
 public static void main(String[] args) {
        Map<String, String> map = new HashMap();
        map.put("Key", "Value");
        System.out.println((String)map.get("Key"));
        GenericTheory.GenericClass<String, String> genericClass = new GenericTheory.GenericClass();
        genericClass.put("Key", "Value");
        System.out.println((String)genericClass.get("Key"));
    }
```

