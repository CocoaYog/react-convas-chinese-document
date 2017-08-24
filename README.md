# react-convas-chinese-documents
# react-canvas 中文文档


# react-canvas

[简介博客地址](http://engineering.flipboard.com/2015/02/mobile-web)

React Canvas增加了React组件渲染到`canvas`而不是DOM的能力。
这个项目正在进行中。尽管大部分代码都是在flipboard.com上生产的，但是React画布绑定是相对较新的，而API可能会发生变化。

## 目的

构建面向移动设备的界面有悠久的历史，我们发现与本地应用程序相比，移动Web应用程序感觉缓慢的原因是DOM。 CSS动画和过渡是在Web上实现平滑动画的最快途径，但它们有一些限制。 React Canvas利用现代移动浏览器拥有硬件加速画布的事实。
虽然还有其他将画布绘图API绑定到React的尝试，但是它们更侧重于可视化和游戏。React Canvas的不同之处在于重点构建应用程序用户界面。事实上，它是对渲染到convas上行为的具体实现。
React Canvas带来了Web开发人员熟悉的一些API，并将它们与高性能绘图引擎混合在一起。

## 安装

React Canvas可通过npm获得：
`npm install react-canvas`

## React Canvas 组件
React Canvas提供了一组标准的React组件，这些组件是底层渲染实现的抽象（封装）。
### &lt;Surface&gt;

**Surface** 是顶级组件。你可以将它视为为绘图画布，并且可以在它里面放置其他组件。
### &lt;Layer&gt;
**Layer** 是构建其他组件的基础组件。常见的样式和属性，如top，width，left，height，backgroundColor和zIndex在此级别表示。
### &lt;Group&gt;
**Group**是容器组件。因为React强制所有组件在render（）中返回单个组件，所以**Group**可用于对一组子组件进行合并。**Group**也是优化滚动性能的重要组成部分，因为它允许渲染引擎缓存昂贵的绘图操作。
### &lt;Text&gt;
**Text** 是一个灵活的组件，支持多行截断，这在DOM中历来是非常困难和昂贵的。
### &lt;Image&gt;
**Image** 和平时使用的Image一样。然而，它增加了一些能力：隐藏图像，直到它被完全加载，并且可以在加载时淡入。
### &lt;Gradient&gt;
**Gradient**可用于设置组或表面的背景。

```javascript
  render() {
    ...
    return (
      <Group style={this.getStyle()}>
        <Gradient style={this.getGradientStyle()} 
                  colorStops={this.getGradientColors()} />
      </Group>
    );
  }
  getGradientColors(){
    return [
      { color: "transparent", position: 0 },
      { color: "#000", position: 1 }
    ]
  }
``` 

### &lt;ListView&gt;
**ListView**是一个触摸滚动容器，用于呈现列中的元素列表。你可以把它理解为web端的UITableView(注：UITableView是ios的表视图组件）。它使用了许多相同的优化，可以像iOS上的表视图和Android上的列表视图一样快速。
## 事件
React Canvas组件支持与正常的React组件相同的事件模型。但是，并不是所有的事件类型都被支持。
有关支持的事件的完整列表，请参阅[EventTypes](https://github.com/Flipboard/react-canvas/blob/master/lib/EventTypes.js)。
## 构建组件
这是一个将文本呈现在图像下方 的简单组件：

```javascript
var React = require('react');
var ReactCanvas = require('react-canvas');

var Surface = ReactCanvas.Surface;
var Image = ReactCanvas.Image;
var Text = ReactCanvas.Text;

var MyComponent = React.createClass({

  render: function () {
    var surfaceWidth = window.innerWidth;
    var surfaceHeight = window.innerHeight;
    var imageStyle = this.getImageStyle();
    var textStyle = this.getTextStyle();

    return (
      <Surface width={surfaceWidth} height={surfaceHeight} left={0} top={0}>
        <Image style={imageStyle} src='...' />
        <Text style={textStyle}>
          Here is some text below an image.
        </Text>
      </Surface>
    );
  },

  getImageHeight: function () {
    return Math.round(window.innerHeight / 2);
  },

  getImageStyle: function () {
    return {
      top: 0,
      left: 0,
      width: window.innerWidth,
      height: this.getImageHeight()
    };
  },

  getTextStyle: function () {
    return {
      top: this.getImageHeight() + 10,
      left: 0,
      width: window.innerWidth,
      height: 20,
      lineHeight: 20,
      fontSize: 12
    };
  }

});
```

## ListView
许多移动界面都会用到无限长的滚动列表。 React Canvas提供了ListView组件来做到这一点。因为ListView在视图外虚拟元素，所以传递子对象不同于在render（）中声明子节点的正常的React组件。

 `numberOfItemsGetter`, `itemHeightGetter` and `itemGetter`等 props 是必需的。
 
 
 ```javascript
 
var ListView = ReactCanvas.ListView;

var MyScrollingListView = React.createClass({

  render: function () {
    return (
      <ListView
        numberOfItemsGetter={this.getNumberOfItems}
        itemHeightGetter={this.getItemHeight}
        itemGetter={this.renderItem} />
    );
  },

  getNumberOfItems: function () {
    // Return the total number of items in the list
  },

  getItemHeight: function () {
    // Return the height of a single item
  },

  renderItem: function (index) {
    // Render the item at the given index, usually a <Group>
  },

});


```
 
请参阅[时间轴示](https://github.com/Flipboard/react-canvas/blob/master/examples/timeline/app.js)例以获取更完整的示例。

目前，ListView要求每个项目的高度相同。未来版本将支持可变高度项目。

## Text sizing
React Canvas提供了用于计算文本度量的`measureText`函数。时间轴示例中的页面组件包含使用measureText实现精确多行折叠文本的示例。自定义字体当前不受支持，但将在以后的版本中添加。
## css-layout
使用[css-layout](https://github.com/facebook/yoga)来设置React Canvas组件的样式，目前还处于的实验阶段。这是比使用标准CSS样式和flexbox定义组件样式更具表现力的方式。未来的版本可能不支持外部css-layout。在作为核心布局原则使用之前，仍需要对性能影响进行调研。

请参阅[css-layout](https://github.com/Flipboard/react-canvas/tree/master/examples/css-layout)示例。
## 适配性
这方面需要进一步的探索。使用“后备内容”（convas DOM子树）允许屏幕阅读器（如VoiceOver）与内容进行交互。我们已经使用我们测试过的iOS设备看到了不同的结果。此外，还有一个标准的焦点管理，但浏览器不支持。[Bespin](http://vimeo.com/3195079)在2009年提出的一种方法是保持与canvas中渲染的元素同步的[并行DOM(parallel DOM)](https://robertnyman.com/2009/04/03/mozilla-labs-online-code-editor-bespin/#comment-560310)。
## 运行样例
```
npm install
npm start
```

这将启动一个位于端口8080的实时重加载服务器。要想覆盖默认服务器和实时重加载端口，请使用PORT和/或RELOAD_PORT环境变量运行npm启动即可。
## 使用 webpack

需要使用[brfs](https://github.com/substack/brfs)转换后，才能使用webpack的项目。

```
npm install -g brfs
npm install --save-dev transform-loader brfs
```

然后将brfs转换添加到您的webpack config文件中

```
module: {
  postLoaders: [
    { loader: "transform?brfs" }
  ]
}
```


## 贡献者
我们欢迎提供错误修复，新特性和改进React Canvas的意见或建议。在主要资料库的贡献者必须接受Flipboard的Apache风格的[个人贡献者许可协议（CLA）](https://docs.google.com/forms/d/1gh9y6_i8xFn6pA15PqFeye19VqasuI9-bGp_e0owy74/viewform)，才能合并任何更改。




