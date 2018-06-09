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

# 实现左侧固定右侧自适应
### float
全部向左浮动，左侧宽度固定。

### grid
```css
.box {
  display: grid;
  grid-template-rows: 300px;
  grid-template-columns: 300px auto;
}
```

### flex
```css
.box {
  display: flex;
  height: 300px;
}
.left {
  flex: 0 0 300px;
}
.main {
  flex: 1;
}
```

### 绝对定位
没什么好说的。

# 双飞翼布局如何实现
```css
<style>
  * {
    margin: 0;
    padding: 0;
  }
  div {
    min-height: 100px;
  }
  .left, .right, .main {
    float: left;
  }
  .left {
    width: 300px;
    margin-left: -100%;
    background: blue;
  }
  .right {
    width: 300px;
    margin-left: -300px;
    background: yellow;
  }
  .main {
    width: 100%;
    background: red;
  }
  .main .main-content {
    margin: 0 300px;
  }
</style>

<section>
  <div class="main">
    <div class="main-content">#main</div>
  </div>
  <div class="left"></div>
  <div class="right"></div>
</section>
```
三个容器都左浮动，然后给左边的容器添加 margin-left: -100% 右边的容器添加 margin-right: -300px;，然后中间的容器中再新建一个子标签，给它添加 margin，就实现了双飞翼布局。

# 怎么使文字垂直排布
设置 writing-mode 为 vertical-lr; 使得文字垂直，阅读方向从 left -> right

# inline 和 block 的区别
- inline 设置上下 margin 无效，设置上下 padding 可以显示，但是不能产生边距效果（也就是不会挤占别的元素）
- inline 不会独占一行，不能设置 width height

# 如何实现垂直排列
使用 flex 然后设置 flex-direction: column; 也就是竖排。

# 重排和重绘
参考：https://www.cnblogs.com/cencenyue/p/7646718.html

> 重排 Reflow：当渲染树中的一部分(或全部)因为元素的规模尺寸，布局，隐藏等改变而需要重新构建, 这就称为重排(reflow)。每个页面至少需要一次重排，就是在页面第一次加载的时候。

> 重绘（repaint 或 redraw）：当盒子的位置、大小以及其他属性，例如颜色、字体大小等都确定下来之后，浏览器便把这些原色都按照各自的特性绘制一遍，将内容呈现在页面上。**重绘是指一个元素外观的改变所触发的浏览器行为，浏览器会根据元素的新属性重新绘制，使元素呈现新的外观。**

重绘和重排的关系：在重排的时候，浏览器会使渲染树中受到影响的部分失效，并重新构造这部分渲染树，完成重排后，浏览器会重新绘制受影响的部分到屏幕中，该过程称为重绘。所以，**重排必定会引发重绘，但重绘不一定会引发重排。**

触发重排的条件：任何页面布局和几何属性的改变都会触发重排，比如：

- 1、页面渲染初始化；(无法避免)
- 2、添加或删除可见的DOM元素；
- 3、元素位置的改变，或者使用动画；
- 4、元素尺寸的改变——大小，外边距，边框；
- 5、浏览器窗口尺寸的变化（resize事件发生时）；
- 6、填充内容的改变，比如文本的改变或图片大小改变而引起的计算值宽度和高度的改变；
- 7、读取某些元素属性：(offsetLeft/Top/Height/Width,　clientTop/Left/Width/Height,　scrollTop/Left/Width/Height,　width/height,　getComputedStyle(),　currentStyle(IE))

### 拓展问题：CSS 中哪些属性对页面 repaint 和 reflow 影响最大
float、line-height
# BFC
参考：https://juejin.im/post/59b73d5bf265da064618731d 理解 BFC 很好的文章。

块格式上下文（BFC）是页面 CSS 视觉渲染的一部分，**用于决定块盒子的布局及浮动相互影响范围的一个区域。**

**BFC的创建方法:**

- 浮动 (元素的float不为none)；
- 绝对定位元素 (元素的position为absolute或fixed)；
- 行内块inline-blocks(元素的 display: inline-block)；
- 表格单元格(元素的display: table-cell，HTML表格单元格默认属性)；
- overflow的值不为visible的元素；
- 弹性盒 flex boxes (元素的display: flex或inline-flex)；

**BFC的效果:**

BFC的最显著的效果就是建立一个隔离的空间，断绝空间内外元素间相互的作用。还有其他的效果：

- 内部的盒会在垂直方向一个接一个排列（可以看作BFC中有一个的常规流）；
- 处于同一个BFC中的元素相互影响，可能会发生margin collapse；
- 每个元素的margin box的左边，与容器块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此；
- BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然；
- 计算BFC的高度时，考虑BFC所包含的所有元素，连浮动元素也参与计算；
- 浮动盒区域不叠加到BFC上；

# 自适应和响应式的区别
参考：https://www.cnblogs.com/yuanziwen/p/6926561.html

自适应是随着游览器的大小变化而变化（百分比布局），响应式是适应屏幕尺寸（使用媒体查询），自动识别屏幕宽度、并做出相应调整的网页设计。

# ::before 和 :before
参考：https://www.cnblogs.com/Isabel4u/p/7398321.html

两者的作用并没有什么区别，但是 :: 和 : 是 CSS3 为了区分 伪类 和 伪元素的一种写法。（伪类，首先是类的一种，作用于标签本身（状态）伪元素首先是元素，作用于内容本身）before 就是伪元素。

还有一些其他的伪元素：
- `::first-letter`：将特殊的样式添加到文本的首字母
- `::first-line`：将特殊的样式添加到文本的首行
- `::after`：在某元素之前插入某些内容

# 细讲一下 rem 和其他单位之间的区别
rem 是一个相对单位，但是相对的只是 HTML 根元素，而 em 是相对父元素的。1rem = 16px，当设置 HTML 的 font-size 为 625% 的时候，1rem 就为 10px。

# flex布局和传统布局有什么区别
布局的传统解决方案是基于盒模型的，依赖 display + position + float 属性，对于特殊的布局非常不方便，比如垂直居中。  

而 flex 布局，可以简便、完整、响应式地实现各种页面布局。


# 三种隐藏方式差别 `:visibility:hidden, display:none, opacity:0`
`display: none` 会让元素消失且不占据页面空间，其他两个会隐藏但是还是会占据页面上的空间。
