# 前言

当我们聊到链表反转的时候，一定说的都是单链表，双链表本身就具有前驱指针 Prev 和后续指针 next，无需进行反转。

而且单链表反转看起来很简单，只需要将所有节点的 next 指向它的前驱节点即可。引入一个栈结构就可以

## 栈实现链表反转

在原本链表的数据结构之外，引入一个栈（数组也可以），将单链表循环遍历，将所有节点入栈，最后再从栈中循环出栈，得到的就是反转后的单链表。

![](.\栈_反转链表.jpeg)

会额外消耗一个栈的内存空间，此时Space complexity为O(n)。并且栈遇到的问题，使用此种方式都会遇到，比如栈的深度。

## Space complexity=O(1)的反转

在排序算法中，有一个概念叫：**原地排序**，指不需要引入额外的存储空间，在原数据结构基础上进行排序。比如：冒泡、插入排序等

原地单链表反转是一种很基础的算法，但是有时在面试时，一时半会也写不出来，容易出错的点在于：**指针丢失**。

当我们在对一个节点进行翻转时，需要知道三个节点：

+ 待反转节点
+ 待反转节点的前驱节点 prev
+ 待反转节点的后续节点 next

~~~java
// 直接可运行的
class Node {
    int data;
    Node next;
// 可以定义构造函数
    @Override
    public String toString() {
        return data + "->" + next;
    }
}

public class ReverseLinked {

    public static Node reverse(Node head) {
        if (head == null || head.next == null) {
            return head;
        }
        // 以下是核心代码
        Node preNode = null; // 定义一个虚拟头结点
        Node nextNode = null;  // 定义后续节点
        while (head != null) {
            nextNode = head.next;
            head.next = preNode;
            preNode = head;
            head = nextNode;
        }
        return preNode;
    }

    public static void main(String[] args) {
        // 初始化一个链表
        Node head = new Node();
        Node head1 = new Node();
        Node head2 = new Node();
        head.data = 1;head1.data = 2;head2.data = 3;
        head.next=head1;head1.next=head2;

        System.out.println("before:" + head.toString());
        Node after = ReverseLinked.reverse(head);
        System.out.println(ReverseLinked.reverse(head));
        System.out.println("after:" + after);
        
        /* 打印结果：
        	before:1->2->3->null
			1->null  ？？？为什么必须定义一个 Node 变量接受返回值？？？
			after:3->2->1->null
        */
    }
}

~~~

## 递归实现反转

~~~java
static Node reverseByRecursion(Node head){
    if(head == null || head.next == null){
        return head;
    }
    Node newHead = reverseByRecursion(head.next);
    head.next.next = head;
    head.next = null;
    return newHead;
}
~~~















