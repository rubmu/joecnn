---
title: WPF 基础之控件与布局
date: 2013/07/12
tags: [c#, wpf]
---

### 一、什么是 WPF
&emsp;&emsp;WPF（Windows Presentation Foundation）是微软推出的基于Windows 的用户界面框架，属于.NET Framework 3.0的一部分。它提供了统一的编程模型、语言和框架，真正做到了分离界面设计人员与开发人员的工作；同时它提供了全新的多媒体交互用户图形界面。

### 二、WPF 做什么用？
&emsp;&emsp;WPF 的功能就是用来编写应用程序的表现层，至于业务逻辑层和数据层的开发可以使用其它专门的技术如：WCF（Windows Communication Foundation）、WF（Windows Workflow Foundation）。

### 三、WPF 元素
> 程序的本质是“数据+算法”，将用户输入的原始数据经过算法处理后展示给用户。  
控件是经过组件化的能够展示数据、响应用户操作的UI元素。

WPF UI 元素的类型：

名称 | 注释
---|---
ContentControl | 单一内容控件
HeaderedContentControl | 带标题的单一内容控件
ItemsControl | 以条目集合为内容的控件
HeaderedItemsControl | 带标题的以条目集合为内容的控件
Decorator | 控件装饰元素
Panel | 面板类元素
Adorner | 文字点缀元素
Flow Text | 流式文本元素
TextBox | 文本输入框
TextBlock | 静态文字
Shape | 图形元素

#### 1. ContentControl 族
本族元素的特点如下：
- 均派生自 ContentControl 类
- 它们都是控件（Control）
- 内容属性名称为 Content
- 只能由单一元素充当其内容

> 特点为内容只能由一个元素填充，不能同时填充多个元素在 Content 中

ContentControl 族包含的控件：

Button | ButtonBase | CheckBox | ComboBoxItem
---|---|---|---
ContentControl | Frame | GridViewColumnHeader | GroupItem
Label | ListBoxItem | ListViewItem | NavigationWidnwo
RadioButton | RepeatButton | ScrollViewer | StatusBarItem
ToggleButton | ToolTip | UserControl | Window

#### 2. HeaderedContentControl 族
本族元素的特点如下：
- 均派生自 HeaderedContentControl 类，是 ContentControl 的子类
- 它们都是控件，用于显示带标题的数据
- 除了显示内容区域外，还具有一个显示标题（Header）的区域
- 内容属性为 Content 和 Header
- 无论是 Content 还是Header 都只能容纳一个元素作为其内容

> 特点为 ContentControl 的子类，增加了一个标题内容的区域

HeaderContentControl 族包含的控件：

Expander | GroupBox | HeaderedContentControl | TabItem
---|---|---|---

#### 3. ItemsControl 族
本族元素的特点如下：
- 均派生自 ItemsControl 族
- 它们都是控件，用于显示列表化的数据
- 内容属性为 Items 或 ItemsSource
- 每种 ItemsControl 都对应有自己的条目内容（Item Container）

> 特点为显示列表元素，每行元素都包裹在 Item Container 中

ItemsControl 族包含的控件：

Menu | MenuBase | ContextMenu | ComboBox
---|---|---|---
ItemsControl | ListBox | ListView | TabControl
TreeView | Selector | StatusBar

ItemsControl 对应的 Item Container：

ItemsControl 名称| 对应的 Item Container
---|---
ComboxBox | ComboBoxItem
ContextMenu | MenuItem
ListBox | ListBoxItem
ListView | ListViewItem
Menu | MenuItem
StatusBar | StatusBarItem
TabControl | TabItem
TreeView | TreeViewItem

#### 4. HeaderedItemsControl 族
本族元素特点如下：
- 均派生自 HeaderedItemControl 类
- 它们都是控件，用于显示列表化的数据，同时可以显示一个标题
- 内容属性为 Items、ItemsSource、Header

与 ItemsControl 非常相似，本族控件只有3个：MenuItem、TreeViewItem、ToolBar。

#### 5. Decorator 族
> 本族元素是在 UI 上起一定的装饰效果。如可以使用 Border 元素为一些组织在一起的内容加个边框。如果需要组织在一起的内容能够自由缩放，则可以使用 ViewBox 元素。

本族元素的特点如下：
- 均派生自 Decorator 类
- 起 UI 装饰作用
- 内容属性为 Child
- 只能由单一元素充当内容

Decorator 族包含的元素：

ButtonChrome | ClassicBorderDecorator | ListBoxChrome | SystemDropShadowChrome
---|---|---|---
Border | LnkPresenter | BulletDecorator | ViewBox
AdornerDecorator | 

#### 6. TextBlock 和 TextBox
&emsp;&emsp;这两个控件最主要的功能是显示文本。TextBlock 只能显示文本不能编辑，所以又称静态文本。TextBox 则允许用户编辑内容。  
&emsp;&emsp;两者的内容属性均为 Text。  
&emsp;&emsp;TextBlock 可以使用丰富的印刷格式，可以设置 Inlines（印刷中的“行”）。

#### 7. Shape 族
&emsp;&emsp;Shape 族元素只是视觉元素，不是控件，专门用来在 UI 上绘制图形的一类元素。
本族元素特点如下：
- 均派生自 Shape 类
- 用于 2D 图形绘制
- 无内容属性
- 使用 Fill 属性设置填充，使用 Stroke 属性设置边线。

#### 8. Panel 族
所有用于 UI 布局的元素都属于这一族。  
本族元素的特点如下：
- 均派生自 Panel 抽象类
- 主要功能是控制 UI 布局
- 内容属性为 Children
- 内容可以是多个元素，Panel 元素将控制它们的布局

本族元素包含如下：

Canvas | DockPanel | Grid | TabPanel
---|---|---|---
StackPanel | WrapPanel | ToolBarPanel | ToolBarOverflowPanel
VirtualizingPanel | VietualizingStackPanel | UniformGrid

### 四、UI 布局
> 友好的用户界面和良好的用户体验离不开设计精良的布局。日常工作中， WPF 设计师工作量最大的两部分就是布局和动画，除了点缀性的动画外，大部分动画也是布局的转换，可见 UI 布局的重要性。

每种布局元素都有自己的特点，有自己的长处、优点，也有自己的短处和缺点，选择合适的布局元素，将极大简化编程，反之将会被迫实现一些布局控件已有的功能。

#### 1. 布局元素
> 传统的 Window Form 或 Asp.Net 开发中，一般把窗口或页面当作一个以左上角为原点的坐标系，控件依靠这个坐标系来布局，而 WPF 的控件有了 Content 概念，所以控件与控件之间又多了一种关系-包含。

WPF 中的布局元素有如下几个：

元素 | 描述
---|---
Grid | 网格，可以自定义行和列，并通过行列的数量、行高和列宽来调整控件的布局。近似于 HTML 中的 Table
StackPanel | 栈式面板。可将包含的元素在竖直或水平方向上排成一条直线，当移除一个元素后，后面的元素会自动向前移动以填充空缺
Canvas | 画布，内部元素可以使用像素为单位的绝对坐标进行定位，类似 Window Form 的布局方式
DockPanel | 泊靠式面板。内部元素可以选择泊靠方向，类似于 Windows Form 中设置控件的 Dock 属性
WrapPanel | 自动折叠面板，内部元素在排满一行后能够自动换行，类似 HTML 中的流式布局

#### 2. Grid
顾名思义，Grid 元素会以网格形式对内容元素进行布局。  
Grid 的特点如下：
- 可以定义任意数量的行和列
- 行的高度和列的宽度可以使用绝对数值、相对比例或自动调整的方式进行设定，并可以设置最大值和最小值
- 内部元素可以设置自己所在的行和列，还可以设置跨几行，跨几列
- 可以设置 Children 元素的对齐方向

基于这些特点，Grid 适用的场景有：
- UI 布局的大框架设计
- 大量 UI 元素需要成行或成列对齐的情况
- UI 整体尺寸改变时，元素需要保持固有的高度和宽度比例
- UI 后期可能有较大变更或扩展

#### 3. StackPanel
StackPanel 可以把内部元素在横向或纵向上紧凑排列、形成栈式布局，通俗讲就是把内部元素相垒积木一样“堆起来”，当把排在前面的积木抽掉几块后排在之后的元素会整体向前移动，补占原来元素的空间。  
基于这些特点，StackPanel 适用的场景有：
- 同类元素需要紧凑排列
- 移动其中的元素后能够自动补缺的布局。

StackPanel 使用3个属性来控制内部元素的布局：
> 如制作菜单或者列表

属性名称 | 数据类型 | 可取值 | 描述
---|---|---|---
Orientation | Orientation 枚举 | Horizontal、 Vertical | 决定内部元素排列方向
HorizontalAlignment | HorizontalAlignment 枚举 | Left、Center、Right、Stretch | 决定内部元素水平方向上的对齐方式
VerticalAlignment | VerticalAlignment 枚举 | Top、Center、Bottom、Stretch | 决定内部元素竖直方向上的对齐方式

#### 4. Canvas
Canvas 画布中布局就像在画布上画控件一样。通过设置 Canvas.Left 与 Canvas.Top 属性进行绝对定位的布局。
> 实际上应该尽量少使用 Canvas 布局，除非这个布局以后不会改变而且窗体尺寸固定。

Canvas 适用的场景包括：
- 一经设计基本不会再有改动的小型布局（如图标）
- 艺术性比较强的布局
- 需要大量使用纵横坐标进行绝对定位的布局

#### 5. DockPanel
DockPanel 内的元素会被附加上 DockPanel.Dock 这个属性，根据 Dock 属性值，元素会向指定的方向累积、切分 DockPanel 内部剩余的空间，就像船舶靠岸一样。
> DockPanel 按照元素顺序进行空间划分，按照顺序确定每个元素是否占据整个边界。

DockPanel 适用的场景包括：
- 需要填充满全部窗体
- 自动根据窗体大小进行改变

#### 6. WrapPanel
WrapPanel 内采用的是流式布局，使用 Orientation 属性来控制流延伸方向，使用 HorizontalAlignment 和 VerticalAlignment 来个属性来控制内部控件的对齐。在延伸方向上 WrapPanel 会排列尽可能多的控件，排不下的控件会新起一行或一列继续排列。
> WrapPanel 会根据窗体大小自动排列元素

WrapPanel 适用的场景包括：
- 实现瀑布流的元素排列
- 根据窗体大小自动调整布局

### 五、小结
本记主要记录了 WPF 控件的类型，任何一个 WPF 控件都不会脱离这几种类型，还记录了如何使用 UI 布局元素将控件排列在 UI 上。根据本记已经可以动手编写 WPF 程序了，在编写过程中再详细查询使用的元素属性。