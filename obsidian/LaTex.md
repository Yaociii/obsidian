---
日期: 2026-08-05
tags:
  - obsidian
  - LaTex
  - latex
---
### 行内公式

行内公式通常使用`$..$`来输入，这通常被称为公式环境，例如：

```tex
若$a>0$, $b>0$, 则$a+b>0$.
```

公式环境通常使用特殊的字体，并且默认为斜体。需要注意的是，只要是公式，就需要放入公式环境中。如果需要在行内公式中展现出行间公式的效果，可以在前面加入`\displaystyle`，例如

```tex
设$\displaystyle\lim_{n\to\infty}x_n=x$.
```

### 行间公式

行间公式需要用`\[..\]`或者`$$..$$`来输入，推荐使用`\[..\]`，输入方式如下：

```tex
若$a>0$, $b>0$, 则
\[
a+b>0.
\]
```

关于具体的输入方式，可以参考[在线LaTeX公式编辑器-编辑器 (latexlive.com)](https://link.zhihu.com/?target=https%3A//www.latexlive.com/)，在这里只列举一些需要注意的。

### 上下标

上标可以用`^`输入，例如`a^n`，效果为  ；下标可以用`_`来输入，例如`a_1`，效果为  。上下标只会读取第一个字符，如果上下标的内容较多的话，需要改成`^{}`或`_{}`。

### 分式

分式可以用`\dfrac{}{}`来输入，例如`\dfrac{a}{b}`，效果为 $\dfrac{a}{b}$ 。为了在行间、分子、分母或者指数上输入较小的分式，可以改用`\frac{}{}`，例如`a^\frac{1}{n}`，效果为$a^\frac{1}{n}$  。

### 括号

括号可以直接用`(..)`输入，但是需要注意的是，有时候括号内的内容高度较大，需要改用`\left(..\right)`。例如`\left(1+\dfrac{1}{n}\right)^n`，效果是$\left(1+\dfrac{1}{n}\right)^n$。

在中间需要隔开时，可以用`\left(..\middle|..\right)`。

另外，输入大括号{}时需要用`\{..\}`，其中`\`起到了转义作用。

### 加粗

对于加粗的公式，建议使用`bm`宏包，并且用命令`\bm{}`来加粗，这可以保留公式的斜体，效果如$\bf{X}$,普通的则是X。

### 大括号

在这里可以使用`cases`环境，可以用于分段函数或者方程组，例如

```tex
$$
f(x)=\begin{cases}
    x, & x>0, \\
    -x, & x\leq 0.
\end{cases}
$$
```

效果为$$
f(x)=\begin{cases}
    x, & x>0, \\
    -x, & x\leq 0.
\end{cases}
$$


### 多行公式

多行公式通常使用`aligned`环境，例如

```tex
$$
\begin{aligned}
a & =b+c \\
& =d+e
\end{aligned}
$$
```

效果为
$$
\begin{aligned}
a & =b+c \\
& =d+e
\end{aligned}
$$


### 矩阵和行列式

矩阵可以用`bmatrix`环境和`pmatrix`环境，分别为方括号和圆括号，例如

```tex
$$
\begin{bmatrix}
    a & b \\
    c & d
\end{bmatrix}
$$
```

其效果如：
$$
\begin{bmatrix}
    a & b \\
    c & d
\end{bmatrix}
$$
如果要输入行列式的话，可以使用`vmatrix`环境，用法同上。


### 向量

向量可以用`\vec{}`输入，效果如$\vec{x}\quad\vec{P}$,不过$\vec{AB}$这种的箭头比较短，推荐使用粗体或使用`\ocerrightarrow{}`，效果如$\overrightarrow{AB}$ 
叉乘用`\times`，效果如$\vec{x}\times \vec{y}$
点乘用`\cdot`，效果如$\vec{x}\cdot \vec{y}$

### 行内间隔

通过`\quad`来分割同行公式，效果如$\vec{x}\quad\vec{d}$  
可以通过`\;`产生更小的间隔，效果如$\vec{x}\;\vec{d}$ 
两公式无其他间隔即可紧贴显示，效果如$\vec{x}\vec{d}$   

### LaTexSuite插件

这个插件大大简化了Latex的文本编辑，具体文档见[LaTexSuite](https://github.com/artisticat1/obsidian-latex-suite/blob/main/src/default_snippets.js)

常用命令
- mk->`$$`
- dm->`$$  $$`
在公式输入中:
- xvec->`$\vec{x}$`
- bmat->`$\begin{bmatrix}\end{bmatrix}$`
- bm->`$\mathbm{}`
- x/y+Tab->`$\frac{x}{y}$`  
