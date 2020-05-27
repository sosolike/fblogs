# 换一种方式看见类-java反射

## 1.什么是反射？

​		反射是java提供的一种在**程序运行时**能够**获取和访问**一个**类完整信息**的**能力**，即通过反射可以在程序运行时获取一个类的属性、方法、构造函数、注解、父类等一切关于该类的信息，并能够直接访问类的属性、调用类的方法、通过类的构造函数创建对象等；

## 2.为何需要反射？

​		正常情况下，我们需要将不同情况下访问不同类信息的代码硬编码在我们代码中，并且在编译期间就会检查硬编码中的类是否存在、访问方式是否合理等，比如根据路由的不同执行不同控制器的代码：

```java
public void doProccess(String route){
  switch(route){
    case "index":
      /* 如果是index路由，则执行indexController控制器中的方法 */
      com.sslike.IndexController indexController = new com.sslike.IndexController();
      indexController.doSomething();
      break;
    case "user":
      /* 如果是user路由，则执行userController控制器中的方法 */
      com.sslike.UserController indexController = new com.sslike.UserController();
      userController.doSomething();
      break;
      ...
    default:
      return null;
  }
}
```

​		虽然通过硬编码的方式可以实现根据不同情况执行不同控制器方法的目的，但如果在route信息较多的时候，那么switch中将会充斥这个巨多case，而且在需要添加一个新的控制器和路由映射关系时还需要手动修改上述代码；

​		这种情况最佳的优化就是在程序运行时能够根据传入的路由自动匹配类的路径，并创建类的对象，执行对应的方法，而不是硬编码在代码中，而反射恰恰具有运行时获取类信息的能力，因此可以通过反射来实现，如下示例：

```java
/* 控制器统一接口 */
public interface Controller{}
/* 首页index控制器 */
public class Index implements Controller{
  public doSomething(){
    System.out.println("index controller");
  }
}
/* 用户控制器 */
public class User implements Controller{
  public doSomething(){
    System.out.println("user controller");
  }
}

/* 路由处理器 */
public class Route{
  private Map<String, String> routeInfo = new HashMap<>();
  
  public Route(){
    routeInfo.put("index","com.sslike.Index");
    routeInfo.put("user","com.sslike.User");
  }
  
  /* 根据路由执行对应方法(此处为了简化代码，假设都执行doSomething方法) */
  public void doProccess(String route){
    /* 根据路由信息查找类路径 */
    String classPath = routeInfo.get(route);
    
    /* 根据类路径获取类构造函数并创建类实例对象 */
    Class class = Class.forName(classPath);
    Constructor constructor = class.getDeclaredConstuctor();
    Controller controller = constructor.newInstance();
    
    /* 获取并执行控制器的方法 */
    Method method = class.getDeclaredMethod("doSomething");
    method.invoke(controller);
  }
}
```

​		通过反射的方式实现根据路由执行控制器方法无需再手动添加映射关系，只需要在启动时将路由和控制的映射关系添加至Map中即可；既简化了代码，又提高了代码的可维护性；

​		从上述例子可以看出，反射最大的特征就在于不会在编译期间检查代码中对类的依赖情况，而是在运行时动态查找并获取类的信息，因此可以通过反射实现代码的动态特征；

## 3.反射相关api

> 以下面的类来说明java中反射相关的api

```java
public class User{
  public static String userName;
  public int userAge;
  private int userId;
  
  public User(){
    
  }
  
  private User(String userName, int userAge, int userId){
    this.userName = userName;
    this.userAge = userAge;
    this.userId = userId;
	}
  
  public User(int userAge, int userId){
    this.userAge = userAge;
    this.userId = userId;
	}
  
  public String getUserName(){
    return this.userName;
  }
  private int getUserAge(){
    return this.userAge;
  }
  
}
```

### 1.通过反射获取构造函数：

#### 1.获取指定参数类型、任意权限的构造方法：

```java
Constructor constructor = tclass.getDeclaredConstructor(int.class, int.class);
```

#### 2.获取指定参数类型、public权限的构造方法：

```java
Constructor constructor = tclass.getConstructor(int.class, int.class);
```

#### 3.获取所有任意权限的构造方法：

```java
Constructor[] constructors = tclass.getDeclaredConstructor();
```

#### 4.获取所有public权限的构造方法：

```java
Constructor[] constructors = tclass.getConstructor();
```

​		示例：

```java
Class tclass = Class.forName("com.sslike.User");
/* 获取无参构造方法 */
Constructor constructor = tclass.getConstructor();

/* 获取指定参数类型、任意权限的构造方法 */
Constructor constructor = tclass.getDeclaredConstructor(String.class, int.class, int.class);

/* 获取指定参数类型、public权限的构造方法*/
Constructor constructor = tclass.getConstructor(int.class, int.class);

/* 遍历获取每一个构造方法，使用getDeclaredConstructor时无论构造方法权限如何都能获取到 */
Constructor[] constructors = tclass.getDeclaredConstructor();
/* 遍历获取每一个构造方法，使用getConstructor时只能获取到public权限的构造方法 */
Constructor[] constructors = tclass.getConstructor();
for(Constructor constructor:constructors){
  	//可通过遍历获取每一个构造方法
    System.out.println(constructor.toString());
}
```

#### 5.通过构造方法创建该类的实例对象：

##### 1.通过无参构造方法创建对象：

```java
/* 使用class通过无参构造函数创建对象 */
Class tclass = Class.forName("com.sslike.User");
User user = (User)tclass.newInstance();

/* 通过constructor通过无参构造函数创建对象 */
Constructor constructor = tclass.getDeclaredConstructor();
User user = (User)constructor.newInstance();
```

##### 2.通过有参构造方法创建对象：

```java
/* 通过有参构造方法创建对象 */
Class tclass = Class.forName("com.sslike.User");
/* 获取指定参数类型的构造方法 */
Constructor constructor = tclass.getDeclaredConstructor(int.class,int.class);
/* 通过构造方法创建对象，必须按照构造方法参数类型、数量、顺序的要求赋值 */
User user = (User)constructor.newInstance(12,23);
```

### 2.通过反射获取类成员变量：

#### 1.获取指定名称、任意权限的成员变量：

```java
Field field = tclass.getDeclaredField("fieldName");
```

#### 2.获取指定名称、public权限的成员变量：

```java
Field field = tclass.getField("fieldName");
```

#### 3.获取所有任意权限的成员变量：

```java
Field[] fields = tclass.getDeclaredFields();
```

#### 4.获取所有public权限的成员变量：

```java
Filed[] fileds = tclass.getFields();
```

​		示例：

```java
Class tclass = Class.forName("com.sslike.User");
/* 获取一个指定名称且任意权限的成员属性 */
Field field = tclass.getDeclaredField("userId");
/* 获取一个指定名称且为public权限的成员属性 */
Field field = tclass.getField("userAge");
/* 获取所有任意权限的成员属性 */
Field[] fields = tclass.getDeclaredFields();
/* 获取所有public权限的成员属性 */
Field[] fields = tclass.getFields();
for(Field field:fields){    
    System.out.println(field.toString());
}
```

### 3.通过反射获取类成员方法：

#### 1.获取指定名称、任意权限的方法：

```java
Method method =  tclass.getDeclaredMethod("methodName");
```

#### 2.获取指定名称、public权限的方法：

```java
Method method = tclass.getMethod("methodName");
```

#### 3.获取所有任意权限的方法：

```java
Method[] methods = tclass.getDeclaredMethod();
```

#### 4.获取所有public权限的方法：

```java
Method[] methods = tclass.getMethods();
```

示例：

```java
Class tclass = Class.forName("com.sslike.User");
/* 获取一个指定名称且任意权限的成员属性 */
Method method = tclass.getDeclaredMethod("getUserAge");
/* 获取一个指定名称且为public权限的成员属性 */
Method method = tclass.getMethod("getUserName");
/* 获取所有任意权限的成员属性 */
Method[] methods = tclass.getDeclaredMethods();
/* 获取所有public权限的成员属性 */
Method[] methods = tclass.getMethods();
for(Method method:methods){    
    System.out.println(method.toString());
}
```



#### 5.执行通过反射获取到的方法：

```java
/*
 * method为需要执行的方法，Method类型
 * object为当前方法所属的对象，如果方法是静态的，那么该参数可以为null
 * arg1、arg2...是传递给method的方法,如果方法没有参数，那么无需传入任何数据
 * 如果该方法有返回值，invoke在执行完毕之后会将返回值返回，否则返回为null
 */
method.invoke(object,arg1,arg2...);
```

示例：

```java
Class tclass = Class.forName("com.sslike.User");
//获取无参构造函数
Constructor constructor = tclass.getDeclaredConstructor();
//创建User类的实例对象
User user = constructor.newInstance();

//获取待执行的方法
Method method = tclass.getDeclaredMethod("getUserAge");
//执行getUserAge方法
method.invoke(user);
```

### 4.类成员的权限限制：

​		在使用getField、getDeclaredField、getConstructor、getDeclaredConstructor、getMethod、getDeclaredMethod通过反射获取类成员信息时是无法获取到public权限以外权限的成员，其关键在于java对指定成员是否开启权限检查；如果开启了权限检查，那么上述方法只能获取public权限的成员信息，如果没有开启权限检查，那么可以获取任意权限的成员信息；

#### 1.查看是否开启权限检查：

```java
/* 
 * 检查指定成员是否开启权限检查 
 * 如果开启权限检查该方法会返回false；如果未开启权限检查方法返回true
 * 权限检查时针对每一个成员的，需要通过Field、Method、Constructor类型的成员来访问
 */
isAccess()
```

示例：

```java
Class tclass = Class.forName("com.sslike.User");
/* 获取一个指定名称且任意权限的成员属性 */
Method methodUserAge = tclass.getDeclaredMethod("getUserAge");
/* 获取一个指定名称且为public权限的成员属性 */
Method methodUserName = tclass.getMethod("getUserName");

/* 查看methodUserName权限检查是否开启 */
System.out.println(methodUserName.isAccess());
/*======--------结果-------======*/
false

/* 查看methodUserAge权限检查是否开启 */
System.out.println(methodUserAge.isAccess());
/*======--------结果-------======*/
false
```

​		通过上述代码发现，无论是public还是其他权限的成员起始都开启了权限检查，但是getDeclaredXxxx方法之所以能够获取到任意权限的成员是因为此类方法内部默认不区分是否是public权限，会获去所有成员方法；

​		如果关闭成员的权限检查，那么同样能够访问除过public权限的其他权限的成员；

#### 2.关闭权限检查：

```java
/*
 * 如果参数为true则关闭权限检查，如果参数为false则开启权限检查
 */
setAccess(true)
```

### 5.通过反射获取类中注解：

#### 1.获取指定注解：

```java
Annotation annotation = (Annotation) tclass.getAnnotation(Deprecated.class);
```

#### 2.获取类所有的注解：

```java
Annotation[] annotations = (Annotation[]) tclass.getAnnotations();
```

### 6.通过反射获取类的其他信息：

#### 1.获取该类名称：

```java
/* 获取类的全限定类名(包名.类名)*/
tclass.getName()
/* 获取类的名称，仅类名，无包名 */
tclass.getSimpleName()
/* 获取类的包名，仅包名，无类名*/
  tclass.getPackage()
```

#### 2.类的类型检测:

```java
/* 检测类是否是集合类*/
boolean isArray = tclass.isArray() 
/* 检测类是否是注解类*/
boolean isAnnotation = tclass.isAnnotation()
/* 检测类是否是接口类*/
boolean isInterface = tclass.isInterface()  
/* 检测类是否是枚举类*/
boolean isEnum = tclass.isEnum()
/* 检测类是否是匿名内部类*/
boolean isAnonymousClass = tclass.isAnonymousClass()
/* 检测类是否是被注解修饰的类*/
boolean isAnnotationPresent = tclass.isAnnotationPresent(Deprecated.class)
```

#### 3.获取类的其他信息：

```java
/* 获取类的访问权限 */
int modify =class1.getModifiers()
/* 获取类的内部类 */
Class<?>[] declaredClasses = class1.getDeclaredClasses() 
/* 获取类的外部类 */
Class<?> declaringClass = class1.getDeclaringClass()
/* 获取类的加载器 */
ClassLoader ClassLoader = class1.getClassLoader()
/* 获取类的所有父类 */
getSuperclass()  
/* 获取类所有实现了的接口 */
getInterfaces()
```

## 4.反射的原理探究：

### 1.为何能够实现反射？

​		在类的加载流程中已经分析过，每一个类在加载到内存中后都会生成一个Class类型的对象，在该对象的内部则存储着对应类完整详细的信息，而类的信息则并不直接存放在Class类内部，而是通过Class类的一个内部类RefelctionData类中，`RelfectionData`是具体存储一个被加载的类的信息的内部类，其内部定义了Field、Method、Constructor等数组用来存放类的属性、方法、构造方法信息；

```java
/* ReflectionData 实现 */
```

​		正因为有了Class对象对类信息的缓存，才能够在程序运行时通过类的Class对象来获取Class对象中缓存的类元信息；获取一个类的Class类型对像有以下三种方式：

```java
/*通过类的全限定名称获取类的Class对象*/
Class tclass = Class.forName("类的全限定名称(包名.类名)");

/*通过类的实例对象获取类的Class对象*/
Class tclass = object.getClass();

/*通过类的class属性获取类的Class对象*/
Class tclass = UserClass.class;
```

​		只要获取到了类的Class对象，就能够通过调用Class类内提供的api获取类的信息，实际上获取类的信息则是获取内部类`RelfectionData`中缓存的类信息；

### 2.类成员信息的获取流程：

> 以下部分以getDeclaredMethod方法来获取一个类成员方法的过程分析类成员信息的获取流程

```java
/* 通过getDeclaredMethod方法获取User类的成员方法 */
```

​		可以看到`getDeclaredMethod`方法返回的method信息是通过`searchMethods`从`privateGetDeclaredMethods`中获取到的；`searchMethods`方法如下：

```java
/* searchMethods */
```

​		`searchMethods`方法内部仅仅只是对`privateGetDeclaredMethods`返回的众多method进行筛选，获取名称和参数匹配的方法并返回；具体的method信息则是从`privateGetDeclaredMethods`获得，`privateGetDeclaredMethods`方法如下：

```java
/* privateGetDeclaredMethods */
```

​		`privateGetDeclaredMethods`方法接收一个叫做publicOnly的参数，这个参数在通过`getDeclaredMethod`方法获取成员方法时是false，这也就是为什么public和其他权限成员都开启权限检查之后，`getDeclaredMethods`方法仍旧能够获取到非public权限成员信息的原因；同时该方法还接收要查找的方法名称和参数；

​		因为类的成员信息均存储在`RelfectionData`内部类中，因此在该方法内部会先获取内部类`RelfectionData`的引用，`RelfectionData`的引用由`reflectionData`方法创建并返回：

```java
/*reflectionData*/
```

​		在Class内部有一个属性`reflectionData`是内部类`RelfectionData`的引用，并且是**软引用**；是因为类的元信息并不仅仅只存在于Class对象中，在类加载流程完毕之后，类的所有信息都会以二进制的形式存在于JVM内存中，而将类的元信息放在Class对象中仅仅只是一层缓存，能够在通过反射获取类信息时效率更高而已，因此将`RelfectionData`的引用设置为软引用，在内存吃紧的时候就能够释放掉这部分占用的内存空间；等到下次需要通过反射获取类信息的时候再重新去JVM中获取然后缓存在`RelfectionData`即可；

```java
/* reflectionData引用 */
```

​		在`reflectionData`方法中会先通过Class属性`reflectionData`拿取`RelfectionData`的引用，如果该引用为null，那么会创建内部类`RelfectionData`的引用，并将其值设置给属性`reflectionData`；在创建`RelfectionData`内部类引用时还会通过`classRedefinedCount`进行计数；

```java
/*关于classRedefinedCount 计数*/
```

​		获取到`RelfectionData`引用之后就会返回该内部类中缓存的方法数组，如果数组中没有类的信息时将会从JVM中获取并缓存在`RelfectionData`之后返回；

```java
/*privateGetDeclaredMethods方法*/
```

### 3.方法的执行:

​		通过反射获取到的方法是Method类型的实例对象，可以通过Method的invoke方法来执行该方法，invoke的代码如下：

```java
/* Method invoke方法*/
```

​		在该方法中会先检查方法的权限，然后获取`MethodAccessor`类型的对象，然后调用`MethodAccessor`对象的invoke方法，因此本质上通过`Method`类型的invoke方法来执行类成员方法时通过`MethodAccessor`的invoke方法来执行的；`MethodAccessor`是一个接口，具体的实现类是`NativeMethodAccessorImpl`

