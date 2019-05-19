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