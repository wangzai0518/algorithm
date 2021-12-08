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

对当前str[i]://下标i右边还有n-i-1个元素

若栈空，则str[i]进栈

若栈不空：

​	若str[i]<=栈顶，则str[i]进栈

​	若str[i]>栈顶，则

​		若栈元素个数+n-i>k，则栈顶弹出，继续最初判断

​		若栈元素个数+n-i<=k，则从栈底输出栈+输出i开始的元素

### 代码

while(栈不空&&str[i]>栈顶&&栈元素个数+n-i>k)

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


