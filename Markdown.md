Markdown是一种轻量级标记语言，它允许人们使用易读易写的纯文本编写文档。
Markdown编写的文档可以到处HTML、Word、图像、PDF、Epub等多种格式的文档。
Markdown编写的文档后缀为.md，.markdown。


Markdown 应用
1、Markdown能被用来撰写电子书，如：gitbook。
2、当前许多网站都广泛使用Markdown来撰写帮助文档或用于论坛上发表消息。例如：github、简书、reddit、等。

编辑器
1、本教程使用Typora编辑器来讲解Markdown的语法，typora支持MacOs、Windows、Linux平台，且包含多种主题，编辑后直接渲染出效果。
2、支持到处HTML、PDF、Word、图片等多种类型文件。



## Markdown标题
1、使用=和-标记一级和二级标题

例如：
我展示的是一级标题
=================

我展示的是二级标题
-----------------

## 2、使用#标记
使用#号可以表示1-6级标题，一级标题#号，以此类推。

# 一级标题
## 二级标题
### 三级标题

#### 四级标题
##### 五级标题

###### 六级标题



​      

Markdown段落

markdown段落没有特殊的格式，直接编写文字就可以，段落的换行就是使用两个以上的空格加上回车。

当然也可以在段落后面使用空行表示重新开始一个段落

   

## 字体
markdown可以使用以下几种字体：
*斜体文本*
_科技文本_
**粗体文本**
__粗体文本__
***粗斜体文本***
___粗斜体文本___



## 分隔线
你可以在一行中用三个以上的星号、减号、底线来建立一个分隔线，行内不能有其他东西。你也可以在星号或是减号中间插入空格。下面每种写法都可以建立分隔线：

***

* * *

******

---

- - -
---------

## 删除线
如果段落上的文字要添加删除线，只要在文字的两段加上两个波浪线~~即可，示例如下：
今天是个好日子
~~今天不是个好日子~~



## 下划线
下划线可以通过HTML的u 标签来实现

<u>带下划线文本</u>



## 脚注
脚注是对文本的补充说明。
markdown 脚注的格式如下

创建脚注格式类似这样 [^Runoob]。
这个是脚注2[^避风塘]
[^Runoob]:学的不仅仅是技术，更是梦想！！
[^避风塘]:在浙江省

## Markdown列表

markdown支持有序列表和无序列表。
无序列表使用星号（*）、加号（+）或是减号（-）作为列表标记，这些标记后面要添加一个空格，然后在填写内容：

* 第一项
* 第二项
* 第三项

+ 第六项
+ 第七项

- 第八项
  
- 第九项


有序号列表使用数字并加上.号来表示，如:
1.第一项
2.第二项
4.第四项



## 列表嵌套
列表嵌套只需要在字列表中的选项面前添加四个空格即可

1.第一项：
 - 第一项嵌套的第一个元素
 - 第一项嵌套的第二个元素

2.第二项:
 - 第二项嵌套的第一个元素
 - 第二项嵌套的第二个元素

---

1.第一项：
  -  第一项嵌套的第一个元素
  -  第一项嵌套的第二个元素

2.第二项:
  -  第二项嵌套的第一个元素
  -  第二项嵌套的第二个元素

## Markdown 区块
Markdown区块引用是在段落开头使用> 符号，然后后面紧紧跟一个空格：

> 区块引用
> 菜鸟教程
> 学习的不仅仅知识，
更是梦想！

另外区块是可以嵌套的，一个> 符号是最外层，两个>>符号是第一层嵌套，以此类推：
> 最外层
> > 第一层嵌套
> >  我的家庭
> >
> > > 第三层嵌套



## 区块中使用列表：
区块中使用列表实例如下：

> 区块中使用列表
> 1.第一项
> 2.第二项
>
> + 第一项
> + 第二项
> + 第三项


##列表中使用区块
如果要在列表项目内让进区块，那么就需要在> 前面添加四个空格的缩进。
区块中使用列表实例如下：
* 第一项
    > 菜鸟教程
    > 学的不仅仅是技术，
    更是梦想
* 第二项



##Markdown代码
如果是段落上的一个函数或片段的代码可以用反引号（'）把它抱起来，例如：

`printf()`窗口

## `代码区块`

代码区块使用4个空格或者一个制表符（tab键）。

示例如下：


```csharp
public class User{
  public User(){
  Console.writeLine();
  }
}
```

    <?php
    echo 'RUNOOB';
    function tewst(){
      echo 'test'
    }
    >

```java
public class Employ
  {
    public Employ()
    {
      System.out.println("jifei");
    }
  }
```

## Markdown 链接
链接使用方法如下：
[链接名称](链接地址)
或者
<链接地址>
例如：
[百度一下你就知道](www.baidu.com)

<https://www.zhcw.com>


##高级链接
我们可以通过变量来设置一个链接，变量赋值在文档末尾进行:
这个连接用 1 作为网址变量 [百度][1]
这个链接用 runoob 作为网址变量 [中彩网][runoob]
然后在文档的结尾为变量赋值(网址)，以下必须有一个空行

[1]:http://www.www.baidu.com/
[runoob]:htp://www.zhcw.com/


## Markdown图片
Markdown图片语法格式如下：

![属性文本](图片地址)
![属性文本](图片地址 "可选标题")
![图片的替代文字](d:/project/Learn/0.png "git 合并")


![不知道](http://static.runoob.com/images/runoob-logo.png "RUNOOB")


1、开头一个感叹号!
2、接着一个方括号，里面放上图片的替代文字
3、接着一个普通括号，里面放上图片的网址，最后还可以一弄宝珠并加上选择性的"title"属性文字。

当然，你也可以向网址那样对图片网址使用变量:
这个链接用1作为网址变量[git 分支合并][1]

[1]: d:/project/Learn/0.png


Markdown 还没有办法指定图片的高度与宽度，如果你需要的话，可以使用\<img>标签。


<img src="http://static.runoob.com/images/runoob-logo.png" width="400">


## Markdown表格

Markdown制作表格使用"|"来分隔不同的单元格，使用"-"来分隔表头和其他行。
语法如下：

|表头|表头|
|----|----|
|单元格|单元格|
|单元格|单元格|

我们可以设置表格的对其方式:
1、 -:  设置内容和标题栏居右对齐。
2、 :-  设置内容和标题栏居左对齐。
3、 :-: 设置内容和标题栏居中对齐。

实例如下:

|名称|合同编号|借款本金|
|:----|:----:|----:|
|大理同人资产运营有限公司|1003040971|473,125.48|
|大理经开投集团公司|1004010110|5,545,510.19|



## Markdown 高级技巧
### 支持的HTML元素
不在Markdown涵盖范围之内的标签，都可以直接在文档里面使用HTML撰写。
目前支持的HTML元素有:\<kbd>\<b>\<i>\<em>\<sup>\<br>等，如:

使用<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Del</kbd>重启电脑


### 转义
Markdown使用了很多特殊符号来表示特定的意义，如果需要显示特定的符号泽需要使用转义字符，Markdown使用反斜杠转义特殊字符:

**文本加粗**
\*\*文本加粗\*\*

Markdown支持以下这些符号前面加上反斜杠来帮助插入普通的符号:

|符号|说明|
|:----:|:----:|
|\\|反斜线|
|\-|反引号|
|\*|星号|
|\_|下划线|
|\{ }|花括号|
|\[ ]|方括号|
|\( )|小括号|
|\#|井字符|
|\+|加号|
|\-|减号|
|\.|英文句点|
|\!|感叹号|

<br>
<hr>

# 数学基本语法

## 公式
当你需要在编辑器中插入数学公式时，可以使用两个美元符号\$$ 包裹TeX或LaTeX格式的数学公式实现。提交后，问答和文章会根据需要加载Mathjax对数学公式进行渲染。如:

$$
 \mathbf{v}_1 \times \mathbf{v}_2 = \begin{vmarix}
 \mathbf{i} & \mathbf{j} & \mathbf{k} \\
 \frac{\partial X}{\partial u} & \frac{\partial Y}{\partial u} & 0 \\
 \frac{\partial X}{\partial v} & \frac{\partial Y}{\partial v} & 0 \\
 \end{vmatrix}
 ${$tep1}{\style{visibility:hidden}{(x+1)(x+1)}}
$$

$$
\sum_{i=0}^N\int_{a}^{b}g(t,i)\text{d}t
$$



## 呈现位置
  -  正文inline中的LaTeX公式用\$...\$定义
  -  语句为$\sum_{i=0}^N\int_{a}^{b}g(t,i)\text{d}t$
  -  单独显示的LaTex公式用\$\$...$$显示。


## 希腊字母
|显示|命令|显示|命令|
|:-:|:-:|:-:|:-:|
|$\alpha$|\alpha|$\beta$|\beta|
|$\gamma$|\gamma|$\delta$|\delta|
|$\epsilon$|\epsilon|$\zeta$|\zeta|
|$\eta$|\eta|$\theta$|\theta|
|$\iota$|\iota|$\kappa$|\kappa|
|$\lambda$|\lambda|$\mu$|\mu|
|$\nu$|\nu|$\xi$|\xi|
|$\pi$|\pi|$\rho$|\rho|
|$\sigma$|\sigma|$\tau$|\tau|
|$\upsilon$|\upsilon|$\phi$|\phi|
|$\chi$|\chi|$\psi$|\psi|
|$\omega$|\omega|$$||

  -  若需要大写希腊字母，将命令首字母大写即可。


## 字母修饰
### 上下标
  -  上标: ^
  -  下标: _
例如: C_n^2 呈现为 $C_n^2$

### 矢量
\vec a 呈现为 $\vec a$
\overrightarrow{xy} 呈现为 $\overrightarrow{xy}$

### 字体
  *  Typewriter: \mathtt{A} 呈现为 $\mathtt{A}$
  *  Blackboard Bold: \mathbb{A} 呈现为 $\mathbb{A}$
  *  Sans Serif: \mathsf{A} 呈现为 $\mathsf{A}$

### 分组
  -  使用{}将具有相同等级的内容扩入其中，成组处理
  -  例如: 10^{10} 呈现为 $10^{10}$，而10^10呈现为 $10^10$

### 括号
  -  小括号:（） 呈现为$()$
  -  中括号:[] 呈现为$[]$
  -  尖括号:\langle,\rangle 呈现为$\langle$   $\rangle$
  -  使用\left(或\right)使得符号大小写于临近的公式相适应；该语句使用于所有括号类型
     -  (\frac{x}{y}) 呈现为 $(\frac{x}{y})$
     -  而\left(\frac{x}{y}\right) 呈现为 $\left(\frac{x}{y}\right)$

### 求和、极限与积分
- 求和: \sum
  - 例如: \sum_{i=1}^n{a_i} 呈现为 $\sum_{i=1}^n{a_i}$

- 极限: \lim_{x\to 0} 呈现为: $\lim_{x\to 0}$
- 积分: \int
  - 例如: \int_0^\infty{fxdx} 呈现为 $\int_0^\infty{fxdx}$

### 分式与根式
- 分式(fractions): \frac{公式1}{公式2} 呈现为 $\frac{x-y}{x+y}$
- 根式: \sqrt[x]{y} 呈现为 $\sqrt[x]{y}$ 

2020年6月29日 https://www.jianshu.com/p/a0aa94ef8ab2/
### 特殊函数
