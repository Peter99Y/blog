---
title: java_se
---

## 单位

计算机最小的组成单位**字节 byte**：其中二进制位称为 **位 bit** 简称 b，使用 8 个二进制位为一组字节保存数据，1byte = 8bit；1KB=1024B；

如：十进制的 6，通过**除二取余法** 转换成二进制 110，字节为 00000110；

## 概念

JVM (java Virtual Machine)： java 虚拟机，真正运行 java 程序的地方；
核心类库：java 自身写好的程序，如：System、String、Math、Scanner 等；
JRE (java Runtime Environment)： java 运行环境；
JDK (java Development Kit)： java 开发工具包(包括以上 3 者)；

### 环境变量

Path 环境变量用于记住程序路径，可以在命令行窗口任意目录启动程序；

### 结构

- project (项目/工程)，module (模块)，package(包)，class (类)；

## 初始

1. 以大驼峰方式创建后缀.java 文件，其中**类名称必须和文件名称一致**；
2. 运行：java HelloWorld

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
        System.out.println(c); // 6，++单独用时，符号在前后都一样；

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

## 扩展运算符

