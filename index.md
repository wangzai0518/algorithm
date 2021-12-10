# 算法笔记

## 用递归逆序一个栈

```java
    public  static Integer getAndRemoveLast(Stack<Integer> stack){
        int value=stack.pop();
        if(stack.empty()) return value;
        Integer last=getAndRemoveLast(stack);
        stack.push(value);
        return last;
    }

    public static void reverse(Stack<Integer> stack){
        if(stack.empty())return ;
        int last=getAndRemoveLast(stack);
        reverse(stack);
        stack.push(last);
    }


    public static void main(String[] args) {
         Stack<Integer> stack=new Stack<>();
         stack.push(1);
         stack.push(2);
         stack.push(3);
         reverse(stack);
         for(Integer i:stack)
             System.out.println(i);
    }
```

- 分解为将栈底元素返回并删除栈底元素 和 逆序
- 为啥想到要分成两个函数呢？因为用递归逆序 那就是分解为n-1和1个的问题规模，比如从顶到底有4 3 2 1，结果为1 2 3 4，若要逆序，则可以变成让（4 3 2 ）逆序，然后把原本栈底1放到栈顶。**注意这里让（4 3 2）逆序，则需要先把1弹出来！**
- 接上，可不可以改成：把原本栈顶4放到栈底，再让（3,2,1）逆序？
- 递归函数逻辑：将栈底元素返回并删除栈底元素。首先肯定要先pop栈顶的，先写简单情况下return的就是last；然后利用递归得到last，最后再把栈顶元素push回去。注意这里仍然是按递归顺序push回去的，先pop后push,顺序没变。



## 用一个栈实现另一个栈的排序（递减）

弹出stack顶为cur:

help为空->cur压入help

help不空->

​	cur小->cur压入help

​	cur大->help弹出几个顶到stack，再把cur压入help

```java
    public static void sortStackByStack1(Stack<Integer> stack) {
        Stack<Integer> help=new Stack<>();
        while(!stack.empty())
        {
            Integer cur = stack.pop();
            while(!help.empty()&&cur>help.peek())
                stack.push(help.pop());
            help.push(cur);
        }
        while(!help.empty())stack.push(help.pop());
    }
```

- help是递增的，cur比help栈顶更小则直接插入，cur更大则在help中找到插入位置，前面被挪位的help几个顶暂时先放在stack中，反正当前help栈顶就是刚插入的cur，这几个都小于等于cur会直接插入help。
- 最后将递增的help传给stack变成递减。

## 生成窗口最大值数组

思路：双端队列qmax存放下标，队头放最大值，元素从队尾添加。arr[i]比队尾更大证明队尾这个元素对当前窗口值绝对没用，因为i肯定比队尾的更新，所以队尾直接弹；arr[i]更小可能后面会用到，放进去。队列为空直接放

### 逻辑

![image-20211207202729043](C:\Users\white\AppData\Roaming\Typora\typora-user-images\image-20211207202729043.png)

空->添加

非空->

​	1.arr[i]比较大->弹出并回到最初判断

​	2.**！**arr[i]比较大->添加

### 代码

while(非空&&arr[i]比较大) 

​	弹出

添加

```java
    public static int[] window(int []arr,int w){
        if(arr==null || w<1 || arr.length<w)return null;
        int []res=new int [arr.length-w+1];
        int index=0;
        LinkedList<Integer> qmax=new LinkedList<>();
        for(int i=0;i<arr.length;i++)
        {
            while(!qmax.isEmpty()&& arr[qmax.peekLast()]<arr[i])
                qmax.pollLast();
            qmax.addLast(i);
            if(qmax.peekFirst()+w==i)//队头过期
                qmax.pollFirst();
            if(i>=w-1)//从下标w-1开始正好是第一个窗口，此后i每一步都会有一个窗口值
                res[index++]=arr[qmax.peekFirst()];
        }
        return res;
    }
```

## 单调栈

找数组中每个元素左边最近的比它小的值和右边最近的比它小的值

### 思路

栈**递减**，若新元素加入后仍满足递减则放入，否则弹出栈顶元素并确认栈顶元素的二值。

### 逻辑

对当前拿到的arr[i]:

若栈空，则arr[i]进新结点

若栈不空：

​	若arr[i]>栈顶，则arr[i]进新结点

​	若arr[i]==栈顶，则arr[i]加入栈顶的list中

​	若arr[i]<栈顶，则栈顶弹出并确定栈顶的二值。再**继续之前判断**（while）

### 代码

while(栈不空&&arr[i]<栈顶) 

//从while出来了 这个里面的条件就不满足了！所以只要考虑其他情况

​	{...}

if(栈不空&&arr[i]==栈顶)

​	arr[i]加入栈顶的list中

else{

​	arr[i]进新结点

}

```java
 public static int[][] getNearLess(int[] arr){
        int[][]res=new int[arr.length][2];
        Stack<List<Integer>> stack=new Stack<>();
        for(int i=0;i<arr.length;i++){
            while(!stack.empty() && arr[i]<arr[stack.peek().get(0)])
            {
                List<Integer> popIs = stack.pop();
                int leftLessIndex=stack.empty()?-1:stack.peek().get(stack.peek().size()-1);
                for(Integer popI:popIs){
                    res[popI][0]=leftLessIndex;
                    res[popI][1]=i;
                }
            }
            if(!stack.empty() && arr[i]==arr[stack.peek().get(0)])
            {
                stack.peek().add(i);
            }
            else{
                List<Integer> list=new ArrayList<>();
                list.add(i);
                stack.push(list);
            }
        }//end of for
        while(!stack.empty())
        {
            List<Integer> popIs = stack.pop();
            int leftLessIndex=stack.empty()?-1:stack.peek().get(stack.peek().size()-1);
            for(Integer popI:popIs){
                res[popI][0]=leftLessIndex;
                res[popI][1]=-1;
            }

        }
        return res;
    }
```



## 字典序最大的子序列

**单调栈**简单版 from腾讯21.9笔试

给定字符串str和正数k，返回长度为k的所有子序列中字典序最大的子序列

### 思路

栈里保存字典序最大的，那栈就是递增的，新来的如果更大，只要剩的够长就踢掉

### 逻辑

对当前str[i]:

若栈空，则str[i]进栈

若栈不空：

​	若str[i]<=栈顶，则str[i]进栈

​	若str[i]>栈顶，则

​		若栈元素个数+n-i>k，则栈顶弹出，继续最初判断

​		若栈元素个数+n-i<=k，则从栈底输出栈+输出i开始的元素

### 代码

while(栈不空&&str[i]>栈顶&&栈元素个数+n-i>k)//n-i是包括i在内往右的元素个数

​	{栈顶弹出}

if(栈不空&&str[i]>栈顶&&栈元素个数+n-i<=k)

​	{return 栈底输出栈+输出i开始的元素 }

else{

stack[size++]=str[i]

}

```java
    public static String maxString(String s,int k){
        if(k<=0||s.length()<k)return "";
        char[]str=s.toCharArray();
        int n=str.length;
        char[] stack=new char[n];
        int size=0;
        for(int i=0;i<n;i++){
            while(size>0 && str[i]>stack[size-1] && size+n-i>k)
                size--;
            if(size>0 && str[i]>stack[size-1] && size+n-i<=k)
            {
                return String.valueOf(stack,0,size)+s.substring(i);
            }
            else{
                stack[size++]=str[i];
            }
        }
        return String.valueOf(stack,0,k);
    }
```

## 链表

## 打印两个有序链表的公共部分

```java
    public static void printCommonPart(Node head1,Node head2){
        while(head1!=null && head2!=null)
        {
            if(head1.value<head2.value)
                head1=head1.next;
            else if(head1.value>head2.value)
                head2=head2.next;
            else{
                System.out.println(head1.value+" ");
                head1=head1.next;
                head2=head2.next;
            }
        }
    }
```

- 默认升序



## 约瑟夫环

### 思路

不断“杀头”

### 代码逻辑

头要删除->尾下一个接头下一个，尾还是尾不动；头后移，这种后移也可表示为尾的下一个

头不要删除->头和尾都后移

由上，头后移可以合并

```java
if(++cnt==m)
{
    last.next=head.next;//去头
    cnt=0;
}
else
    last=last.next;
head=head.next;//右边也可以写成last.next
```

```java
    public static Node josephusKill(Node head,int m){
        Node last=head;
        Node p=head;
        while(last.next!=head)
            last=last.next;
        int cnt=0;//p指向的head初始序号为1
        while(head!=last)
        {
            if(++cnt==m)
            {
                last.next=head.next;//去头
                cnt=0;
            }
            else
                last=last.next;
            head=head.next;//右边也可以写成last.next

        }
        return head;
    }
```

## L1 两数之和

给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

### 解1 暴力

```java
public static int[] sumOfTwo(int[] nums,int target){
    int[] res=new int[2];
    for(int i=0;i<nums.length;i++)
    {
        for(int j=i+1;j<nums.length;j++)
            if(nums[i]+nums[j]==target)
            {
                res[0]=i;
                res[1]=j;
                return res;
            }
    }
    res[0]=-1;
    res[1]=-1;
    return res;
}
```



### 解2 哈希表法

```java
    public static int[] sumOfTwoHash(int[] nums,int target){
        int[] res=new int[2];
        HashMap<Integer,Integer> map=new HashMap<>();//value:index
        for(int i=0;i<nums.length;i++)
            map.put(nums[i],i);
        for(int i=0;i<nums.length;i++)
        {
            if(map.containsKey(target-nums[i]) && i!=map.get(target-nums[i]))
            {
                res[0]=i;
                res[1]=map.get(target-nums[i]);
                return res;
            }
        }
        res[0]=-1;
        res[1]=-1;
        return res;
    }
```

- 分析暴力法，最慢的地方在于对每个nums[i]，要找往后的所有值，即**每一趟是O(n)**。以（value,index）的哈希形式，找value的时间变为O(1)，**每一趟是O(1)**

**哈希表demo：**

```java
        HashMap<String,String> Map=new HashMap<>();
        Map.put("1","1st");Map.put("2","2nd");Map.put("3","3rd");
        Collection c1=Map.values();
        Iterator it=c1.iterator();//该句和上句决定对值遍历，也可改成对keySet遍历
        while(it.hasNext())
            System.out.println(it.next());
```



## L2 两数相加

给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```

#### 解1 递推法

##### 思路

设头结点，最后返回头结点.next即可。再设一个尾指针保存每一位的结果

l1和l2都非空->

​	对当前l1和l2：

​		res=值相加+进位

​		next1=res%10

l1不空->(l2同理)

​	对当前l1:

​		res=l1.v+进位

​		next1=res%10

l1和l2都空但进位不空->

​	再加个1结点

##### 代码

```java
public static ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head=new ListNode();
        ListNode r=head;
        int res=0;
        int next1=0;
        while(l1!=null && l2!=null)
        {
            res=l1.val+l2.val+next1;
            next1=res/10;
            res=res%10;
            ListNode node=new ListNode(res);
            node.next=null;
            r.next=node;
            r=node;
            l1=l1.next;
            l2=l2.next;
        }

        while(l1!=null){
            res=l1.val+next1;
            next1=res/10;
            res=res%10;
            ListNode node=new ListNode(res);
            node.next=null;
            r.next=node;
            r=node;
            l1=l1.next;
        }
        while(l2!=null){
            res=l2.val+next1;
            next1=res/10;
            res=res%10;
            ListNode node=new ListNode(res);
            node.next=null;
            r.next=node;
            r=node;
            l2=l2.next;
        }
        if(l1==null && l2==null && next1!=0)
        {
            ListNode node=new ListNode(1);
            node.next=null;
            r.next=node;
            r=node;
        }
        return head.next;
    }
```

- 注意分支：l1和l2都空但进位不空在最后判断！放在前面就是无效的

#### 解2 递归法

