# float 和 display:inline-block
display是指**显示状态**，float是**针对块级元素的浮动**。  
inline-block：控制元素的垂直对齐跟横向排列元素。

### 清除浮动
- 定义一个 clearfix 类，利用伪元素 ::after 清除浮动
- overflow: auto; 或者 overflow: hidden;
- 空 div 方法：`<div style="clear:both;"></div>`

# CSS 优先级
!important > 内联> #id > .class > 标签选择器

# 居中
### 水平居中
- text-align: center
- margin: 0 auto;
- margin-left + translate
- 绝对定位 + translate
- justify-content: center;

### 垂直居中
- 行内元素 line-height
- 绝对定位 + translate
- flex  align-items: center;
- margin-top: 50%; + translateY

# 为什么要把 CSS 放在 head 中呢？
其实放在那里都是可以的，如果把 CSS 放在 body 中的话，如果 html 先加载完成而 css 后加载完成的话，页面会因为没有样式而无法正常显示。

# 怪异盒模型（IE 盒模型）
标准盒模型的 `width = content`  
怪异盒模型 `width = content + border + padding`

**如何设置？**  
`box-sizing: boder-box;`

# flex 实现左侧固定右侧

# 怎么使文字垂直排布
设置 writing-mode 为 vertical-lr; 垂直然后阅读方向从 left -> right