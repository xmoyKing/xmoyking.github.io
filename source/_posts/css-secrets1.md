---
title: CSS揭秘笔记1
categories: css
tags:
  - css
  - css-secrets
date: 2018-01-31 20:57:35
updated: 2018-01-31 20:57:35
---

本系列笔记来自《CSS揭秘》。

PS:本书在网上有公开的文字版本PDF电子书。

本节主要介绍一些具体CSS版本内容及编程技巧。

### Web标准
#### CSS的各种版本及其具体内涵
CSS 1 的规范发表于 1996 年，它非常短，而且比较简单。

CSS 2 发表于 1998 年，它的定义更加严格，囊括了更多的功能，此时，规范的篇幅暴增，凭人的记忆已经无能为力了。

在 CSS 2 之后，CSS 工作组意识到这门语言已经变得非常庞大，再也无法把它塞进单个规范中了。这样不仅阅读和编辑极其困难，而且限制了 CSS本身的快速发展。一项规范如果要推进到最终阶段，其中的每项特性都必须具备两个独立的实现和全面的测试。原先的那种方式已经玩不转了。因此，将 CSS 打散到多个不同的规范（ 模块 ）中，每个模块都可以独立更新版本。这其中，那些延续 CSS 2.1 已有特性的模块会升级到 3 这个版本号。比如以下模块：
- CSS 语法（ http://w3.org/TR/css-syntax-3 ）
- CSS 层叠与继承（ http://w3.org/TR/css-cascade-3 ）
- CSS 颜色（ http://w3.org/TR/css3-color ）
- 选择符（ http://w3.org/TR/selectors ）
- CSS 背景与边框（ http://w3.org/TR/css3-background ）
- CSS 值与单位（ http://w3.org/TR/css-values-3 ）
- CSS 文本排版（ http://w3.org/TR/css-text-3 ）
- CSS 文本装饰效果（ http://w3.org/TR/css-text-decor-3 ）
- CSS 字体（ http://w3.org/TR/css3-fonts ）
- CSS 基本 UI 特性（ http://w3.org/TR/css3-ui ）

此外，如果某个模块是前所未有的新概念，那它的版本号将从 1 开始。比如下面这些：
- CSS 变形（ http://w3.org/TR/css-transforms-1 ）
- 图像混合效果（ http://w3.org/TR/compositing-1 ）
- 滤镜效果（ http://w3.org/TR/filter-effects-1 ）
- CSS 遮罩（ http://w3.org/TR/css-masking-1 ）
- CSS 伸缩盒布局（ http://w3.org/TR/css-flexbox-1 ）
- CSS 网格布局（ http://w3.org/TR/css-grid-1 ）

尽管“CSS3”这个名词非常流行，但它实际上并没有在任何规范中定义过。这一点跟 CSS 2.1 或更早的 CSS 1 不一样。真正的情况是，绝大多数在提到这个词时，指的是一个非正式的集合，它包括 CSS 规范第三版（ Level 3 ）再加上一些版本号还是 1 的新规范。

#### 浏览器前缀
在标准的开发过程中，总是有大大的“第 22 条军规”挡在面前：标准的工作组需要网页开发者这一端的输入，以确保各项规范可以处理真实的开发需求；但是开发者往往没有兴趣尝试那些在生产环境中还不能使用的东西。当实验性的技术被广泛应用到生产时，工作组就被这些技术早期的、实验性的版本捆住手脚了，因为一旦这些技术有变动，那些已经在用这些技术的网站就挂了。显然，这完全否定了让开发者尝试早期标准的好处。

这些年来，为了解决这个难题，许多方案被提了出来，但都不够完美。饱受诟病的浏览器前缀就是其中之一。这个方案是指每个浏览器都可以实现这些实验性的（ 甚至是私有的、非标准的 ）特性，但要在名称前面加上自己特有的前缀。最常见的前缀分别是 Firefox 的 -moz- 、IE 的 -ms- 、Opera的 -o- 以及 Safari 和 Chrome 的 -webkit- 。

网页开发者可以自由地尝试这些加了前缀的特性，并把试用结果反馈给工作组，而工作组随后会将这些反馈吸收到规范之中，并且逐渐完善该项特性的设计。由于最终标准化的版本会有一个不同的名称（ 没有前缀 ），它在实际应用中就不会跟加前缀版本相冲突了。

听起来不错，对吧？但是现实与我们的期望往往有很大的落差。当开发者发现这些实验性的、加了前缀的属性可以轻而易举地实现以前大费周章才能达到的效果时，他们就开始滥用了。这些加了浏览器前缀的属性迅速成为 CSS 领域的一大潮流。网上的教程会写到它们，StackOverflow 上的问答会提到它们……很快，几乎每个有上进心的 CSS 开发者都开始争先恐后地使用它们。

不久，网页开发者们就发现，在使用这些神奇的新特性时，如果只写出当下有效的浏览器前缀，就意味着以后要经常回来打补丁：每当又一个浏览器实现了这个新特性时，他们都需要多加一行。跟进各个特性在各个浏览器下是不是要加前缀，光是想想就让人头皮发麻。开发者会怎样应对？那就是先发制人地加上所有可能的浏览器前缀，再把无前缀的版本放在最后，以图一劳永逸。我们最终写出的代码可能就是这样的：
```css
-moz-border-radius: 10px;
-ms-border-radius: 10px;
-o-border-radius: 10px;
-webkit-border-radius: 10px;
border-radius: 10px;
```
这里面有两条声明是完全多余的： -ms-border-radius 和 -o-border-radius 这两个属性从来没有在任何浏览器中出现过，因为 IE 和 Opera 从一开始就是直接实现 border-radius 这个无前缀版本的。显然，把每个声明都重复五遍是相当枯燥的，而且很难维护。因此出现某个工具来把这项工作自动化只是个时间问题。
- 像 CSS3, Please!（ http://css3please.com ）和 pleeease（ http://pleee-ase.io/playground.html ）这样的网站允许你把无前缀的 CSS 代码粘贴进去，它们会自动帮你把必要的前缀都加好。这类网站是“前缀危机”所催生出的第一批工具，很快就过气了，因为跟其他方案相比，它们的使用成本太高了。
- Autoprefixer（ https://github.com/ai/autoprefixer ）采用 Can I Use...（ http://caniuse.com ）的数据库来判断哪些前缀是需要添加的；此外，它是在本地完成编译的，类似预处理器。
- 作者自己开发的 -prefix-free（ http://leaverou.github.io/prefixfree ）会在浏览器中进行特性检测，来决定哪些前缀是需要的。它的好处在于几乎不需要更新，因为其所有信息都是用一份属性清单在真实的浏览器环境中跑出来的结果。
- 类 似 Stylus（ http://stylus-lang.com/ ） 、 LESS（ http://lesscss.org ） 或 Sass（ http://sass-lang.com ）的预处理器并不自带任何加前缀的方法，但很多人开发过一些能为常用属性加前缀的 mixin；社区中也有一些库提供了这类 mixin。

由于网页开发者使用无前缀的属性是想确保代码的向前兼容，那么工作组想要修改这些无前缀语法就变得不可能了。基本上就跟这些半生不熟的早期规范绑在一起了，只能通过极其有限的途径来修改它们。用不了多久，这个“坑”里的每个人就会意识到，浏览器前缀已是一场史诗般的失败。

最近，浏览器厂商已经很少以前缀的方式来实验性地实现新特性了。取而代之的是，这些实验性特性需要通过配置开关来启用，这可以有效防止开发者在生产环境中使用它们，因为你不能要求用户为了正确地浏览你的网站而去修改浏览器设置。当然，这会导致一个后果：尝试这些实验性特性的开发者会减少；但工作组仍然会得到足够多的反馈，甚至是更高质量的反馈（ 你可能会质疑 ），同时还避免了浏览器前缀的所有缺点。不过还需要很长的时间，才能从浏览器前缀所引发的涟漪效应中解脱出来。

### CSS 编码技巧
#### 尽量减少代码重复
在软件开发中，保持代码的 DRY 和可维护性是最大的挑战之一，而这句话对 CSS 也是适用的。在实践中，代码可维护性的最大要素是尽量减少改动时要编辑的地方。举例来说，如果在放大一个按钮时需要在一堆规则中进行 10 处修改，那就很可能会漏改其中某处，当你在给别人善后时更是如此。即使这些要修改的地方很明显，或者最终可以找齐它们，但你还是浪费了时间，原本可以利用这些时间来做点更有意义的事情。

而且，这还不仅仅是后期修改的问题。灵活的 CSS 通常更容易扩展：在写出基础样式之后，只用极少的代码就可以扩展出不同的变体，因为只需覆盖一些变量就可以了。让我们来看一个例子。先来看看下面这段 CSS，它给按钮添加了一些效果:
```css
padding: 6px 16px;
border: 1px solid #446d88;
background: #58a linear-gradient(#77a0bb, #58a);
border-radius: 4px;
box-shadow: 0 1px 5px gray;
color: white;
text-shadow: 0 -1px 1px #335166;
font-size: 20px;
line-height: 30px;
```
这段代码在可维护性方面存在一些问题，我们来一一修复。最软的杮子应该是跟字体尺寸相关的部分了。如果我们决定改变字号 1① （可能是为了生成一个更大、更重要的按钮），就得同时调整行高，因为这两个属性都写成了绝对值。更麻烦的是，行高并没有反映出它跟字号的关系，因此我们还得做些算术，算出字号改变之后的行高该是多少。当某些值相互依赖时，应该把它们的相互关系用代码表达出来。在这个例子中，行高是字号的 1.5 倍。因此，把代码改成下面这样会更易维护：
```css
font-size: 20px;
line-height: 1.5;
```
既然跨出了这一步，我们为什么还把字号定为绝对长度值呢？没错，绝对值很容易掌控，但每当你想要修改它们的时候，它们都会回头反咬你一口。比如说，如果我们决定把父级的字号加大，就不得不修改每一处使用绝对值作为字体尺寸的样式。如果改用百分比或 em 单位就好多了：
```css
font-size: 125%; /* 假设父级的字号是 16px */
```
现在，如果我们改变父级的字号，按钮的尺寸就会随之变化。但是，它看起来很不协调，因为所有其他效果都是为一个小按钮设计的，并没有跟着缩放。如果我们把这些长度值都改成 em 单位，那这些效果的值就都变成可缩放的了，而且是依赖字号进行缩放 1① 。按照这种方法，我们就可以在一处控制按钮的所有尺寸样式了：
```css
padding: .3em .8em;
border: 1px solid #446d88;
background: #58a linear-gradient(#77a0bb, #58a);
border-radius: .2em;
box-shadow: 0 .05em .25em gray;
color: white;
text-shadow: 0 -.05em .05em #335166;
font-size: 125%;
line-height: 1.5;
```
这里我们希望字号和其他尺寸能够跟父级的字号建立关联，因此采用了 em 单位。但在某些情况下，你可能希望这些尺寸是和根级字号（即 html 元素的字号）相关联的，此时使用em 可能会导致复杂的计算。在这种情况下，你可以使用 rem 单位。在 CSS 中，相关性是一个很重要的特性，但你得想清楚到底哪些东西是真正相关的。

现在我们的大号按钮看起来更像是一个原按钮的等比例放大版本了。请注意还有一些长度值是绝对值。此时就需要重新审视到底哪些效果应该跟着按钮一起放大，而哪些效果是保持不变的。比如在这个例子中，我们希望按钮的边框粗细保持在 1px ，不受按钮尺寸的影响。

不过，让按钮变大或变小并不是我们唯一想要改动的地方。颜色是另一个重要的变数。比如，假设我们要创建一个红色的取消按钮，或者一个绿色的确定按钮，该怎么做呢？眼下，我们可能需要覆盖四条声明（ border-color 、 background 、 box-shadow 和 text-shadow ），而且还有另一大难题：要根据按钮的亮面和暗面相对于主色调 #58a 变亮和变暗的程度来分别推导出其他颜色各自的亮色和暗色版本。此外，若我们想把按钮放在一个非白色的背景之上呢？显然使用灰色（ gray ）作投影只适用于纯白背景的情况。

其实只要把半透明的黑色或白色叠加在主色调上，即可产生主色调的亮色和暗色变体，这样就能简单地化解这个难题了：
```css
border: 1px solid rgba(0,0,0,.1);
background: #58a linear-gradient(hsla(0,0%,100%,.2),
transparent);
```
推荐使用HSLA而不是RGBA来产生半透明的白色，因为它的字符长度更短，敲起来也更快。这归功于它的重复度更低。

现在我们只要覆盖 background-color 属性，就可以得到不同颜色版本的按钮了
```css
button.cancel {
  background-color: #c00;
}
button.ok {
  background-color: #6b0;
}
```

##### 1. 代码易维护 vs. 代码量少
有时候，代码易维护和代码量少不可兼得。比如在上面的例子中，我们最终采用的代码甚至比一开始的版本略长。来看看下面的代码片断，我们要为一个元素添加一道 10px 宽的边框，但左侧不加边框。
```css
border-width: 10px 10px 10px 0;
```
只要这一条声明就可以搞定了，但如果日后要改动边框的宽度，你需要同时改三个地方。如果把它拆成两条声明的话，改起来就容易多了，而且可读性或许更好一些：
```css
border-width: 10px;
border-left-width: 0;
```

##### 2.  currentColor
在 CSS 颜色（第三版）（http://w3.org/TR/css3-color）规范中，增加了很多新的颜色关键字，比如 lightgoldenrodyellow 等，其实并不是很常用。但是，我们还得到了一个特殊的颜色关键字 currentColor ，它是从SVG 那里借鉴来的。这个关键字并没有绑定到一个固定的颜色值，而是一直被解析为 color 。实际上，这个特性让它成为了 CSS 中有史以来的第一个变量 1① 。虽然功能很有限，但它真的是个变量。

举个例子，假设我们想让所有的水平分割线（所有 hr 元素）自动与文本的颜色保持一致。有了 currentColor 之后，我们只需要这样写：
```css
hr {
height: .5em;
background: currentColor;
}
```
你可能已经注意到了，很多已有的属性也具有类似的行为。举例来说，如果你没有给边框指定颜色，它就会自动地从文本颜色那里得到颜色。这是因为 currentColor 本身就是很多 CSS 颜色属性的初始值，比如border-color 和 outline-color ，以及 text-shadow 和 box-shadow 的颜色值，等等。

未来，我们在原生 CSS 中拥有处理颜色的函数后， currentColor 就会变得更加有用，因为我们可以用这些函数来产生其各种深浅明暗的变体。

##### 3. 继承
尽管绝大多数开发者都知道有 inherit 这个关键字，但还是很容易遗忘它。 inherit 可以用在任何 CSS 属性中，而且它总是绑定到父元素的计算值（对伪元素来说，则会取生成该伪元素的宿主元素）。举例来说，要把表单元素的字体设定为与页面的其他部分相同，你并不需要重复指定字体属性，只需利用 inherit 的特性即可：
```css
input, select, button { font: inherit; }
```
与此类似，要把超链接的颜色设定为与页面中其他文本相同，还是要用inherit ：
```css
a { color: inherit; }
```

这个 inherit 关键字对于背景色同样非常有用。举个例子，在创建提示框的时候，你可能希望它的小箭头能够自动继承背景和边框的样式
```css
.callout { position: relative; }
.callout::before {
  content: "";
  position: absolute;
  top: -.4em; left: 1em;
  padding: .35em;
  background: inherit;
  border: inherit;
  border-right: 0;
  border-bottom: 0;
  transform: rotate(45deg);
}
```

#### 相信你的眼睛，而不是数字
人的眼睛并不是一台完美的输入设备。有时候精准的尺度看起来并不精准，而我们的设计需要顺应这种偏差。举一个在视觉设计领域广为人知的例子吧，我们的眼睛在看到一个完美垂直居中的物体时，会感觉它并不居中。实际上，我们应该把这个物体从几何学的中心点再稍微向上挪一点，才能取得理想的视觉效果。

与此类似，在字体设计领域广为人知的是，圆形的字形（比如 0）与矩形字形相比，需要稍微放大一些，因为我们倾向于把圆形感知得比其实际尺寸更小一些。

这些视觉上的错觉在任何形式的视觉设计中都普遍存在，需要我们有针对性地进行调整。一个非常常见的例子是给一个文本容器设置内边距。不论内容文本有多长，是一个单词还是几个段落，这个问题都会出现。假如我们给容器的四边指定相同的内边距，则实际效果看起来并不相等。原因在于，字母的形状在两端都比较整齐，而顶部和底部则往往参差不齐，从而导致你的眼睛把这些参差不齐的空缺部分感知为多出来的内边距。因此，如果我们希望四边的内边距看起来是基本一致的，就需要减少顶部和底部的内边距。

#### 关于响应式网页设计
响应式网页设计（Responsive Web Design，RWD）在最近几年风靡一时。但是，人们大多只是在不停念叨网页的“响应式”是多么重要，而极少有人去深入探讨怎样才能做好响应式设计。

比较常见的实践是用多种分辨率来测试一个网站，然后添加越来越多的媒体查询（Media Query）规则来修补网站在这些分辨率下出现的问题。然而对于今后的 CSS 改动来说，每个媒体查询都会增加成本，而这种成本是不应轻易上升的。未来每次对 CSS 代码的修改都要求我们逐一核对这些媒体查询是否需要配合修改，甚至可能要求我们反过来修改这些媒体查询的设置。这一点常常被我们忽略，后患无穷。你添加的媒体查询越多，你的 CSS代码就会变得越来越经不起折腾。

这并不是说媒体查询是一种不良实践。只要用对了，它就是利器。但是，你只应该把它作为最后的手段。比如你想把网站做得弹性灵活，但其他尝试全都失败了；或者我们希望在较大或较小的视口下完全改变网站的设计形态（譬如，把侧栏改成水平布局）。我这么说的原因在于，媒体查询不能以一种连续的方式来修复问题。它们的工作原理基于某几个特定的阶梯（亦称“断点”），如果大部分样式代码并不是以弹性的方式来编写的，那么媒体查询能做的只是修补某个特定分辨率下的特定问题——这本质上只是把灰尘扫到地毯下面而已。

当然，有一点上面并没有提到，媒体查询的断点不应该由具体的设备来决定，而应该根据设计自身来决定。这不仅是因为我们的网站需要面向的设备太多了（尤其是考虑到未来的设备时），还因为一个网站在桌面端可能会以任意尺寸的窗口来显示。如果你有信心自己的设计在任何可能出现的视口尺寸下都能良好工作，谁关心这些设备的分辨率具体是多少呢？

遵从“尽量减少代码重复”所描述的原则对此也是有帮助的，因为你不需要去覆盖媒体查询里同样数量的声明。这在本质上减轻了它们所产生的维护成本,下面还有一些建议，可能会帮你避免不必要的媒体查询。
 - 使用百分比长度来取代固定长度。如果实在做不到这一点，也应该尝试使用与视口相关的单位（ vw 、 vh 、 vmin 和 vmax ），它们的值解析为视口宽度或高度的百分比。
 - 当你需要在较大分辨率下得到固定宽度时，使用 max-width 而不是width ，因为它可以适应较小的分辨率，而无需使用媒体查询。
 - 不要忘记为替换元素（比如 img 、 object 、 video 、 iframe 等）设置一个 max-width ，值为 100% 。
 - 假如背景图片需要完整地铺满一个容器，不管容器的尺寸如何变化，background-size: cover 这个属性都可以做到。但是，我们也要时刻牢记——带宽并不是无限的，因此在移动网页中通过 CSS 把一张大图缩小显示往往是不太明智的。
 - 当图片（或其他元素）以行列式进行布局时，让视口的宽度来决定列的数量。弹性盒布局（即 Flexbox）或者 display: inline-block加上常规的文本折行行为，都可以实现这一点。
 - 在使用多列文本时，指定 column-width （列宽）而不是指定column-count （列数），这样它就可以在较小的屏幕上自动显示为单列布局。

总的来说，我们的思路是尽最大努力实现弹性可伸缩的布局，并在媒体查询的各个断点区间内指定相应的尺寸。当网页本身的设计足够灵活时，让它变成响应式应该只需要用到一些简短的媒体查询代码。

如果你发现自己需要一大堆媒体查询才能让设计适应大大小小的屏幕，那么不妨后退一步，重新审视你的代码结构。因为在所有的情况下，响应式都不是唯一需要考虑的问题。

#### 合理使用简写
你可能知道，以下两行 CSS 代码并不是等价的：
```css
background: rebeccapurple;
background-color: rebeccapurple;
```
前者是简写，它可以确保你得到 rebeccapurple 纯色背景；但如果你用的是展开式的单个属性（ background-color ），那这个元素的背景最终有可能会显示为一个粉色的渐变图案、一张猫的图片或其他任何东西，因为同时可能会有一条 background-image 声明在起作用。在使用展开式属性的写法时，通常会遇到这样的问题：展开式写法并不会帮助你清空所有相关的其他属性，从而可能会干扰你想要达到的效果。

当然，你可以把所有的展开式属性全都设置一遍，然后收工，但你可能会漏掉几个；又或者，CSS 工作组可能会在未来引入更多的展开式属性，那时你的代码就无法完全覆盖它们了。不要害怕使用简写属性。合理使用简写是一种良好的防卫性编码方式，可以抵御未来的风险。当然，如果我们要明确地去覆盖某个具体的展开式属性并保留其他相关样式，那就需要用展开式属性，就像我们在“尽量减少代码重复”一节中为了得到按钮的其他颜色版本所做的那样。

展开式属性与简写属性的配合使用也是非常有用的，可以让代码更加DRY。对于那些接受一个用逗号分隔的列表的属性（比如 background ），尤其如此。下面的例子可以很好地解释这一点：
```css
background: url(tr.png) no-repeat top right / 2em 2em,
            url(br.png) no-repeat bottom right / 2em 2em,
            url(bl.png) no-repeat bottom left / 2em 2em;
```
请注意 background-size 和 background-repeat 的值被重复了三遍，尽管每层背景的这两个值确实是相同的。其实我们可以从 CSS 的“列表扩散规则”那里得到好处。它的意思是说，如果只为某个属性提供一个值，那它就会扩散并应用到列表中的每一项。因此，我们可以把这些重复的值从简写属性中抽出来写成一个展开式属性：
```css
background: url(tr.png) top right,
            url(br.png) bottom right,
            url(bl.png) bottom left;
background-size: 2em 2em;
background-repeat: no-repeat;
```

你可能已经注意到前面那个背景属性简写的例子了：在 background 简写属性中指定 background-size时，需要同时提供一个 background-position 值（哪怕它的值就是其初始值也需要写出来），而且还要使用一个斜杠（ / ）作为分隔。为什么有些简写的语法如此怪异？

这通常都是为了消除歧义。在这个例子中， top right 显然是 background-position ，而 2em 2em 是background-size ，不管它们的顺序如何。但是，请设想一下 50% 50% 这样的值，它到底是 background-size 还是 background-position 呢？当你在使用展开式属性时，CSS 解析器明白你的意图；而当你使用简写属性时，解析器需要在没有属性名提示的情况下弄清楚 50% 50% 到底指什么。这就是需要引入斜杠的原因。

对绝大多数的简写属性来说，并没有这样的歧义问题，因而简写属性的多个值往往可以随意排列。不过，我还是建议你养成随手查阅语法的好习惯，以免犯错。如果你对正则表达式以及规范的语法描述方式（grammar）很熟悉的话，不妨直接在相关规范中查询语法描述。如果要确定某个属性的值是否有明确的顺序要求，这可能是最快的方式。


#### 应该使用预处理器吗
你很可能听说过像 Stylus（ http://stylus-lang.com/ ）、Sass（ http://sass-lang.com/ ）或 LESS（ http://lesscss.org/ ）这样的 CSS 预处理器。它们为 CSS的编写提供提供了一些便利，比如变量、mixin、函数、规则嵌套、颜色处理等。

如果使用得当，它们在大型项目中可以让代码更加灵活，而 CSS 自身在这方面确实有很大局限。只要我们在代码健壮性、灵活性和 DRY 方面有追求，就会感受到 CSS 在这方面的局限。不过，预处理器也不是完美无缺的。

 - CSS 的文件体积和复杂度可能会失控。即使是简洁明了的源代码，在经过编译之后也可能会变成一头从天而降的巨兽。
 - 调试难度会增加，因为你在开发工具中看到的 CSS 代码并不是你写的源代码。不过这个问题已经大大好转了，因为已经有越来越多的调试工具开始支持 SourceMap。SourceMap 是一种非常酷的新技术，正是为了解决这个痛点而生的，它会告诉浏览器哪些编译生成的CSS 代码对应哪些预处理器 CSS 代码，精确到行号。
 - 预处理器在开发过程中引入了一定程度的延时。尽管它们通常很快，但仍然需要差不多一秒钟的时间来把你的源代码编译成 CSS，而你不得不等待这段时间才能预览到代码的效果。
 - 每次抽象都必然会带来更高的学习成本，每当有新人加入到我们的代码库中，这个问题都会重演。他要么已经对我们选择的这门预处理器“方言”很熟悉，要么得从头学。这意味着我们要么强制协作者接受我们的选择，要么花费额外的时间来培训，而这两者都不是我们想要的。
 - 另外，别忘了还有抽象泄漏法则：“所有重大的抽象机制在某种程度上都存在泄漏的情况。”预处理器是由人类写出来的，就像所有由人类写出来的大型程序一样，它们有它们自己的 bug。这些 bug 可能会潜伏很久，因为我们很少会怀疑预处理器的某个 bug 才是我们CSS 出错的幕后元凶。

除了上面列出的这些问题，预处理器还可能导致这种风险：网站开发者可能会不自觉地“依赖”和“滥用”。因为在某些时候，预处理器并不必要。比如在小型项目中；或者在未来，说不定预处理器最受欢迎的那些特性都被加入了原生 CSS 中。很惊讶吗？没错，很多受预处理器启发的特性都已经以各种方式融入到原生 CSS 中了。

 - 有一份关于（跟变量类似的）自定义属性的草案，叫作 CSS 自定义属性暨层叠式变量（http://w3.org/TR/css-variables-1）。
 - CSS 值与单位（第三版）中的 calc() 函数，不仅在处理运算时非常强大，而且已经得到了广泛的支持，当下可用。
 - CSS 颜色（第四版）（http://dev.w3.org/csswg/css-color）引入的 color()函数会提供颜色运算方法。
 - 关于嵌套，CSS 工作组内部正在进行一些正式的讨论，甚至以前还有过一份相关的草案（ED）。

请注意，这些原生特性通常比预处理器提供的版本要强大得多，因为它们是动态的。举个例子，预处理器完全不知道如何完成100% - 50px 这样的计算，因为在页面真正被渲染之前，百分比值是无法解析的。但是，原生CSS 的 calc() 在计算这样的表达式时没有任何压力。与此类似，下面这样的变量玩法在预处理器中是不可能做到的：
```css
ul { --accent-color: purple; }
ol { --accent-color: rebeccapurple; }
li { background: var(--accent-color); }
```
你看清楚这段代码的意图了吗？在有序列表中，列表项的背景色将是rebeccapurple ；但在无序列表中，列表项的背景色将是 purple 。试试用预处理器能否做到！当然，在这个例子中，我们可以直接使用后代选择符，只不过这个例子的重点在于向你展示 CSS 的原生变量所具备的动态性。

上面提到的原生 CSS 特性绝大多数在目前还没有得到很好的支持，因此在很多情况下，如果可维护性很重要（它确实很重要），使用预处理器是不可避免的。我的建议是，在每个项目开始时使用纯 CSS，只有当代码开始变得无法保持 DRY 时，才切换到预处理器的方案。为了避免可能发生的“依赖”或“滥用”，在引入预处理器的问题上需要冷静决策，不应该在每个项目一开始时就不动脑筋顺着惯性来。