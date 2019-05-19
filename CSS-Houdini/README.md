## CSS Houdini
js in css

在现今的 Web 开发中，JavaScript 几乎占据所有版面，除了控制页面逻辑与操作 DOM 对象以 外，连 CSS 都直接写在 JavaScript 里面了，就算浏览器都还沒有实现的特性，总会有人做出对应 的 Polyfills，让你快速的将新 Feature 应用到 Production 环境中，更別提我们还有 Babel 等工具 帮忙转译。
而 CSS 就不同了，除了制定 CSS 标准规范所需的时间外，各家浏览器的版本、实战进度差异更是 旷日持久，顶多利用 PostCSS、Sass 等工具來帮我們转译出浏览器能接受的 CSS。开发者们能操 作的就是通过 JS 去控制 DOM 与 CSSOM来影响页面的变化，但是对于接下來的 Layout、Paint 与 Composite 就几乎沒有控制权了。
为了解決上述问题，为了让 CSS 的魔力不再浏览器把持，Houdini 就诞生了!( Houdini 是美国的 伟大魔术师，擅长逃脱术，很适合想将 CSS 从浏览器中解放的概念)
CSS Houdini 是由一群來自 Mozilla, Apple, Opera, Microsoft, HP, Intel, IBM, Adobe 与 Google 的工程 师所组成的工作小组，志在建立一系列的 API，让开发者能够介入浏览器的 CSS engine

### 新增API
CSS Parser API 还没有被写⼊入规范，随时都会有变化，但是它的基本思想不不会变:允许开发者⾃自由扩展CSS词法分析器器，引⼊新的结构(constructs)， ⽐比如新的媒体规则、新的伪类、嵌套、@extends、@apply 等等。
只要新的词法分析器器知道如何解析这些新结构，CSSOM就不会直接忽略略它们，⽽是把这些结构放到正确的地⽅方.

CSS Layout API允许开发 者可以通过 CSS Layout API 实现⾃己的布局模块 (layout module)，这⾥的“布局模块”指的是display的属性值。也就是说，这个API实现以后，开发者首次拥有了像CSS原⽣代码(⽐如 display:flex、display:table)那样的布局能力。

CSS Paint API Layout API ⾮常相似。它提供了registerPaint方法，操作⽅式和registerLayout方法也很相似。当想要构建一个CSS图像的时候，开发者随时可以调用paint()函数，可以使⽤刚刚注册好的名字。

具体的Coding过程要借助WorkLet

```javascript
.item-list { display: layout(waterfall); } CSS Layout API
registerLayout('waterfall',class{
    static get inputProperties() 
    static get childrenInputProperties()
    static get childDisplay()
    *intrinsicSizes(children,styleMap)
    *layout(constrains,children,
        styleMap,edges,breakToken
    )
})

.test { background-image: paint(circle); }
```

### worklets
来⼀一些Worklets的实战 Worklets 的概念和 web worker 类似

(registerLayout和registerPaint)已经了解过了了，估计现在你想知道的是，这些代码得放在哪里呢?答案就是worklet脚(⼯作流脚本⽂件)。
Worklets的概念和web worker类似，它们允许你引入脚本文件并执行特定的JS代码，这样的JS代码要满⾜两个条件:第一，可以渲染 流程中调用;第⼆二，和主线程独⽴。
Worklet脚本严格控制了了开发者所能执行的操作类型，这就保证了了性能 Worklets 的特点就是轻量以及生命周期较短。
```javascript
CSS.paintWorklet.addModule('xxx.js')
CSS.layoutWorklet.addModule(‘xxx.js')
```
首先⾄少你要掌握CSS WorkFlow

```javascript
.el {
    --elUnit: 500px;
    --arcColor: #8266ff;
    height: var(--elUnit);
    width: var(--elUnit);
    background: url("../test.png");
    --background-canvas: (ctx, geom)=> {
        ctx.strokeStyle= `var(--arcColor)`;
        ctx.lineWidth=2;
        ctx.beginPath();
        ctx.arc(200, 200, 50, 0, 2*Math.PI);
        ctx.stroke();
        ctx.closePath();
    };
    background: paint(background-canvas);
}
<div class="el"></div>

CSS.paintWorklet.addModule("./arc.js");

/*
    arc.js
*/
if (typeof registerPaint !== "undefined") {
    registerPaint("background-canvas", class {
        static get inputProperties() {
            return ["--background-canvas"];
        }
        paint(ctx, geom, properties) {
            eval(properties.get("--background-canvas").toString())(
                ctx, geom, properties
            );
        }
    });
}
```
### Properties/Values 

```javascript
//注册一个属性
CSS.registerProperty({
    name:'--stop-color',
    syntex:'<color>',
    inheries:false,
    initialValue:'rgba(0,0,0,0)'
})
//卸载一个属性
CSS.unregisterProperty({
    '--stop-color'
})
```

### Typed OM Object
解决⽬目前模型的⼀一些问题，并实现 CSS Parsing API 和 CSS 属性与值 API 相关的特性。
