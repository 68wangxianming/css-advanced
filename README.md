## CSS Layered

### 为什么要分层？
原因:
• CSS有语义化的命名约定和CSS层的分离，将有助于它的可扩展性，性能的提高 和代码的组织管理。
• 大量的样式，覆盖、权重和很多!important，分好层可以让团队命名统一规范， 方便维护。
• 有责任感地去命名你的选择器。

### BEM

https://en.bem.info/

BEM主要用来如何给项目命名。一个简单命名更容易让别人 一起工作。比如选项卡导航是一个块(Block)，这个块里的元素的是其中标签之一 (Element)，而当前选项卡是一个修饰状态(Modifier):
• block -代表了更高级别的抽象或组件
• block__element -代表.block的后代，用于形成一个完整的.block的整体。 • .block--modifier -代表.block的不同状态或不同版本。
• 修饰符使用的是_，子模块使用__符号。(不用一个-的原因是CSS单词连接)

```javascript  
<ul class="menu">
    <li class="menu__item">.....</li>
    <ll class="menu__item_state_current">.....</li>
</ul>
```

### ACSS

http://patternlab.io/

• 考虑如何设计一个系统的接口。原子(Atoms)是创建一个区块的最基本的特质， 比如说表单按钮。分子(Molecules)是很多个原子(Atoms)的组合，比如说一个表 单中包括了一个标签，输入框和按钮。生物(Organisms)是众多分子(Molecules) 的组合物，比如一个网站的顶部区域，他包括了网站的标题、导航等。而模板 (Templates)又是众多生物(Organisms)的结合体。比如一个网站⻚面的布局。而 最后的⻚面就是特殊的模板。

```javascript
.m-10{
    margin:10px;
}
.w-50{
    width:50px;
}
```   


## CSS WorkFlow

### CSS预处理器

less、sass、stylus

#### 预处理器常用规范

变量  
混合(Mixin)Extend   
嵌套规则   
运算  
函数  
Namespaces & Accessors Scope   
注释   

### CSS后处理器

CSS压缩CLEAN-CSS   
自动添加浏览器前缀Autoprefixer   
CSS更加美观排序CSScomb   
Rework取代Stylus   
后处理器发热   
前后通吃PostCSS   

![Image text](https://github.com/68wangxianming/css-advanced/blob/master/source-material/1.jpg)  

### cssnext和postcss-preset-env对比

https://cssnext.github.io/playground/

https://preset-env.cssdb.org/playground

变量不需要再编译
正则发生了变化

### css in js
npm init   
npm install webpack webpack-cli style-loader css-loader --save-dev   

```javascript
/*
    index.js
*/
import "./index.css";

console.log('hello next css!')

/*
    index.css
*/
:root{
    --baseBackground:yellowgreen;
}

body{
   background:var(--baseBackground)
}
/*
    webpack.config.js
*/
module.exports = {
    module: {
      rules: [
        {
          test: /\.css$/,
          use: ['style-loader', 'css-loader'],
        },
      ],
    },
  };
```
在dist的main.js把css打包进了js

### modules
```javascript
use: ['style-loader', 'css-loader?modules']

import index from "./index.css"

console.log(index.xianming);//_11sKt886rl5-ZZQmiUYO13

console.log('hello next css!')
```
如果你觉得生成的类型名称太丑可以是用**localIdentName**改成自己喜欢格式的类名
```javascript
use: ['style-loader', 'css-loader?modules&localIdentName=[name]__[local]'],//index__xianming
```

### 提取css 

npm install --save-dev mini-css-extract-plugin

### 编写符合BEM格式文件
```javascript
/*
  index.html
*/
<body>
    <div class="_11sKt886rl5-ZZQmiUYO13">modules test</div>

    <ul class="widget-nav">
        <li class="widget-nav__item widget-nav--active">
            <a href="">百度一下你知道</a>
        </li>
        <li class="widget-nav__item">widget-nav__item</li>
    </ul>
</body>
/*
  index.css
*/
@custom-selector :--heading h1, h2, h3, h4, h5, h6;

:root{
    --baseBackground:yellowgreen;
    --baseSize:1rem;
}

.widget-nav{
    float:left;
    font-size: var(--baseSize);
}

.widget-nav__item{
    & a{
        color: var(--baseBackground);
    }
}

.widget-nav--active{
    color: aqua;
}

:--heading {
    margin-block: 0;
}

//这个时候没有任何变化 npm run dev生成的文件没有任何变化 main.css
//需要postcss处理
/*
  webpack.config.js
*/
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {

  plugins: [
    new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // both options are optional
      filename: '[name].css',
      chunkFilename: '[id].css',
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
          },
          { loader: 'css-loader', options: { importLoaders: 1 } },
          'postcss-loader'
        ],
      },
    ],
  },
};
/*
postcss.config.js
*/
module.exports = {
    plugins:{
        "postcss-preset-env":{
            stage:0//编译到什么步骤https://preset-env.cssdb.org/playground
        }
    }
}
```
编译成功后的css文件
```javascript
:root{
    --baseBackground:yellowgreen;
    --baseSize:1rem;
}

.widget-nav{
    float:left;
    font-size: 1rem;
    font-size: var(--baseSize);
}

.widget-nav__item a{
        color: yellowgreen;
        color: var(--baseBackground);
    }

.widget-nav--active{
    color: aqua;
}

h1,h2,h3,h4,h5,h6 {
    margin-top: 0;
    margin-bottom: 0;
  }
```

### css函数
```javascript
:root{
    --number-var:1;
}

div::before{
    counter-reset: number var(--number-var);
    content:counter(number)
}

//js操作 跨过浏览器的重绘重排
document.documentElement.style.setProperty("--number-var",100)
```

### POSTCSS值得收藏的插件
POSTCSS-CUSTOM-PROPERTIES 运行时变量   
POSTCSS-SIMPLE-VARS 与SCSS一致的变量实现   
POSTCSS-MIXINS 实现类似SASS的@MIXIN的功能    
POSTCSS-EXTEND 实现类似SASS的继承功能    
POSTCSS-IMPORT 实现类似SASS的IMPORT    
CSSNext 面向未来 CSS Grace 修复过去

跟多的骚操作新特性浏览器的兼容可以看还是上面那个网站https://preset-env.cssdb.org cssdb    

## CSS Doodle
官网地址
https://css-doodle.com/
作者的codepen地址：
https://codepen.io/yuanchuan/   










