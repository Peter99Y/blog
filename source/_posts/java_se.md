---
title: java_se
---

## 概念

核心类库：java 自身写好的程序，如：System、String、Math、Scanner 等 (一个类 等同于 一个 java 文件；类库就是存放多个 java 文件的仓库)；
JVM (java Virtual Machine)： java 虚拟机 (真正运行 java 程序的地方)；
JRE (java Runtime Environment)： java 运行环境，包含 JVM 虚拟机 和 java 核心类库 (如不开发只运行 java 程序，只需安装 JRE)；
JDK (java Development Kit)： java 语言的软件开发工具包，内部包括 JRE 和 VM；

## 安装

- 环境变量: Path 环境变量用于记住程序路径，可以在命令行窗口任意目录启动程序；
- 命令行： cmd 命令行窗口，java -version 查看版本；

## java 结构

- project (项目/工程)
- module (模块)
- package
  src 下的包；命名规则最好是域名的格式的反向，如 com.google;
- class (类)
  package com.google; 在类中，指定报名;
  import com.google.Person; 导入类 (不在同一个包需要导入);
  import com.google.\*; 导入所有类;

## 运行

- 以大驼峰方式创建后缀.java 文件，其中**类名称必须和文件名称一致**；
- 在.java 文件所处位置打开命令行窗口，运行：java HelloWorld (.java 的文件名)；

## 计算机单位

计算机最小的组成单位**字节 byte**：其中二进制位称为 **位 bit** 简称 b，使用 8 个二进制位为一组字节保存数据，1byte = 8bit；1KB = 1024B；

如：十进制的 6，通过**除二取余法** 转换成二进制 110，字节为 00000110；

## 基本数据类型

| 类型                  | 占用内存 (字节数) | 数据范围                                    |
| --------------------- | ----------------- | ------------------------------------------- |
| byte (整数)           | 1                 | -128 ~ 127                                  |
| short (整数)          | 2                 | -32768 ~ 32767                              |
| int (整数，默认)      | 4                 | -2147483648 ~ 2147483647 (10 位数，21 亿多) |
| long (整数)           | 8                 | -9223372036854775808 ~ 9223372036854775807  |
| float (浮点型)        | 4                 | 1.4E-45 ~ 3.4E+38                           |
| double (浮点型，默认) | 8                 | 4.9E-324 ~ 1.8E+308                         |
| char (字符型)         | 2                 | 0 ~ 65535 (Unicode)                         |
| boolean (布尔型)      | 1                 | true/false                                  |

```JAVA HelloWorld.java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println(' ');
        System.out.println(''); // err, 单引号不能空字符，必须是单个字符；
        System.out.println('hello'); // err, 双引号可向下兼容空字符串和单个字符；

        System.out.println("hello");
        System.out.println('\n'); // 打印一个换行符，
        System.out.println("\tworld"); // 由于 println() 函数会自动换行，因此会有2个换行符；
    }
-----------------------------------------------
    public static void main(String[] args) {
        {
            int a = 1;
        }

        System.out.println(a); // err, 局限于块作用域
    }
-----------------------------------------------
    public static void main(String[] args) {
        double a;
        System.out.println(b); // err, 初始化可无值，使用时必须有值；
    }
------------------------------------------------
    public static void main(String[] args) {
        char b = 'b';
        System.out.println(b + 1); // 99，转成ascii码再加1
        System.out.println(b); // b

        long a = 11111112223; // err, 后缀需加L/l；赋值默认是int类型，虽然值没有超过long类型，但超过了int类型；
        float a = 11.1; // err, 后缀需加加F/f；赋值默认是double类型，虽然值没有超过float类型，但超过了double类型；
    }
```

## 类型转换

- 某个类型的变量赋值给另一种类型变量，存在不同类型的数据一起运算；

### 自动类型转换

- 类型数据范围小的变量可以直接赋值给类型数据范围大的变量，但反之不行，根据占用字节数的大小来判断，如 char 字符类型也能转换为 int 类型；

type -> int: type 是 1 字节，在内存中占据 8 位二进制；int 4 字节，占 32 位二进制；

### 表达式的自动类型转换

- 在表达式中，小范围类型的变量会自动转换成范围大的类型再参与运算；
- byte、short、char 是直接转换成 int 类型再参与运算；

```JAVA
    public static void main(String[] args) {
        byte a = 10;
        short b = 20;
        long c = 30;

        long e = a + b + c; // 最高类型；
        double d = a + b + 1.0; // 最高类型；

        int f = a + b; // byte, short, char 直接转int；
    }
```

### 强制类型转换

- 浮点型转整型，丢失小数部分；
- 大转小超出小的范围，会丢失精度；

```JAVA
    public static void main(String[] args) {
        int a = 20;
        byte aa = (byte) a;
        System.out.println(aa); // 20

        int c = 15000;
        byte cc = (byte) c;
        System.out.println(cc); // -36，精度丢失

        double d = 100.5;
        int dd = (int) d;
        System.out.println(dd); // 100，小数点丢失

        int i = 5;
        int j = 2;
        System.out.println(i / j); // 2，精度丢失
        System.out.println(1.0 * i / j); // 2.5，运算时，先转成最高字节类型double，再除；
    }
```

## 运算符

- 扩展运算符

a += b 等价于 a = a + b; (a 与 b 相加后，类型强制转换为 a 类型，再赋值)

```JAVA
    public static void main(String[] args) {
        int a = 5;

        System.out.println(a + 5); // 10

        System.out.println(a + 'a'); // 102
        System.out.println('a' + a); // 102

        System.out.println(a + "a"); // 5a
        System.out.println("a" + a); // a5

        System.out.println("abc" + a + 'a'); // abc5a
        System.out.println(a + 'a' + "abc"); // 105abc
    }
```

```JAVA
    public static void main(String[] args) {
        int c = 5;
        c++;
        System.out.println(c); // 6，++单独使用时，符号在前后都一样；

        int a = 5;
        int aa = a++;
        System.out.println(a); // 6，给变量aa先赋值完，a再自增；
        System.out.println(aa); // 5

        int b = 5;
        int bb = ++b;
        System.out.println(b); // 6， b变量先自增，再赋值给变量bb；
        System.out.println(bb); // 6
    }
```

```JAVA
    public static void main(String[] args) {
        double total = 100;
        int spend =  80;

        total -= spend;

       System.out.println(total);   // 20.0
    }
--------------------------------------------
    public static void main(String[] args) {
        System.out.println(!(2 > 1)); // false, 逻辑非
        System.out.println(2 > 1 & 2 > 0); // true, 逻辑与，无论左侧是否为true, 右侧都会执行
        System.out.println(1 > 2 | 2 > 1); // true, 逻辑或，无论左侧是否为true, 右侧都会执行
    }
--------------------------------------------
    public static void main(String[] args) {
        System.out.println(true ^ true); // false, 逻辑异或
        System.out.println(false ^ false); // false
        System.out.println(true ^ false); // true
    }
--------------------------------------------
    public static void main(String[] args) {
        System.out.println(2 > 10 && 3 > 2); // false, 短路与，左侧false，右侧就不会执行
        System.out.println(2 > 1 || 3 < 5); // true, 逻辑或, 左侧true，右侧就不会执行
    }
--------------------------------------------
    public static void main(String[] args) {
        System.out.println(10 > 1 || 10 > 1 && 10 < 1); // true, 短路与 比 短路或的优先级高

        System.out.println((10 > 3 || 10 > 3) && 10 < 3); // false
    }
```

## 案例 键盘录入

```JAVA
  public static void main(String[] args) {
        Scanner sc = new Scanner(System.in); // 获取扫描器

        System.out.println("请输入您的年龄：");
        int age = sc.nextInt(); // 等待输入一个整数，获取用户按下回车键拿到数据；
        System.out.println("您的年龄是：" + age);

        System.out.println("请输入您的名字：");
        String name = sc.next();  // 等待用户输入；
        System.out.println("您输入的名字是：" + name);

        System.out.println("欢迎您");
    }
```

## 流程控制

- 顺序结构，从上到下执行代码；
- 条件结构，根据条件执行对应代码；
- 循环结构，重复执行代码；

```JAVA
   public static void main(String[] args) {
        // 1.switch不支持long, float, double；
        // 2.case值不能重复；
        // 3.case值不能是变量；
        // 4.需要写break,否则会造成穿透现象；

        double a = 0.1;
        double b = 0.2;
        double c = a + b; // 0.300000000004;

        int d = 10;

        switch (c) {
            case 1:
                System.out.println('1');
                break;
            case 1:
                System.out.println('1');
                break;
            case d:
                System.out.println("不能是变量d");
                break;
        }
    }
------------------------------------------------
    // 利用穿透现象
    public static void main(String[] args) {
        String day = "周三";

        switch (day) {
            case "周一":
                System.out.println("crying face");
                break;
            case "周二":
            case "周三":
            case "周四":
                System.out.println("blank face");
                break;
            case "周五":
                System.out.println("smile face");
                break;
            default:
                System.out.println("happy face");
        }
    }
```

```JAVA
    public static void main(String[] args) {
        int a = 0;
        while (a < 5){
            System.out.println(a);
            a++;
        }
    }
--------------------------------------------
    public static void main(String[] args) {
        int a = 0;

        // 先执行do里面代码，后判断条件；
        do{
            System.out.println(a);
            a++;
        }while(a < 5);
    }
```

## Random

```JAVA
    public static void main(String[] args) {
        Random rand = new Random();

        int num = rand.nextInt(10); // 生成0-9之间的数字；
        int num2 = rand.nextInt(10) + 1; // 生成 1-10之间的数字；

        int num3 = rand.nextInt(15) + 3; // 生成 3-17之间的数字；18-3=15,也就是生成0-14，再+3等于 3-17；
        int num4 = rand.nextInt(15); // 生成65-91之间的数字；92-65=27，也就是0-26，再+65等于 65-91；
    }
```

## 数组

```JAVA
    public static void main(String[] args) {
        int[] numbers = new int[]{1, 2, 3}; // 方式1
        double[] scores = new double[]{1.1, 1.1, 1.1};

        int numbers2[] = {4, 5, 6}; // 方式2 (不推荐)
        double scores2[] = {4.1, 5.1, 6.1};

        int[] numbers3 = {7, 8, 9}; // 方式3 (常)
        double[] scores3 = {7.1, 8.1, 9.1};
    }
--------------------------------------------
    public static void main(String[] args) {
        int[] arr = {1, 2, 3};

        System.out.println(arr.length); // 3
        System.out.println(arr[0]); // 1

        System.out.println(arr[3]); // err
    }
--------------------------------------------
    public static void main(String[] args) {
        // 动态数组，只确定数组存储类型和长度；
        int[] arr = new int[3];
        System.out.println(arr[0]); // 默认0

        char[] strs = new char[3];
        System.out.println(strs[0]); // char类型默认是0，只是取出来是乱码；
        System.out.println((int)strs[0]); // 0, 使用强制转换为整数类型；

        double[] numbers  = new double[3];
        System.out.println(numbers[0]); // 默认0.0

        boolean[] bools = new boolean[3];
        System.out.println(bools[0]); // 默认false

        String[] names = new String[3];
        System.out.println(names[0]); // null, 引用数据类型默认是是null；
    }
```

### 扩展

- 基本数据类型和引用数据类型执行原理
  基本数据类型，a 变量直接放在栈中，a 变量存储的数据就是变量的值；
  引用数据类型，a 变量直接放在栈中，a 变量存储的数据是堆的地址，堆中开辟新的内存存储对象；

- 运行一个 java 程序，JVM 中包含哪几部分内存区域
  方法区，栈内存，堆内存

---

> 面向对象
> 人类、鸟类、鱼类都是对一类事务的描述，是抽象的概念；
> 对象是某类事务实际存在的每个个体，因而也被称之为实例；
> 定义一个类，然后建议不创建许多这个类的实例对象，称之为面向对象编程；

## 类

### 属性

定义类的属性，作为成员变量，具体的实例对象中才可以使用这些变量；

### 方法

方法被调用时，是进入到栈内存中运行；由于栈是遵循先进后出原则，main 方法调 a 方法，a 方法调 b 方法，b 方法执行完回到 a 方法中，a 方法执行完回到 main 方法中。

```java
public class Person {
    // 定义类的属性，作为成员变量，具体的实例对象中才可以使用这些变量；
    String name;
    int age;
    String gender;

    int sum(int a, int y) {
        return a + y;
    }

    void setName (String name) {
        // name = name; 重名时，只是对形参操作；
        this.name = name; // this 指向实例对象
    }
}


public class Demo {
    public static void main(String[] args) {
        Person p1 = new Person(); // 通过new关键字创建实例对象
        Person p2 = new Person();
        p1.name = "Tom";
        p2.name = "Jack";

        System.out.println( p1.name ); // Tom
        System.out.println( p2.name ); // Jack

        int res = p2.sum(1, 2);
        System.out.println(res);    // 3
    }
}
```

### 方法重载

- 一个类中，出现多个方法的名称相同，只是**形参列表不同 (个数、类型、顺序)**；

```JAVA
    public static void main(String[] args) {

        overLoad();
        overLoad(1.0, 2);
        overLoad(2.0, 1);
    }

    public static void overLoad() {
        System.out.println("o");
    }

    public static void overLoad(double a, int b) {
        System.out.println(a + b);
    }

    public static void overLoad(int b, double a) {
        System.out.println(a + b);
    }
```

### 构造方法

- 类默认会有一个无参的构造方法；父类有一个有参构造方法，子类必须在构造方法中调用初始化父类的构造方法 (无参构造可省略);
- 与类名称相处；构造方法会在实例对象创建时执行，此时可以将各种需要的初始化操作都在这里执行;
- final 给类，此类不允许被继承;
- final 给方法，不允许子类重写此方法;
- finla 给属性，属性必须在构造函数中被初始化 (不允许在其他方法中修改)，或者在定义属性时初始值 (构造函数中也不能初值了);

```java
public class Person {
    // 也可以直接定义属性变量是设置默认值
    String name = "nobody";
    int age = 1;
    String gender = "male";

    {
        System.out.println(name); // nobody; 属性变量先初始化，其次是此处代码块执行，然后才是构造函数执行
        name = "somebody";
    }

    // 通过构造方法给成员变量设置初始值
    Person (String name, int age, String gender) {
        System.out.println(this.name); // somebody;

        this.name = name;
        this.age = age;
        this.gender = gender;
    }
}
```

### 静态变量方法

- 成员属性(变量)/方法，是对象所具有的特征，只有具体的实例对象才可以使用；
- 静态属性(变量)/方法，是类所具有的特征，类本身就可以使用；静态变量是属于类，不依赖对象，所以静态方法中无法获取成员变量、无法使用 this 关键字；

```java
public class Person {
    // 也可以直接定义属性变量是设置默认值
    String name = "somebody";
    static String nickname;

    static String say (){
        System.out.println("我的名字是" + name); // err
        System.out.println("我的昵称是" + nickname);
    }
}
```

### 访问权限

为成员变量/方法，静态白变量/方法 指定访问权限；

| 权限      | 当前类 | 同一包下的类 | 不同包下的子类 | 不同包下的类 |
| --------- | ------ | ------------ | -------------- | ------------ |
| private   | √      | ×            | ×              | ×            |
| default   | √      | √            | ×              | ×            |
| protected | √      | √            | √              | ×            |
| public    | √      | √            | √              | √            |

### 封装

类中的成员变量可直接查看和修改，封装目的是保证类的变量安全性，通过 Getter 和 Setter 方法访问；

```java
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```

### 继承

- 标记为 final 的类不能被继承；
- 定义不同类时，存在相同属性，为了方便使用可以将共同属性抽象成一个父类，再定义其他子类继承父类，减少代码重复定义，子类可以使用父类**非 private**的属性和方法；
- 类默认会有一个无参的构造方法；父类有一个有参的构造方法，子类必须在构造方法中调用实现父类的构造方法；

```java
public class Person {
     String name;
     int age;
     String city;

     public void test(){
         System.out.println("父类 person");
     }
     public Person(String name, int age, String city) {
         this.name = name;
         this.age = age;
         this.city = city;
     }
}

public class Student extends Person {
    String city;

    public Student(String name, int age, String city) {
        super(name, age, city); // 使用super代表父类，父类的构造方法就是super();
        this.city = city;
    }

    public void say () {
        test();
        System.out.println("我叫" + name);
        System.out.println("我在" + city);
        System.out.println("父类在" + super.city);
    }
}

public class Demo {
    public static void main(String[] args) {
        // 使用父类的变量去引用一个子类对象（类型转换，向上转型）
        Person p1 = new Student("人类", 1, "Beijing");
        // p1.say(); // err, 父类对象的引用相当于父类来使用，只能访问父类的成员;

        Student s1 = new Student("小明", 1, "Beijing");
        s1.say();
    }
}
```

### 多态

- 多个不同的对象对同一个消息作出响应，同一个消息根据不同对象而采用不同的方法;
- 方法重写，给父类方法添加 final 表示最终形态，不允许子类重写;

```java Person.java
public class Person {
     String name;
     int age;
     String city;

     public Person(String name, int age, String city) {
         this.name = name;
         this.age = age;
         this.city = city;
     }
     // 重写顶级Object中的equals方法
     @Override
     public boolean equals(Object obj) {
        if(obj == null) return false;
        if(obj instanceof  Person){
            Person person = (Person) obj;
            return this.name.equals(person.name) && this.age == person.age && this.city.equals(person.city);
        }
        return false;
     }
     // 重写顶级Object中的toString方法
     @Override
     public String toString(){
         return "Person {" +"name:'" + name + '\'' + ", age:" + age + ", city:'" + city + '\'' + '}';
     }
}
```

```java
public class Student extends Person {
    String city;

    public Student(String name, int age, String city) {
        super(name, age, city); // 使用super代表父类，父类的构造方法就是super();
        this.city = city;
    }

    public void say () {
        System.out.println("我在" + this.city);
    }
}

public class Worker extends Person {

    public Worker(String name, int age, String city) {
        super(name, age, city);
    }

    public void say () {
        System.out.println("我在" + super.city);
    }
}
```

```java
public class Demo {
    public static void main(String[] args) {
        Student s1 = new Student("小明", 1, "Beijing");
        Student s2 = new Student("小明", 1, "Beijing");

        System.out.println(s1 == s2); // false
        System.out.println(s1.equals(s2)); // true

        System.out.println(s1.toString()); // Person {name:'小明', age:1, city:'Beijing'}
        s1.say(); // 我在Beijing

        Worker w1 = new Worker("小刚", 1, "Shanghai");
        w1.say(); // 我在Shanghai
    }
}
```

### 抽象类

- 一般只用于继承使用，不能实例化;
- 给类添加关键字 abstract，表示该类为抽象类，不能实例化 (只能是实例化继承的子类);
- 子类必须重写实现父类的抽象方法;

```java
public abstract class Person {
     String name;
     int age;
     String city;

     public Person(String name, int age, String city) {
         this.name = name;
         this.age = age;
         this.city = city;
     }

     public void test(){
         System.out.println("参加考试");
     }
     public abstract void exam ();
}
```

```java
public class Student extends Person {
    String city;

    public Student(String name, int age, String city) {
        super(name, age, city);
        this.city = city;
    }
    @Override
    public void exam() {
        System.out.println("只会考试");
    }
}
```

## 接口

- 接口中不能有成员属性，可以有静态变量和方法；
- 接口中的定义的默认方法 与 类中的父类中的方法重名时，会优先使用父类的方法；

```java Person.java
public abstract class Person {
     String name;
     int age;
     String city;

     public Person(String name, int age, String city) {
         this.name = name;
         this.age = age;
         this.city = city;
     }
     public void test(){
         System.out.println("参加考试");
     }
     public abstract void exam ();
}
```

```java
public class Student extends Person implements Study {
    String city;

    public Student(String name, int age, String city) {
        super(name, age, city);
        this.city = city;
    }
    @Override
    public void test() {
        Study.super.exam();
    }
    @Override
    public void exam() {
        System.out.println("只会考试");
    }
}
```

```java interface
public interface Study {
    // 接口中不能有成员属性，可以有静态变量和方法
    public static final int a = 10;

    public void test();

    default void exam () {
        System.out.println("从java 8开始，可以默认实现接口");
    }
}
```

- Object 类上有的或继承而来的，可以不必在自己的类里实现，也可以重写；
- 接口中默认方法，不能重写 Object 类的方法；

```java
public interface A {
    void test();
}

public interface B {
    void hello();
}

public interface C extends A, B {
    void test();  // 重名会覆盖
    boolean equals(Object obj); // Object类上有的或继承而来的，可以不必在自己的类里实现，也可以重写；
}
```

## 枚举

```java
public enum Status {
    RUNNING,
    STUDYING,
    SLEEPING,
}

class Student {
    private Status studentStatus;

    public Status getStatus() {
        return this.studentStatus;
    }
    public void setStatus(Status status) {
        this.studentStatus = status;
    }
}

public class Demo  {
    public static void main(String[] args) {
        Student hong = new Student();

        System.out.println(Status.values()); // 枚举列表
        System.out.println(Status.valueOf("SLEEPING")); // SLEEPING

        hong.setStatus(Status.RUNNING);
        System.out.println(hong.getStatus()); // RUNNING
    }
}
```

## 包装类

java 中的基本数据类型不是对象，通过包装类使得可以以对象的形式使用，体现面向对象的思想， 如可以进行一些数学运算，如加减乘除，比较大小等。

| 基本数据类型 | 包装类    |
| ------------ | --------- |
| byte         | Byte      |
| boolean      | Boolean   |
| short        | Short     |
| char         | Character |
| int          | Integer   |
| long         | Long      |
| float        | Float     |
| double       | Double    |

### Integer 包装类

```java
public class Demo  {
    public static void main(String[] args) {
        Integer count = new Integer(10); // Integer包装类

        Integer count1 = 10; // 自动装箱，类似于包装类简写
        int c = count1; //  包装类自动拆箱，将对象类型转成普通数字类型
        System.out.println(c);

        Integer count2 = Integer.valueOf(10); //  自动装箱等价于调用此方法
        int cc = count2.intValue(); // 自动拆箱等价于调用此方法
        System.out.println(cc);

        Integer a = 10, b = 10;
        int x = a + b; // 等价于 a.intValue() + b.intValue();

        System.out.println(x);
        System.out.println(a == b); // true, 自动拆箱后再比较 a.intValue() == b.intValue()
    }
}
```

### 数组包装类

```java
public class Demo  {
    public static void main(String[] args) {
        int[] arr = new int[3]; // 指定数组长度

        int[] arr2 = new int[]{1, 2, 3}; // 设置默认值

        int[] arr3 = {1, 2, 3}; // 设置默认值

        // forEach方法
        for(int a: arr3){
            System.out.println(a);
        }

        int[][] arr4 = new int[3][3];
        int[][] arr5 = new int[][]{{1,2,3}, {4,5,6}, {7,8,9}};
        System.out.println(arr5[2][2]); // 9

        test(arr2);
        test2(false, 1,2,3,4,5);
    }

    public static void test(int[] args){
        for(int it : args){
            System.out.println(it);
        }
    }

    public static void test2(Boolean a, int... args){
        for(int it : args){
            System.out.println(it);
        }
    }
}
```

#### String包装类
字符串类是比较特殊的类；char基本类型可以保存一个2字节的Unicode字符，字符串是一系列字符的序列；