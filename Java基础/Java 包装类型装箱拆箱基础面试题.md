## Java 包装类型装箱拆箱基础面试题

##### 1、如下程序运行结果是什么？

```java
Long l1 = 128L;
Long l2 = 128L;
System.out.println(l1==l2);		//1
System.out.println(l1==128L);	//2
Long l3 = 127L;
Long l4 = 127L;
System.out.println(l3==l4);		//3
System.out.println(l3==127L);	//4
```

**答：**对于注释 1 的语句，Long 包装类型常量 cache 为 -128 到 127 之间，所以 l1 和 l2 变量是两个对象，== 比较的是对象的地址，所以打印为 false。

对于注释 2 的语句，由于包装类型在表达式中且表达式中至少有一个不是包装类型，所以 Long l1 == 128L 中 l1 自动拆箱退化为基本类型比较，所以数值比较为 true。

对于注释 3 的语句，Long 包装类型 -128 到 127 之间的值维护在一个常量池中，所以 l3 和 l4 引用同一个对象，故打印 true。

对于注释 4 的语句类似注释 2 语句，所以打印为 true。



##### 2、java 是否存在使得语句 i > j || i <= j 结果为 false 的 i、j 值？

**答：**存在，java 的数值 `NaN` 代表 not a number，无法用于比较，例如使 `i =  Double.NaN; j = i;` 最后 `i == j` 的结果依旧为 false，这是一道非常变态的题，巨坑，谁特么会这么用。



##### 3、java 1.5 的自动装箱拆箱机制是编译特性还是虚拟机运行时特性？分别是怎么实现的？

**答：**java 1.5 开始的自动装箱拆箱机制其实是编译时自动完成替换的，装箱阶段自动替换为了 valueOf 方法，拆箱阶段自动替换为了 xxxValue 方法。对于 Integer 类型的 valueOf 方法参数如果是 -128~127 之间的值会直接返回内部缓存池中已经存在对象的引用，参数是其他范围值则返回新建对象；而 Double 类型与 Integer 类型类似，一样会调用 Double 的 valueOf 方法，但是 Double 的区别在于不管传入的参数值是多少都会 new 一个对象来表达该数值（因为在指定范围内浮点型数据个数是不确定的，整型等个数是确定的，所以可以 Cache）。

注意：Integer、Short、Byte、Character、Long 的 valueOf 方法实现类似，而 Double 和 Float 比较特殊，每次返回新包装对象，对于两边都是包装类型的比较 == 比较的是引用，equals 比较的是值，对于两边有一边是表达式（包含算数运算）则 == 比较的是数值（自动触发拆箱过程），对于包装类型 equals 方法不会进行类型转换。

##### 4、下面是一组 java 包装类型、自动拆箱、装箱的题目，请写出运行结果？

```java
Integer i1 = 100;
Integer i2 = 100;
Integer i3 = 200;
Integer i4 = 200;
System.out.println( i1 == i2 );  //true
System.out.println( i3 == i4 );  //false
Double d1 = 100.0;
Double d2 = 100.0;
Double d3 = 200.0;
Double d4 = 200.0;
System.out.println( d1 == d2 );  //false
System.out.println( d3 == d4 );  //false
Boolean b1 = false;  Boolean b2 =  false;
Boolean b3 = true;  Boolean b4 =  true;
System.out.println( b1 == b2 );  //true
System.out.println( b3 == b4 );  //true
Integer a = 1;
Integer b = 2;
Integer c = 3;
Integer d = 3;
Integer e = 321;
Integer f = 321;
Long g =  3L;
Long h =  2L;
System.out.println( c == d );  //true
System.out.println( e == f );  //false
System.out.println( c == ( a + b ));  //true
System.out.println( c.equals ( a + b ));  //true
System.out.println( g == ( a + b ));  //true
System.out.println( g.equals( a + b ));  //false
System.out.println( g.equals( a + h ));  //true
Integer a = 444;  
int b = 444;
System.out.println( a == b );  //true
System.out.println( a.equals( b ));  //true
```

**答：**答案如上注释部分，核心考察点就是上道题的答案，即 java 1.5 开始的自动装箱拆箱机制其实是编译器自动完成的替换，装箱阶段自动替换为了 valueOf 方法，拆箱阶段自动替换为了 xxxValue 方法。对于 Integer 类型的 valueOf 方法参数如果是 -128~127 之间的值会直接返回内部缓存池中已经存在对象的引用，参数是其他范围值则返回新建对象；而 Double 类型与 Integer 类型一样会调用到 Double 的 valueOf 方法，但是 Double 的区别在于不管传入的参数值是多少都会 new 一个对象来表达该数值（因为在指定范围内浮点型数据个数是不确定的，整型等个数是确定的，所以可以 Cache）。 注意：Integer、Short、Byte、Character、Long 的 valueOf 方法实现类似，而 Double 和 Float 比较特殊，每次返回新包装对象。 对于两边都是包装类型的比较 == 比较的是引用，equals 比较的是值，对于两边有一边是表达式（包含算数运算）则 == 比较的是数值（自动触发拆箱过程），对于包装类型 equals 方法不会进行类型转换。 



##### 5、java 语句 Integer i = 1; i += 1; 做了哪些事情？

**答：**首先 Integer i = 1; 做了自动装箱（使用 valueOf() 方法将 int 装箱为 Integer 类型），接着 i += 1; 先将 Integer 类型的 i 自动拆箱成 int（使用 intValue() 方法将 Integer 拆箱为 int），完成加法运行之后的 i 再装箱成 Integer 类型。



