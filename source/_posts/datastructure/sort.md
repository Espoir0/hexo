---
title: 排序算法
date: 2019-08-07
tags:
- 排序算法
categories:
- 数据结构与算法
---
排序算法
<!-- more -->
#### 简介:
Sort Algorithm)，排序是将一组数据，依指定的顺序进行排列的过程。
排序的分类：
1. 内部排序:将需要处理的所有数据都加载到内存中进行排序。
2. 外部排序法：数据量过大，无法全部加载到内存中，需要借助外部存储进行排序。

时间复杂度:
一般情况下，算法中的基本操作语句的重复执行次数是问题规模n的某个函数，用T(n)表示，若有某个辅助函数f(n)，使得当n趋近于无穷大时，T(n) / f(n) 的极限值为不等于零的常数，则称f(n)是T(n)的同数量级函数。记作 T(n)=Ｏ( f(n) )，称Ｏ( f(n) )  为算法的渐进时间复杂度，简称时间复杂度。

计算时间复杂度的方法：
1. 用常数1代替运行时间中的所有加法常数  T(n)=n²+7n+6  => T(n)=n²+7n+1
2. 修改后的运行次数函数中，只保留最高阶项  T(n)=n²+7n+1 => T(n) = n²
3. 去除最高阶项的系数 T(n) = n² => T(n) = n² => O(n²)

T(n)不同，但时间复杂度可能相同如：
T(n)=n²+7n+6 与 T(n)=3n²+2n+2 它们的T(n) 不同，但时间复杂度相同，都为O(n²)。

常见时间复杂度:

    常数阶O(1)
    对数阶O(log2n)
    线性阶O(n)
    线性对数阶O(nlog2n)
    平方阶O(n^2)
    立方阶O(n^3)
    k次方阶O(n^k)
    指数阶O(2^n)

Ο(1)＜Ο(log2n)＜Ο(n)＜Ο(nlog2n)＜Ο(n2)＜Ο(n3)＜ Ο(nk) ＜Ο(2n) ，随着问题规模n的不断增大，上述时间复杂度不断增大，算法的执行效率越低

常数阶O(1)
它消耗的时候并不随着某个变量的增长而增长，无论代码执行了多少行，只要是没有循环等复杂结构，那这个代码的时间复杂度就都是O(1)

对数阶O(log2n)
```
int i=1;
while(i<n){
    i=i*2;
}
```
2 的 x 次方等于 n，那么 x = log2n循环次数：log2n,时间复杂度为：O(log2n)

线性阶O(n)
```
for(int i=1;i<=n;i++){
    i++;
    j++;
}
```
消耗的时间是随着n的变化而变化的，因此这类代码都可以用O(n)来表示它的时间复杂度

线性对数阶O(nlogN)
```java
for(int i=0;i<n;i++){
    while(j<n){
        j++;
    }
}
```
将时间复杂度为O(logn)的代码循环N遍的话，那么它的时间复杂度就是 n * O(log(n))即O(nlog(n))
平方阶O(n²)
```java
for(int i=0;i<n;i++){
    for(int j=0;j<n;j++){
        m++;
    }
}
```
***
#### 冒泡排序（Bubble Sorting）
基本思想：通过对待排序序列从前向后（从下标较小的元素开始）,依次比较相邻元素的值，若发现逆序则交换，使值较大的元素逐渐从前移向后部
排序的过程中，各元素不断接近自己的位置，如果一趟比较下来没有进行过交换，就说明序列有序，因此要在排序过程中设置一个标志flag判断元素是否进行过交换。从而减少不必要的比较
```java
//冒泡 O(n^2)
private static void bubbleSort(int[] arr) {

    boolean flag;
    for (int i = 0; i < arr.length; i++) {
        flag=true;    //假定有序，若发生交换则表示无序
        //-i 表示依次循环后，排好序的数就有i个就不必在比较
        for (int j = 0; j < arr.length-1-i; j++) { 
            if (arr[j] > arr[j+1]) {
                int temp=arr[j];
                arr[j]=arr[j+1];
                arr[j+1]=temp;
                flag=false;
            }
        }
        if (flag){
            break;
        }
    }
}
```
***
#### 选择排序（select sorting）
基本思想是：第一次从arr[0]--arr[n-1]中选取最小值，与arr[0]交换，第二次从arr[1]--arr[n-1]中选取最小值，与arr[1]交换，第三次从arr[2]--arr[n-1]中选取最小值，与arr[2]交换，…，第i次从arr[i-1]--arr[n-1]中选取最小值，与arr[i-1]交换，…, 第n-1次从arr[n-2]--arr[n-1]中选取最小值，与arr[n-2]交换，总共通过n-1次，得到一个按排序码从小到大排列的有序序列。
```java
//选择排序
private static void choiceSort(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        int min=i;     //最小值对应的下标
        for (int j=i+1;j<arr.length;j++){
            if (arr[j]<arr[min]){
                min=j;
            }
        }
        int temp=arr[i];
        arr[i]=arr[min];
        arr[min]=temp;
    }
}
```
***
#### 插入排序（Insertion Sorting

基本思想是：把n个待排序的元素看成为一个有序表和一个无序表，开始时有序表中只包含一个元素，无序表中包含有n-1个元素，排序过程中每次从无序表中取出第一个元素
，把它的排序码依次与有序表元素的排序码进行比较，将它插入到有序表中的适当位置，使之成为新的有序表

```
/**
 *    1.[9,6,2,8,1]  将数据分为两部分，[9] [6,2,8,1]
 *    2.然后将后面部分数据，在前面数据中找到合适的位置插入
 */

private static void insertSort(int[] arr) {
    for (int i=1;i<arr.length;i++){
        int insertVal=arr[i];                //存储待插入的值
        int insertIndex=i;                   //假定要插入的索引就是自身的位置
        //循环找的合适的插入位置，结束后，insertIndex的值就是要插入的位置
        while (insertIndex>0 && insertVal<arr[insertIndex-1]){
            arr[insertIndex]=arr[insertIndex-1];
            insertIndex--;
        }
        if (insertIndex!=i){
            arr[insertIndex]=insertVal;
        }
    }
}
```
***
