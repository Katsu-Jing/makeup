# Java语法基础知识

## Chapter1 语法基础

### section 1 一切都是对象

1. Java中的变量都有哪些类型呢，基本类型又有哪些？
2. 为什么会有基本类型的包装器类？Java的自动拆装箱机制？鬼畜的魔鬼数字128，看下面例子，你能猜到结果吗？

```java
public class Main {
    /**
    * 自动拆装箱的坑集锦：
    * 你能看出有多少次拆箱多少次装箱吗？
    * 你认为程序的运行结果是啥呢？
    */
    public static void main(String[] args){
        Integer a = 10;
        Integer b = 1007;
        Integer c = new Integer(10);
        Integer d = new Integer(1007);

        if(a == 10){
            System.out.println("a == 10");
        }
        if(a == c){
            System.out.println("a == c");
        }
        if(b == 1007){
            System.out.println("b == 1007");
        }
        if(b == d){
            System.out.println("b == d");
        }
        if(a.equals(10)){
            System.out.println("a equals 10");
        }
        if(a.equals(c)){
            System.out.println("a equals c");
        }
        if(b.equals(1007)){
            System.out.println("b equals 1007");
        }
        if(b.equals(d)){
            System.out.println("b equals d");
        }
    }
}
```

3. Java中，方法参数的传递是按值传递还是按引用传递呢？你能写一段程序证明你的观点吗？

4. Java的方法签名为什么不包含返回值？

5. 理解static关键词

6. JVM的内存模型？堆是什么？栈又是什么，有什么区别？哪些数据在堆里，哪些在栈里呢？

7. 通过String这个类，考考你对上述问题的理解程度呀，下面代码会输出啥呢？（我又考考你了- -！）

```java
public class UnderstandString {
    public static void main(String[] args){
        String a = "fan";
        String b = new String("fan");
        String c = String.intern("fan");
        if(a == "fan") System.out.println("a == fan");
        if(a.equals("fan")) System.out.println("a equals fan");
        if(a == b) System.out.println("a == b");
        if(a.equals(b)) System.out.println("a equals b");
        if(a == c) System.out.println("a == c");
        if(a.equals(c)) System.out.println("a equals c");
    }
}
```

### section 2 操作符

1. 赋值运算符，只要充分理解Java中引用的概念就ok。

```java
class Letter {
    char c;
}

public class PassObject {
    static void f(Letter y){
        y.c = "f";
    }
    
    public static void main(String[] args){
        Letter x = new Letter();
        x.c = "t";
        System.out.println("x.c="+x.c);
        f(x);
        System.out.println("x.c="+x.c);
    }
}
```

2. i++和++i的区别是什么？

```java
public class UnderstandPlusPlus {
    public static void main(String[] args){
        int i = 0;
        System.out.println("i="+i);
        System.out.println("i++ = "+(i++));
        System.out.println("++i = "+(++i));
    }
}
```

3. == 和 equals\(\)方法的区别是什么？其实能理解上一节中String的各种j就差不多

4. 利用逻辑运算符的短路现象精简代码的书写，显得专业，另外注意Java中不支持除true或false以外的值出现在if语句中。

5. 10.7 转化为int类型变成多少了？

### section 3 控制执行流程

编程练习：斐波那契数列由1 1 2 3 5 8 13 21……组成，从第三个数字开始，每个数字是前两个数字的和。请写一个方法，它有一个参数，指定数列的长度n，它会打印出前n个斐波那契数列值。例如fibonacci\(5\)的输出为：1 1 2 3 5

## Chapter2 面向对象引论

## Chapter3 容器框架

## Chapter4 异常体系

## Chapter5 类型系统

## Chapter6 IO系统

## Chapter7 并发



