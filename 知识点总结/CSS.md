# position: sticky
元素根据正常文档流进行定位，相对它的最近滚动祖先元素和containing block(最近块级祖先)，包括table-related元素，基于top, right, bottom, left的值进行偏移，偏移值不会影响其他任何元素的位置。

该值总会创建一个新的层叠上下文，一个sticky元素会固定在离他最近的一个拥有‘滚动机制’的祖先上(该祖先的overflow是hidden，scroll, auto, 或overlay)，即便这个祖先不是最近的真实可滚动祖先。

# 浏览器层
web页面的展示一般分为五个步骤：  
- 构建DOM树：浏览器将HTML解析成梳妆结构的DOM树，一般来说，这个过程发生在页面初次加载，或页面JavaScript修改了节点结构的时候
- 构建渲染树：浏览器将css 解析成树形结构的cssOM树，再和DOM树合并成渲染树
- 布局（layout）：浏览器根据渲染树所体现的节点、各个节点的css定义以及他们的从属关系，计算出每个节点在屏幕中的位置。web页面中元素的布局是相对的，在页面元素位置、大小发生变化，往往会导致其他节点联动，需要重新计算布局，这时候的布局过程一般称为回流（reflow）
- 绘制（paint）：遍历渲染树，调用渲染器的paint()方法在屏幕上绘制出节点内容，本质上是一个像素填充的过程。这个过程也出现于回流或一些不影响布局的css修改引起的屏幕局部重画，这时候它被称为重绘（repaint）。实际上，绘制过程是多个层上完成的，这些层我们成为渲染层（renderLayer）
- 渲染层合成（composite）：多个绘制后的渲染层恰当的重叠顺序进行合并，而后生成位图，最终通过显卡展示到屏幕上

**图层**  
一般来说，可以把普通文档留看成一个图层，特定的属性可以生成一个新的图层，不同的图层渲染互不影响，所以对于某些频繁需要渲染的建议单独生成一个新图层，提高性能。但是也不能生成过多的图层，会引起反作用。  
通常以下几个常用属性可以生成新图层：  
- 3D变换：translate3d, translateZ
- will-change
- video, iframe标签
- 通过动画实现的opacity动画转换
- position: fixed

# 重绘（repaint）和回流（reflow）
- 重绘是当节点需要更改外观而不会影响布局的，比如改变color
- 回流是布局或者几何属性需要改变  

回流必定会发生重绘，重绘不一定会引发回流。回流所需的成本比重绘高得多。  
以下几个动作可能会导致性能问题：  
- 改变window大小
- 改变字体
- 添加或删除样式
- 文字改变
- 定位或者浮动
- 盒模型  

重绘和回流和Event Loop有关 

# 减少重绘和回流
- 使用translate代替top
- 使用visibility代替`display:none`，因为前者只会引起重绘，后者会引发回流（改变了布局）
- 把DOM离线后修改，比如：先把DOM给display:none（有一次reflow），修改完之后再把它显示出来
- 不使用table布局，可能一个小的改动会造成整个table的重新布局
- 动画使用requestAinmationFrame
- css选择器避免层级过深
- 将频繁运行的动画变为图层，图层能阻止该节点回流影响别的元素，比如对于video标签，浏览器会自动将该节点变为图层

# margin重叠
块的上外边距和下外边距有时合并为单个边距，其大小为单个边距的最大值，这种行为称为边距重叠  
有三种情况会形成外边距重叠：
- 同一层相邻元素之间：相邻的两个元素之间的外边距重叠，除非后一个元素加上 clear-fix 清除浮动
- 没有内容将父元素和后代元素分开
- 空的块级元素

# css优先级
- !import 无条件优先
- 内联样式 1000
- id选择器 100
- class、伪类、属性 10
- 标签、伪元素 1

# 标准盒模型和怪异盒模型
标准盒的总宽度：width+padding+border+margin  
怪异盒模型总宽度：width+margin（width已经包含了padding和border）  
box-sizing: content-box  为标准盒模型
box-sizing: border-box 为怪异盒模型