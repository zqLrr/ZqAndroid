## Position

### static：

HTML 元素的默认值，即没有定位，遵循正常的文档流对象。

静态定位的元素不会受到 top, bottom, left, right影响。

### fixed:

固定定位，元素会被移出正常文档流，并不为元素预留空间，而是通过指定元素相对于屏幕视口（viewport）的位置来指定元素位置。元素的位置在屏幕滚动时不会改变。

### relative ：

元素先放置在未添加定位时的位置，再在不改变页面布局的前提下调整元素位置（因此会在此元素未添加定位时所在位置留下空白）。

> 可以设置子view的绝对定位依靠父view的相对定位

### absolute：

绝对定位，元素会被移出正常文档流，并不为元素预留空间，通过指定元素相对于最近的非 static 定位祖先元素的偏移，来确定元素位置。。

## FlexLayout 

> 弹性布局
>
> display: flex;

### 常用属性

* flex-direction

  `flex-direction`属性决定主轴的方向（即项目的排列方向）。

  ```css
  flex-direction: row | row-reverse | column | column-reverse;
  ```

  * row：从左到右
  * row-reverse：从右到左
  * column：从上到下
  * column-reverse:从下到上

* flex-wrap

  默认情况下，项目都排在一条线（又称"轴线"）上。`flex-wrap`属性定义，如果一条轴线排不下，如何换行。

  ```css
  flex-wrap: nowrap | wrap | wrap-reverse;
  ```

  * nowrap:不换行
  * wrap：换行，第一行在下方
  * wrap-reverse:换行，第一行在上方

* flex-flow

  `flex-flow`属性是`flex-direction`属性和`flex-wrap`属性的简写形式，默认值为`row nowrap`。

*  justify-content

  `justify-content`属性定义了项目在主轴上的对齐方式。

  ```css
  justify-content: flex-start | flex-end | center | space-between | space-around;
  ```

  * flex-start（默认值）：左对齐
  * flex-end：右对齐
  * center： 居中
  * space-between：两端对齐，项目之间的间隔都相等。
  * space-around：每个item两侧的间隔相等。所以，item之间的间隔比项目与边框的间隔大一倍。

* align-items

  `align-items`属性定义项目在交叉轴(竖轴)上如何对齐。

  ```css
  align-items: flex-start | flex-end | center | baseline | stretch;
  ```

  * `flex-start`：交叉轴的起点对齐。
  * `flex-end`：交叉轴的终点对齐。
  * `center`：交叉轴的中点对齐。
  * `baseline`: 项目的第一行文字的基线对齐。
  * `stretch`（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。

* align-content

  `align-content`属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

  ```css
  align-content: flex-start | flex-end | center | space-between | space-around | stretch
  ```

  - `flex-start`：与交叉轴的起点对齐。
  - `flex-end`：与交叉轴的终点对齐。
  - `center`：与交叉轴的中点对齐。
  - `space-between`：与交叉轴两端对齐，轴线之间的间隔平均分布。
  - `space-around`：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
  - `stretch`（默认值）：轴线占满整个交叉轴。

### Flex-item常用属性

* order

  order`属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。

* flex-grow

  `flex-grow`属性定义项目的放大比例，默认为`0`，即如果存在剩余空间，也不放大

*  flex-shrink

  `flex-shrink`属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。0,则不缩小，图形不缩小

* flex-basis

  `flex-basis`属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。

* flex

  `flex`属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。

​	   该属性有两个快捷值：`auto` (`1 1 auto`) 和 none (`0 0 auto`)。

* align-self

  `align-self`属性允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`。

* flexGrow="1"

  实现权重比



