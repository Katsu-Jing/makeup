# Java语法基础知识

## Chapter1 语法基础

1. Java中的变量都有哪些类型呢，基本类型又有哪些？
2. 为什么会有基本类型的包装器类？Java的自动拆装箱机制？鬼畜的魔鬼数字128，看下面例子，你能猜到结果吗？

```java
public class Main {
    /**
    * 自动拆装箱的坑集锦：你能看出有多少次拆箱多少次装箱吗
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
    }
}
```

## Chapter2 面向对象引论



