[TOC]

## 反射机制

1. 反射

- 反射是将类中的属性，方法，构造方法等解剖成一个个小的对象，并且能够调用

2. 为什么使用反射

- 在一个类中，可以创建另外一个类的对象，调用其的属性和方法，无论那个类是否被创建了。

3. 如何使用反射

- 类

	- `Class class=Class.forName(包名.类名)`，每一个类都有唯一的一个类对象，这个类对象可以的得到类中的所有信息。

- 构造方法

	- `class.getConstructor(null);`     得到无参构造方法，返回`Constructor`

	- `class.getConstructor(String.clss,int.class);`    得到有参构造方法，返回`Constructor`

	- `constructor2.newInstance("曹菜菜",21);`    返回`Object`类型的对象

	- `class.getConstructors();`     得到所有构造方法，返回`Constructor[]`数组

- 方法

	- `getMethod();`                得到普通的方法，返回`Method`，指定方法名

	- `class1.getMethod("eat", null);`      无参、无返回值、非私有的方法。

	- `class1.getMethod("play", String.class);` 有参、无返回值。非私有方法。参数一，`方法名`。参数二，`参数类型.class`

	- `method(n).invoke(object,null);`      方法的执行，参数一是对象，参数二是传的参数值。

	- `getMethods();`      得到子类和父类所有普通的方法，返回`Method[]`数组

	- `class1.getDeclaredMethod("sleep", null);`      得到私有的方法

	- `method6.setAccessible(true);`

	- `class1.getDeclaredMethods();`        得到自己类的所有方法，包括私有的，返回`Method[]`

- 属性

	- `getFields();`                得到`public`所有属性，返回`Field[]`

	- `getFileld("name");`          得到`public`属性，返回`Field`,指定属性名

	- `field3.set(object5, "菜菜");`

	- `Object object6 = field3.get(object5);`

	- `getDeclareFields(); `        得到所有属性，包括私有的，返回`Field[]`

	- `getDeclareField(); `         得到属性，包括私有的，指定属性名，返回`Field`

4. 反射的优点

	- 提高了`Java`程序的灵活性和扩展性，降低了耦合性，提高了自适应的能力

	- 允许程序创建和控制任何类的对象，无需提前编码目标类

5. 反射的缺点

- 性能问题

- 代码维护问题    

## 反射
[反射-网络来源](https://blog.csdn.net/huangliniqng/article/details/88554510)

### 基本反射实现
- 根据一个字符串得到一个类
1. `getClass`方法
```java
 String name = "Huanglinqing";
 Class c1 = name.getClass();
 System.out.println(c1.getName());
```

2. `Class.forName()`
```java
   String name = "java.lang.String";
   Class c1 = null;
   try {
          c1 = Class.forName(name);
          System.out.println(c1.getName());
      } catch (ClassNotFoundException e) {
  }
```

这里也通过捕获异常，因为我们传的这个字符串可能不合法，字符串合法命名是类的命名空间和类的名称组成。
我们也可以通过`c1.getSuperclass()`获取其父类。

3. Type属性
基本类型都有type属性，可以得到这个基本类型的类型
```java
Class c1 = Boolean.TYPE;
Class c2 = Byte.TYPE;
Class c3 = Float.TYPE;
Class c4 = Double.TYPE;
```

### 获取类的成员
当类中方法定义为私有的时候我们能调用？不能！当变量是私有的时候我们能获取吗？不能！但是反射可以，比如源码中有你需要用到的方法，但是那个方法是私有的，这个时候你就可以通过反射去执行这个私有方法，并且获取私有变量。

1. 获取类的构造函数
Test类中我们定义是三个私有变量，生成两个公有的含参构造方法和一个私有的含参构造方法以及一个公有的无参构造方法。
```java
public class Test {
 
    private int age;
    private String name;
    private int testint;
 
    public Test(int age) {
        this.age = age;
    }
 
    public Test(int age, String name) {
        this.age = age;
        this.name = name;
    }
 
    private Test(String name) {
        this.name = name;
    }
 
    public Test() {
    }
```

2. 获取类的构造方法
```java
 Test test = new Test();
 Class c4 = test.getClass();
 Constructor[] constructors ;
 constructors = c4.getDeclaredConstructors();
```

通过`getDeclaredConstructors`可以返回类的所有构造方法，返回的是一个数组因为构造方法可能不止一个，通过`getModifiers`可以得到构造方法的类型，`getParameterTypes`可以得到构造方法的所有参数，返回的是一个`Class`数组，所以我们如果想获取所有构造方法以及每个构造方法的参数类型
```java
  for (int i = 0; i < constructors.length; i++) {
        System.out.print(Modifier.toString(constructors[i].getModifiers()) + "参数：");
        Class[] parametertypes = constructors[i].getParameterTypes();
        for (int j = 0; j < parametertypes.length; j++) {
             System.out.print(parametertypes[j].getName() + " ");
       }
      System.out.println("");
  }
```

3. 获取类中特定的构造方法
我们可以通过`getConstructors`方法获取类中 所有的`public`类型的构造方法，代码和上面一样就不演示了。

我们可以通过`getDeclaredConstructor()`方法传参获取特定参数类型的构造方法，这里注意是`getDeclaredConstructor()`不是  `getDeclaredConstructors()` ，所以返回的是一个`Class`对象而不是一个`Class`数组。

获取无参构造方法直接不传参数,这里要进行异常捕获，因为可能不存在对应的构造方法.
```java
   try {
          constructors = c4.getDeclaredConstructor();
          System.out.print(Modifier.toString(constructors.getModifiers()) + );
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
      }
```

想获取有两个参数分别为`int`和`String`类型的构造方法
```java
  Class[] p = {int.class,String.class};
  try {
       constructors = c4.getDeclaredConstructor(p);
       System.out.print(Modifier.toString(constructors.getModifiers()) + "参数:");
       Class[] parametertypes = constructors.getParameterTypes();
       for (int j = 0; j < parametertypes.length; j++) {
            System.out.print(parametertypes[j].getName() + " ");
          }
       } catch (NoSuchMethodException e) {
            e.printStackTrace();
     }
```

4. 调用构造方法
从这里开始慢慢到了关键的一步，得到类的实例，我们主要借助于`newInstance`方法，为了方便演示我们将测试类的两个构造方法打印出来. 
```java
   public Test(int age, String name) {
        this.age = age;
        this.name = name;
        System.out.println("hello" + name + "i am" + age);
    }
 
 
    private Test(String name) {
        this.name = name;
        System.out.println("My Name is" +
                name);
    }
```
```java
// 调用public的方法 
Class[] p = {int.class,String.class};
 constructors = c4.getDeclaredConstructor(p);
 constructors.newInstance(24,"HuangLinqing");
```

那么调用私有构造方法呢，和上面一样，只是我们要设置`constructors.setAccessible(true);`
```java
Class[] p = {String.class};
  constructors = c4.getDeclaredConstructor(p);
  constructors.setAccessible(true);
  constructors.newInstance("HuangLinqing");
```

5. 调用类的私有方法
如何调用类中的私有方法呢，我们先在测试类中编写一个测试的私有方法
```java
private void welcome(String tips){
        System.out.println(tips);
    }
```

我们知道如果我们要正常的调用类的方法都是通过类.方法调用，所以我们调用私有方法也需要得到类的实例，而我们上面`newInstace`已经得到了类的实例，这样就好办了。
```java
Class[] p4 = {String.class};
   Method method = c4.getDeclaredMethod("welcome",p4);
   method.setAccessible(true);
```

我们首先通过` getDeclaredMethod`方法获取到这个私有方法，第一个参数是方法名，第二个参数是参数类型
然后通过`invoke`方法执行，`invoke`需要两个参数一个是类的实例，一个是方法参数。

```java
Class[] p4 = {String.class};
     Method method = c4.getDeclaredMethod("welcome",p4);
     method.setAccessible(true);
     Object arg1s[] = {"欢迎关注代码男人技术公众号"};
     method.invoke(test,arg1s);
```

`test`类的实例当不能`new `获取的时候我们也可以通过反射获取，就是上面的`newInstance`方法。

6.  获取类的私有字段并修改值
看到这里你可能会说，有了`set`方法，什么私有不私有，`test.set`不就可以了，但是这里要注意我们是没有办法得到这个类的实例的，要不然都可以得到实例就没有反射一说了。我们在通过反射得到类的实例之后先获取字段。
```java
Field field = c4.getDeclaredField("name");
field.setAccessible(true);
field.set(o,"代码男人");
```

o是我们上面通过反射构造方法获取的实例, 打印`field.get(o).toString()`的值。
不过要注意的是我们修改了`name`的值只对当前的实例对象有效。

### 反射封装类
```java
package jnidemo.hlq.com.hookdemo;
 
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;
 
/**
 * @author Huanglinqing
 * @date 2019/4/28
 */
 
public class Reflex {
 
    /**
     * 获取无参构造函数
     * @param className
     * @return
     */
    public static Object createObject(String className) {
        Class[] pareTyples = new Class[]{};
        Object[] pareVaules = new Object[]{};
 
        try {
            Class r = Class.forName(className);
            return createObject(r, pareTyples, pareVaules);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
 
        return null;
    }
 
    /**
     * 获取无参构造方法
     * @param clazz
     * @return
     */
    public static Object createObject(Class clazz) {
        Class[] pareTyple = new Class[]{};
        Object[] pareVaules = new Object[]{};
 
        return createObject(clazz, pareTyple, pareVaules);
    }
 
    /**
     * 获取一个参数的构造函数  已知className
     *
     * @param className
     * @param pareTyple
     * @param pareVaule
     * @return
     */
    public static Object createObject(String className, Class pareTyple, Object pareVaule) {
 
        Class[] pareTyples = new Class[]{pareTyple};
        Object[] pareVaules = new Object[]{pareVaule};
 
        try {
            Class r = Class.forName(className);
            return createObject(r, pareTyples, pareVaules);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
 
        return null;
    }
 
 
    /**
     * 获取单个参数的构造方法 已知类
     *
     * @param clazz
     * @param pareTyple
     * @param pareVaule
     * @return
     */
    public static Object createObject(Class clazz, Class pareTyple, Object pareVaule) {
        Class[] pareTyples = new Class[]{pareTyple};
        Object[] pareVaules = new Object[]{pareVaule};
 
        return createObject(clazz, pareTyples, pareVaules);
    }
 
    /**
     * 获取多个参数的构造方法 已知className
     * @param className
     * @param pareTyples
     * @param pareVaules
     * @return
     */
    public static Object createObject(String className, Class[] pareTyples, Object[] pareVaules) {
        try {
            Class r = Class.forName(className);
            return createObject(r, pareTyples, pareVaules);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
 
        return null;
    }
 
 
    /**
     * 获取构造方法
     *
     * @param clazz
     * @param pareTyples
     * @param pareVaules
     * @return
     */
    public static Object createObject(Class clazz, Class[] pareTyples, Object[] pareVaules) {
        try {
            Constructor ctor = clazz.getDeclaredConstructor(pareTyples);
            ctor.setAccessible(true);
            return ctor.newInstance(pareVaules);
        } catch (Exception e) {
            e.printStackTrace();
        }
 
        return null;
    }
 
 
    /**
     * 获取多个参数的方法
     * @param obj
     * @param methodName
     * @param pareTyples
     * @param pareVaules
     * @return
     */
    public static Object invokeInstanceMethod(Object obj, String methodName, Class[] pareTyples, Object[] pareVaules) {
        if (obj == null) {
            return null;
        }
 
        try {
            //调用一个private方法 //在指定类中获取指定的方法
            Method method = obj.getClass().getDeclaredMethod(methodName, pareTyples);
            method.setAccessible(true);
            return method.invoke(obj, pareVaules);
 
        } catch (Exception e) {
            e.printStackTrace();
        }
 
        return null;
    }
 
    /**
     * 获取一个参数的方法
     * @param obj
     * @param methodName
     * @param pareTyple
     * @param pareVaule
     * @return
     */
    public static Object invokeInstanceMethod(Object obj, String methodName, Class pareTyple, Object pareVaule) {
        Class[] pareTyples = {pareTyple};
        Object[] pareVaules = {pareVaule};
 
        return invokeInstanceMethod(obj, methodName, pareTyples, pareVaules);
    }
 
    /**
     * 获取无参方法
     * @param obj
     * @param methodName
     * @return
     */
    public static Object invokeInstanceMethod(Object obj, String methodName) {
        Class[] pareTyples = new Class[]{};
        Object[] pareVaules = new Object[]{};
 
        return invokeInstanceMethod(obj, methodName, pareTyples, pareVaules);
    }
 
 
    /**
     * 无参静态方法
     * @param className
     * @param method_name
     * @return
     */
    public static Object invokeStaticMethod(String className, String method_name) {
        Class[] pareTyples = new Class[]{};
        Object[] pareVaules = new Object[]{};
 
        return invokeStaticMethod(className, method_name, pareTyples, pareVaules);
    }
 
    /**
     * 获取一个参数的静态方法
     * @param className
     * @param method_name
     * @param pareTyple
     * @param pareVaule
     * @return
     */
    public static Object invokeStaticMethod(String className, String method_name, Class pareTyple, Object pareVaule) {
        Class[] pareTyples = new Class[]{pareTyple};
        Object[] pareVaules = new Object[]{pareVaule};
 
        return invokeStaticMethod(className, method_name, pareTyples, pareVaules);
    }
 
    /**
     * 获取多个参数的静态方法
     * @param className
     * @param method_name
     * @param pareTyples
     * @param pareVaules
     * @return
     */
    public static Object invokeStaticMethod(String className, String method_name, Class[] pareTyples, Object[] pareVaules) {
        try {
            Class obj_class = Class.forName(className);
            return invokeStaticMethod(obj_class, method_name, pareTyples, pareVaules);
        } catch (Exception e) {
            e.printStackTrace();
        }
 
        return null;
    }
 
    /**
     * 无参静态方法
     * @param method_name
     * @return
     */
    public static Object invokeStaticMethod(Class clazz, String method_name) {
        Class[] pareTyples = new Class[]{};
        Object[] pareVaules = new Object[]{};
 
        return invokeStaticMethod(clazz, method_name, pareTyples, pareVaules);
    }
 
    /**
     * 一个参数静态方法
     * @param clazz
     * @param method_name
     * @param classType
     * @param pareVaule
     * @return
     */
    public static Object invokeStaticMethod(Class clazz, String method_name, Class classType, Object pareVaule) {
        Class[] classTypes = new Class[]{classType};
        Object[] pareVaules = new Object[]{pareVaule};
 
        return invokeStaticMethod(clazz, method_name, classTypes, pareVaules);
    }
 
    /**
     * 多个参数的静态方法
     * @param clazz
     * @param method_name
     * @param pareTyples
     * @param pareVaules
     * @return
     */
    public static Object invokeStaticMethod(Class clazz, String method_name, Class[] pareTyples, Object[] pareVaules) {
        try {
            Method method = clazz.getDeclaredMethod(method_name, pareTyples);
            method.setAccessible(true);
            return method.invoke(null, pareVaules);
        } catch (Exception e) {
            e.printStackTrace();
        }
 
        return null;
    }
 
 
    public static Object getFieldObject(String className, Object obj, String filedName) {
        try {
            Class obj_class = Class.forName(className);
            return getFieldObject(obj_class, obj, filedName);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return null;
    }
 
    public static Object getFieldObject(Class clazz, Object obj, String filedName) {
        try {
            Field field = clazz.getDeclaredField(filedName);
            field.setAccessible(true);
            return field.get(obj);
        } catch (Exception e) {
            e.printStackTrace();
        }
 
        return null;
    }
 
 
    public static void setFieldObject(Class clazz, Object obj, String filedName, Object filedVaule) {
        try {
            Field field = clazz.getDeclaredField(filedName);
            field.setAccessible(true);
            field.set(obj, filedVaule);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
 
    public static void setFieldObject(String className, Object obj, String filedName, Object filedVaule) {
        try {
            Class obj_class = Class.forName(className);
            setFieldObject(obj_class, obj, filedName, filedVaule);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
 
 
    public static Object getStaticFieldObject(String className, String filedName) {
        return getFieldObject(className, null, filedName);
    }
 
    public static Object getStaticFieldObject(Class clazz, String filedName) {
        return getFieldObject(clazz, null, filedName);
    }
 
    public static void setStaticFieldObject(String classname, String filedName, Object filedVaule) {
        setFieldObject(classname, null, filedName, filedVaule);
    }
 
    public static void setStaticFieldObject(Class clazz, String filedName, Object filedVaule) {
        setFieldObject(clazz, null, filedName, filedVaule);
    }
}
```