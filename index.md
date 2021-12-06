## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/wangzai0518/algorithm.github.io/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/wangzai0518/algorithm.github.io/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.


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

一个更容易理解的版本：

```java
    public static void sortStackByStack(Stack<Integer> stack){
        Stack<Integer> help=new Stack<>();
        while(!stack.empty())
        {
            Integer cur = stack.pop();
            if(help.empty())
                help.push(cur);
            else{
             if(cur<=help.peek())
                 help.push(cur);
             else{//cur>help 把cur插到递增的help里
                 while(!help.empty()&&cur>help.peek())
                     stack.push(help.pop());
                 help.push(cur);
             }
            }
        }
        while(!help.empty())
            stack.push(help.pop());
    }
```

- help是递增的，cur比help栈顶更小则直接插入，cur更大则在help中找到插入位置，前面被挪位的help几个顶暂时先放在stack中，反正当前help栈顶就是刚插入的cur，这几个都小于等于cur会直接插入help。
- 最后将递增的help传给stack变成递减。
