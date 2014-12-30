#### 样式的来源：  
作者，即写样式的人。  
用户，使用的人。因为一些终端用户可以使用一些样式改变页面的呈现。  
用户代理，一般指浏览器。  

#### 替代元素：（img, embedded document, applet）
其自身固有一些样式。如width height。如果浏览器认为该元素向第三方泄露信息，那么将不会让它拥有固有属性。CSS渲染机制不会管替代元素中的内容。

#### 固有样式：
就是元素自己设置的模型。

#### 忽略样式：
如果样式是不合规范的，或者元素没有该样式。

#### 标签约束
在html4中，即使文档中没有head元素，也会在渲染时推测出来。同样，解释器懂得p, li在何时结束。但在XHML（或其他XML为基础的的语言）会是另一种表现：没有推测，且所有元素必须有结束符。

#### CSS 2.1解析：
如果是这样：
```javascript
@import "subs.css";
h1 { color: blue }
@import "list.css";
```
那么将被解析成这样：
```javascript
@import "subs.css";
h1 { color: blue }
```
如果是这样：
```javascript
@import "subs.css";
@media print {
  @import "print-main.css";
  body { font-size: 10pt }
}
h1 {color: blue }
```
那么将被解析成这样：
```javascript
@import "subs.css";
@import "print-main.css" print;
@media print {
  body { font-size: 10pt }
}
h1 {color: blue }
```
####元素的最终样式经历了如下几个阶段计算：
1、指定的值（specified values）
2、继承的值
3、如有必要，将其转换为一个绝对的值
4、按照设备等限制转换
+ specified values:
1、如果是级联结果，就使用它。
2、否则，如果有继承样式，那么就使用继承父元素的计算过后的样式。
3、否则，就使用属性的默认值。

+ computed values:
经过计算的样式

+ used values:
计算样式尽可能不用去处理文档。但是，有些时候必须这么做，例如如果宽度使用百分比，那么必须要等到外部容器确定后才可以。而将百分比值按外部容器的值计算好后的值称为used values.

+ actual values:
used values原则上已经是要用来渲染的值了，但是UA在实际环境中，不一定按照该值做渲染。如果UA只能使用英寸做单位，又或者只能使用黑白色调做渲染，那么这时候经过所有近似值计算的最终呈现依据我们称之为actual values.

+ @import：
@import必须放在其他规则前面（除了@charset）
用来引用样式文件，下面两种用法效果是一样的。
```javascript
@import "mystyle.css";
@import url("mystyle.css");
```

+ 为了UA能避免一些设备不支持的样式，可以在后面定义设备依赖。在URL后面加上单独的设备名称。如：
```javascript
@import url("fineprint.css") print;
@import url("bluish.css") projection, tv;
```
注：即使使用@import连接了两个在不同的地方但相同的样式，依然会被当成不同的样式处理。
#### 样式级联顺序：
1. 找出适用于当前设备的所有该元素的相关样式。
2. 根据重要程度和来源排序，按优先级从小到大排是：
    1. UA定义
    2. 用户定义
    3. 作者的一般定义
    4. 作者的重要定义
    5. 用户的重要定义
3. 按选择器的定义排序，特定选择器大于普通选择器。伪类和伪元素优先级等同于普通类普通元素。
4. 最后，按先后顺序排。如果有相同的优先级、来源和定义，那么使用最晚定义的样式。在@import中引用的样式被当成是先于本文件定义样式的前面。
除去“!import”，相比用户样式表，这种策略给了作者样式表更高的优先级。

CSS试图在用户样式和作者样式创造一个平衡点。默认情况下，作者样式将覆盖用户样式。
以“!import”结尾的样式较普通样式有更高的优先级，作者样式和用户样式都可以使用!import，而且用户样式中的!import样式将覆盖作者样式中的!import样式。这一措施给用户特定的需求控制权，提升了文档的可用性

#### 计算选择器优先级
如果是行内则为a=1,b=0,c=0,d=0  
以ID为选择器为a=0,b=1,c=0,d=0  
其他属性或伪类为a=0,b=0,c=1,d=0  
标签名或伪元素为a=0,b=0,c=0,d=1  

```javascript
*             {}  /* a=0 b=0 c=0 d=0 -> specificity = 0,0,0,0 */
 li            {}  /* a=0 b=0 c=0 d=1 -> specificity = 0,0,0,1 */
 li:first-line {}  /* a=0 b=0 c=0 d=2 -> specificity = 0,0,0,2 */
 ul li         {}  /* a=0 b=0 c=0 d=2 -> specificity = 0,0,0,2 */
 ul ol+li      {}  /* a=0 b=0 c=0 d=3 -> specificity = 0,0,0,3 */
 h1 + *[rel=up]{}  /* a=0 b=0 c=1 d=1 -> specificity = 0,0,1,1 */
 ul ol li.red  {}  /* a=0 b=0 c=1 d=3 -> specificity = 0,0,1,3 */
 li.red.level  {}  /* a=0 b=0 c=2 d=1 -> specificity = 0,0,2,1 */
 #x34y         {}  /* a=0 b=1 c=0 d=0 -> specificity = 0,1,0,0 */
 style=""          /* a=1 b=0 c=0 d=0 -> specificity = 1,0,0,0 */
```
#### 非CSS定义的样式优先级
如b标签会显示粗体，这些样式的优先级为0。在执行时，看做是在作者样式的最前面插入的样式。

#### 关于长度：
如果属性不支持负值，那么该样式将被忽略。
```javascript
em: 相关的font-size
ex: 相关的x-height
```
单位"em"等于应用到该元素上的字体大小。除非字体大小本身使用了“em”。这种情况下就参照父元素的字体大小。有时会用在垂直布局方面。
单位"ex"取决于元素的第一个可用字体。除非字体大小使用了“ex”。这种情况下参照父元素的ex
之所以叫“x-height”,是因为通常情况下，它等于字体中小写x的高度。字体中含不含“x”是没有影响的。
字体的"x-height"有多种途径获取，一些字体有x-height的可靠值，如果这个值不可用，UA会根据小写字符的高度确定。一种方法是看小写o以下距离基线的高度，减去其边界框上部的高度。如果没有办法获取x-height，那么就使用0.5em.

