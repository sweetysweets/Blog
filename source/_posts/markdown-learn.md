---
title: markdown+Typora基本语法
date: 2018-10-02 17:25:26
tags:
---

最近决定每天认真写博客了，以前用markdown只会一小部分语法，今天下载了Typora，系统的记录一下这个软件中的markdown语法，以后写博客可以用得上～

首先备注一下反斜杠 \ 可做转义= =

输入`［toc］`然后按下`Enter`就会产生一个自动根据标题和标题等级自动创建的目录框。（所以标题不要乱搞啊）

[TOC]

### 段落和行间隔

段落，顾名思义就是由一行或多行文本组成的，以段为形式的结构。在Markdown语法中，段落间以一行以上的空行作分隔。在Typora中，按一下`Enter`就可以插入一个新的段落。

按`Shift`+`Enter`可以创建一个比段落间距更小的行间距。然而，大多数Markdown解析器会忽略这个方式创建的行间距，但是你可以通过在这一行的最后插入两个空格`Space`或者插入`<br/>`令解析器强制识别。

###标题系列

\# 一级标题 

\## 二级标题

\### 三级标题

\#### 四级标题

\##### 五级标题

\###### 六级标题

效果如下：

![image-20181002160209215](markdown-learn/image-20181002160209215.png)

#+空格+文字是标准的写法，写标题的时候不要忘了空格～

### 列表系列

`- 无序列表`  `1. 有序列表`  `- [ ] 未完成事项` `- [x] 已完成事项`

效果如下：

- list
- list
  - list
- list

1. list 
2. list

- [x] test
- [ ] list

### 代码系列  

\```java(此处写语法名称，可以根据它高亮)

代码块

```
效果如下：

​```java
/**
 * 该方法对数组求和
 * @param int数组
 * @return int和
 * @see  查看某个关联方法
 */
 public int sum(int[] arr){
	 int sum=0;
	  for(int i:arr){
		  sum+=i;
	  }
	  return sum;
 }
```

在行内显示代码则用\`代码\`即可，如：`代码`

### 引用系列

`>文字，引用内部也可以继续加引用`

什么时候用引用？

效果如下：

> 这是一段引用，换行依然会在引用中
>
> 叮叮～
>
> > 继续引用
>
> 引用完毕回车两下即可～

### 表格系列

输入`|标题一|标题二|`然后按下`Enter`将会创建一个有两个列的表格。

表格创建之后，你会看到一个顶部工具栏也会随之出现，通过工具栏你可以实现调整大小，增添和删除表格的功能，你也可以使用

下面的描述可以跳过，因为表格的源码语法是Typora自动生成的。

在markdown语法中，它们如下所示：

```text
| First Header  | Second Header |
| ------------- | ------------- |
| Content Cell  | Content Cell  |
| Content Cell  | Content Cell  |
```

效果如下：

| First Header | Second Header |
| ------------ | ------------- |
| Content Cell | Content Cell  |
| Content Cell | Content Cell  |

你也可以修饰内部的文本格式，比如链接、粗体、斜体、删除线等。

最后，通过使用冒号`：`你可以实现标题栏文字的对齐功能，比如向左对齐、向右对齐和居中对齐：

```text
| Left-Aligned  | Center Aligned  | Right Aligned |
| :------------ |:---------------:| -----:|
| col 3 is      | some wordy text | $1600 |
| col 2 is      | centered        |   $12 |
| zebra stripes | are neat        |    $1 |
```

效果如下：

| Left-Aligned  | Center Aligned  | Right Aligned |
| :------------ | :-------------: | ------------: |
| col 3 is      | some wordy text |         $1600 |
| col 2 is      |    centered     |           $12 |
| zebra stripes |    are neat     |            $1 |

最左侧的`：`是向左对齐；最右侧的`：`是向右对齐；两侧各一个`：`是居中对齐。

### 一些文字语法

`~~错误的文本~~`     ~~错误的文本~~注意是英文符号

`<u>下划线</u>` <u>下划线</u>

表情： `:cake:`  :cake:   

下标：`H~2~O` 和`X~long\ text~` 显示为 H~2~O 和X~long\ text~ 

上标：`X^2^` 显示为 X^2^ 

高亮： `==highlight==` 显示为 ==highlight==

强调：

`**两个乘号连用**`

**两个乘号连用**
__两个下划线连用也可，但不推荐__

斜体：

`*一个乘号*   _一个下划线_`

*一个乘号*  最好用*号，因为下划线在名字编码中常用。

