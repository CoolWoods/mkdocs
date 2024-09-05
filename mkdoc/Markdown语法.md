# Markdown语法
## 样式标题

```markdown
语法 # 文本内容，需要几级标题就有几个#
# 这是H1标题
## 这是H2标题
### 这是H3标题
```

## 段落
段落由一行或者多行文本组成，每段之间需要有一个空行隔开。

```markdown
这是一个段落，他包含了很多句子。

这是另外一个段落，他也有很多句子。
```

## 强调
强调用于突出显示文本。

### 斜体
```markdown
 *斜体文本* 或者 _斜体文本_
```

### 粗体
```markdown
** 粗体文本 ** 或者 __粗体文本__
```

### 斜体加粗
```markdown
***斜体加粗*** 或者___斜体加粗___
```

## 列表
列表分为无序列表和有序列表。

### 无序列表
```markdown
- 项目1
- 项目2
- 项目3
或者
* 项目1
* 项目2
* 项目3
```

+ 项目1
+ 项目2
+ 项目3

### 有序列表
```markdown
1. 项目1
2. 项目2
3. 项目3
```

### 嵌套列表
```markdown
- 项目1
- 项目2
  - 项目2.1
  - 项目2.2
- 项目3

1. 项目1
2. 项目2
   2.1 子项目1
   2.2 子项目2
3. 项目3
```

+ 项目1
+ 项目2
    - 项目2.1
    - 项目2.2
+ 项目3
1. 项目1
2. 项目2
    1. 子项目1
    2. 子项目2
3. 项目3

## 链接
### 文体链接
```markdown
[链接文本](https://www.yuque.com)
```

[链接文本](https://www.yuque.com)

### 图片链接
图片链接就是文本链接前多加一个 **!**，文本内容变成图片无法加载时才展示。

```markdown
![语雀图片](https://mdn.alipayobjects.com/huamei_0prmtq/afts/img/A*IVdnTJqUp6gAAAAAAAAAAAAADvuFAQ/original)
```

![](https://mdn.alipayobjects.com/huamei_0prmtq/afts/img/A*IVdnTJqUp6gAAAAAAAAAAAAADvuFAQ/original)

## 代码
### 行内代码
```markdown
`行内代码`
```

  `行内代码`

### 代码块
#### 普通代码块
```markdown
```
public class Main{
  public static void main(String[] args){
    System.out.println("");
  }
}
```
```

```plain
public class Main{
  public static void main(String[] args){
    System.out.println("");
  }
}
```

#### 高亮代码块
可以通过指定语言来实现代码块的高亮。基本上所有的Markdown编辑器都支持此功能，并且涵盖了几乎所有语言。

```markdown
```java
public class Main{
  public static void main(String[] args){
    System.out.println("");
  }
}
```
```

```java
public class Main{
  public static void main(String[] args){
    System.out.println("");
  }
}
```

## 表格
表格用代码写比较繁琐，所以一般用图形化工具来生成表格。

### 普通表格
```markdown
| 列1 | 列2 | 列3 |
| ---- | ---- | ---- |
| 数据1 | 数据2 | 数据3 |
| 数据4 | 数据5 | 数据6 |
```

| 列1 | 列2 | 列3 |
| --- | --- | --- |
| 数据1 | 数据2 | 数据3 |
| 数据4 | 数据5 | 数据6 |


### 指定对齐方式表格
```markdown
| 列1 | 列2 | 列3 |
| :---- | :----: | ----: |
| 数据1 | 数据2 | 数据3 |
| 数据4 | 数据5 | 数据6 |
```

| 列1 | 列2 | 列3 |
| :--- | :---: | ---: |
| 数据1 | 数据2 | 数据3 |
| 数据4 | 数据5 | 数据6 |


## 水平线
```markdown
---
```

---

## 引用
### 普通引用
```markdown
> 这是一个引用。
```

> 这是一个引用。
>

### 嵌套引用
语雀不支持嵌套引用。

```markdown
> 这是一个引用。
> > 这是一个嵌套引用。
```

## 删除线
```markdown
 ~~补删除的文字~~ 
```

~~ 补删除的文字~~

## 任务列表
mkdocs不支持任务列表
```markdown
- [ ] 未完成的任务
- [x] 已完成的任务
```

- [ ] 未完成的任务
- [x] 已完成的任务

## *数学公式
Markdown本身不支持数学公式，但是许多Markdown编辑器支持使用LaTeX格式来插入数学公式。

### 行内公式
```markdown
$E = mc^2$
```

### 独立公式
```markdown
$$ E = mc^2 $$
```

## *脚注
脚注支持的编辑器不多，一般不使用

```markdown
这里有一个脚注[^1]

[^1]这是脚注的内容
```

## *定义列表
定义列表支持的编辑器不多，一般不使用

```markdown
名词:
: 定义
: 解释
```

## 转义字符
在Markdown，如果要显示某些字符而不使其补解析成Markdown语法中的一部分可以使用反斜杠`\`来转义这些字符。

```markdown
\\
```

## 内嵌HTML标签
```markdown
<b>加粗文本</b>
<i>斜体文本</i>
<em>强调文本</em>
<strong>强烈强调文本</strong>
<small>小字文本</small>
<h1>标题1</h1>
<h2>标题2</h2>
<a id="jump2">位置2</a>
<p style="color: red;">这是一个带有内联样式的段落。</p>
<table>
  <tr>
    <th>标题 1</th>
    <th>标题 2</th>
  </tr>
  <tr>
    <td>数据 1</td>
    <td>数据 2</td>
  </tr>
</table>
<img src="https://mdn.alipayobjects.com/huamei_0prmtq/afts/img/A*IVdnTJqUp6gAAAAAAAAAAAAADvuFAQ/original" alt="示例图像" title="这是示例图像">

```