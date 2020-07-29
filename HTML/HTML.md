# HTML5 学习


### HTML5<!DOCTYPE>
` <!DOCTYPE html>`声明必须位于HTML5第一行，使用非常简单。

### HTML5的改进
- 新元素
- 新属性
- 完全支持CSS3
- Video和Audio
- 2D和3D绘图
- 本地存储
- 本地SQL数据
- Web应用

### HTML5多媒体
使用HTML5你可以简单的在网页中播放视频和音频。
- `HTML<video>`
- `HTML5<audio>`

### HTML5图形
使用HTML5你可以简单的绘制图形：
- 使用`<canvas>`元素
- 使用内联SVG。
- 使用CSS3 2D转换和CSS3 3D转换。

### HTML5使用CSS3
- 新选择器
- 新属性
- 动画
- 2D和3D转换
- 圆角
- 阴影效果
- 可下载的字体

### 语义元素
|标签	|描述|
|:---|:--|
|\<article> |	定义页面独立的内容区域。|
|\<aside>	|定义页面的侧边栏内容。|
|\<bdi>	    |允许您设置一段文本，使其脱离其父元素的文本方向设置。|
|\<command>	|定义命令按钮，比如单选按钮、复选框或按钮|
|\<details>	|用于描述文档或文档某个部分的细节|
|\<dialog>	|定义对话框，比如提示框|
|\<summary>	|标签包含 details 元素的标题|
|\<figure>	|规定独立的流内容（图像、图表、照片、代码等等）。|
|\<figcaption>	|定义 \<figure> 元素的标题|
|\<footer>	|定义 section 或 document 的页脚。|
|\<header>	|定义了文档的头部区域|
|\<mark>	|定义带有记号的文本。|
|\<meter>	|定义度量衡。仅用于已知最大和最小值的度量。|
|\<nav>	    |定义导航链接的部分。|
|\<progress>	|定义任何类型的任务的进度。|
|\<ruby>	|定义 ruby 注释（中文注音或字符）。|
|\<rt>	    |定义字符（中文注音或字符）的解释或发音。|
|\<rp>	    |在 ruby 注释中使用，定义不支持| ruby 元素的浏览器所显示的内容。|
|\<section>	|定义文档中的节（section、区段）。|
|\<time>	|定义日期或时间。|
|\<wbr>	    |规定在文本中的何处适合添加换行符。|

### HTML5 表单
新表单元素、新属性、新输入类型，自动验证。

### 已经移除的元素
- `<acronym>`
- `<applet>`
- `<basefont>`
- `<big>`
- `<center>`
- `<dir>`
- `<font>`
- `<frame>`
- `<noframes>`
- `<strike>`

```html
<!DOCTYPE HTML>
<html>
<head>
<meta charset="utf-8">
<title>不知道</title>
</head>
<body>
  <video width="320" height="240" controls>
    <source src="movie.mp4" tpye="video/mp4">
    <source src="movie.ogg" tpye="video/ogg">
    你的浏览器不支持video标签
  </video>
</body>
</html>

```

### HTML5新元素
#### `<canvas>新元素`
`canvas` 标签定义图形，比如图表和其他图像。改标签基于javaScript的绘图API。

#### `新多媒体元素`
- `<audio>` 定义音频内容
- `video` 定义视频（video或者movie）
- `<source>` 定义多媒体资源`<video>` 和 `<audio>`
- `<embed>` 定义嵌入的内容，比如插件.
- `<track>` 为`<video>` 和 `audio` 元素之类的媒介规定外部文本轨道。


#### 新表单元素
- `<datalist>` 定义选项列表。请与input元素配合使用钙元素。
- `<keygen>` 定义了用于表单的秘钥对生成器字段

### HTML5 Canvas
`canvas` 标签定义图形，您必须使用脚本来绘制图形。
#### 创建一个画布(canvas)


<canvas id="myCanvas" width="200" height="100" style="border:1px solid#000000"></canvas>

```javascript
<script>
var c=document.getElementById("myCanvas");
var ctx=c.getContext("2d");
ctx.fillStyle="#FF0000";
ctx.fillRect(0,0,150,75);
</script>
```

|标签	|描述|
|:--|:--|
|\<!--...-->|	定义注释
|\<!DOCTYPE>|	定义文档类型
|\<a>	|定义超文本链接
|\<abbr>|	定义缩写
|\<acronym>|	定义只取首字母的缩写，不支持HTML5
|\<address>	|定义文档作者或拥有者的联系信息
|\<applet>	|HTML5中不赞成使用。定义嵌入的 applet。
|\<area>|	定义图像映射内部的区域
|\<article>New|	定义一个文章区域
|\<aside>New	|定义页面的侧边栏内容
|\<audio>New	|定义音频内容
|\<b>	|定义文本粗体
|\<base>	|定义页面中所有链接的默认地址或默认目标。
|\<basefont>	|HTML5不支持，不赞成使用。定义页面中文本的默认字体、颜色或尺寸。
|\<bdi>New	|允许您设置一段文本，使其脱离其父元素的文本方向设置。
|\<bdo>	|定义文字方向
|\<big>	|定义大号文本，HTML5不支持
|\<blockquote>	|定义长的引用
|\<body>	|定义文档的主体
|\<br>	|定义换行
|\<button>	|定义一个点击按钮
|\<canvas>New	|定义图形，比如图表和其他图像,标签只是图形容器，您必须使用脚本来绘制图形
|\<caption>	|定义表格标题
|\<center>	|HTML5不支持，不赞成使用。定义居中文本。
|\<cite>	|定义引用(citation)
|\<code>	|定义计算机代码文本
|\<col>	|定义表格中一个或多个列的属性值
|\<colgroup>	|定义表格中供格式化的列组
|\<command>New	|定义命令按钮，比如单选按钮、复选框或按钮
|\<datalist>New	|定义选项列表。请与 input 元素配合使用该元素，来定义 input 可能的值。
|\<dd>	|定义定义列表中项目的描述
|\<del>	|定义被删除文本
|\<details>New	|用于描述文档或文档某个部分的细节
|\<dfn>	|定义定义项目
|\<dialog>New	|定义对话框，比如提示框
|\<dir>	|HTML5不支持，不赞成使用。定义目录列表。
|\<div>	|定义文档中的节
|\<dl>	|定义列表详情
|\<dt>	|定义列表中的项目
|\<em>	|定义强调文本
|\<embed>New	|定义嵌入的内容，比如插件。
|\<fieldset>	|定义围绕表单中元素的边框
|\<figcaption>New	|定义\<figure> 元素的标题
|\<figure>New	|规定独立的流内容（图像、图表、照片、代码等等）。
|\<font>	|HTML5不支持，不赞成使用。定义文字的字体、尺寸和颜色。
|\<footer>New	|定义 section 或 document 的页脚。
|\<form>	|定义了HTML文档的表单
|\<frame>	|定义框架集的窗口或框架
|\<frameset>	|定义框架集
|\<h1> to \<h6>	|定义 HTML 标题
|\<head>	|定义关于文档的信息
|\<header>New	|定义了文档的头部区域
|\<hr>	|定义水平线
|\<html>	|定义 HTML 文档
|\<i>	|定义斜体字
|\<iframe>	|定义内联框架
|\<img>	|定义图像
|\<input>	|定义输入控件
|\<ins>	|定义被插入文本
|\<kbd>	|定义键盘文本
|\<keygen>New	|规定用于表单的密钥对生成器字段。
|\<label>	|定义 input 元素的标注
|\<legend>	|定义 fieldset 元素的标题。
|\<li>	|定义列表的项目
|\<link>	|定义文档与外部资源的关系
|\<main>	|定义文档的主体部分。
|\<map>	|定义图像映射
|\<mark>New	|定义带有记号的文本。请在需要突出显示文本时使用 \<m> 标签。
|\<menu>	|不赞成使用。定义菜单列表。
|\<meta>	|定义关于 HTML 文档的元信息。
|\<meter>New	|定义度量衡。仅用于已知最大和最小值的度量。
|\<nav>New	|定义导航链接的部分
|\<noframes>	|定义针对不支持框架的用户的替代内容。HTML5不支持
|\<noscript>	|定义针对不支持客户端脚本的用户的替代内容。
|\<object>	|定义内嵌对象
|\<ol>	|定义有序列表。
|\<optgroup>	|定义选择列表中相关选项的组合。
|\<option>	|定义选择列表中的选项。
|\<output>New	|定义不同类型的输出，比如脚本的输出。
|\<p>	|定义段落。
|\<param>	|定义对象的参数。
|\<pre>	|定义预格式文本。
|\<progress>New	|定义运行中的进度（进程）。
|\<q>	|定义短的引用。
|\<rp>New	|\<rp> 标签在 ruby 注释中使用，以定义不支持 ruby 元素的浏览器所显示的内容。
|\<rt>New	|\<rt> 标签定义字符（中文注音或字符）的解释或发音。
|\<ruby>New	|\<ruby> 标签定义 ruby 注释（中文注音或字符）。
|\<s>	|不赞成使用。定义加删除线的文本。
|\<samp>	|定义计算机代码样本。
|\<script>	|定义客户端脚本。
|\<section>New	|\<section> 标签定义文档中的节（section、区段）。比如章节、页眉、页脚或文档中的其他部分。
|\<select>	|定义选择列表（下拉列表）。
|\<small>	|定义小号文本。
|\<source>New	|\<source> 标签为媒介元素（比如 \<video> 和 \<audio>）定义媒介资源。
|\<span>	|定义文档中的节。
|\<strike>	|HTML5不支持，不赞成使用。定义加删除线文本。
|\<strong>	|定义强调文本。
|\<style>	|定义文档的样式信息。
|\<sub>	|定义下标文本。
|\<summary>New	|\<summary> 标签包含 details 元素的标题，"details" 元素用于描述有关文档或文档片段的详细信息。
|\<sup>	|定义上标文本。
|\<table>	|定义表格。
|\<tbody>	|定义表格中的主体内容。
|\<td>	|定义表格中的单元。
|\<textarea>	|定义多行的文本输入控件。
|\<tfoot>	|定义表格中的表注内容（脚注）。
|\<th>	|定义表格中的表头单元格。
|\<thead>	|定义表格中的表头内容。
|\<time>New	|定义日期或时间，或者两者。
|\<title>	|定义文档的标题。
|\<tr>	|定义表格中的行。
|\<track>New	|\<track> 标签为诸如 video 元素之类的媒介规定外部文本轨道。
|\<tt>	|定义打字机文本。
|\<u>	|不赞成使用。定义下划线文本。
|\<ul>	|定义无序列表。
|\<var>	|定义文本的变量部分。
|\<video>New	|\<video> 标签定义视频，比如电影片段或其他视频流。
|\<wbr>New	|规定在文本中的何处适合添加换行符。


## 表格
### 定义和用法
<table>标签定义HTML元素表格
简单的HTML表格由table元素和一个多个tr、th和td元素组成。

tr元素定义表格行，th元素定义表头，td元素定义表格单元。
更复杂的HTML表格也可能包括caption、col、colgroup、thead、ftoot以及tbody元素。

### HTML与XHTML之间的差异


|属性|值|描述|
|:--|:--|:--|
|align|left、center、right|规定表格相对周围元素的对齐方式|
|bgcolor|rgb(x,x,x)、#xxxxxx，colorname|规定表格背景色|
|border|pixels|规定表格边框的宽度|
|cellpadding|pixels, %|规定单元边沿与其内容之间的空白|
cellspacing|pixels,%|固定单元格之间的空白|
|frame|void、above、below、hsides、lhs、rhs、vsides、box、border|规定外侧边框的那部分是可见的|
|rules|none、groups、rows、cols、all|规定内边框的那个部分是可见的|
|summary|text|规定表格的再要|
|width|%，pixels|规定表格的宽度|

```html
<body>

<h4>带有普通的边框：</h4>  
<table border="1">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>

<h4>带有粗的边框：</h4>  
<table border="8">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>

<h4>带有很粗的边框：</h4>  
<table border="15">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>

</body>
```

#### 带有普通的边框
<table border="1">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>

<h4>带有粗的边框：</h4>  
<table border="8">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>

<h4>带有很粗的边框：</h4>  
<table border="15">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>


|表格|描述|
|:--|:--|
|table|定义表格|
|caption|定义表格标题|
|th|定义表格的表头|
|tr|定义表格的行|
|td|定义表格单元|
|thead|定义表格的页眉|
|tbody|定义表格的主体|
|tfoot|定义表格的页脚|
|col|定义用于表格列的属性|
|colgroup|定义表格列的组|

<hr>

```html
<h4>横跨两列的单元格：</h4>
<table border="1">
<tr>
  <th>姓名</th>
  <th colspan="2">电话</th>
</tr>
<tr>
  <td>Bill Gates</td>
  <td>555 77 854</td>
  <td>555 77 855</td>
</tr>
</table>

<h4>横跨两行的单元格：</h4>
<table border="1">
<tr>
  <th>姓名</th>
  <td>Bill Gates</td>
</tr>
<tr>
  <th rowspan="2">电话</th>
  <td>555 77 854</td>
</tr>
<tr>
  <td>555 77 855</td>
</tr>
</table>
```
<h4>横跨两列的单元格：</h4>
<table border="1">
<tr>
  <th>姓名</th>
  <th colspan="2">电话</th>
</tr>
<tr>
  <td>Bill Gates</td>
  <td>555 77 854</td>
  <td>555 77 855</td>
</tr>
</table>

<h4>横跨两行的单元格：</h4>
<table border="1">
<tr>
  <th>姓名</th>
  <td>Bill Gates</td>
</tr>
<tr>
  <th rowspan="2">电话</th>
  <td>555 77 854</td>
</tr>
<tr>
  <td>555 77 855</td>
</tr>
</table>

<hr>

```html
<table border="1">
<tr>
  <td>
   <p>这是一个段落。</p>
   <p>这是另一个段落。</p>
  </td>
  <td>这个单元包含一个表格：
   <table border="1">
   <tr>
     <td>A</td>
     <td>B</td>
   </tr>
   <tr>
     <td>C</td>
     <td>D</td>
   </tr>
   </table>
  </td>
</tr>
<tr>
  <td>这个单元包含一个列表：
   <ul>
    <li>苹果</li>
    <li>香蕉</li>
    <li>菠萝</li>
   </ul>
  </td>
  <td>HELLO</td>
</tr>
</table>
```
<table border="1">
<tr>
  <td>
   <p>这是一个段落。</p>
   <p>这是另一个段落。</p>
  </td>
  <td>这个单元包含一个表格：
   <table border="1">
   <tr>
     <td>A</td>
     <td>B</td>
   </tr>
   <tr>
     <td>C</td>
     <td>D</td>
   </tr>
   </table>
  </td>
</tr>
<tr>
  <td>这个单元包含一个列表：
   <ul>
    <li>苹果</li>
    <li>香蕉</li>
    <li>菠萝</li>
   </ul>
  </td>
  <td>HELLO</td>
</tr>
</table>

<hr>

```html
<h4>背景颜色：</h4>
<table border="1" 
bgcolor="red">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>

<h4>背景图像：</h4>
<table border="1" 
background="/i/eg_bg_07.gif">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>
```

<h4>背景颜色：</h4>
<table border="1" 
bgcolor="red">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>

<h4>背景图像：</h4>
<table border="1" 
background="/i/eg_bg_07.gif">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>


<hr>

```html
<h4>这个表格没有边框：</h4>
<table>
<tr>
  <td>100</td>
  <td>200</td>
  <td>300</td>
</tr>
<tr>
  <td>400</td>
  <td>500</td>
  <td>600</td>
</tr>
</table>

<h4>这个表格也没有边框：</h4>
<table border="0">
<tr>
  <td>100</td>
  <td>200</td>
  <td>300</td>
</tr>
<tr>
  <td>400</td>
  <td>500</td>
  <td>600</td>
</tr>
</table>
```

<h4>这个表格没有边框：</h4>
<table>
<tr>
  <td>100</td>
  <td>200</td>
  <td>300</td>
</tr>
<tr>
  <td>400</td>
  <td>500</td>
  <td>600</td>
</tr>
</table>

<h4>这个表格也没有边框：</h4>
<table border="0">
<tr>
  <td>100</td>
  <td>200</td>
  <td>300</td>
</tr>
<tr>
  <td>400</td>
  <td>500</td>
  <td>600</td>
</tr>
</table>


<hr>

```html
<table border="1">
<tr>
  <td>Some text</td>
  <td>Some text</td>
</tr>
<tr>
  <td></td>
  <td>Some text</td>
</tr>
</table>

<p>正如您看到的，其中一个单元没有边框。这是因为它是空的。在该单元中插入一个空格后，仍然没有边框。</p>

<p>我们的技巧是在单元中插入一个 no-breaking 空格。</p>

<p>no-breaking 空格是一个字符实体。如果您不清楚什么是字符实体，请阅读关于字符实体的章节。</p>

<p>no-breaking 空格由和号开始 ("&")，然后是字符"nbsp"，并以分号结尾(";")。</p>
```

<table border="1">
<tr>
  <td>Some text</td>
  <td>Some text</td>
</tr>
<tr>
  <td></td>
  <td>Some text</td>
</tr>
</table>

<p>正如您看到的，其中一个单元没有边框。这是因为它是空的。在该单元中插入一个空格后，仍然没有边框。</p>

<p>我们的技巧是在单元中插入一个 no-breaking 空格。</p>

<p>no-breaking 空格是一个字符实体。如果您不清楚什么是字符实体，请阅读关于字符实体的章节。</p>

<p>no-breaking 空格由和号开始 ("&")，然后是字符"nbsp"，并以分号结尾(";")。</p>



<hr>

```html
<h4>没有 cellpadding：</h4>
<table border="1">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>

<h4>带有 cellpadding：</h4>
<table border="1" 
cellpadding="10">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>
```

<h4>没有 cellpadding：</h4>
<table border="1">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>

<h4>带有 cellpadding：</h4>
<table border="1" 
cellpadding="10">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>


<hr>

```html
<h4>没有 cellspacing：</h4>
<table border="1">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>

<h4>带有 cellspacing：</h4>
<table border="1" 
cellspacing="10">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>
```

<h4>没有 cellspacing：</h4>
<table border="1">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>

<h4>带有 cellspacing：</h4>
<table border="1" 
cellspacing="10">
<tr>
  <td>First</td>
  <td>Row</td>
</tr>   
<tr>
  <td>Second</td>
  <td>Row</td>
</tr>
</table>


<hr>

```html
<h4>单元背景：</h4>  
<table border="1">
<tr>
  <td bgcolor="red">First</td>
  <td>Row</td>
</tr>   
<tr>
  <td 
  background="/i/eg_bg_07.gif">
  Second</td>
  <td>Row</td>
</tr>
</table>
```

<h4>单元背景：</h4>  
<table border="1">
<tr>
  <td bgcolor="red">First</td>
  <td>Row</td>
</tr>   
<tr>
  <td 
  background="/i/eg_bg_07.gif">
  Second</td>
  <td>Row</td>
</tr>
</table>

<hr>


```html
<table width="400" border="1">
 <tr>
  <th align="left">消费项目....</th>
  <th align="right">一月</th>
  <th align="right">二月</th>
 </tr>
 <tr>
  <td align="left">衣服</td>
  <td align="right">$241.10</td>
  <td align="right">$50.20</td>
 </tr>
 <tr>
  <td align="left">化妆品</td>
  <td align="right">$30.00</td>
  <td align="right">$44.45</td>
 </tr>
 <tr>
  <td align="left">食物</td>
  <td align="right">$730.40</td>
  <td align="right">$650.00</td>
 </tr>
 <tr>
  <th align="left">总计</th>
  <th align="right">$1001.50</th>
  <th align="right">$744.65</th>
 </tr>
</table>
```

<table width="400" border="1">
 <tr>
  <th align="left">消费项目....</th>
  <th align="right">一月</th>
  <th align="right">二月</th>
 </tr>
 <tr>
  <td align="left">衣服</td>
  <td align="right">$241.10</td>
  <td align="right">$50.20</td>
 </tr>
 <tr>
  <td align="left">化妆品</td>
  <td align="right">$30.00</td>
  <td align="right">$44.45</td>
 </tr>
 <tr>
  <td align="left">食物</td>
  <td align="right">$730.40</td>
  <td align="right">$650.00</td>
 </tr>
 <tr>
  <th align="left">总计</th>
  <th align="right">$1001.50</th>
  <th align="right">$744.65</th>
 </tr>
</table>


<hr>

```html
<p><b>注释：</b>frame 属性无法在 Internet Explorer 中正确地显示。</p>

<p>Table with frame="box":</p>
<table frame="box">
  <tr>
    <th>Month</th>
    <th>Savings</th>
  </tr>
  <tr>
    <td>January</td>
    <td>$100</td>
  </tr>
</table>

<p>Table with frame="above":</p>
<table frame="above">
  <tr>
    <th>Month</th>
    <th>Savings</th>
  </tr>
  <tr>
    <td>January</td>
    <td>$100</td>
  </tr>
</table>

<p>Table with frame="below":</p>
<table frame="below">
  <tr>
    <th>Month</th>
    <th>Savings</th>
  </tr>
  <tr>
    <td>January</td>
    <td>$100</td>
  </tr>
</table>

<p>Table with frame="hsides":</p>
<table frame="hsides">
  <tr>
    <th>Month</th>
    <th>Savings</th>
  </tr>
  <tr>
    <td>January</td>
    <td>$100</td>
  </tr>
</table>

<p>Table with frame="vsides":</p>
<table frame="vsides">
  <tr>
    <th>Month</th>
    <th>Savings</th>
  </tr>
  <tr>
    <td>January</td>
    <td>$100</td>
  </tr>
</table>

```

<p><b>注释：</b>frame 属性无法在 Internet Explorer 中正确地显示。</p>

<p>Table with frame="box":</p>
<table frame="box">
  <tr>
    <th>Month</th>
    <th>Savings</th>
  </tr>
  <tr>
    <td>January</td>
    <td>$100</td>
  </tr>
</table>

<p>Table with frame="above":</p>
<table frame="above">
  <tr>
    <th>Month</th>
    <th>Savings</th>
  </tr>
  <tr>
    <td>January</td>
    <td>$100</td>
  </tr>
</table>

<p>Table with frame="below":</p>
<table frame="below">
  <tr>
    <th>Month</th>
    <th>Savings</th>
  </tr>
  <tr>
    <td>January</td>
    <td>$100</td>
  </tr>
</table>

<p>Table with frame="hsides":</p>
<table frame="hsides">
  <tr>
    <th>Month</th>
    <th>Savings</th>
  </tr>
  <tr>
    <td>January</td>
    <td>$100</td>
  </tr>
</table>

<p>Table with frame="vsides":</p>
<table frame="vsides">
  <tr>
    <th>Month</th>
    <th>Savings</th>
  </tr>
  <tr>
    <td>January</td>
    <td>$100</td>
  </tr>
</table>







