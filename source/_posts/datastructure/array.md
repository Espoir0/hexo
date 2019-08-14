---
title: 数组实现队列，及稀疏矩阵的应用
date: 2019-08-03
tags:
- 队列 稀疏矩阵
categories:
- 算法与数据结构
---
1. 数组实现队列
2. 数组实现环形队列
3. 稀疏矩阵的应用
***
<!-- more -->
#### 稀疏矩阵的应用
基本介绍<br/>
当一个数组中大部分元素为0，或者为同一个值时，可以使用稀疏数组来保存该数组。

稀疏数组的处理方法是:
1. 记录数组一共有几行几列，有多少个不同的值
2. 把具有不同值的元素的行列及值记录在一个小规模的数组中，从而缩小程序的规模
```java
/**
 * 使用稀疏数组存储棋盘数据存入文件和从文件中读取数据，
 */
public class sparseArray {

    public static void main(String[] args) throws FileNotFoundException {
        int chessArr[][] = new int[11][11];           //创建一个11*11数组模拟棋盘

        chessArr[1][2] = 1;                           //0表示没有棋子，1表示黑子，2表示白子
        chessArr[2][3] = 2;
        toSparseArr(chessArr);
        toChessArr();
    }


    //普通数组转为稀疏数组
    public static void toSparseArr(int[][] chessArr) throws FileNotFoundException {
        //遍历数组统计非0数字个数
        int sum = 0;
        for (int[] row : chessArr) {
            for (int data : row) {
                if (data != 0) sum++;
            }
        }

        //根据有效数字构建稀疏数组 [sum][value]
        int[][] sparseArr = new int[sum + 1][3];
        sparseArr[0][0] = 11;
        sparseArr[0][1] = 11;
        sparseArr[0][2] = sum;

        int count = 0;
        for (int i = 0; i < chessArr.length; i++) {
            for (int j = 0; j < chessArr.length; j++) {
                if (chessArr[i][j] != 0) {
                    count++;
                    sparseArr[count][0] = i;
                    sparseArr[count][1] = j;
                    sparseArr[count][2] = chessArr[i][j];
                }
            }
        }

        StringBuffer sb = new StringBuffer();
        for (int[] data : sparseArr) {
            sb.append(data[0]+","+data[1]+","+data[2]+"\n");
        }

        //将转化后的稀疏数组存入文件，实现存档
        try (
                FileWriter fw = new FileWriter("E://code/test.txt")
        ){
            fw.write(sb.toString());
        } catch (IOException e) {
            e.printStackTrace();
        }
        print(sparseArr);
        System.out.println("数据存盘成功");
    }

    //从文件中读取数据转为数组，实现数据恢复，
    public static void toChessArr(){
        try (
                BufferedReader br = new BufferedReader(new FileReader("E://code/test.txt"))
        ){
            //根据读取数据的第一行，创建棋盘对应的数组
            String data = br.readLine();
            String[] datas = data.split(",");
            int row = Integer.parseInt(datas[0]);
            int col = Integer.parseInt(datas[1]);
            int[][] chessArr = new int[row][col];

            while ((data=br.readLine())!=null){
                datas = data.split(",");
                row = Integer.parseInt(datas[0]);
                col = Integer.parseInt(datas[1]);
                chessArr[row][col] = Integer.parseInt(datas[2]);
            }
            System.out.println("数据读取成功");
            print(chessArr);

        }catch (IOException e) {
            e.printStackTrace();
        }


    }

    public static void print(int[][] arr){
        for (int[] row : arr) {
            for (int i = 0; i < row.length; i++) {
                System.out.print(row[i]+" ");
            }
            System.out.println();
        }
        System.out.println("****************我是分割线**********************************");
    }
}
```

#### 普通数组模拟队列及数组模拟环形队列
##### 数组模拟队列
```java
/**
 * 数组模拟队列
 */
public class ArrayQueue {
    private int front;      //front指向队列第一个元素的前面位置
    private int rear;       //rear指向队列尾部的位置
    private int maxsize;
    private int[] arr;      //该数组用于存储数据

    public ArrayQueue(int maxsize){
        this.maxsize=maxsize;
        this.front=-1;
        this.rear=-1;
        this.arr=new int[maxsize];        //根据maxsize创建数组
    }

    //判断队列是否空
    public boolean isEmpty(){
        return front==rear;
    }

    //判断是否为满
    public boolean isFull(){
        return rear==maxsize-1;
    }

    //将元素添加到队列尾部
    public void addElement(int element){
        if (isFull()){
            System.out.println("队列已满");
            return;
        }
        arr[++rear]=element;
    }

    //将对列头数据返回
    public int getElement(){
        if(isEmpty()){
            throw new RuntimeException("队列为空");
        }
        return arr[++front];
    }

    //显示队列的所有数据
    public void showQueue(){
        if (isEmpty()){
            System.out.println("队列为空");
            return;
        }
        for (int i = 0; i < arr.length; i++) {
            System.out.println(arr[i]+" ");
        }
    }

    //查看队列头的元素
    public int showFrontElement(){
        if (isEmpty()){
            throw new RuntimeException("队列为空");
        }
        return arr[++front];
    }
}

```

```java
/**
 * 数组模拟环形队列
 */
public class CircleArrayQueue {
    private int front;      //front指向队列第一个元素的位置
    private int rear;       //rear指向队列尾部的后面一个位置，
    private int maxsize;    //有效容量是 maxsize-1 ，因为判定队列满的条件是(rear+1)%maxsize==front;
    private int[] arr;      //该数组用于存储数据

    public CircleArrayQueue(int maxsize){
        this.maxsize=maxsize;
        this.front=0;
        this.rear=0;
        this.arr=new int[maxsize];        //根据maxsize创建数组
    }

    //判断队列是否空
    public boolean isEmpty(){
        return front==rear;
    }

    //判断是否为满
    public boolean isFull(){
        return (rear+1)%maxsize==front;
    }

    //将元素添加到队列尾部
    public void addElement(int element){
        if (isFull()){
            System.out.println("队列已满");
            return;
        }
        arr[rear]=element;     //此时的rear指向队尾的后面一个位置，故可以直接添加
        rear = (rear+1)%maxsize;    //rear后移
    }

    //将对列头数据返回
    public int getElement(){
        if(isEmpty()){
            throw new RuntimeException("队列为空");
        }
        int temp=arr[front];     //临时变量记录要取出的值
        front= (front+1)%maxsize;
        return temp;
    }

    //显示队列的所有数据
    public void showQueue(){
        if (isEmpty()){
            System.out.println("队列为空");
            return;
        }
        for (int i = front; i < front+size(); i++) {
            System.out.print(arr[i%maxsize]+" ");  //i=front+size（）i值可能大于数组的容量，所以要取模
        }
    }

    //当前队列的有效个数
    public int size(){
        return (rear+maxsize-front)%maxsize;
    }

    //查看队列头的元素
    public int showFrontElement(){
        if (isEmpty()){
            throw new RuntimeException("队列为空");
        }
        return arr[front];
    }
}

```
##### 数组模拟环形队列
```java
/**
 * 数组模拟环形队列
 */
public class CircleArrayQueue {
    private int front;      //front指向队列第一个元素的位置
    private int rear;       //rear指向队列尾部的后面一个位置，
    private int maxsize;    //有效容量是 maxsize-1 ，因为判定队列满的条件是(rear+1)%maxsize==front;
    private int[] arr;      //该数组用于存储数据

    public CircleArrayQueue(int maxsize){
        this.maxsize=maxsize;
        this.front=0;
        this.rear=0;
        this.arr=new int[maxsize];        //根据maxsize创建数组
    }

    //判断队列是否空
    public boolean isEmpty(){
        return front==rear;
    }

    //判断是否为满
    public boolean isFull(){
        return (rear+1)%maxsize==front;
    }

    //将元素添加到队列尾部
    public void addElement(int element){
        if (isFull()){
            System.out.println("队列已满");
            return;
        }
        arr[rear]=element;     //此时的rear指向队尾的后面一个位置，故可以直接添加
        rear = (rear+1)%maxsize;    //rear后移
    }

    //将对列头数据返回
    public int getElement(){
        if(isEmpty()){
            throw new RuntimeException("队列为空");
        }
        int temp=arr[front];     //临时变量记录要取出的值
        front= (front+1)%maxsize;
        return temp;
    }

    //显示队列的所有数据
    public void showQueue(){
        if (isEmpty()){
            System.out.println("队列为空");
            return;
        }
        for (int i = front; i < front+size(); i++) {
            System.out.print(arr[i%maxsize]+" ");  //i=front+size（）i值可能大于数组的容量，所以要取模
        }
    }

    //当前队列的有效个数
    public int size(){
        return (rear+maxsize-front)%maxsize;
    }

    //查看队列头的元素
    public int showFrontElement(){
        if (isEmpty()){
            throw new RuntimeException("队列为空");
        }
        return arr[front];
    }
}

```
##### 测试类
```java
//测试类
public class Test {
    public static void main(String[] args) {
        CircleArrayQueue queue=new CircleArrayQueue(4);  //此时的有效容量为3
        //ArrayQueue queue = new ArrayQueue(3);
        Scanner sc = new Scanner(System.in);
        boolean flag=true;
        System.out.println("请输入下列任意命令,输入首字母即可:");
        System.out.println("h (head)查看队列头部元素 ");
        System.out.println("a (add) 添加元素到队尾 ");
        System.out.println("g (get) 获取队列第一个元素");
        System.out.println("s (show) 查看队列所有元素");
        System.out.println("e (exit) 退出程序");
        while (flag){
            System.out.println("请输入命令： h a g s e");
            char cmd = sc.next().charAt(0);
            switch (cmd){
                case 'h': {
                    try {
                        queue.showFrontElement();
                    } catch (Exception e) {
                        System.out.println(e.getMessage());
                    }
                    break;
                }
                case 'a':{
                    int element = sc.nextInt();
                    queue.addElement(element);
                    break;
                }
                case 'g':{
                    try{
                        System.out.println(queue.getElement());
                    }catch (Exception e){
                        System.out.println(e.getMessage());
                    }
                    break;
                }
                case 's':{
                    try{
                        queue.showQueue();
                    }catch (Exception e){
                        System.out.println(e.getMessage());
                    }
                    break;
                }
                case 'e' : {
                    flag=false;
                    System.out.println("退出程序");
                    break;
                }default:{
                    break;
                }
            }
        }
    }
}

```
