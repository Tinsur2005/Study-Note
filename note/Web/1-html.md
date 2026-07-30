# HTML

## 一、Web 网页标准

Web标准也叫网页标准，由一系列网页开发标准组成，绝大部分由 **W3C（World Wide Web Consortium，万维网联盟）** 制定，是网页开发的统一规范，主要分为三层，各司其职、相互配合：

1. **HTML（结构层）**：负责搭建网页骨架，定义页面所有元素和核心内容。
2. **CSS（表现层）**：负责美化网页，控制页面元素的外观、尺寸、颜色、位置、布局等样式效果。
3. **JavaScript（行为层）**：负责实现网页交互，绑定页面动态行为、响应用户操作，实现动态效果。

## 二、HTML 基础认知与常用标签

### 2.1 HTML 定义

HTML 全称 **HyperText Markup Language（超文本标记语言）**，是搭建网页的基础语言。

**超文本**：突破普通纯文本的限制，除文字外，可定义图片、音频、视频、超链接等丰富内容。

### 2.2 HTML 基础文档骨架

所有HTML网页都遵循固定基础结构，是网页的必备骨架：

```html
<!DOCTYPE html>
<html>
    <head>
       <meta charset="utf-8" /> <!-- 设定网页字符编码，防止乱码 -->
       <title>Hello Html</title> <!-- 网页标题，显示在浏览器标签栏 -->
    </head>
    <body>
       <!-- 网页所有可见内容均写在body标签内 -->
    </body>
</html>
```

### 2.3 常用基础标签

#### 1. 标题标签 h1~h6

用于定义网页标题，层级从大到小，`h1` 层级最高、字体最大，`h6` 层级最低、字体最小，独占一行。

```html
<h1>一级标题</h1>
<h2>二级标题</h2>
<h3>三级标题</h3>
<h4>四级标题</h4>
<h5>五级标题</h5>
<h6>六级标题</h6>
```

#### 2. 段落标签 p

全称 paragraph，用于包裹网页段落文本，自带上下边距，独占一行。

```html
<p>京东（股票代码：JD），中国自营式电商企业，创始人刘强东担任京东集团董事局主席兼首席执行官 。旗下设有京东商城、京东金融、拍拍网、京东智能、O2O及海外事业部等。2013年正式获得虚拟运营商牌照。</p>
<p>王老师是Java讲师,朱老师是Java讲师</p>
```

#### 3. 图片标签 img

用于在网页中插入图片，属于自闭合标签，行内元素。核心属性：`src`（图片路径，必填）、`width`（宽度）、`height`（高度）。

```html
<!-- 默认尺寸图片 -->
<img src="img/bd.png"/>
<!-- 自定义宽高图片 -->
<img src="img/bd.png" width="100px" height="100px"></img>
```

#### 4. 换行标签 br

自闭合标签，用于文本强制换行，无样式、无间距。

```html
<br/>
```

#### 5. 超链接标签 a

用于实现页面跳转，行内元素。核心属性：

- `href`：跳转地址（必填），支持外网链接、本地文件路径
- `target="_blank"`：新窗口打开链接，默认当前窗口跳转

```html
<!-- 当前窗口跳转 -->
<a href="http://www.baidu.com">超链接跳转到Baidu</a>
<!-- 新窗口跳转 -->
<a href="http://www.baidu.com" target="_blank">超链接跳转到Baidu</a>
```

#### 6. 列表标签

用于规整展示列表内容，分为无序列表和有序列表。

**无序列表 ul+li**：无序号，默认圆点标识

```html
<ul>
    <li>北京</li>
    <li>上海</li>
    <li>深圳</li>
    <li>广州</li>
</ul>
```

**有序列表 ol+li**：带数字序号，自动排序

```html
<ol>
    <li>北京</li>
    <li>上海</li>
    <li>深圳</li>
    <li>广州</li>
</ol>
```

## 三、div 与 span 布局标签

div 和 span 是网页开发中最常用的**无语义布局标签**，专门用于页面布局划分，无默认样式。

### 3.1 区别

- **div（块级布局标签）**：全称 division，独占一整行，可嵌套所有标签，用于划分页面大区域布局。
- **span（行内布局标签）**：不独占行，可与其他行内元素并排显示，用于局部文本、小范围样式调整。

### 3.2 块级元素与行内元素区分

- **块级元素（block）**：单独独占一行，可设置宽高、边距。常见：h1~h6、ul、ol、div、p
- **行内元素（inline）**：多个元素可并排显示，默认无法设置宽高。常见：img、a、span、input

```html
<!DOCTYPE html>
<html>
    <head>
       <meta charset="utf-8" />
       <title>布局标签演示</title>
    </head>
    <body>
       <div>Hello Div1</div>
       <div>Hello Div2</div>
       <span>Span1</span>
       <span>Span2</span>
    </body>
</html>
```

## 四、表格 table 标签

HTML 表格是用于**结构化展示规整数据**的核心标签，适用于展示课程表、成绩单、人员信息、数据统计等行列结构清晰的内容。表格由**表格容器、表格行、表头单元格、普通单元格**四部分组成，层级严格：`table > tr > th/td`，即行只能嵌套在表格中，单元格只能嵌套在行中。

### 4.1 表格标签

所有表格内容必须遵循固定层级结构，标签各司其职，不可错乱嵌套：

- table：表格最外层容器，包裹所有表格内容，用于定义整个表格的范围、尺寸、边框样式。
- **tr（table row）**：表格行，每一组 tr 代表表格的一行，一个表格可包含多行 tr。
- **th（table head）**：表头单元格，专门用于表格第一行标题，**默认自带居中、加粗样式**，用于定义列名称。
- **td（table data）**：普通数据单元格，用于存放表格具体数据，默认左对齐、不加粗，是表格核心内容载体。

### 4.2 常用属性

表格原生属性可快速实现基础样式，无需额外CSS，适合基础页面开发：

- **border**：设置表格边框宽度，单位像素，取值为 `0/1/2...`，border="1" 显示默认边框，border="0" 隐藏边框。
- **cellspacing**：设置**单元格之间的间距**，默认有间距，设置 `cellspacing="0"` 可合并单元格边框，实现无缝表格。
- **cellpadding**：设置**单元格内容与边框的内边距**，可让文字不贴边，页面更美观（原笔记补充高频属性）。
- **width**：设置表格整体宽度，支持像素（px）、百分比（%），如 width="700px"、width="100%"。
- **align**：设置表格整体对齐方式，可选值：left、center、right。

```html
<!-- 标准结构化表格：无边距、合并边框、固定宽度、居中对齐 -->
<table border="1" cellspacing="0" cellpadding="8" width="700px" align="center">
    <!-- 表头行：定义列标题 -->
    <tr>
        <th>讲师名称</th>
        <th>所教班级</th>
        <th>授课科目</th>
        <th>授课年限</th>
    </tr>
    <!-- 数据行1 -->
    <tr align="center">
        <td>张老师</td>
        <td>计算机一班</td>
        <td>C语言</td>
        <td>5年</td>
    </tr>
    <!-- 数据行2 -->
    <tr align="center">
        <td>赵老师</td>
        <td>计算机二班</td>
        <td>Java</td>
        <td>8年</td>
    </tr>
    <!-- 数据行3 -->
    <tr align="center">
        <td>李老师</td>
        <td>Python专班</td>
        <td>Python</td>
        <td>6年</td>
    </tr>
</table>
```

| 讲师名称 | 所教班级   | 授课科目 | 授课年限 |
| :------- | :--------- | :------- | :------- |
| 张老师   | 计算机一班 | C语言    | 5年      |
| 赵老师   | 计算机二班 | Java     | 8年      |
| 李老师   | Python专班 | Python   | 6年      |

## 五、form 表单标签

HTML 表单是用于**收集用户信息、实现前后端数据交互**的核心模块，广泛应用于登录、注册、留言、搜索等场景。所有表单输入控件必须包裹在 `form` 标签内部，才能正常实现数据提交功能。

### 5.1 表单标签与层级规范

表单开发拥有固定层级规范，结构清晰、语义统一，禁止随意嵌套：

- **form**：表单最外层容器，用于包裹所有表单控件，定义数据提交规则，是表单的核心载体。
- **input**：单行输入控件，通过 `type` 属性切换不同输入形态（文本、密码、单选、复选、按钮等）。
- **select + option**：下拉选择控件，用于固定选项选择场景，适合城市、学历、分类等固定数据选择。
- **button**：通用按钮标签，可自定义功能，搭配JavaScript实现交互效果。

### 5.2 form 标签属性

- **action**：规定表单数据**提交的服务器接口地址**，为空时默认提交到当前页面。
- **method**：规定数据提交方式，常用 `GET`（参数可见）、`POST`（参数隐藏，安全）。
- **name**：表单名称，用于JS、后台识别对应表单数据。

### 5.3 所有表单控件

| 控件类型   | 代码写法                                  | 核心功能                                       | 关键属性说明                     |
| :--------- | :---------------------------------------- | :--------------------------------------------- | :------------------------------- |
| 单行文本框 | <input type="text">                       | 接收用户普通文本输入（用户名、昵称、搜索内容） | value：设置默认显示文本          |
| 密码输入框 | <input type="password">                   | 接收密码输入，输入内容自动隐藏加密             | 无默认值，提升账号安全           |
| 单选按钮   | <input type="radio">                      | 单选选择（性别、状态），同一组只能选一个       | name相同为一组、checked 默认选中 |
| 复选框     | <input type="checkbox">                   | 多选选择（爱好、标签），支持多选项同时选中     | checked 可设置默认选中项         |
| 下拉选择框 | <select><option></option></select>        | 下拉弹窗选择，节省页面空间                     | selected 设置默认选中选项        |
| 提交按钮   | <input type="submit">                     | 点击后自动提交表单所有数据到服务器             | value 修改按钮显示文字           |
| 重置按钮   | <input type="reset">                      | 点击一键清空、还原表单所有初始内容             | value 修改按钮显示文字           |
| 普通按钮   | <input type="button"> / <button></button> | 无默认提交/重置功能，纯自定义按钮              | 需搭配JS实现点击交互事件         |

```html
<form action="">
    <table border="1" cellspacing="0">
        <tr>
            <td>用户名:</td>
            <td><input type="text" value="zhangsan"></td>
        </tr>
        <tr>
            <td>密码：</td>
            <td><input type="password"></td>
        </tr>
        <tr>
            <td>性别：</td>
            <td>
                <input type="radio" name="gender" checked="true">男
                <input type="radio" name="gender">女
                <input type="radio" name="gender">未知
            </td>
        </tr>
        <tr>
            <td>城市：</td>
            <td>
                <select>
                    <option>--请选择--</option>
                    <option>青岛</option>
                    <option selected>济南</option>
                    <option>烟台</option>
                </select>
            </td>
        </tr>
        <tr>
            <td>兴趣爱好：</td>
            <td>
                <input type="checkbox">音乐<br>
                <input type="checkbox">看书<br>
                <input type="checkbox">爬山<br>
            </td>
        </tr>
        <tr>
            <td><input type="submit" value="注册"></td>
            <td><input type="reset" value="重置"></td>
        </tr>
    </table>
    <br>
    <button>按钮1</button>
    <input type="button" value="按钮2">
</form>
```