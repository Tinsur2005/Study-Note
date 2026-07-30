# CSS 基础学习笔记

## 一、介绍和选择器

### 1.1 CSS 概念

CSS（Cascading Style Sheet）：层叠样式表，专门用于控制网页页面的样式与表现效果。

### 1.2 CSS 使用核心逻辑

1. 通过**选择器**选中页面中的 HTML 标签
2. 针对选中的标签设置对应的样式属性

### 1.3 CSS 基础语法格式

```css
选择器 {
     属性名1:属性值1;
     属性名2:属性值2;
     属性名3:属性值3;
     属性名4:属性值4;
}
```

### 1.4 基础选择器分类

CSS 基础常用选择器共3种，用于精准选中页面标签：

1. **标签选择器**：直接匹配对应标签，作用于页面所有该标签 `p{}`
2. **类选择器**：以 `.` 开头，自定义类名，可多标签复用、一个标签可多类名 `.className{}`
3. **id 选择器**：以 `#` 开头，id 页面唯一，仅作用于单个标签`#id{}`

### 1.5 常用基础样式属性

| 样式代码                     | 功能说明           |
| :--------------------------- | :----------------- |
| `color:red;`                 | 设置文字颜色       |
| `font-size:40px;`            | 设置文字字体大小   |
| `background-color:blue;`     | 设置标签背景颜色   |
| `text-decoration:underline;` | 为文字添加下划线   |
| `text-decoration:none;`      | 去除文字默认下划线 |

## 二、CSS 使用思想

### 2.1 思路

将单一样式拆分为**独立公共类**，每个类只负责一种样式效果，多个公共类可叠加作用于同一个标签，灵活实现多样式组合效果。

示例需求：实现 50px 字体、红色文字、文字下划线 三种样式的自由组合

### 2.2 示例

```html
<!DOCTYPE html>
<html>
    <head>
       <meta charset="utf-8">
       <title></title>
       <style>
          /* 大字体公共类 */
          .big-font{
             font-size: 50px;
          }
          /* 红色文字公共类 */
          .red{
             color: red;
          }
          /* 下划线公共类 */
          .underline{
             text-decoration: underline;
          }
       </style>
    </head>
    <body>
       <p class="red big-font">段落1</p>
       <p class="red underline">段落2</p>
       <p class="big-font underline">段落3</p>
       <p class="big-font red underline">段落4</p>
    </body>
</html>
```

### 2.3 总结

1. 禁止用一个类写完标签所有样式，一个标签可叠加**多个公共类**组合样式
2. 每个类尽量单一、精简，具备**公共复用性**，可作用于页面多个标签

## 三、CSS 盒模型

### 3.1 盒模型核心属性

所有 HTML 标签都可看作一个盒子，盒模型核心 5 个属性：**width、height、padding、border、margin**

- **width**：盒子内容宽度
- **height**：盒子内容高度
- **padding**：内边距，盒子内容与边框之间的距离
- **border**：盒子边框
- **margin**：外边距，盒子与其他盒子之间的距离

### 3.2 常用初始化操作

浏览器会默认给部分标签自带 padding 和 margin，开发中通常统一清零，避免样式错乱：

```css
/* 全局清除标签默认内外边距 */
*{
    margin: 0;
    padding: 0;
}
```

### 3.3 示例

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>盒模型示例</title>
    <style>
        /* 去掉标签默认带着的padding和margin */
        *{
            margin: 0;
            padding: 0;
        }
        div{
            width: 100px;
            height: 100px;
            background-color: red;    /* 盒子背景色 */
            border: 2px solid blue;   /* 边框：2px 实线 蓝色 */
            padding: 10px;            /* 内边距 10px */
            margin: 10px;             /* 外边距 10px */
        }
    </style>
</head>
<body>
    <div></div>
</body>
</html>
```

### 3.4 div+css 布局

前端主流布局方式，通过 div 搭建页面结构，配合 CSS 盒模型、样式属性，实现页面结构化、标准化布局，替代传统 table 布局，代码更简洁、维护性更强。