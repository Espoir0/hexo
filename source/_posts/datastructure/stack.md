---
title: Stack
date: 2019-08-06
tags:
- 算法与数据结构
categories:
- 算法与数据结构
---
1. 使用数组实现栈结构
2. 使用栈计算一组数学表达式<br/>
***
<!-- more -->
简介:
* 栈(stack)是一个先入后出(FILO-First In Last Out)的有序列表。
* 栈(stack)是限制线性表中元素的插入和删除只能在线性表的同一端进行的一种特殊线性表。允许插入和删除的一端，为变化的一端，称为栈顶(Top)，另一端为固定的一端，称为栈底(Bottom)。
* 根据栈的定义可知，最先放入栈中元素在栈底，最后放入的元素在栈顶，而删除元素刚好相反，最后放入的元素最先删除，最先放入的元素最后删除

栈的应用:
* 子程序的调用：在跳往子程序前，会先将下个指令的地址存到堆栈中，直到子程序执行完后再将地址取出，以回到原来的程序中。 	
* 处理递归调用：和子程序的调用类似，只是除了储存下一个指令的地址外，也将参数、区域变量等数据存入堆栈中。
* 表达式的转换\[中缀表达式转后缀表达式]与求值(实际解决)。
* 二叉树的遍历。
* 图形的深度优先(depth一first)搜索法

### 数组实现栈
```java
//使用数组模拟栈
public class ArrayStack {
    private int maxSize;           //栈的容量
    private int[] stack;           //数组模拟栈
    private int top=-1;            //top表示栈顶元素

    public ArrayStack(int maxSize) {
        this.maxSize = maxSize;
        this.stack=new int[maxSize];
    }

    public boolean isFull(){
        return top==maxSize-1;
    }

    public boolean isEmpty(){
        return top==-1;
    }

    //出栈
    public void push(int e){
        if (isFull()){
            return;
        }
        stack[++top]=e;
    }

    //出栈
    public int pop(){
        if (isEmpty()){
            new RuntimeException("栈为空");
        }
        return stack[top--];
    }

    //查看栈中所有元素

    @Override
    public String toString() {
        StringBuffer buffer = new StringBuffer();
        for (int i = stack.length - 1; i >= 0; i--) {
            buffer.append(stack[i]);
        }
        return buffer.toString();
    }
}
```

### 利用栈计算数学表达式
```java
public class StackApp {
    public static void main(String[] args) {
        String s="(1+((2+3)*(4*5)))";
        Stack<String> opsStack=new Stack<>();         //存储操作符号的栈
        Stack<Double> numberStack = new Stack<>();    //存储数字的栈

        char[] chars=s.toCharArray();
        for(int i=0;i<chars.length;i++ ){
            String temp=(""+chars[i]);
            if(temp.equals("("));                     //"("忽略，其他运算符入栈。
            else if(temp.equals("+")) opsStack.push(temp);
            else if(temp.equals("-")) opsStack.push(temp);
            else if(temp.equals("*")) opsStack.push(temp);
            else if(temp.equals("/")) opsStack.push(temp);
            else if(temp.equals("sqrt")) opsStack.push(temp);
            else if(temp.equals(")")) {               //  符号为 ")"时弹出运算符和操作数，计算结果并压入栈
                String s1=opsStack.pop();
                Double number=numberStack.pop();
                if(s1.equals("+")) number= numberStack.pop() +  number;
                else if(s1.equals("-")) number=numberStack.pop() -number;
                else if(s1.equals("*")) number=numberStack.pop() *number;
                else if(s1.equals("/")) number=numberStack.pop() /number;
                else if(s1.equals("sqrt")) number=Math.sqrt(number);
                numberStack.push(number);
            }
            else numberStack.push(Double.parseDouble(temp));//不是运算符或者括号时，作为double值压入栈
        }

        System.out.println(numberStack.pop());
    }
}

```
