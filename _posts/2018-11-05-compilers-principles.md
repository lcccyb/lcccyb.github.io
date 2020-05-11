---
layout: post
title: 《编译原理》龙书笔记
date: 2018-11-5
categories: study
tags: 
  - 编译原理
---

# 第二章 一个简单的语法制导翻译器

## 2.1 引言

首先快速浏览一下编译器的前端模型，也就是词法分析，语法分析，中间代码生成的这三个过程。

源程序通过词法分析器得到了一个个的词法单元，之后这些词法单元通过语法分析器被构造成了一棵抽象语法树。之后传给中间代码生成器，会产生树或者三地址形式的中间形式表达。

## 2.2 语法定义

我们要介绍的是一种用于描述程序设计语言语法的表示方法——“上下文无关文法”。

#### 2.2.1 文法定义

一个上下文无关文法（context free grammar）由以下四个元素组成：

* 一个终结符号集合。终结符号是该文法所定义的语言的基本符号的集合。
* 一个非终结符号集合。每个非终结符号表示一个终结符号串的集合。
* 一个产生式集合。产生式的结构包括产生式头（左部）的非终结符号，一个箭头，和一个产生式体（右部）的由终结符号和非终结符号组成的序列。产生式主要用来描述一种非终结符号的结构体是如何被定义的，即一种非终结符号的书写方式。
* 指定一个非终结符号作为开始符号。


为了更好地理解上下文无关文法，我们在此举一个例子。

```
终结符号为 + - 0 1 2 3 4 5 6 7 8 9 
list → list + digit  
list → list - digit  
list → digit  
digit → 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9
```


上述例子说明了digit是一个非终结符号，这个非终结符号digit是由数字0123456789之中的某些单一数字组成的。而list这个非终结符号，是由另一个list 加上（注意这里只是表达式的加减，不会做出结果）digit 形成。根据开始符号的定义，我们还能看到这段文法的开始符号就是 list 。

零个终结符号组成的串成为空串，记为 ϵ .

#### 2.2.2 推导

我们可以根据文法推导所有可能生成的符号串，比如可以从开始符号出发，不断将某个非终结符号替换为该非终结符号产生式的体（右部），这样可以推导得到所有终结符号串能得到的集合。这一套集合被称为语言。

在语法分析（parsing）的过程中，我们的目的是接受一个终结符号串作为输入，找出从文法的开始符号推导出这个串的方法。如果不能从文法的开始符号推导得到该输入的终结符号串，则报告语法错误。

用上面例子中的一套定义的话，我们如果输入 9 - 5 + 2，编译器就会开始推导说这个表达式是一个list，所以没有语法错误。这个即是推导的作用。

#### 2.2.3 语法分析树

在上述从文法的开始符号推导到给定的终结符号串的过程中，我们可以用树状结构来简化（更好的描述）我们的推导过程。我们把具有以下性质的树称作是语法分析树：

* 根节点的标号是文法的开始符号
* 每个叶子节点的编号为一个终结符号或者是空串ϵ 
* 每个内部节点的标号都是一个非终结符号。
* 如果非终结符号A是某个内部节点的标号，并且它的子节点的标号从左到右分别为

$$
X_{1},X_{2},\cdots,X_{n}
$$

则必然有一个产生式是 
$$
A \rightarrow X_{1},X_{2}\cdots X_{n}
$$

#### 2.2.4 二义性

我们最终的目的总是要对编译器进行优化的，语法分析的优化也是其中重要的一部分。然而有一些优化并不是正确的。例如在上面digit list 的例子中，可能我们会想把它变成如下的形式： 

$$
str \rightarrow str \, + \, str \, | \, str \,-\, str \,|\,0\,|\,1\,|\,2\,|\,3\,|\,4\,|\,5\,|\,6\,|\,7\,|\,8\,|\,9
$$
看上去是一段不错的优化，然而很大的问题就是这里的语法表达式中存在着ambiguity，也就是二义性。

二义性意味着对于同样一段代码，我们可以用两棵语法树去表达他，并且得到不同的结果。比如说对于 9 - 5 + 2。

上面的两棵树都符合我们对于语法结构的要求，但是却得到了不同的结果，这种后果是毁灭性的。

![](https://img-blog.csdnimg.cn/20181105172633728.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xjY2N5Yg==,size_16,color_FFFFFF,t_70)

#### 2.2.5 运算符的结合性

出现上述问题的原因是因为运算符应该具有结合性。依照生活中的惯例，（9-5）+2 和 9-5+2 是等价的，其原因就是运算符“+”是左结合的。在绝大多数程序设计语言中，加减乘除四种算术运算符都是左结合的。

右结合的例子有赋值号 = ，比如说 a = b = c 等价于 a = (b = c) 而非 (a = b) = c。在这两种关系所对应的语法分析树中，左结合运算符往往往左下延伸，而右结合运算符是往右下延伸。

#### 2.2.6 运算符的优先级

优先级这种东西嘛，是个人类就知道该怎么做了。那么我们要考虑的是在编译器的学习中，如何根据优先级以及结合律的表格，构建出一套文法的生成式子。

这里我们就拿四则运算的表达式做一个例子好了。

首先记住，有多少个优先级，我们最好就建立多少个对应这些优先级的非终结符号。在四则运算中，我们用expr和term来对应加减和乘除的终结符号，并且用factor来表达基本单元。在正常计算中，表达式的基本单元应该为                                                        
$$
 factor→digit|(expr) 
$$
，剩下的应当为 
$$
\begin{align*}
term &\rightarrow term \times factor \\
&| \; term\,/\,factor \\
&| \; factor
\end{align*}
$$


$$
\begin{align*}
expr &\rightarrow expr + term\\
&| \; expr - term \\
&| \; term
\end{align*}
$$
注意在上述的表达式中，term 由 factor 组成，expr 再由 term 组成，这样就确保了优先级不会出现问题。

# 第3章 词法分析

## 3.1 词法分析器的作用

词法分析器的主要任务是读入源程序的输入字符、将它们组成词素，生成并输出一个词法单元序列。词法分析器还负责过滤掉源程序中的注释和空白，同时将编译器生成的错误消息与源程序的位置联系起来。如果源程序使用了一个宏预处理器，则宏的扩展也可以由词法分析器完成。

词法分析的三个相关但有区别的术语：

- 词法单元：由一个词法单元名和一个可选的属性值组成。
- 模式：描述了一个词法单元的词素可能具有的形式。
- 词素：源程序中的一个字符序列，与某个词法单元的模式匹配，并被词法分析器是被为词法单元的实例。

最简单的错误恢复策略是“恐慌模式”恢复，但是恢复技术会给语法分析器带来混乱。

## 3.2 输入缓冲

利用两个交替读入的缓冲区可以减少用于处理单个输入字符的时间开销。

在缓冲区末端加入“哨兵标志”字符EOF，可以将对缓冲区末端的测试和对当前字符的测试合二为一。

## 3.3 词法单元的规约

正则表达式是一种用来描述词素模式的重要表示方法。

**字母表(alphabet)**是一个有限的符号集合，包括字母、位数和标点符号。集合${0, 1}$是二进制字母表(binary alphabet)，ASCII是字母表的一个重要例子。

以字符为下标的地址来存放各个case的指令地址，可以实现多路分支，处理不同的字符。

**串(string)**是字母表中符号的一个有穷序列，串$s$的长度记作$|s|$。空串是长度为0的串，用$\epsilon$表示。**语言(language)**是某个给定字母表上一个任意的可数的串集合。

| 运算          | 定义和表示                      |
| ------------- | ------------------------------- |
| L和M的并      | $L \cup M = { s                 |
| L和M的连接    | $LM = { st                      |
| L的Kleene闭包 | $L^* = \cup_{i=0}^{\infty} L^i$ |
| L的正闭包     | $L^+ = \cup_{i=1}^{\infty} L^i$ |

假定r和s都是正则表达式，分别表示语言L(r)和L(s)，那么：

- $(r)|(s)$是一个正则表达式，表示语言$L(r) \cup L(s)$。
- $(r)(s)$是一个正则表达式，表示语言$L(r)L(s)$。
- $(r)^*$是一个正则表达式，表示语言$(L(r))^*$。
- $(r)$是一个正则表达式，表示语言$L(r)$。

在优先级上，`*`具有最高优先级，连接次之，`|`优先级最低，且三者均为左结合的。

可以用一个正则表达式定义的语言叫做**正则集合(regular set)**。

- `+`：一个或多个实例
- `?`：零个或一个实例
- `[abcd]`：字符类

## 3.4 词法单元的识别

**状态转换图(transition diagram)**有一组被称为“状态”(state)的结点或圆圈，边(edge)从图的一个状态指向另一个状态。某些状态称为接受状态或最终状态。

## 3.5 词法分析器生成工具Lex

Lex，也称为Flex，支持使用正则表达式来描述各个词法单元的模式，由此给出一个词法分析器的规约。Lex通常与Yacc一起使用。

一个Lex程序具有如下形式：

```
声明部分
%%
转换规则
%%
辅助函数
```

声明部分包括变量和明示常量(manifest constant)，每个转换规则有`模式 {动作}`的形式，动作部分是代码片段(为C或其它语言)。

声明部分有特殊的括号对`%{`和`%}`，出现在括号内的所有内容都被直接复制到文件`lex.yy.c`中，不会被当成正则定义处理。

**Lex中冲突的解决**：

- 总是选择最长的前缀。
- 如果最长的可能前缀与多个模式匹配，总是选择在Lex程序中先被列出的模式。

## 3.6 有穷自动机

Lex将输入程序变成一个词法分析器，转换的核心是被称为**有穷自动机(finite automata)**的表示方法。本质上有穷自动机与状态转换图类似，但又下列不同点：

- 有穷自动机是识别器(recognizer)，只能对输入串回答“是”或“否”。
- 有穷自动机分为**不确定的有穷自动机(Nondeterministic Finite Automata, NFA)**、**确定的有穷自动机(Deterministic Finite Automata, DFA)**。

可以将NFA表示为一张**转换表(transition table)**，表的各行对应于状态，各列对应于输入符号和$\epsilon$。

## 3.7 从正则表达式到自动机

**子集构造法**给出了一个直接模拟NFA的算法，其基本思想是让构造得到的DFA的每个状态对应于NFA的一个状态集合。

## 3.8 词法分析器生成工具的设计

模拟NFA运行的过程中，最终会到达一个没有后续状态的输入点。那时，不可能有任何更长的输入前缀使得这个NFA到达某个接受状态，此后的状态集将一直为空。

另一种体系结构，使用子集构造法将表示所有模式的NFA转换为等价的DFA。

## 3.9 基于DFA的模式匹配器的优化

如果一个NFA状态有一个标号非$\epsilon$的离开转换，那么称这个状态为**重要状态(important state)**。请注意，子集构造法在计算$\epsilon-closure(move(T,a))$的时候，它只使用了集合T的重要状态。也就是说，只有当状态s是重要的，状态集合$move(s,a)$才可能是非空的。

在子集构造法的应用过程中，两个NFA状态集合可以被认为是一致的，条件是：

- 具有相同的重要状态。
- 要么都包含接受状态，要么都不包含接受状态。