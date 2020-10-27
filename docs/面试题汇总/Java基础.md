# java有几种基本类型，分别是什么？String是基本类型吗？

## java有8种基本数据类型 

| 数据类型 | 长度 |
| -------- | ---- |
| boolean  | 1    |
| short    | 2    |
| byte     | 1    |
| char     | 2    |
| int      | 4    |
| float    | 4    |
| double   | 8    |
| long     | 8    |

## String是基本类型吗

String是一个类，不是基本数据类型

#  Final、 static 、this、super关键字

## Final

用于修饰变量、类，方法

1. 修饰如果是基本数据类型	变量一旦初始化后便不能修改。如果是引用类型的变量，初始化后不能再指向别的对象。
2. 修饰类的时候，该类不能被继承，final类中的所有成员方法都会被隐式地指定为final方法。

## static 

使用场景

1. 修饰成员变量和成员方法：被 static 修饰的成员属于类，不属于单个这个类的某个对象，被类中所有对象共享，可以并且建议通过类名调用。被static 声明的成员变量属于静态成员变量，静态变量 存放在 Java 内存区域的方法区。调用格式：**类名.静态变量名**   **类名.静态方法名()**
2. 修饰静态代码块：静态代码块定义在类中方法外, 静态代码块在非静态代码块之前执行(静态代码块—>非静态代码块—>构造方法)。 该类不管创建多少对象，静态代码块只执行一次.
3. 静态内部类：静态内部类与非静态内部类之间存在一个最大的区别: 非静态内部类在编译完成之后会隐含地保存着一个引用，该引用是指向创建它的外围类，但是静态内部类却没有。没有这个引用就意味着它的创建是不需要依赖外围类的创建和它不能使用任何外围类的非static成员变量和方法。
4. 静态导包：格式为：import static这两个关键字连用可以指定导入某个类中的指定静态资源，并且不需要使用类名调用类中静态成员，可以直接使用类中静态成员变量和成员方法。



## this

代表本类对象的引用，this 调用本类中的其他构造方法时，要放在首行，否则编译器会报错。

## super

用于访问父类变量和方法，在构造器中使用 super（）调用父类中的其他构造方法时，该语句必须处于构造器的首行，否则编译器会报错。

# sc.next() 和 sc.nextLine 的区别

* next()从遇到第一个有效字符（非空格、换行符）开始扫描，遇到第一个分隔符或结束符（空格’ ‘或者换行符 ‘\n’）时结束。
*  nextLine()则是扫描剩下的所有字符串知道遇到回车为止。

例子

```java 
Scanner sc = new Scanner(System.in);
//加入输入的是:aaa bbb ccc
str1=sc.next();
//str1="aaa"
str2=sc.nextline();
//str2=" bbb ccc"

```

#  final，finalize， finally有什么区别

## final

用于修饰变量、类，方法

1. 修饰如果是基本数据类型	变量一旦初始化后便不能修改。如果是引用类型的变量，初始化后不能再指向别的对象。
2. 修饰类的时候，该类不能被继承，final类中的所有成员方法都会被隐式地指定为final方法。

## finalize

finalize()是在java.lang.Object里定义的，当垃圾收集确定不再有对该对象的引用时，垃圾收集器在对象上调用该对象,完成对该对象回收。 该方法已被废弃。



## finally

finally作为异常处理的一部分，它只能用在try/catch语句中，如果程序进入到try/catch语句中，则大部分情况下finally语句块都会执行。(若在try/catch语句前发生异常，或者在try/catch语句中使用System.exit(0)则不会执行finally语句)。

#  String、Stringbuffer和StringBuilder的区别

三者都是final类，不可以继承。

## String

是一个字符串常量，长度不可变。每次对String修改都会生成新的String对象，然后指针指向新的对象。若要频繁改变长度，会导致生成多个对象，JVM的会频繁垃圾回收，影响系统性能。

## StringBuilder

线程不安全，长度可变。单线程操作大量数据，使用StringBuilder。

## StringBuffer

线程安全 ，长度可变，每次都会对StringBuffer对象本身进行操作，而不是生成新的对象并改变对象引用。多线程操作大量数据使用StringBuffer。

#  Equals方法重写时为什么要重写hashcode方法

## Object对象自带的方法

### HashCode

```java
public native int hashCode();
```

直接给对象返回一个哈希值。

### equals

```java
public boolean equals(Object obj) {
        return (this == obj);
    }
```

该方法比较的是两个对象地址是否相同。

### HashCode和equals的关系

1. 两个对象相等，HashCode一定相等。
2. 两个对象不相等，HashCode不一定不相等。
3. 两个对象HashCode相等，两个对象不一定相等。
4. 两个对象HashCode不相等，两个对象一定不相等。

### Equals方法重写时为什么要重写hashcode

hashcode返回对象的内存地址经过处理后的值，由于每个对象的内存地址都不一样，所以哈希码也不一样

```java
public class Test {
    public static void main(String[] args) {
        Student a = new Student("jc",1);
        Student b = new Student("jc",1);
        System.out.println(a.equals(b));
      	System.out.println(a.hashCode());
        System.out.println(b.hashCode());
    }
    static class Student {
        String name;
        int id;

        public Student(String name, int id) {
            this.name = name;
            this.id = id;
        }
    }
}

/** 输出
false
116211441
607635164
/
```

上述代码，若没有重写hashcode和equals的话，由于a和b对象地址不一样，hash值也不一样，所以输出false。但是实际上a和b对象里面的内容是一样的，因此重写equals方法。

```java
public class Test {
    public static void main(String[] args) {
        Student a = new Student("jc",1);
        Student b = new Student("jc",1);
        System.out.println(a.equals(b));
        System.out.println(a.hashCode());
        System.out.println(b.hashCode());
    }
    static class Student {
        String name;
        int id;

        public Student(String name, int id) {
            this.name = name;
            this.id = id;
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            Student student = (Student) o;
            return id == student.id &&
                    Objects.equals(name, student.name);
        }
    }
}
/** 输出
true
1360875712
1625635731
/
```

重写equals后，输出为true，但是a和b的hash值仍然是不相等的，这样就照成一个很大的问题。

例如当你使用HashSet和HashMap的时候，HashSet和HashMap是根据hash值来求出对象所在的位置的(**hash&(n-1)**)，若两个hash值不相同的对象都放入了HashSet和HashMap，HashSet有重复元素，HashMap有两个相同元素，但是后一个元素没有覆盖前一个。HashSet和HashMap都认为a和b不是同一个元素，但实际上a.equals(b)为true，它们是同一个元素。

```java
public class Test {
    public static void main(String[] args) {
        Student a = new Student("jc",1);
        Student b = new Student("jc",1);
        System.out.println(a.equals(b));
        System.out.println(a.hashCode());
        System.out.println(b.hashCode());
    }
    static class Student {
        String name;
        int id;

        public Student(String name, int id) {
            this.name = name;
            this.id = id;
        }
        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            Student student = (Student) o;
            return id == student.id &&
                    Objects.equals(name, student.name);
        }
        @Override
        public int hashCode() {
            int result = 1;
            result = result * 31 + (name == null ? 0 : name.hashCode());
            result = result * 31 + id;
            return result;
        }
    }
}
/**
true
105897
105897
/
```

hashCode()方法是根据每个变量的值来计算对象的hash值，重写后可以发现，两个对象相等，则他们的hash值也是相等的。

#  ==和equals的区别 

1. ==是操作符，equals是方法

2. 当时基本数据类型的时候只能使用==，比较的是它们的值

3. 对于引用类型的变量来说，如String只能使用equals，因为 String 继承了 Object 类， equals 是 Object 类的通用方法。对于该类型对象的比较，默认情况下，也就是没有复写 Object 类的 equals 方法，使用 == 和 equals 比较是一样效果的，都是比较的是它们在内存中的存放地址。

   * 当equals没有被重写，默认使用就是 Object 类的方法

   ```java
   /*
   从源码可以看出，里面使用的就是 == 比较，所以这种情况下比较的就是它们在内存中的存放地址。
   */
   public boolean equals(Object obj) {
         return (this == obj);
     }
   ```

   * 当equals被重写，如String类里面的equeal方法

   ```java
   /*
   从源码可以看出， String 类复写了 equals 方法，当使用 == 比较内存的存放地址,当不相等时接下来会比较字符串的内容是否相等，所以String类中的equals方法会比较两者的字符串内容是否一样。
   */
   public boolean equals(Object anObject) {
           if (this == anObject) {
               return true;
           }
           if (anObject instanceof String) {
               String anotherString = (String)anObject;
               int n = value.length;
               if (n == anotherString.value.length) {
                   char v1[] = value;
                   char v2[] = anotherString.value;
                   int i = 0;
                   while (n-- != 0) {
                       if (v1[i] != v2[i])
                           return false;
                       i++;
                   }
                   return true;
               }
           }
           return false;
       }
   ```

   

#  接口和抽象类的区别 

## 抽象类

抽象方法是一种特殊的方法:它只有声明，而没有具体的实现。抽象方法的声明格式为

```java
abstract void sayHolle()
```

### 特点

1. 抽象方法必须用abstract关键字进行修饰，如果一个类含有抽象方法，则称这个类为抽象类。
2. 抽象类必须在类前用abstract关键字修饰。
3. 因为抽象类中含有无具体实现的方法，所以不能用抽象类创建对象。
4. 抽象类可以拥有成员变量和普通的成员方法。

### 与普通类区别

1. 抽象方法必须为public或者protected（因为如果为private，则不能被子类继承，子类便无法实现该方法），缺省情况下默认为public。
2. 不能用来创建对象
3. 如果一个类继承于一个抽象类，则子类必须实现父类的抽象方法。如果子类没有实现父类的抽象方法，则必须将子类也定义为为abstract类。



## 接口

```java
public interface SayHello{}
```

1. 接口中可以含有 变量和方法。
2. 接口中的变量会被隐式地指定为public static final变量，并且只能是public static final变量，用private修饰会报编译错误。
3. 方法可以用public abstract或者static表示，若没有修饰符，则方法会被隐式地指定为public abstract，用其他关键字，比如private、protected、 final等修饰会报编译错误，并且接口中所有的方法不能有具体的实现(除了static修饰的方法)。



## 两者区别

1. 抽象类可以没有抽象方法，也可以抽象方法和非抽象方法并存。而在JKD8前，接口中的方法只能是抽象的，JKD8版本后开始提供了接口中的方法的default实现。
2. 抽象类中的成员变量可以是各种类型的；而接口中的成员变量只能是public static final类型的，必须被初始化，接口只有常量，没有变量。
3. 接口中不能含有静态代码块,但可以含有静态方法，而抽象类可以有静态代码块和静态方法。
4. 抽象类和类一样是单继承，接口可以实现多个父接口。

#  JDK和JRE的区别

* JRE 是 Java 的运行环境。面向 Java 程序的使用者，而不是开发者。如果你仅下载并安装了 JRE，那么你的系统只能运行 Java 程序。JRE 是运行 Java 程序所必须环境的集合，包含 JVM 标准实现及 Java 核心类库。它包括 Java 虚拟机、Java 平台核心类和支持文件
* JDK是 Java 开发工具包，它提供了 Java 的开发环境(提供了编译器 javac 等工具，用于将 java 文件编译为 class 文件)和运行环境(提 供了 JVM 和 Runtime 辅助包，用于解析 class 文件使其得到运行)。如果你下载并安装了 JDK，那么你不仅可以开发 Java 程序，也同时拥有了运行 Java 程序的平台。JDK 是整个 Java 的核心，包括了 Java 运行环境JRE，一堆 Java 工具 tools.jar 和 Java 标准类库 。

#  深拷贝、浅拷贝 

## 浅拷贝

浅拷贝是创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值，如果属性是引用类型，拷贝的就是内存地址 ，所以如果其中一个对象改变了这个地址，就会影响到另一个对象。

## 深拷贝

深拷贝是将一个对象从内存中完整的拷贝一份出来,从堆内存中开辟一个新的区域存放新对象,且修改新对象不会影响原对象。

#  Java中为何要有泛型

泛型的本质是参数化类型，即所操作的数据类型被指定为一个参数。这种类型参数可以用在类、接口和方法的创建中，分别称为泛型类、泛型接口、泛型方法，目的是为了可以在编译器防止将错误类型的对象放置到容器中

## 没使用泛型

```java
public class Gerbil {
    Object gerbilNumber;
    public Gerbil(Object gerbilNumber) {
        this.gerbilNumber = gerbilNumber;
    }

    public Object getGerbilNumber() {
        return gerbilNumber;
    }

    public void setGerbilNumber(Object gerbilNumber) {
        this.gerbilNumber = gerbilNumber;
    }

    public void showType() {
        System.out.println("实际类型为" + gerbilNumber.getClass().getName());
    }
}
public class Test {
    public static void main(String[] args) {
        Gerbil gerbil1 = new Gerbil(new Integer(1));
        gerbil1.showType();
        int i = (int) gerbil1.getGerbilNumber();
        System.out.println("value=" + i);

        Gerbil gerbil2 = new Gerbil("1");
        gerbil2.showType();
        String j = (String) gerbil2.getGerbilNumber();
        System.out.println("value=" + j);
       
    }
}

/**
运行结果：
实际类型为java.lang.Integer
value=1
实际类型为java.lang.String
value=1
**/
```

在没有泛型的情况下，通过对类型Object的引用来实现参数的转换，这种转换是需要显式地强制类型转换，给开发带来了麻烦。

## 使用泛型

```java
public class Gerbil<T> {
    T gerbilNumber;
    public Gerbil(T gerbilNumber) {
        this.gerbilNumber = gerbilNumber;
    }

    public T getGerbilNumber() {
        return gerbilNumber;
    }

    public void setGerbilNumber(T gerbilNumber) {
        this.gerbilNumber = gerbilNumber;
    }

    public void showType() {
        System.out.println("实际类型为" + gerbilNumber.getClass().getName());
    }
}

public class Test {
    public static void main(String[] args) {
        Gerbil<Integer> gerbil1 = new Gerbil(1);
        gerbil1.showType();
        int i = gerbil1.getGerbilNumber();
        System.out.println("value=" + i);

        Gerbil<String> gerbil2 = new Gerbil("1");
        gerbil2.showType();
        String j =gerbil2.getGerbilNumber();
        System.out.println("value=" + j);
    }
}

/**
实际类型为java.lang.Integer
value=1
实际类型为java.lang.String
value=1
**/
```

使用泛型可以消除强制类型转换。

## 泛型擦除

```java
public static void main(String[] args) {
        Class c1 = new ArrayList<String>().getClass();
        Class c2 = new ArrayList<Integer>().getClass();
        System.out.println(c1 == c2);//true
    }
```

程序内部会把ArrayList<String>和ArrayList<Integer> 擦除成原生的ArrayList<T>,所以输出true。



## 泛型的好处

1. 类型安全：泛型的主要目标是提高 Java 程序的类型安全。通过知道使用泛型定义的变量的类型限制，编译器可以在一个高得多的程度上验证类型假设。
2. 消除强制类型转换： 泛型的一个附带好处是，消除源代码中的许多强制类型转换。这使得代码更加可读，并且减少了出错机会。
3. 潜在的性能收益：泛型为较大的优化带来可能。在泛型的初始实现中，编译器将强制类型转换（没有泛型的话，要指定这些强制类型转换）插入生成的字节码中。但是更多类型信息可用于编译器这一事实，为未来版本的 JVM 的优化带来可能。由于泛型的实现方式，支持泛型（几乎）不需要 JVM 或类文件更改。所有工作都在编译器中完成，编译器生成类似于没有泛型和强制类型转换时所写的代码，更能确保类型安全。

#  方法重写与重载的区别？

重写：子类继承了父类原有的方法，但有时子类并不想原封不动的继承父类中的某个方法，所以在方法名，参数列表，返回类型(除过子类中方法的返回值是父类中方法返回值的子类时)都相同的情况下， 对方法体进行修改或重写覆盖，这就是重写。但要注意子类函数的访问修饰权限不能少于父类的

重载

在一个类中，同名的方法如果有不同的参数列表（**参数类型不同、参数个数不同甚至是参数顺序不同**）则视为重载。同时，重载对返回类型没有要求，可以相同也可以不同，但**不能通过返回类型是否相同来判断重载**

##### 

#  Java类初始化顺序

```java
public class Parent {
    public static String prent_value = "父类静态变量";
    public String value = "父类普通变量";
    static {
        System.out.println(prent_value);
        System.out.println("父类静态块");
    }

    public Parent() {
        System.out.println(value);
        System.out.println("父类构造方法");
    }

}

```

```java
public class Children extends Parent {
    public static String children_value = "子类静态变量";
    public String value = "子类普通变量";
    static {
        System.out.println(children_value);
        System.out.println("子类静态块");
    }

    public Children() {
        System.out.println(value);
        System.out.println("子类构造方法");
    }

    public static void main(String[] args) {
        new Children();
    }
}
/**
父类静态变量
父类静态块
子类静态变量
子类静态块
父类普通变量
父类构造方法
子类普通变量
子类构造方法
**/
```

由输出结果可以得知，初始化顺序为， 父类静态变量->父类静态块->子类静态变量->子类静态块->父类普通变量->父类构造方法->子类普通变量->子类构造方法



#  java的三大特性 面向对象的特性（封装、继承、多态） 的含义

## 封装

把事物封装成一个类，**减少耦合，隐藏细节**，保持特定的接口与外界联系，当接口内部发生变化，不会影响外部调用方。

## 继承

从一个已知的类派生出一个新的类，新类可以拥有已知的类所拥有的属性和行为，并且可以通过覆盖/重写来增强已知类的能力。

## 多态 

本质就是一个程序存在多个同名的不同方法，主要有三种方式实现

1. 通过子类对父类的覆盖。
2. 通过在一个类中对方法进行重载。
3. 通过将子类对象作为父类对象使用来实现

# 多态的实现原理

## 覆盖

覆盖也叫重写，是子类与父类之间的一种关系。

```java
public class Test {
    class Parent {
        public void say() {
            System.out.println("我是Parent");
        }
    }

    class Son extends Parent {
        @Override
        public void say() {
            System.out.println("覆盖了父类方法，我是Son");
        }
    }

    public void test() {
        new Son().say();
    }
    public static void main(String[] args) {
        new Test().test();
    }
}

/**
覆盖了父类方法，我是Son
**/
```

## 重载

是指一个类中(包括父类)，存在多个同名的不同方法，这些方法的参数个数，顺序以及类型不同都可以构成方法的重载，如果两个方法仅仅是修饰符，返回值，抛出异常不同，那么这两个是相同的方法。

```java
public class Test {
    public void say(int number) {
        System.out.println("序号 "+number);
    }
    //参数个数相同，但是类型不同
    public void say(String name) {
        System.out.println(" 姓名 "+name);
    }
    public void say(int number,String name) {
        System.out.println("序号 "+number+"  姓名 "+name);
    }

    //参数顺序不同
    public void say(String name, int number) {
        System.out.println("序号 " + number + "  姓名 " + name);
    }

    //参数个数不同
    public void say(int number, String name, int sex) {
        String temp;
        if (sex == 1) {
            temp = "男";
        } else {
            temp = "女";
        }
        System.out.println("序号 "+number+"  姓名 "+name+" 性别 "+temp);
    }
    public static void main(String[] args) {
        new Test().say(1);
        new Test().say(1, "Mick");
        new Test().say(1,"Mack",1);
    }
}
```

* 注意，返回类型不同不能作为函数重载的条件。

## 子类对象作为父类对象

将不同子类作为父类去看待，可以屏蔽不同子类之间的差异，写出通用的代码。对象的引用型变量具有多态性，因为一个引用变量可以指向不同形式的对象，即子类对象可以作为父类对象使用。主要是向上转型和向下转型

### 向上转型

子类专为父类，父类可以是接口。

Praent p=new Son();Praent 是父类或者接口，Son是子类

```java
/**常用例子
*/
List<Integer> list = new ArrayList<>();
List<Integer> list1 = new LinkedList<>();
List<Integer> list2 = new ArrayQueue<>(1);
```



### 向下转型

父类对象转为子类

Son s=(Son)p;

向上转型的时候可以直接转，但是必须要强制类型转换，该父类必须实际指向了一个子类对象才可以强制类型向下转型。

* 若Parent p=new Son() 创建父类对象，可以通过强制类型转换实现向下转型。但是若是Parent p=new Parent()这种方式创建的父类对象，不可以通过强制类型转换实现向下转型为Son对象，运行报错，因为其本质还是一个Parent对象。

# Java的访问权限，默认是哪个

|                                  | private | friend | protect | Public |
| -------------------------------- | ------- | ------ | ------- | ------ |
| 同一个类的成员                   | Y       | Y      | Y       | Y      |
| 同一个包中的成员                 |         | Y      | Y       | Y      |
| 不同包中但存在继承关系的子类成员 |         |        | Y       | Y      |
| 全局                             |         |        |         | Y      |

- private：只有同一类内部的方法可见，在有就是内部类也可以访问到。 
- 默认（friendly）：包内可见。 
- protected:不同包的时候继承可见。
- public：所有类可见。

默认是friend

#  Java中类修饰符、成员变量修饰符、方法修饰符的种类与类别

## 类修饰符

1. public：将一个类声明为公共类，他可以被任何对象访问，一个程序的主类必须是公共类
2. abstract：将一个类声明为抽象类，没有实现的方法，需要子类提供方法实现。
3. 将一个类生命为最终（即非继承类），表示他不能被其他类继承。
4. friendly：默认的修饰符，只有在相同包中的对象才能使用这样的类。

## 成员变量修饰符

1. public：指定该变量为公共，可以被任何对象的方法访问。
2. private：指定该变量只能自己的类的方法访问，其他类的方法均不可以访问。
3. protect：指定该变量可以别被同一个包内，自己的类和不同包的子类访问。在子类中可以覆盖此变量。
4. friendly：在同一个包中的类可以访问，其他包中的类不能访问。
5. static：静态修饰符，指定该变量被所有对象共享，即所有实例都可以使用该变量，即变量属于这个类
6. final：最终修饰符，指定该变量的值不能变
7. transient：过度修饰符，指定该变量是系统保留，暂无特别作用的临时性变量。
8. volatile：指定该变量具有可见性和防止指令重排的功能，但不保证原子性

## 方法修饰符的种类

1. public：所有类都可以访问。
2. private：该方法只能被它自己的类访问，其他类不能访问。
3. protected：该方法可以被它的类和子类访问。
4. final：该方法不能重载。
5. static：指定不需要实例化就可以激活的一个方法。
6. synchronize：同步修饰符，防止其他线程的访问，等方法运行结束后才解锁。
7. native：本地修饰符。指定此方法的方法体是用其他语言在程序外部编写的。

#  什么是内存泄漏，Java中存在内存泄漏吗

* Java中存在内存泄漏。内存泄漏就是当长生命周期的对象持有短生命周期对象的引用就很可能发生内存泄露，尽管短生命周期对象已经不再需要，但是因为长生命周期对象持有它的引用而导致不能被回收。

具体发生场景

1. 集合类：如果集合类如果仅仅是局部变量，不会造成内存泄露，因为在方法栈退出后就没有引用了会被*jvm*正常回收。但是如果这个集合类是全局性的变量（比如类中的静态属性，全局性的*map*等即有静态引用或*final*一直指向它），那么没有相应的删除机制，很可能导致集合所占用的内存只增不减，因此提供这样的删除机制或者定期清除策略非常必要。
2. 单例模式：单例对象在被初始化后将在*JVM*的整个生命周期中存在（以静态变量的方式），如果单例对象持有外部对象的引用，当外部对象不再使用的时候，但是这个单例持有它的引用，导致这个外部对象将不能被*jvm*正常回收，导致内存泄露。

#  RuntimeException有哪些，怎么避免空指针异常

## RuntimeException主要有

1. ArithmeticException：数学计算异常。
2.  NullPointerException：空指针异常。
3.  NegativeArraySizeException：负数组长度异常。
4.  ArrayOutOfBoundsException：数组索引越界异常。
5.  ClassNotFoundException：类文件未找到异常。
6.  ClassCastException：类型强制转换异常。
7.  SecurityException：违背安全原则异常。

## 非RuntimeException类型主要有

1. 其他非RuntimeException类型的常见异常主要有以下几种。
2.  NoSuchMethodException：方法未找到异常。
3.  IOException：输入输出异常。
4.  EOFException：文件已结束异常。
5.  FileNotFoundException：文件未找到异常。
6.  NumberFormatException：字符串转换为数字异常。
7.  SQLException：操作数据库异常

## 怎么避免空指针异常

每个变量要初始化，并且每次执行的时候对变量，对象都要做判空处理。

#  反射了解吗，应用场景

反射是Java的特征之一，是一种间接操作目标对象的机制，核心是JVM在运行的时候才动态加载类，并且对于任意一个类，都能够知道这个类的所有属性和方法，调用方法/访问属性，不需要提前在编译期知道运行的对象是谁，他允许运行中的Java程序获取类的信息，并且可以操作类或对象内部属性。程序中对象的类型一般都是在编译期就确定下来的，而当我们的程序在运行时，可能需要动态的加载一些类，这些类因为之前用不到，所以没有加载到jvm，这时，使用Java反射机制可以在运行期动态的创建对象并调用其属性，它是在运行时根据需要才加载。

![反射](/Users/jiangcheng/blog/source/_posts/img/反射.png)

## 反射的优缺点

优点：使用反射，我们就可以在运行时获得类的各种内容，进行反编译，对于Java这种先编译再运行的语言，能够让我们很方便的创建灵活的代码，这些代码可以在运行时装配，无需在组件之间进行源代码的链接，更加容易实现面向对象。



缺点：（1）反射会消耗一定的系统资源，因此，如果不需要动态地创建一个对象，那么就不需要用反射；

​			（2）反射调用方法时可以忽略权限检查，因此可能会破坏封装性而导致安全问题。

## 应用场景

举例：比如说我和程序员A一起完成一个项目，上司规定了我们的工作（框架），于是乎我们各自为战。我们要保证自己的代码能够编译成功再交工给上司，但是，我的程序需要调用A要完成的那部分的方法，如果我不利用反射机制，就必须要等A完工了才能编译测试。显然，这在工作中是不现实的（不仅仅是时间问题）。有了反射之后，我只需要知道接口就OK了，方法没有实现也不会影响我的工作。

#  JDK动态代理原理 

JDK动态代理只提供接口的代理，不支持类的代理。核心InvocationHandler接口和Proxy类，InvocationHandler 通过invoke()方法反射来调用目标类中的代码，动态地将横切逻辑和业务编织在一起；接着，Proxy利用 InvocationHandler动态创建一个符合某一接口的的实例, 生成目标类的代理对象。

# Arrays.sort()的实现

## Arrays.sort()底层源码

```java
static void sort(int[] a, int left, int right,
                 int[] work, int workBase, int workLen) {
    /**INSERTION_SORT_THRESHOLD常量为286，如果a数组长度小于INSERTION_SORT_THRESHOLD就使用快速排序，否则用归并排序。
    **/
    if (right - left < QUICKSORT_THRESHOLD) {
      //进入到sort方法中，
        sort(a, left, right, true);
        return;
    }

    int[] run = new int[MAX_RUN_COUNT + 1];
    int count = 0; run[0] = left;

    // 判断该数组是否接近排序完成
    for (int k = left; k < right; run[count] = k) {
        if (a[k] < a[k + 1]) { // ascending
            while (++k <= right && a[k - 1] <= a[k]);
        } else if (a[k] > a[k + 1]) { // descending
            while (++k <= right && a[k - 1] >= a[k]);
            for (int lo = run[count] - 1, hi = k; ++lo < --hi; ) {
                int t = a[lo]; a[lo] = a[hi]; a[hi] = t;
            }
        } else { // equal
            for (int m = MAX_RUN_LENGTH; ++k <= right && a[k - 1] == a[k]; ) {
                if (--m == 0) {
                    sort(a, left, right, true);
                    return;
                }
            }
        }

        /*若count == MAX_RUN_COUNT，则证明数组并非高度结构化, 则调用前文所述私有sort方法进行
         * 快速排序
         */
        if (++count == MAX_RUN_COUNT) {
            sort(a, left, right, true);
            return;
        }
    }

    
    
    if (run[count] == right++) { 
        run[++count] = right;
    } else if (count == 1) { // 数组已经排序好，直接返回。
        return;
    }

    // 归并排序
    byte odd = 0;
    for (int n = 1; (n <<= 1) < count; odd ^= 1);

    int[] b;                 
    int ao, bo;              
    int blen = right - left;
    if (work == null || workLen < blen || workBase + blen > work.length) {
        work = new int[blen];
        workBase = 0;
    }
    if (odd == 0) {
        System.arraycopy(a, left, work, workBase, blen);
        b = a;
        bo = 0;
        a = work;
        ao = workBase - left;
    } else {
        b = work;
        ao = 0;
        bo = workBase - left;
    }

    // 合并
    for (int last; count > 1; count = last) {
        for (int k = (last = 0) + 2; k <= count; k += 2) {
            int hi = run[k], mi = run[k - 1];
            for (int i = run[k - 2], p = i, q = mi; i < hi; ++i) {
                if (q >= hi || p < mi && a[p + ao] <= a[q + ao]) {
                    b[i + bo] = a[p++ + ao];
                } else {
                    b[i + bo] = a[q++ + ao];
                }
            }
            run[++last] = hi;
        }
        if ((count & 1) != 0) {
            for (int i = right, lo = run[count - 1]; --i >= lo;
                b[i + bo] = a[i + ao]
            );
            run[++last] = right;
        }
        int[] t = a; a = b; b = t;
        int o = ao; ao = bo; bo = o;
    }
}
```

## Arrays.sort()方法里面的sort部分源码

```java
private static void sort(int[] a, int left, int right, boolean leftmost) {
        int length = right - left + 1;
				//INSERTION_SORT_THRESHOLD为47
        //再次判断a数组长度是否大于47，若大于47使用快速排序，小于47使用插入排序
        if (length < INSERTION_SORT_THRESHOLD) {
            if (leftmost) {
                //
                for (int i = left, j = i; i < right; j = ++i) {
                    int ai = a[i + 1];
                    while (ai < a[j]) {
                        a[j + 1] = a[j];
                        if (j-- == left) {
                            break;
                        }
                    }
                    a[j + 1] = ai;
                }
            } else {
                /*
                 * 跳过最长的生序序列
                 */
                do {
                    if (left >= right) {
                        return;
                    }
                } while (a[++left] >= a[left - 1]);

               
				//双基准快排的开始
        // 通过位运算获取数组长度的1/7的近似值
        int seventh = (length >> 3) + (length >> 6) + 1;

        /*
         * 把数组分为三段，有两个基准元素
         */
        int e3 = (left + right) >>> 1; // The midpoint
        int e2 = e3 - seventh;
        int e1 = e2 - seventh;
        int e4 = e3 + seventh;
        int e5 = e4 + seventh;

        // Sort these elements using insertion sort
        if (a[e2] < a[e1]) { int t = a[e2]; a[e2] = a[e1]; a[e1] = t; }

        if (a[e3] < a[e2]) { int t = a[e3]; a[e3] = a[e2]; a[e2] = t;
            if (t < a[e1]) { a[e2] = a[e1]; a[e1] = t; }
        }
        if (a[e4] < a[e3]) { int t = a[e4]; a[e4] = a[e3]; a[e3] = t;
            if (t < a[e2]) { a[e3] = a[e2]; a[e2] = t;
                if (t < a[e1]) { a[e2] = a[e1]; a[e1] = t; }
            }
        }
        if (a[e5] < a[e4]) { int t = a[e5]; a[e5] = a[e4]; a[e4] = t;
            if (t < a[e3]) { a[e4] = a[e3]; a[e3] = t;
                if (t < a[e2]) { a[e3] = a[e2]; a[e2] = t;
                    if (t < a[e1]) { a[e2] = a[e1]; a[e1] = t; }
                }
            }
        }
        // Pointers
        int less  = left;  // The index of the first element of center part
        int great = right; // The index before the first element of right part

        if (a[e1] != a[e2] && a[e2] != a[e3] && a[e3] != a[e4] && a[e4] != a[e5]) {
            
            int pivot1 = a[e2];
            int pivot2 = a[e4];

            a[e2] = a[left];
            a[e4] = a[right];

            /*
             * Skip elements, which are less or greater than pivot values.
             */
            while (a[++less] < pivot1);
            while (a[--great] > pivot2);

            /*
             * Partitioning:
             *
             *   left part           center part                   right part
             * +--------------------------------------------------------------+
             * |  < pivot1  |  pivot1 <= && <= pivot2  |    ?    |  > pivot2  |
             * +--------------------------------------------------------------+
             *               ^                          ^       ^
             *               |                          |       |
             *              less                        k     great
             *
             * Invariants:
             *
             *              all in (left, less)   < pivot1
             *    pivot1 <= all in [less, k)     <= pivot2
             *              all in (great, right) > pivot2
             *
             * Pointer k is the first index of ?-part.
             */
            outer:
            for (int k = less - 1; ++k <= great; ) {
                int ak = a[k];
                if (ak < pivot1) { // Move a[k] to left part
                    a[k] = a[less];
                    a[less] = ak;
                    ++less;
                } else if (ak > pivot2) { // Move a[k] to right part
                    while (a[great] > pivot2) {
                        if (great-- == k) {
                            break outer;
                        }
                    }
                    if (a[great] < pivot1) { // a[great] <= pivot2
                        a[k] = a[less];
                        a[less] = a[great];
                        ++less;
                    } else { // pivot1 <= a[great] <= pivot2
                        a[k] = a[great];
                    }
                   
                    a[great] = ak;
                    --great;
                }
            }

            a[left]  = a[less  - 1]; a[less  - 1] = pivot1;
            a[right] = a[great + 1]; a[great + 1] = pivot2;

            sort(a, left, less - 2, leftmost);
            sort(a, great + 2, right, false);

            /*
             * If center part is too large (comprises > 4/7 of the array),
             * swap internal pivot values to ends.
             */
            if (less < e1 && e5 < great) {
                /*
                 * Skip elements, which are equal to pivot values.
                 */
                while (a[less] == pivot1) {
                    ++less;
                }

                while (a[great] == pivot2) {
                    --great;
                }

                /*
                 * Partitioning:
                 *
                 *   left part         center part                  right part
                 * +----------------------------------------------------------+
                 * | == pivot1 |  pivot1 < && < pivot2  |    ?    | == pivot2 |
                 * +----------------------------------------------------------+
                 *              ^                        ^       ^
                 *              |                        |       |
                 *             less                      k     great
                 *
                 * Invariants:
                 *
                 *              all in (*,  less) == pivot1
                 *     pivot1 < all in [less,  k)  < pivot2
                 *              all in (great, *) == pivot2
                 *
                 * Pointer k is the first index of ?-part.
                 */
                outer:
                for (int k = less - 1; ++k <= great; ) {
                    int ak = a[k];
                    if (ak == pivot1) { // Move a[k] to left part
                        a[k] = a[less];
                        a[less] = ak;
                        ++less;
                    } else if (ak == pivot2) { // Move a[k] to right part
                        while (a[great] == pivot2) {
                            if (great-- == k) {
                                break outer;
                            }
                        }
                        if (a[great] == pivot1) { // a[great] < pivot2
                            a[k] = a[less];
                            /*
                             * Even though a[great] equals to pivot1, the
                             * assignment a[less] = pivot1 may be incorrect,
                             * if a[great] and pivot1 are floating-point zeros
                             * of different signs. Therefore in float and
                             * double sorting methods we have to use more
                             * accurate assignment a[less] = a[great].
                             */
                            a[less] = pivot1;
                            ++less;
                        } else { // pivot1 < a[great] < pivot2
                            a[k] = a[great];
                        }
                        a[great] = ak;
                        --great;
                    }
                }
            }

            // Sort center part recursively
            sort(a, less, great, false);

        } else { // Partitioning with one pivot
            /*
             * Use the third of the five sorted elements as pivot.
             * This value is inexpensive approximation of the median.
             */
            int pivot = a[e3];

            /*
             * Partitioning degenerates to the traditional 3-way
             * (or "Dutch National Flag") schema:
             *
             *   left part    center part              right part
             * +-------------------------------------------------+
             * |  < pivot  |   == pivot   |     ?    |  > pivot  |
             * +-------------------------------------------------+
             *              ^              ^        ^
             *              |              |        |
             *             less            k      great
             *
             * Invariants:
             *
             *   all in (left, less)   < pivot
             *   all in [less, k)     == pivot
             *   all in (great, right) > pivot
             *
             * Pointer k is the first index of ?-part.
             */
            for (int k = less; k <= great; ++k) {
                if (a[k] == pivot) {
                    continue;
                }
                int ak = a[k];
                if (ak < pivot) { // Move a[k] to left part
                    a[k] = a[less];
                    a[less] = ak;
                    ++less;
                } else { // a[k] > pivot - Move a[k] to right part
                    while (a[great] > pivot) {
                        --great;
                    }
                    if (a[great] < pivot) { // a[great] <= pivot
                        a[k] = a[less];
                        a[less] = a[great];
                        ++less;
                    } else { // a[great] == pivot
                        /*
                         * Even though a[great] equals to pivot, the
                         * assignment a[k] = pivot may be incorrect,
                         * if a[great] and pivot are floating-point
                         * zeros of different signs. Therefore in float
                         * and double sorting methods we have to use
                         * more accurate assignment a[k] = a[great].
                         */
                        a[k] = pivot;
                    }
                    a[great] = ak;
                    --great;
                }
            }
            sort(a, left, less - 1, leftmost);
            sort(a, great + 1, right, false);
        }
    }
```



# Java8 有什么新特性 

* 接口可以使用默认方法和静态方法

```java
public interface Animal {
    default void run(){
        System.out.println("run!");
    }
  static void jump(){
    System.out.println("jump!");
  }
}

```



* 函数式接口FunctionInterface与lambda表达式

函数式接口FunctionInterface引入了函数式思想，也就是说函数可以作为另一个函数的参数。函数式接口，接口中有且仅有一个抽象方法，但是可以有若干个默认方法，如Runnable，Callable接口就是典型的函数式接口。可以使用@FunctionalInterface注解，声明一个接口是函数式接口。

```java
@java.lang.FunctionalInterface
public interface FunctionalInterface {
   abstract void handle();
}
```

lambda表达式，是一段没有函数名的函数体，可以作为参数直接传递给相关的调用者。lambda表达式极大的增加了Java语言的表达能力。有以下特点

1. 可选类型声明：不需要声明参数类型，编译器可以统一识别参数值。
2. 可选的参数圆括号：一个参数无需定义圆括号，但多个参数需要定义圆括号。
3. 可选的大括号：如果主体包含了一个语句，就不需要使用大括号。
4. 可选的返回关键字：如果主体只有一个表达式返回值则编译器会自动返回值，大括号需要指定明表达式返回了一个数值。

```java
public class Test {
    public static void main(String args[]){
        Test tester = new Test();

        // 类型声明
        MathOperation addition = (int a, int b) -> a + b;

        // 不用类型声明
        MathOperation subtraction = (a, b) -> a - b;

        // 大括号中的返回语句
        MathOperation multiplication = (int a, int b) -> { return a * b; };

        // 没有大括号及返回语句
        MathOperation division = (int a, int b) -> a / b;

        System.out.println("10 + 5 = " + tester.operate(10, 5, addition));
        System.out.println("10 - 5 = " + tester.operate(10, 5, subtraction));
        System.out.println("10 x 5 = " + tester.operate(10, 5, multiplication));
        System.out.println("10 / 5 = " + tester.operate(10, 5, division));

        // 不用括号
        GreetingService greetService1 = message ->
                System.out.println("Hello " + message);

        // 用括号
        GreetingService greetService2 = (message) ->
                System.out.println("Hello " + message);

        greetService1.sayMessage("Runoob");
        greetService2.sayMessage("Google");
    }

    interface MathOperation {
        int operation(int a, int b);
    }

    interface GreetingService {
        void sayMessage(String message);
    }

    private int operate(int a, int b, MathOperation mathOperation){
        return mathOperation.operation(a, b);
    }
}

```

* 方法引用

方法引用是为了进一步简化lambda表达式，通过类名或者实例名与方法名的组合来直接访问到类或者实例已经存在的方法或者构造方法。方法引用使用**::来定义，::**的前半部分表示类名或者实例名，后半部分表示方法名，如果是构造方法就使用NEW来表示。主要有以下几种形式

1. 静态方法引用：ClassName::methodName;
2. 实例上的实例方法引用：instanceName::methodName;
3. 超类上的实例方法引用：supper::methodName;
4. 类的实例方法引用：ClassName:methodName;
5. 构造方法引用Class:new;
6. 数组构造方法引用::TypeName[]::new

```java
public class Test {
    public static void main(String[] args) {
        List<User> users = new ArrayList<>();
        for (int i = 0; i < 10; i++) {
            User user = User.create(User::new);
            users.add(user);
        }
        users.forEach(User::showUser);
    }

    @FunctionalInterface
    interface Factory<T> {
        T create();
    }
    static class User {
        public void showUser() {
            System.out.println(this.toString());
        }

        public static User create(Factory<User> factory) {
            return factory.create();
        }
    }

}

```

* Stream

Java8中有一种新的数据处理方式，那就是流Stream，结合lambda表达式能够更加简洁高效的处理数据。Stream使用一种类似于SQL语句从数据库查询数据的直观方式，对数据进行如筛选、排序以及聚合等多种操作。

生成Stream的方法

1. 从接口Collection中和Arrays
   1. Collection.stream();
   2. Collection.parallelStream(); //相较于串行流，并行流能够大大提升执行效率
   3. Arrays.stream(T array);
2. Stream中的静态方法
   1. Stream.of()；
   2. generate(Supplier s);
   3. iterate(T seed, UnaryOperator f);
   4. empty();



* Optional

为了解决空指针异常，在Java8之前需要使用if-else这样的语句去防止空指针异常，而在Java8就可以使用Optional来解决。Optional可以理解成一个数据容器，甚至可以封装null，并且如果值存在调用isPresent()方法会返回true。

# 创建对象的几种方式

1. 使用New方法创建对象

   执行这条语句时JVM首先在方法区的常量池中查看是否有new 后面参数（也就是类名）的符号引用，并检查是否有类的加载信息也就是是否被加载解析和初始化过。如果已经加载过了就不在加载，否则执行类的加载全过程。过程如下例子Instance instance=new Instance()

   1. 给实例分配内存：此内存中存放对象自己的实例变量和从父类继承过来的实例变量（即使这些从超类继承过来的实例变量有可能被隐藏也会被分配空间），同时这些实例变量被赋予默认值（零值）
   2. 调用构造函数，初始化成员字段：在Java对象初始化过程中，主要涉及三种执行对象初始化的结构，分别是实例变量初始化**、**实例代码块初始化以及构造函数初始化。
   3. 设置instance对象指向刚分配的内存空间： 注意：new操作不是原子操作，2和3的顺序可能会调换，导致引用型变量指向一个不为null但是也不完整的对象(可以通过volatile禁止指令重排)。

   

2. 使用clone()方法创建对象

   1. clone就是克隆，也就是复制；使用某个对象的clone()方法时（前提是此对象的对应的类中已经实现clone方法），JVM根据被拷贝的对象分配内存、创建新的对象，然后会把被clone的对象的值全都拷贝进去；clone()方法是属于Object类的，clone是在堆内存中用二进制的方式进行拷贝，重新分配给对象一块内存；Object类的clone方法是一个native方法。

   2. Object类的clone()方法是线程不安全的。

   3. clone()有浅拷贝和深拷贝两种模式。浅拷贝是拷贝被拷贝对象的值，若被拷贝对象的属性有引用类型的，则只拷贝引用的地址；深拷贝是拷贝被拷贝对象的所有值（深层），若有被拷贝对象有引用类型的属性，则也要拷贝其引用类型的属性所对应的对象。

      * 浅拷贝

        ```java
        public class Parent implements Cloneable {
            public String name;
        
            public Child child;
        
            @Override
            protected Object clone() throws CloneNotSupportedException {
                return super.clone();
            }
        
        
            static class Child {
                String name;
            }
        
            public static void main(String[] args) throws CloneNotSupportedException {
                Parent parent1 = new Parent();
                parent1.name = "a";
                parent1.child = new Child();
                parent1.child.name = "a child";
                Parent parent2 = (Parent) parent1.clone();
                System.out.println("parent1 == parent2:"+(parent1 == parent2));
                System.out.println("parent1 hashCode() " + parent1.hashCode());
                System.out.println("parent2 hashCode() " + parent2.hashCode());
        
                System.out.println("parent1 child== parent2 child:" + (parent1.child == parent2.child));
                System.out.println("parent1 child hashCode() " + parent1.child.hashCode());
                System.out.println("parent2 child hashCode() " + parent2.child.hashCode());
                
            }
        }
        
        /**  输出
        parent1 == parent2:false
        parent1 hashCode() 1639705018
        parent2 hashCode() 1627674070
        parent1 child== parent2 child:true
        parent1 child hashCode() 1360875712
        parent2 child hashCode() 1360875712
        
        从最后child的输出可以看到，parent1 和 parent2的child实际上指向同一个对象，引用类型是传递引用。
        */
        ```

        

      * 深拷贝

      ```java
      public class Parent implements Cloneable {
          public String name;
      
          public Child child;
      
          @Override
          protected Object clone() throws CloneNotSupportedException {
              Parent parent = (Parent) super.clone();
              parent.child = (Child) this.child.clone();
              return parent;
          }
      
      
          static class Child implements Cloneable{
              String name;
      
              @Override
              protected Object clone() throws CloneNotSupportedException {
                  return super.clone();
              }
          }
      
          public static void main(String[] args) throws CloneNotSupportedException {
              Parent parent1 = new Parent();
              parent1.name = "a";
              parent1.child = new Child();
              parent1.child.name = "a child";
              Parent parent2 = (Parent) parent1.clone();
              System.out.println("parent1 == parent2:"+(parent1 == parent2));
              System.out.println("parent1 hashCode() " + parent1.hashCode());
              System.out.println("parent2 hashCode() " + parent2.hashCode());
      
              System.out.println("parent1 child== parent2 child:" + (parent1.child == parent2.child));
              System.out.println("parent1 child hashCode() " + parent1.child.hashCode());
              System.out.println("parent2 child hashCode() " + parent2.child.hashCode());
      
          }
      }
      
      /**
      输出
      parent1 == parent2:false
      parent1 hashCode() 1639705018
      parent2 hashCode() 1627674070
      parent1 child== parent2 child:false
      parent1 child hashCode() 1360875712
      parent2 child hashCode() 1625635731
      
      可以看到，对child也进行了一次拷贝，实际对于child是浅拷贝，但是对于parent是深拷贝。
      /
      ```

      

3. 使用反射方法创建对象

   1. 获取类的Class对象实例，获取方式主要有
      1. Class.forName("类全路径");
      2. 类名.class; 如：Animal.class;
      3. 对象名.getClass();
   2. 通过反射创建类对象的实例对象；获取Class对象实例后，可以通过Java反射机制创建类对象实例对象；主要有两种方式：
      1. Class.newInstance()：调用无参的构造方法，必需确保类中有无参数的可见的构造函数，否则将会抛出异常；
      2. 调用类对象的构造方法。
   3. 强制转换成用户所需类型。

   ```java
   public class User {
       private int age;
       private String name;
       public User(int age, String name) {
           this.age = age;
           this.name = name;
       }
       public User() {
       }
       public void setAge(int age) {
           this.age = age;
       }
       public void setName(String name) {
           this.name = name;
       }
       @Override
       public String toString() {
           return "User{" +
                   "age=" + age +
                   ", name='" + name + '\'' +
                   '}';
       }
       public static void main(String[] args) {
           //正常new 对象
           User user = new User(1, "user1");
           System.out.println(user);
           //使用反射创建对象
           try {
               Class cla = Class.forName("reflect.User");
               Constructor constructor = cla.getConstructor();
               //根据Constructor对象的newInstance方法获取反射类对象
               Object user2 = constructor.newInstance();
               System.out.println(user2);
               //强制类型转换
               User user3 = (User) user2;
               user3.setName("user3");
               user3.setAge(3);
               System.out.println(user3);
           } catch (Exception e) {
               e.printStackTrace();
           }
       }
   }
   
   ```

   

# 元注解 

元注解是用于修饰注解的注解，通常用在注解的定义上，一般用于指定某个注解生命周期以及作用目标等信息，Java有四种元注解 。

1. @Target

   说明注解所修饰的对象范围，关键源码如下：

   ```java
   public @interface Target {  
       ElementType[] value();  
   }  
   public enum ElementType {  
     TYPE,FIELD,METHOD,PARAMETED,CONSTRUCTOR,LOCAL_VARIABLE,ANNOCATION_TYPE,PACKAGE,TYPE_PARAMETER,TYPE_USE  
   }  
   /**
   	Type 作用在类/接口
   	FIELD 作用在变量 
   	METHOD 作用在方法
   	PARAMETER 作用在方法参数上
   	CONSTRUCTOR 作用在构造器上
   	LOCAL_VARIABLE 作用在本地局部变量上
   	ANNOTATION_TYPE 允许作用在注解上
   	PACKAGE 允许作用在包上
   /
   ```

2. @Retention(保留策略)

   保留策略定义了该注解被保留的时间长短。关键源码如下：

   ```java
   public @interface Retention {  
       RetentionPolicy value();  
   }  
   public enum RetentionPolicy {  
       SOURCE, CLASS, RUNTIME  
   } 
   /**
   	SOURCE：表示在源文件中有效（即源文件保留）
   	CLASS：表示在class文件中有效（即class保留）
   	RUNTIME：表示在运行时有效（即运行时保留）
   /
   ```

   

3. @Documented

   该注解用于描述其它类型的annotation应该被作为被标注的程序成员的公共API，因此可以被javadoc此类的工具文档化。Documented是一个标记注解，没有成员。关键源码如下：

   ```java
   public @interface Documented {
   }
   ```

4. @Inherited

   该注解是一个标记注解，@Inherited阐述了某个被标注的类型是被继承的。如果一个使用了@Inherited修饰的annotation类型被用于一个class，则这个annotation将被用于该class的子类。关键源码如下

   ```java
   public @interface Inherited {
   }
   ```

   

# 注解原理

注解本质是一个继承了Annotation的特殊接口，其具体实现类是Java运行时生成的动态代理类。通过代理对象调用自定义注解（接口）的方法，会最终调用AnnotationInvocationHandler的invoke方法。该方法会从memberValues这个Map中索引出对应的值。而memberValues的来源是Java常量池。

# 序列化与反序列化 

## 系列化

对象序列化的最主要的用处就是在传递和保存对象的时候，保证对象的完整性和可传递性。序列化是把对象转换成有序字节流，以便在网络上传输或者保存在本地文件中。序列化后的字节流保存了Java对象的状态以及相关的描述信息。序列化机制的核心作用就是对象状态的保存与重建

## 反序列化

客户端从文件中或网络上获得序列化后的对象字节流后，根据字节流中所保存的对象状态及描述信息，通过反序列化重建对象。

## 为什么需要序列化与反序列化

当两个进程进行远程通信时，可以相互发送各种类型的数据，包括文本、图片、音频、视频等， 而这些数据都会以二进制序列的形式在网络上传送。发送方需要把这个Java对象转换为字节序列，然后在网络上传送。接收方需要从字节序列中恢复出Java对象。

### 序列化好处

1. 永久性保存对象，保存对象的字节序列到本地文件或者数据库中；
2. 通过序列化以字节流的形式使对象在网络中进行传递和接收；
3. 通过序列化在进程间传递对象；

# Java的3种IO模型区别

## 3种IO模型

1. BIO(阻塞IO模型)

   线程发起IO请求后，一直阻塞IO，直到缓冲区数据就绪后，再进入下一步操作。针对网络通信都是一请求一应答的方式，虽然简化了上层的应用开发，但在性能和可靠性方面存在着巨大瓶颈，如果每个请求都需要新建一个线程来专门处理，那么在高并发的场景下，机器资源很快就会被耗尽。

2. NIO(IO复用模型)

   线程发起io请求后，立即返回（非阻塞io）。同步指的是必须等待IO缓冲区内的数据就绪，而非阻塞指的是，用户线程不原地等待IO缓冲区，可以先做一些其他操作，但是要定时轮询检查IO缓冲区数据是否就绪。Java中的NIO 是new IO的意思。其实是NIO加上IO多路复用技术。普通的NIO是线程轮询查看一个IO缓冲区是否就绪，而Java中的new IO指的是线程轮询地去查看一堆IO缓冲区中哪些就绪，这是一种IO多路复用的思想。IO多路复用模型中，将检查IO数据是否就绪的任务，交给系统级别的select或epoll模型，由系统进行监控，减轻用户线程负担。

3. AIO(异步IO模型)

   AIO是真正意义上的异步非阻塞IO模型。 NIO需要用户定时轮询，去检查IO缓冲区数据是否就绪，占用应用程序线程资源，其实轮询相当于还是阻塞的，并非真正解放当前线程，因为它还是需要去查询哪些IO就绪。而真正的理想的异步非阻塞IO应该让内核系统完成，用户线程只需要告诉内核，当缓冲区就绪后，通知我或者执行我交给你的回调函数。

# Linux5种IO模型的区别 

## 5种IO模型

1. 阻塞IO模型

   用户线程发出IO请求之后，内核会去查看数据是否就绪，如果没有就绪就会等待数据就绪，而用户线程就会处于阻塞状态，用户线程交出CPU。当数据就绪之后，内核会将数据拷贝到用户线程，并返回结果给用户线程，用户线程才解除block状态

2. 非阻塞IO模型

   当用户线程发起一个read操作后，并不需要等待，而是马上就得到了一个结果。如果结果是一个error时，它就知道数据还没有准备好，于是它可以再次发送read操作。一旦内核中的数据准备好了，并且又再次收到了用户线程的请求，那么它马上就将数据拷贝到了用户线程，然后返回。

3. 多路复用IO模型

   在多路复用IO模型中，会有一个线程不断去轮询多个socket的状态，只有当socket真正有读写事件时，才真正调用实际的IO读写操作。因为在多路复用IO模型中，只需要使用一个线程就可以管理多个socket，系统不需要建立新的进程或者线程，也不必维护这些线程和进程，并且只有在真正有socket读写事件进行时，才会使用IO资源，所以它大大减少了资源占用。

4. 信号驱动IO模型

   在信号驱动IO模型中，当用户线程发起一个IO请求操作，会给对应的socket注册一个信号函数，然后用户线程会继续执行，当内核数据就绪时会发送一个信号给用户线程，用户线程接收到信号之后，便在信号函数中调用IO读写操作来进行实际的IO请求操作。

5. 异步IO模型

   在异步IO模型中，当用户线程发起read操作之后，立刻就可以开始去做其它的事。而另一方面，从内核的角度，当它受到一个asynchronous read之后，它会立刻返回，说明read请求已经成功发起了，因此不会对用户线程产生任何block。然后，内核会等待数据准备完成，然后将数据拷贝到用户线程，当这一切都完成之后，内核会给用户线程发送一个信号，告诉它read操作完成了。也就说用户线程完全不需要实际的整个IO操作是如何进行的，只需要先发起一个请求，当接收内核返回的成功信号时表示IO操作已经完成，可以直接去使用数据了。

# Object方法

Object类是所有类的父类，它有九大方法

1. clone方法（浅拷贝）：保护方法，实现对象的浅复制，只有实现了Cloneable接口才可以调用该方法，否则抛出CloneNotSupportedException异常。
2. getClass方法：getClass就是返回一个运行实例的Class，通常返回类名。
3. toString方法：返回该对象的字符串表示，如果不重写，返回类名@哈希值。
4. finalize方法：当垃圾回收器确定不存在对该对象的更多引用时，由对象的垃圾回收器调用此方法，释放资源。
5. equals方法：一般用来比较两个对象是否相等，如果没有重写equals方法，则它的作用与 == 相同。
6. hashCode方法：该方法用于哈希查找，可以减少在查找中使用equals的次数，重写了equals方法一般都要重写hashCode方法。
7. wait方法：wait()的作用是让当前线程进入等待状态，同时，wait()也会让当前线程释放它所持有的锁。直到其他线程调用此对象的 notify() 方法或 notifyAll() 方法，当前线程被唤醒(进入“就绪状态”)。
8. notify方法：该方法唤醒在该对象上等待的某个线程。
9. notifyAll方法：该方法唤醒在该对象上等待的所有线程。