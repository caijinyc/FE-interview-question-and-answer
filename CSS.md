# CSS
# float 和 display:inline-block

display是指**显示状态**，float是**针对块级元素的浮动**。  
inline-block：控制元素的垂直对齐跟横向排列元素。

#### 清除浮动
- 定义一个 clearfix 类，利用伪元素 ::after 清除浮动
- overflow: auto; 或者 overflow: hidden;
- 空 div 方法：`<div style="clear:both;"></div>`

# CSS 优先级
!important > 内联> #id > .class > 标签选择器

# 居中
#### 水平居中
- text-align: center
- margin: 0 auto;
- margin-left + translate
- 绝对定位 + translate
- justify-content: center;

#### 垂直居中
- 行内元素 line-height
- 绝对定位 + translate
- flex  align-items: center;
- margin-top: 50%; + translateY
