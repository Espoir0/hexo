---
title: 链表
date: 2019-08-06
tags:
- 算法与数据结构
categories:
- 算法与数据结构
---
1. 单链表
2. 双向链表
3. 单链表环（约瑟夫环)
***
<!-- more -->
#### 单链表<br/>
单链表需要注意的几个方法就是:反转链表，逆序打印(用栈)，以及把两个链表合并为一个链表。
    
    bug警告:在根据索引index获取第index个链表元素的时候，注意不要直接返回结点对象，如果调用该方法并且对返回的
    结点进行了操作，将会改变链表本身的结构。不要返回对象的引用，应该返回新的结点。   
```java
//单链表
public class SingleLinkedList {
    private Node head =new Node(0,"");           //链表头结点,不存放任何数据

    //添加元素
    public void add(Node node){
        Node temp=head;
        while (temp.next!=null){
            temp=temp.next;
        }
        //循环结束后，temp指向最后一个结点，然后将temp.next指向要添加的node结点
        temp.next=node;
    }

    //根据id按照顺序添加元素
    public boolean addById(Node node){
        Node temp=head;

        while (true){
            //如果下个结点为空，或者下个结点的id大于要插入结点的id，
            // 表示要插入的结点应该插入temp结点的后面
            if(temp.next==null || temp.next.id>node.id){
                node.next=temp.next;
                temp.next=node;
                return true;//判断下个结点是否存在，如果不存在表示temp已经指向最后一个结点
            }else if(temp.next.id==node.id) {
                System.out.printf("id为%d的英雄已存在\n", node.id);
                return false;
            }
            temp = temp.next;
        }

    }

    //更新结点
    public void updateById(Node node){
        Node temp=head;
        boolean flag=false;

        if (temp.next==null){
            System.out.println("链表为空");
        }

        while (temp.next!=null){
            if (temp.next.id==node.id){
                temp.next.data=node.data;
                flag=true;      // 表示修改成功
                break;
            }
            temp=temp.next;
        }
        if (!flag){
            System.out.println("链表中不存在该元素");
        }
    }

    //根据id删除链表元素
    public Node delete(int id){
        Node temp=head;
        if (temp.next==null){
            System.out.println("链表为空");
        }
        while (temp.next!=null){
            if (temp.next.id==id){
                temp.next=temp.next.next;
                return temp.next;
            }
            temp=temp.next;
        }
        return null;

    }

    //统计链表有效结点个数，（不包括头结点）
    public int size(){
        int size=0;
        Node temp=head;
        while (temp.next!=null){
            size++;
            temp=temp.next;
        }
        return size;
    }

    //返回链表的倒数第 index 个结点
    public Node findlastNode(int index){
        int size=size();
        if (head.next==null || index<0 || index>size){
            return null;
        }
        Node temp=head.next;
        //8个元素，倒数第三个是顺数第六个
        for (int i=0;i < size-index;i++){
            temp=temp.next;
        }
        return temp;
    }

    //翻转链表元素，
    public void reverse(){
        //如果链表为空，或者只有一个结点，无需反转
        if (head.next==null || head.next.next==null){
            return ;
        }else{
            Node temp=head.next;             //在依次指向原链表循环中的结点
            Node newHead=new Node(0,"");
            Node next=null;

            //遍历，将原先链表的元素依次取出，插入到新链表的head后面
            while (temp!=null){
                next=temp.next;              //用来记录原链表中取出的结点的下一个结点
                temp.next=newHead.next;      //将取出的结点的下一个结点指向新链表表头指向的下一个结点
                newHead.next=temp;           //将当前结点放到新链表表头后面

                temp=next;                   //temp指向原链表的下个结点，想·
            }
            head.next=newHead.next;
        }
    }

    //输出链表元素
    public void show(){
        Node temp=head;
        while (temp.next!=null){
            System.out.println(temp.next);
            temp=temp.next;
        }
    }

    /**
     *    逆序输出链表元素,
     *    1.可以使用reverse()方法，将链表反转然后输出，但改变了链表自身的结构
     *    2.使用Stack
     */
    public void reversePrint(){
        Node temp=head.next;
        if(temp==null){
            return;
        }
        Stack<Node> stack = new Stack();
        while (temp!=null){
            stack.push(temp);
            temp=temp.next;
        }

        while (stack.size()>0){
            System.out.println(stack.pop());
        }
    }

    public Node getFirst(){
        Node temp=head.next;
        head.next=temp.next;
        return temp;
    }

    public Node getNode(int index){
        if (index<0 || index>size()){
            return null;
        }
        Node temp=head;
        for (int i=0;i<index;i++){
            temp=temp.next;
        }

        //注意，此处不能直接返回找到的结点，该节点是个对象，如果直接返回，返回的是对象，
        // 并且外面操作了该节点会改变链表结构
        Node node = new Node(0,"");
        node.id=temp.id;
        node.data=temp.data;
        return node;
    }

    //判断链表是否为空
    public boolean isEmpty(){
        return head.next==null;
    }
}

//链表结点
class Node{
    int id;
    String data;
    Node next;         //下个结点

    public Node(int id, String data) {
        this.id = id;
        this.data = data;
    }

    @Override
    public String toString() {
        return "Node{" + "id=" + id + ", data='" + data+ '}';
    }
}

class Test1 {
    /**    将两个链表合并为一个链表
     *     思路: 取出B链表中的元素，判断A链表中是否存在相同的元素，果然不存在则插入到A链表中
     */
    public static void merge(SingleLinkedList dstList,SingleLinkedList srcList){

        //list2.show();

        //遍历list2的每一个元素
        for (int i=0;i<srcList.size();i++){
            boolean flag=false;             //标识是否有相同元素
            Node srcNode = srcList.getNode(i+1);
            for (int j = 0;  j< dstList.size(); j++) {
                //list2.show();
                Node dstNode =dstList.getNode(j+1);
                //如果id有相同的，该元素不插入结束list1的遍历，进行下个元素的插入
                if (srcNode.id==dstNode.id){    
                    flag=true;            //标识有相同元素
                    break;
                }
            }
            if (!flag){                     //如果没相同元素则插入
                dstList.addById(srcNode);
            }
            //list2.show();
        }
    }

}
```

#### 双链表<br/>
```java
public class DoubleLinkedList {
    private Node2 head =new Node2(0,"");           //链表头结点,不存放任何数据

    //添加
    public void add(Node2 node){
        Node2 temp=head;
        while (temp.next!=null){
            temp=temp.next;
        }
        //循环结束后，temp指向最后一个结点，然后将temp.next指向要添加的node结点
        temp.next=node;
        node.pre=temp;
    }

    //根据id按顺序添加
    public boolean addById(Node2 node){
        Node2 temp=head;

        while (true){
            //如果下个结点为空，或者下个结点的id大于要插入结点的id，
            // 表示要插入的结点应该插入temp结点的后面
            if(temp.next==null ){
                node.next=temp.next;
                temp.next=node;
                node.pre=temp;
                return true;//判断下个结点是否存在，如果不存在表示temp已经指向最后一个结点
            }else if(temp.next.id==node.id) {
                System.out.printf("id为%d的英雄已存在\n", node.id);
                return false;
            }else if(temp.next.id>node.id){
                node.next=temp.next;
                node.next.pre=node;

                node.pre=temp;
                temp.next=node;
                return true;
            }
            temp = temp.next;
        }

    }


    //修改
    public void updateById(Node2 node){
        Node2 temp=head;
        boolean flag=false;

        if (temp.next==null){
            System.out.println("链表为空");
        }

        while (temp.next!=null){
            if (temp.next.id==node.id){
                temp.next.data=node.data;
                flag=true;      // 表示修改成功
                break;
            }
            temp=temp.next;
        }
        if (!flag){
            System.out.println("链表中不存在该元素");
        }
    }

    public Node2 delete(int id){
        Node2 temp=head.next;
        if (temp==null){
            System.out.println("链表为空");
        }
        while (temp!=null){
            if (temp.id==id){
                temp.pre.next=temp.next;
                if (temp.next!=null){//如果待删除的结点不是最后一个结点，则执行if块代码，因为最后一个结点的next为空
                    temp.next.pre=temp.pre;
                }
                return temp;
            }
            temp=temp.next;
        }
        return null;
    }

    @Override
    public String toString(){
        Node2 temp=head.next;
        StringBuffer result=new StringBuffer();
        while (temp!=null){
            result.append(temp+"\n");
            temp=temp.next;
        }

        return result.toString() ;
    }
}

//链表结点
class Node2{
    int id;
    String data;
    Node2 next;         //下个结点
    Node2 pre;          //上个结点

    public Node2(int id, String data) {
        this.id = id;
        this.data = data;
    }

    @Override
    public String toString() {
        return "Node2{" + "id=" + id + ", data='" + data+ '}';
    }
}

class TestDoubleLinkedList{
    public static void main(String[] args) {
        DoubleLinkedList list = new DoubleLinkedList();
        list.add(new Node2(1,"李白"));
        list.add(new Node2(7,"关羽"));
        list.addById(new Node2(9,"张飞"));
        list.addById(new Node2(4,"火舞"));
        list.addById(new Node2(8,"雅典娜"));
        list.addById(new Node2(2,"雅典娜"));
        list.addById(new Node2(6,"雅典娜"));

        System.out.println(list.toString());

        //list.updateById(new Node2(2,"武则天"));
        list.delete(1);
        System.out.println(list.toString());


    }
}
```

#### 单链表环<br/>
Josephu(约瑟夫环) 问题：设编号为1，2，… n的n个人围坐一圈，约定编号为k（1<=k<=n）的人从1开始报数，数到m 的那个人出列，它的下一位又从1开始报数，数到m的那个人又出列，依次类推，直到所有人出列为止，由此产生一个出队编号的序列。
解析：
* 用一个不带头结点的循环链表来处理Josephu,先构成一个有n个结点的单循环链表，然后由k结点起从1开始计数，计到m时，对应结点从链表中删除，然后再从被删除结点的下一个结点又从1开始计数，直到最后一个结点从链表中删除算法结束。
* 构造链表环，如果只有一个结点，next指向自身,构成环。结点数大于1，让结点的next指向下个结点即可，最后一个结点指向first
* 出圈的时候使用first标记第一个结点， last标记最后一个结点，
   * first 和last移动start次  ,表示从start位置开始计数
   * first 和last移动count次，表示计数count次
   * 出圈的时候
    `first=first.next;lastNode.next=first;`
    
```java
//环形单链表约瑟夫链表
public class CircleSingleLinkedList {
    private CNode first=null;         //第一个结点

    //构建环形链表
    public void init(int num){
        if (num<1){
            System.out.println("数量必须大于等于1");
            return;
        }

        CNode curNode =null;              //用来标记当前的结点
        for (int i = 1; i <= num; i++) {
            CNode node = new CNode(i);
            if (i==1){         //如果是第一个结点，next指向自身,构成环
                first=node;
                node.next=node;
                curNode=first;
            }else {         //1.当前结点.next=新结点  2.新结点.next=first  3.当前结点=新结点
                curNode.next=node;
                node.next=first;
                curNode=node;
            }
        }
    }

    public void show(){
        if (first==null){
            System.out.println("链表为空");
            return;
        }

        CNode curNode =first;             //表示当前结点

        while (curNode.next!=first){       //当前结点.next不是first的时候 ,执行循环
            System.out.println(curNode.id);
            curNode=curNode.next;
        }
        System.out.println(curNode.id);

    }

    /**    //根据用户的输入计算出小孩出圈的顺序
     *    start  从第start个结点数，
     *                         (last)* ---> *(first)
     *
     *                               *      *
     *    count   数几下,数到count的人出圈，下个人继续数
     *    num     最初的圈中孩子数量
     */
    public void order(int start ,int count ,int num ){
        if(start<1 || first==null || start>num){
            System.out.println("参数有误");
            return;
        }

        //标记出最后一个结点
        CNode lastNode=first;
        while (true){
            if (lastNode.next==first){       //当前结点指向最后一个结点
                break;
            }
            lastNode=lastNode.next;
        }

        //first 和last移动start次  ,表示从start位置开始计数
        for (int i=1;i<start;i++){
            first=first.next;
            lastNode=lastNode.next;
        }

        //开始数，数到count的人出圈
        while (lastNode!=first){
            for (int i = 0; i < count-1; i++) {
                first=first.next;
                lastNode=lastNode.next;
            }
            System.out.println("出圈"+first.id);

            first=first.next;
            lastNode.next=first;
        }
        System.out.println("最后一个出圈"+first.id);

    }
}
class CNode{
    int id;         //编号
    CNode next;     //指向下个结点

    public CNode(int id) {
        this.id = id;
    }

    @Override
    public String toString() {
        return "CNode{" +
                "id=" + id;
    }
}

class Test{
    public static void main(String[] args) {
        CircleSingleLinkedList clist = new CircleSingleLinkedList();
        clist.init(5);
        clist.show();
        clist.order(1,2,5);
    }
}

```
