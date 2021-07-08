# markDown
### MD文档规则
我展示的是一级标题
=================

我展示的是二级标题
-----------------
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题

Markdown 段落没有特殊的格式，直接编写文字就好，段落的换行是使用两个以上空格加上回车。
当然也可以在段落后面使用一个空行来表示重新开始一个段落。

### 字体
*斜体文本*
_斜体文本_
**粗体文本**
__粗体文本__
***粗斜体文本***
___粗斜体文本___

### 分割线
***

* * *

*****

- - -

----------

### 删除线
~~BAIDU.COM~~

<u>带下划线文本</u>
[^要注明的文本]
### 列表
* 第一项
* 第二项
* 第三项

+ 第一项
+ 第二项
+ 第三项


- 第一项
- 第二项
- 第三项

1. 第一项：
    - 第一项嵌套的第一个元素
    - 第一项嵌套的第二个元素
2. 第二项：
    - 第二项嵌套的第一个元素
    - 第二项嵌套的第二个元素
### 区块
> 最外层
> > 第一层嵌套
> > > 第二层嵌套
### 代码
`printf()` 函数

代码块
```javascript
$(document).ready(function () {
    alert('RUNOOB');
});
```
### 链接
[链接名称](链接地址)

或者

<链接地址>
### 图片
![alt 属性文本](https://avatars.githubusercontent.com/u/25358018?s=48&v=4)

![alt 属性文本](https://avatars.githubusercontent.com/u/25358018?s=48&v=4 "可选标题")
### 表格
|  表头   | 表头  |
|  ----  | ----  |
| 单元格  | 单元格 |
| 单元格  | 单元格 |
### 支持HTML
使用 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Del</kbd> 重启电脑
### 转义
**文本加粗** 

\*\* 正常显示星号 \*\*
### 公式

$$
\mathbf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix} 
\mathbf{i} & \mathbf{j} & \mathbf{k} \\
\frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\
\frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0 \\
\end{vmatrix}
${$tep1}{\style{visibility:hidden}{(x+1)(x+1)}}
$$
