# Java面向对象-基础语法

## 1.面向对象简介

​	**面向对象的思想 **是指一种将每一个事物都看成一个独立的个体，而每一个独立的个体就是我们所面对、研究和使用的对象，每一个对象都会有自己的静态属性和动态行为；同时，还可以将对象按照共同的特征进行分类，称该对象为某种类型的对象；

​	**面向对象编程 **是指按照面向对象的思想来组织和实践代码，将有共同特征的实例对象抽象为类，对象的共同特征由类来提供和保证，类就是对象的模板，对象就是类的实体化；对于代码的实践则是通过创建并组合实例对象的方式实现；

## 2.类(class)

### 	1.类的特征

​			1.**从面向对象的概念来看**，类是实例对象的模板，抽象了实例对象共有的特征，通过同一个类可以创建出的独立且具有相同特征的实例对象；

​			2.**从数据处理的角度看**，我们所处理和面对的一切都是数据，实例对象本身也是一种数据，而创建出实例对象的类就是我们自定义的一种数据类型，因此可以通过类来声明变量，所声明的变量将可以持有该类实例对象的引用，而该变量的数据类型也称为引用类型；

### 2.类的声明方式

​		1.类的声明语法

```java
权限 修饰符 class ClassName{
  /*
  * 类成员：
  *	1.构造方法
  *	2.成员属性
  *	3.成员方法
  *	4.初始化块
  *	5.内部类
  */
}
```

> **说明：**
>
> **1.权限：**
>
> ​	1.类的权限只有 ***public*** 和 ***默认权限(无权限修饰符)***  两种；public的权限意味着该类可被该类所属的包以及包以外的类访问，而默认权限意味着该类只能被其所属的包中的类访问；
>
> ​	2.一个java源文件中只能有一个public权限的类，此时java源文件的名称也要和public权限的类名相同；
>
> **2.修饰符：**
>
> ​	1.通常情况下，无需任何修饰符；但在以下情况下需要修饰符：
>
> ​		1.如果该类中有抽象方法时，该类也成为抽象类，必须使用 **abstract** 修饰；
>
> ​		2.如果该类不希望被子类继承，需要使用 **final** 修饰；
>
> **3.class关键字：** class关键字表明该代码块是一个类，在声明类时不能生略；
>
> **4.ClassName：**类名，必须符合Java标识符命名规范，推荐采用首字母大写的方式命名；
>
> **5.类成员：**类成员必须使用 **{}** 包裹，类成员包括:*构造方法、成员属性、成员方法、初始化块、内部类*；



### 3.类成员

####     1.构造方法

​			**1.构造方法的功能：**构造方法用来在创建对象时对实例对象进行初始化；

> ​		特别注意：1.构造方法仅仅只是用来初始化实例对象的，并不是创建实例对象；
>

​			**2.构造方法的声明方式:**

```java
	   构造方法名(形参列表){
           /*构造方法实体*/
       }				
```
> 说明：
>
> ​	 1.类中必须要有构造方法，如果没有显式的声明构造方法时，系统将会按照继承体系逐级查找该类所有父类中的构造方法来对该类的实例对象进行初始化；如果该类没有继承任何类，系统将会为该类提供一个默认无参的构造函数；
>
> ​	2.权限表示外部类在创建该类实例对象时，能否有足够的权限对创建的对象进行初始化，如果构造方法的权限为private，那么对象将无法初始化，对象也将创建失败；          
>
> ​	3.构造方法名

1：功能：用来初始化实例对象（注意是初始化实例对象，而非创建实例对象）；
                    一个类是否能够创建实例对象，取决于是否有构造方法以及构造方法的权限：
                        1：如果没有显式的创建一个构造方法，系统将会调用该类父类的构造方法或者创建一个默认的构造方法；
                        2：如果有构造方法，但是构造方法权限不足同样无法实例化对象；
            2：定义：权限 构造方法名(形参列表){
                        /*构造方法实体*/
                    }
            3：权限：public(公共权限)、protected(受保护权限)、private(私有权限)；构造方法必须是public权限，如果是protected或者private权限，该类将不能创建实例对象；
            4：构造方法名：构造方法名称并不固定，要和当前类名保持一致；
            5：形参：表示初始化该实例对象需要的参数，每个参数由参数类型和参数名称构成(参数类型 参数名称），多个参数之间使用 , 隔开；
            6：返回值：
                1：构造方法不能定义返回值类型也不能使用void定义没有返回值；
                2：构造方法体本身也不能使用return返回一个值；
                3：如果为该方法定义了void或者具体的返回值类型，那么该方法将会被当做普通方法使用；
                4：虽然构造方法不能使用void来声明没有返回值，也不能使用return返回一个值，但是构造方法实际是有返回值的，返回值就是该类的实例对象，返回方式是隐式的，不允许使用return语句返回当前实例对象本身；
            7：构造方法的重载：
                1：java中允许为一个类设置多个不同形参的构造器；使用该类创建对象时可以按照多种不同的方式创建，称之为构造方法的重载；
                2：在构造方法中可以通过this关键字调用另一个构造方法，来实现构造方法的代码重用；

​	



## 3.实例对象



## 4.面向对象的三大特征

