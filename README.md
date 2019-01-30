# 如何阅读 ECMAScript 规范

- 原文链接:

  <https://timothygu.me/es-howto/>

- 原作者:

  [Timothy Gu](https://timothygu.me/) [timothygu99@gmail.com](mailto:timothygu99@gmail.com)

- 说明：这篇文档由我本人（槐念，微信公众号Huainian_1994）逐字翻译而得，力求保留原意，包括感情色彩。**虽原文行文赘余，但我未对其作删改**。此外，原文中的4.1章节被我删除，4.2变更为4.1。本文不得用于商业用途，而对于其他用途，在注明出处和作者的前提下可随意转载、删改本翻译文档，同时需要遵守原文的版权规范。
- 注意：如果你有外语障碍，那么本文你读了也收获不大。因为第一视角是默认无语言障碍的，且ECMAScript标准文档为英文，丢翻译引擎会导致大量专业术语语义扭曲，行文偏离原意，失去阅读的意义。而本文没有阐述“如何克服语言障碍获取知识”的相关内容。(有人会问，那你翻译了作甚？无语言障碍的人直接读原文，谁看你译文？对此我的回答是：我高兴，关你鸟事 :)
- 下列是原文版权信息：

[![CC BY-SA 4.0](https://i.creativecommons.org/l/by-sa/4.0/80x15.png)](https://creativecommons.org/licenses/by-sa/4.0/) This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/), which is available at https://creativecommons.org/licenses/by-sa/4.0/. Parts of this work may be from another specification document. If so, those parts are instead covered by the license of that specification document.

------

## 概述

ECMAScript语言规范 (也叫做JavaScript规范, 或者 ECMA-262) 是深入理解JavaScript 的必备材料。例如，它会告诉你JavaScript的工作方式。 然而万事开头难，你最初阅读时会对它的海量内容感到困惑和恐惧（ES6有八百多页，每页大概650个单词）。而我这篇文档，正是为了让你更容易地上手、深入ECMAScript规范。

## 内容结构

1. [前言](#prelude)

   [1.1我为什么需要阅读ECMAScript规范](#why-should-i-read-the-ecmascript-specification)

   [1.2哪些属于ECMAScript规范，哪些不属于呢](#what-belongs-to-the-ecmascript-specification)

   [1.3ECMAScript规范怎么找?](#where-is-the-ecmascript-specification)

   [1.4 开始阅读ECMASCript规范](#navigating-the-spec)

2. [执行语义](#Runtime-sematics)

   [2.1算法步骤（Algorithm steps）](#算法步骤（Algorithm-steps）)

   [2.2抽象操作（Abstract Operation）](#Abstract-Operation)

   <a href="#what-is-this">2.3 形如[[Notation]]的结构的含义</a> 

   ​	[2.3.1它是一个记录的一个字段（ A field of a Record）](#2-3-1)	

   ​	[2.3.2它表示了JS对象的“内部插槽”](#2-3-2)

   ​	[2.3.3它表示了JS对象的一个内置方法](#2-3-3)

   [2.4. “完成记录” 与 速记符号 `?`&`!`](#completion-records-and-shorthands)

   [2.5JavaScript Objects](#javascript-objects)

   [2.6Example: `String.prototype.substring()`](#example-string-prototype-substring)

   [2.7. Example: Boolean() 和 String() 会抛出异常吗？](#example-can-boolean-and-string-ever-throw-exceptions)

   [2.8. Example: 操作符 typeof ](#example-typeof-operator)

3. [术语表](#glossary)

   3.1[常用的抽象操作](#common-abstract-operations)

4. [用词出处](#index)

   4.1[本文所用术语的标准来源](#index-defined-elsewhere)

5. [参考资料](#references)

   5.1[开卷有益](#informative)



<h2 id="prelude" >1. 前言</h2>

现在，你决定每天阅读一点ECMAScript规范。也许是为了陶冶情操，或者这是新年立的FLAG，亦或你妈妈觉得你需要读。哎，不管是什么原因，欢迎入坑!

> ​	注意: 在我的这篇文档中，我只会使用 "ECMAScript" 来表示规范本身，即ECMA-262，而对于非规范的泛指我则使用 "JavaScript" 。 (ECMAScript和JavaScript之间有一些历史上的区别，但是讨论这些已经超出了本文的范围，您可以很容易地[查询这个区别](https://www.google.com/search?q=ecmascript+vs.+ JavaScript)。)



  <h3 id="why-should-i-read-the-ecmascript-specification" >1.1.我为什么需要阅读ECMAScript规范</h3>

ECMAScript 规范是所有JavaScript实例的行为的权威来源。不管这个实例实现于你的浏览中、在你服务器Node.js环境中、在宇航服中[[NODEJS-NASA\]](#biblio-nodejs-nasa)，还是在你的物联网设备里 [[JOHNNY-FIVE\]](#biblio-johnny-five).。所有JavaScript 引擎的开发者都依据ECMAScript规范开发，让程序可以表现出各种闪亮的特性。JavaScript 引擎本身也如此。

但是，我认为规范的效用超出了“JavaScript引擎开发人员”这种神秘生物的范围。 就是说，这个规范对于一般的JS编程人员也是大有帮助的，也许你还没有意识到这一点。

*假设有一天你在工作中发现了下面这种奇怪的并置现象：

```js
> Array.prototype.push(42)
1
> Array.prototype
[ 42 ]
> Array.isArray(Array.prototype)
true
> Set.prototype.add(42)
TypeError: Method Set.prototype.add called on incompatible receiver #<Set>
    at Set.add (<anonymous>)
> Set.prototype
Set {}
```

并且非常困惑为什么一个方法在它自己的原型上可以工作，而外来的方法不能在它的原型上工作。在你最需要的时刻，你谷歌不到这个问题了，连最最有用的 Stack Overflow也没有相关的回答。怎么办呢？

那就阅读ECMAScript规范吧。

有时，你可能想知道臭名昭著的宽松的相等操作符`== `究竟是如何起作用的。作为一个勤奋的软件工程师，你在MDN上查找它，却发现它的解释段落如此密集，对你的眼睛的伤害比帮助更大。

那就阅读ECMAScript规范吧。

另一方面，我不提倡JS新手阅读ECMAScript规范。如果你是个新人，那么你现在应该去玩，去探所web。比如开发一些WEB应用，或者用做一个基于JS的家用视频监控程序。总之，等你玩熟了，经历多了，见识多了，再回来看这篇文章吧。：）

Okay，现在你知道阅读规范会帮助你了解、看清一门编程语言或者一个运行平台（例如Google的V8引擎）的纷繁难懂之处。但是，ECMAScript 规范的边界在哪?



<h3 id="what-belongs-to-the-ecmascript-specification" >1.2. ECMAScript标准的内容范围</h3>

课本给出的答案是： "ECMAScript 规范只包含语言特性"。呵呵，这句话说了等于没说，相当于说是 "JavaScript 的特性就是 JavaScript"，"宪法的第一条是宪法的第一条"。

相反，我会列出常见的JS应用所共有的东西，然后逐一告诉你这共通点些是否为JS的语言特性：

（译者注：对于下表中的“syntactic element”，我将其称作**“语法单元分隔符”**。括号、分号等具有分割语句、标记语法单元、表达式，让语句拥有层次结构的作用的符号都可被称为“syntactic element”。）

| syntactic element的语法(例如：一个合法的`for`..`in` 循环的结构是什么) | ✔    |
| ------------------------------------------------------------ | ---- |
| syntactic element的语义 (例如：  `typeof null`和 `{ a:b }` 的返回值是什么) | ✔    |
| 导入陷阱：`import a from 'a';`                               | ❓[1] |
| 内置对象：`Object`, `Array`, `Function`, `Number`, `Math`, `RegExp`, `Proxy`, `Map`, `Promise`, `ArrayBuffer`, `Uint8Array`, ... | ✔    |
| 非标准的通用方法：`console`, `setTimeout()`, `setInterval()`, `clearTimeout()`, `clearInterval()` | ✘[2] |
| Node.js的一些全局对象：`Buffer`, `process`, `global`*        | ✘[3] |
| Node.js特有语句：`module`, `exports`, `require()`, `__dirname`, `__filename` | ✘[4] |
| 浏览器特有语句：`window`, `alert()`, `confirm()`, the DOM (`document`, `HTMLElement`, `addEventListener()`, `Worker`, ...) | ✘[5] |



> [1] ECMAScript标准规定了声明的语法、规定了这些声明的含义，但没有规定如何加载模块。
>
> [2] 这些方法在浏览器和Node.js平台上都是可用的，但他们都是非标准的。对于 Node.js，这些方法都在[Node.js的文档](https://nodejs.org/api/globals.html#globals_global_objects)中有明确的规定规范。而对于浏览器，这些方法由W3C组织规定。其中`console`方法有自己的标准([Console Standard](https://console.spec.whatwg.org/))，而其余方法被列在HTML标准中([HTML Standard](<https://html.spec.whatwg.org/multipage/>))，具体可参见[8.5timers](https://html.spec.whatwg.org/multipage/timers-and-user-prompts.html#timers)。
>
> [3] 这些都是Node.js独有的全局对象。规定于它[自己的官方文档](https://nodejs.org/api/globals.html#globals_global_objects).中。  注： `global` 实际上有机会成为ECMAScript的一部分，并在浏览器中实现。[[ECMA-262-GLOBAL\]](#biblio-ecma-262-global)
>
> [4] 这些都是Node.js独有的模块级“全局”对象，规定于它[自己的官方文档](https://nodejs.org/api/modules.html#modules_modules)。
>
> [5] 他们都是浏览器独有的。





<h3 id="where-is-the-ecmascript-specification" >1.3. ECMAScript规范*在哪*？</h3>

当你搜索 "ECMAScript 规范"时，你会看到很搜索结果都会王婆卖瓜，称自己是“最正统的ECMAScript规范”。 你该如何选择？

太长不看：ECMA-262 可在这里找到：https://tc39.github.io/ecma262/



ECMAScript 语言规范由Ecma International Technical Committee 39 维护。(更熟悉的说法是TC39 [[TC39\]](#biblio-tc39))，其中成员的背景各有不同。 TC39 负责维护ECMAScript 规范并将最新版本发布于https://tc39.github.io/



> 注：ISO/IEC还将ECMAScript语言标准重新发布为ISO/IEC  [[ISO-16262-2011\]](#biblio-iso-16262-2011).。不过不要担心，因为标准的文本与Ecma International发布的标准完全相同，唯一的区别是您必须支付[198瑞士法郎](https://www.google.com/search?q=198+swiss+francs+in+my+currency)。
>
>

<h3 id="navigating-the-spec" >1.4. 开始阅读ECMASCript规范</h3>

ECMAScript 规范包含了海量的信息。尽管它的作者们尽力把它拆分为逻辑块，但它的内容依然繁杂。

就我而言，我会把它分为五个部分：

- 约定 & 基础知识 ("什么是*Number*类型？	标准中说的“throw a **TypeError** exception”是什么意思？")
- 语法结构 (" `for`-`in`循环怎么写？")
- 语言的静态语义 ("变量名是如何在`var`语句定义好的？")
- 语言的执行语义 (" `for`-`in` 是怎么执行的？")
- APIs (" `String.prototype.substring()`是干嘛的？")

但规范并不是按照上述思路组织书写的。它把上述思路的第一点放到了第五章[§5 Notational Conventions](https://tc39.github.io/ecma262/#sec-notational-conventions) 到第九章[§9 Ordinary and Exotic Objects Behaviours](https://tc39.github.io/ecma262/#sec-ordinary-and-exotic-objects-behaviours) 。而思路中的二、三、四点则交错出现在了第十章 [§10 ECMAScript Language: Source Code](https://tc39.github.io/ecma262/#sec-ecmascript-language-source-code) 到第15章 [§15 ECMAScript Language: Scripts and Modules](https://tc39.github.io/ecma262/#sec-ecmascript-language-scripts-and-modules)，如：

> - [§13.6 The `if` Statement](https://tc39.github.io/ecma262/#sec-if-statement) **Grammar productions**
>   - §13.6.1-6 **Static semantics**
>   - §13.6.7 **Runtime sematics**
> - [§13.7 Iteration Statements](https://tc39.github.io/ecma262/#sec-iteration-statements) **Grammar productions**
>   - §13.7.1 Shared **static** and **runtime semantics**
>   - §13.7.2 The `do`-`while` Statement
>     - §13.7.2.1-5 **Static semantics**
>     - §13.7.2.6 **Runtime semantics**
>   - §13.7.3 The `while` Statement
>     - ...

然鹅，APIs分布于十八章 [§18 The Global Object](https://tc39.github.io/ecma262/#sec-global-object)到二十六章 [§26 Reflection](https://tc39.github.io/ecma262/#sec-reflection)的条款中。

从这点说，**没有人**会从头到尾地阅读ECMAScript规范（没有人：“谁叫我？”），而是查看与要查找的内容相对应的部分，并且在该部分中只看需要的内容。 试着确定你的具体问题涉及了上述五个点中的哪个点。如果你不知道是哪一个方面有困难，问问你自己“这个问题在哪个阶段(无论你想要确认什么)**计算**的？”不要担心，多看几遍文档就好，实践出真知。





<h2 id="Runtime-sematics" >2、执行语义（Runtime sematics） </h2>

JS语言和大量API的执行语义占据了ECMAScript规范的很大篇幅，这通常也是人们问题最多的地方。

（译者注：所谓执行语义，就是这段代码执行后会干啥。相同一段代码，放到不同代码块中执行的流程、结果会有很大的差异。直接从一段代码可阅读出的意思称为所谓的**静态语义**，而**执行语义**表示了一段代码的执行流程、执行结果。也成为**动态语义**。）

大体说，执行语义相关章节的表述非常直接，不绕弯子。然鹅蛋碎的是， ECMAScript规范使用了大量的简写*，对于刚起步的人来说这简直讨厌至极（至少对我来说是如此）。为了了解一些语句的工作方式，我会尝试解释其中的一些约定，并将它们应用到我的日常工作流程中。

> *译者注：简写（shorthand）：从ECMAScript 2015开始，在对象初始器中引入了一种更简短定义方法的语法，这是一种把方法名直接赋给函数的简写方式。
>
> 在ES5中。我们定义一个对象的方法如下：
>
> ```js
> var obj = {
>   foo: function() {
>     /* code */
>   };
> ```
>
> 而在ES6及其之后：
>
> ```js
> var obj = {
>   foo() {
>     /* code */
>   };
> ```



<h3 id="Algorithm-steps" >2.1. 算法步骤（Algorithm steps） </h3>

ECMAScript中，大部分语句的执行语义由Algorithm steps给出，正如同伪代码一样，只不过Algorithm steps会更加的严格。

> 例一、一个Algorithm steps的示例：
>
> <hr>
>
> 1. Let a be **1**.
> 2. Let b be a+a.
> 3. If b is **2**, then
>    1. Hooray! Arithmetics isn’t broken.
> 4. Else
>    1. Boo!

延伸阅读: [§5.2 Algorithm Conventions](https://tc39.github.io/ecma262/#sec-algorithm-conventions)



<h3 id="Abstract-Operation" >2.2. 抽象操作（Abstract Operation） </h3>

有时你会在本规范中看到类函数的抽象操作。例如， `Boolean()` 函数运行的第一步规定为：

> 当 `Boolean` 被传参时，将会经历如下几步：
>
> 1. Let b be [ToBoolean](https://tc39.github.io/ecma262/#sec-toboolean)(value).
> 2. ...

其中， "ToBoolean" 这个函数就被称为所谓的抽象操作：它是抽象的，因为在此前的代码中，它并没有作为函数暴露给JS代码以供其使用。它只是作者虚构的一个符号，这样虚构的函数避免了作者在一个规定中想用时需要一遍又一遍地把这个函数的功能说清楚，而是想用就用。

延伸阅读： [§5.2.1 Abstract Operations](https://tc39.github.io/ecma262/#sec-algorithm-conventions-abstract-operations)



<h3 id="what-is-this" >2.3.形如[[Notation]]的结构的含义 </h3>

有时，你会看到形如[[Notation]]的结构被写到了 这样的表达式中 `Let proto be obj.[[Prototype]]`。根据上下文的不同，这种符号在技术上可以有不同的含义。 如果你认为这个[[Notation]]表示了某种“根据现有JS代码不可察觉到的内置属性“，那么我看好你哦，你的前途无可限量。

恰如所说， 这样的写法有三种含义。下面我将以ECMAScript标准中的内容为例来阐述这三种含义.。爱看不看：



<h4 id="2-3-1" >2.3.1. 它表示了一对键值对（ A field of a Record） </h4>

ECMAScript规范使用术语`Record`来引用具有固定键集合的`键-值`映射，这是一种类C的结构。`Record`中的每一对`键-值`对都被称为字段*（field）*。 由于术语`Record`只会出现于规范而不是日常的JS代码中，使用形如*[[Notation]]*的样子来引用`Record`的字段是有意义的。

> 例二、属性描述符 [*Property Descriptors*](https://tc39.github.io/ecma262/#sec-property-descriptor-specification-type) 被建模为以下字段：[[Value]]、[[Writable]]、[[Get]]、[[Set]]、[[Enumerable]] 和 [[Configurable]]。形如[IsDataDescriptor](https://tc39.github.io/ecma262/#sec-isdatadescriptor) 的抽象操作广泛使用了这种*[[Notation]]*样式的结构：
>
> （译者注：根据ECMA-262， 6.2.5章节*The Property Descriptor Specification Type*（属性描述符的规范类型）：
>
> *Property Descriptor* 类型的数据用于描述一个对象**属性的属性**的操作及其实例化。*Property Descriptor*类型的值称为记录*(Records)*。每一个字段*（field）*的名称作为一个属性的名称，而根据ECMA-262 [6.1.7.1](https://tc39.github.io/ecma262/#sec-property-attributes)的规定，这个字段的值正是属性所对应的值。此外， 任何字段都可以存在或者不存在。 该规范中用于标记属性描述符记录的文字描述的模式的名称正是*Property Descriptor*）
>
> <hr>
>
> IsAccessorDescriptor ( Desc )
>
> When the abstract operation IsDataDescriptor is called with [Property Descriptor](https://tc39.github.io/ecma262/#sec-property-descriptor-specification-type) Desc, the following steps are taken:
>
> 1. If Desc is **undefined**, return **false**.
> 2. If both Desc.[[Value]] and Desc.[[Writable]] are absent, return **false**.
> 3. Return **true**.

其他关于 [Records](#record) 的实例在这个章节中： [§2.4 Completion Records; ? and !](#completion-records-and-shorthands)

延伸阅读： [§6.2.1 The List and Record specification Types](https://tc39.github.io/ecma262/#sec-list-and-record-specification-type)



<h4 id="2-3-2" >2.3.2. 它表示了JS对象的“内部插槽”</h4>

根据ECMAScript规范，JavaScript对象中可能会有所谓的用于**保存数据**的“内部插槽”（internal slot）。 正如记录`Record`的字段*(field)*，这些所谓的内部插槽在使用JS的过程中是不可见的，它不会体现于代码中。

（译者注：但是它会体现于代码结果上：例如内置对象**Data**使用了[[DateValue]]这个“内部插槽”，用于存储以世界协调时为基准的当前时间的时间戳。码代码时不可见，但你可以把它打印出来。）

但是，他们会暴露于那些实现了特定细节的工具中，例如谷歌Chrome自带的开发者调试工具。因此，使用形如[[Notation]]的结构来描述“内部插槽”是有意义的。关于“内部插槽”的规定在2.2章节中 [§2.5 JavaScript Objects](#javascript-objects)。

现在，你专心看以下的例子就好，不要老想着内部插槽到底是干嘛的：

> 例三：大部分JS对象都会拥有这个内部插槽*[[Prototype]]* ，它指向了这个JS对象的继承来源。这个内部插槽的值正是方法 `Object.getPrototypeOf()` 的返回值。在抽象操作 [OrdinaryGetPrototypeOf](https://tc39.github.io/ecma262/#sec-ordinarygetprototypeof) 中，内部插槽*[[Prototype]]*的值被访问了：
>
> <hr>
>
> When the abstract operation OrdinaryGetPrototypeOf is called with Object O, the following steps are taken:
>
> 1. Return O.[[Prototype]].

心得：一个JS对象的内部插槽和记录字段的样子看起来一样，但看一眼*[[Notation]]*之前的的内容即可立辨雌雄（在`.`之前的部分）。



<h4 id="2-3-3" >2.3.3. 它表示了JS对象的一个内置方法</h4>

JavaScript对象中也可能会有所谓的”内置方法”（internal method） 。正如“内部插槽”，这些内置方法也不能通过JS代码直接观察到。 因此，使用形如*[[Notation]]*的结构来表示内置方法也是有意义的。

第2.5章节阐述了内置方法的相关细节 [§2.5 JavaScript Objects](#javascript-objects).。现在，你专心看以下的例子就好，不要老想着内置方法用来干嘛：

> 例四：All JavaScript functions have an internal method [[Call]] that runs that function. The [Call](https://tc39.github.io/ecma262/#sec-call) abstract operation has the following step:
>
> 1. Return ? F.[[Call]] (V, argumentsList).
>
> 其中F为JavaScript函数对象。在本例中，F使用形参*V*和*argumentsList*对内置方法*[[Call]]*进行了调用。

心得： 我们可以根据这个*[[Notation]]*周围的内容看出它是否长得像一个函数。从而判定它是否为一个内置函数。



<h3 id="completion-records-and-shorthands" >2.4. “完成记录” 与 速记符号 <code>?</code>&<code>!</code></h3>

每段ECMAScript规范中的执行语义都显式或隐式地返回了一个描述代码输出结果的**完成记录**（Completion Records）。这个完成记录有三个可能的字段：

> - 首先，这个完成记录形如：[[Type]]
>   - (可能的类型有`normal`、 `return`、`throw`、`break`或者`continue`。)
> - 如果 [[Type]]是 `normal`、`return`、或 `throw`，那么它还会拥有 [[Value]] ("抛出了什么/返回值是啥")。
>
> - 如果 [[Type]]是  `break` 或者 `continue`，那么它还会视情况带上一个  [**标记语句**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label#) [[Target]]，来标记这个语句中断的地方 ，或表示中断后从何处继续执行。

注： 形如`[[ ]]`的双括号常用来表示一个记录的字段（即键值对）。

一个[[Type]]类型为`normal` 的完成记录称作“正常完成”*（normal completion）*。除了“正常完成”之外，其他的完成记录称为“突然完成”*（abrupt completion）*。

大多情况下，对于你处理到的*abrupt completion*记录，其 [[[Type\]]](#completion-record-type) 为 `throw`，而其他三种属于*abrupt completion*的完成记录只会在你观察“特定语法元素的计算过程”时有点作用。实际上，你并不会在内置函数的定义中看到除`break` / `continue` / `return` 以外的其他 [[[Type\]]](#completion-record-type)，因为这三者不会跨函数域工作。

延伸阅读： [§6.2.3 The Completion Record Specification Type](https://tc39.github.io/ecma262/#sec-completion-record-规范-type)

------

由于所谓“完成记录”的定义，一些诸如 “bubbling errors until a `try`-`catch`”的JavaScript细节于ECMAScript规范中并不存在。事实上，错误（或者更精确地说是“突然完成”）已被显式地处理了。

不写简写时，规范中的一个常见的抽象函数的调用操作可以返回计算结果或者抛出一个错误。正如：

> 例五：
>
> ------
>
> A few steps that call an abstract operation that may throw **without any shorthands**:
>
> 1. Let resultCompletionRecord be AbstractOp().
>
>    > 注: resultCompletionRecord 属于所谓的“完成记录”。
>
> 2. If resultCompletionRecord is an abrupt completion, return resultCompletionRecord.
>
>    > 注：此处，若resultCompletionRecord的[[Type]] 是 “突然完成” 的，那么他会直接返回它本身。也就是说，在抽象操作中抛出的错误将被转发，而接下来的步骤将被终止。
>
> 3. Let result be resultCompletionRecord.[[Value]].
>
>    > 注： 确定了它是一个 *normal completion*之后， 我们可以打开这个“完成记录”来获得我们需要的计算的实际结果。
>
> 4. result is the result we need. We can now do more things with it.
>
> 在C语言中，下列代码也许会含糊地提示你手动地进行错误处理：
>
> ```c
> int result = abstractOp();              // Step 1
> if (result < 0)                         // Step 2
>   return result;                        // Step 2 (continued)
>                                         // Step 3 is unneeded
> // func() succeeded; carrying on...     // Step 4
> ```



但是，为了减少这些繁重的引用步骤，ECMAScript的编辑们加入了一些简写。从ES2016起，ECMAScript规范被写为下列两种等价的形式：

> 例六：
>
> ------
>
> A few steps that call an abstract operation that may throw **with ReturnIfAbrupt**：
>
> 1. Let result be AbstractOp().
>
>    > 注：此处， result 属于所谓的“完成记录”。
>
> 2. ReturnIfAbrupt (result).
>
>    > 注： ReturnIfAbrupt 通过接收 *abrupt completion*的转发来进行计算，并自动将结果返回到result的[[Value]]中。
>
> 3. result is the result we need. We can now do more things with it.

或者，要更简洁的话，使用一个问号`?`来标记：

> 例七、
>
> ------
>
> A few steps that call an abstract operation that may throw **with a question mark (?)**:
>
> 1. Let result be ? AbstractOp().
>
>    > 注：在这种表示法中，我们根本不用处理“完成记录“。 符号`?`为我们处理了一切，并且结果可以立即使用。
>
> 2. result is the result we need. We can now do more things with it.



------



为使读者更深刻地了解规范要传达的意图，当AbstractOp()不返回形如*abrupt completion*的完成记录时，会使用一个感叹号`!`表示：



> 例八、
>
> ------
>
> A few steps that call an abstract operation that cannot ever throw **with an exclamation mark (!)**:
>
> 1. Let result be ! AbstractOp().
>
>    > 注：虽然`?`转发了一些我们可能得到的错误，但是`!`决定了我们不会从这个调用中得到形如*abrupt completion*的完成记录。正如使用问号`?`的情形，我们不用去处理完成记录，结果可立即被使用。
>
> 2. result is the result we need. We can now do more things with it.



警告：不可否认，如果`!`是比较的一部分，那么它会变得相当混乱：

> 1. ..., return the result of the comparison ! [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber)(x) == y.
>
> — Excerpted from [§7.2.14 Abstract Equality Comparison](https://tc39.github.io/ecma262/#sec-abstract-equality-comparison).

在此， `!`仅仅意为ToNumber()不会返回一个异常。而不是说这个表达式取反。

延伸阅读：[§5.2.3.4 ReturnIfAbrupt Shorthands](https://tc39.github.io/ecma262/#sec-returnifabrupt-shorthands).

<h3 id="javascript-objects">2.5. JavaScript Objects</h3>

在ECMAScript中， 每个对象都有一整套的内置方法。有一些内置方法是所对象都拥有的。例如：

- [[Get]]，获取对象中的属性 (即： `obj.prop`)

- [[Set]]，设定对象的属性 (即：`obj.prop = 42;`)

- [[GetPrototypeOf]]，获取一个对象的原型对象 (例如： `Object.getPrototypeOf(obj)`)

- [[GetOwnProperty]]，获取一个对象自身属性的属性描述符*( Property Descriptor )*

  (例如：`Object.getOwnPropertyDescriptor(obj,"prop")`)

- [[Delete]]，删除一个对象的属性 (例如： `delete obj.prop`)

(这份详细的清单在6.1.7.2章节中 [§6.1.7.2 Object Internal Methods and Internal Slots](https://tc39.github.io/ecma262/#sec-object-internal-methods-and-internal-slots))。

基于这种定义，函数不过是额外拥有了[[Call]]内置方法的对象，也许也会内置[[Construct]]方法。 因此，函数也被称为可调用的对象。

ECMAScript规范把所有对象分为了两个阵营：**普通对象** *（ordinary object）*，和**外来对象** *（exotic object）* *。大部分你遇到的对象都是普通对象，也就是说，根据9.1章节的规定 [§9.1 Ordinary Object Internal Methods and Internal Slots](https://tc39.github.io/ecma262/#sec-ordinary-object-internal-methods-and-internal-slots)，他们所有的内部方法都是默认的方法。

(*译者注：从ES6开始，对象被分为 普通对象 *（ordinary object）*，和外来对象 *（exotic object）*。所谓普通对象，就是具备了对象的所有基本内置方法的那些对象。而“不完全具备所有对象拥有的基本内置方法”的对象则被称为外来对象。一个对象不是普通对象就是外来对象。)

不仅如此， ECMAScript 还定义了外来对象的种类，其中一些会覆盖他们默认继承的内部方法。虽然有少部分的硬性规定限制了外来对象的行为，但通常来说，一些被覆写的内置方法可以在不违反ECMAScript规定的情况下实现很多黑科技特效。

> 例九、`Array` 对象就是一个外来对象。 一些关于`Array`对象`length` 属性的特殊语义不能通过使用“普通对象的可用工具”来实现。
>
> 比如，虽然 `length` 属性看起来不过是一个普通的数据属性，但设置 `Array`对象的 `length` 属性将会导致元素从数组中删除。与之相反，`new Map().size` 只是一个由`Map.prototype`指定的用于获取的函数。并不会像`[].length` 那样有一些诡异的效果。
>
> ------
>
> ```js
> > const arr = [0, 1, 2, 3];
> > console.log(arr);
> [ 0, 1, 2, 3 ]
> > arr.length = 1;
> > console.log(arr);
> [ 0 ]
> > console.log(Object.getOwnPropertyDescriptor([], "length"));
> { value: 1,
>   writable: true,
>   enumerable: false,
>   configurable: false }
> ```
>
> ```js
> > console.log(Object.getOwnPropertyDescriptor(new Map(), "size"));
> undefined
> > console.log(Object.getOwnPropertyDescriptor(Map.prototype, "size"));
> { get: [Function: get size],
>   set: undefined,
>   enumerable: false,
>   configurable: true }
> ```
>
> 通过覆写内置方法 [[DefineOwnProperty]]，这个行为可被实现。详情见9.4.2章节 [§9.4.2 Array Exotic Objects](https://tc39.github.io/ecma262/#sec-array-exotic-objects) 。





ECMAScript规范也允许其他规范来定义他们自己的外来对象。正是由于这个机制，HTML规范才 (见 [WindowProxy](https://html.spec.whatwg.org/multipage/window-object.html#windowproxy))才规定了浏览器实现跨源API访问 [cross-origin API access](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy#Cross-origin_script_API_access) 。也正是因此，JS程序猿们可以通过使用[Proxy](https://tc39.github.io/ecma262/#sec-proxy-objects) API来创造他们自己的“外来对象”。

------

JavaScript 对象可能也会定义有一些用于包含某种类型的值的内部插槽*（internal slot）*。我倾向于把这种连[Object.getOwnPropertySymbols()](https://tc39.github.io/ecma262/#sec-object.getownpropertysymbols)都不可见的内部插槽看做“依据符号命名的”属性。依据规定，普通对象和外来对象都可以拥有内部插槽。  

> 在上面的[2.3.2章节](#2-3-2)中，我提到：大部分对象都有一个名为[[Prototype]]的内部插槽（实际上，所有普通对象甚至一些外来对象都有这个内部插槽，包括Array对象）。与此同时，我们也知道有个叫[[GetPrototypeOf]]的内部插槽。二者有何区别？
>
> 最大的区别就是关键字不同：**大部分**对象都拥有[[Prototype]]这个内部插槽，而所有对象**都**实现了 [[GetPrototypeOf]] 这个内置方法。注意：代理对象*[(proxy objects)](https://tc39.github.io/ecma262/#sec-proxy-objects)*没有自己的[[Prototype]]，而他自己的内置方法[[[GetPrototypeOf\]]](https://tc39.github.io/ecma262/#sec-proxy-object-internal-methods-and-internal-slots-getprototypeof) 监听的是所注册的handler，或者形参target的原型对象。这个被监听的对象存放于代理对象的内部插槽[[ProxyTarget]]中。
>
> 因此在处理对象时，最好引用适合的内置方法，而不是直接看内部插槽的值。

------

另一种思考“对象之间的关系”的思路：内置方法*（internal method）*和内部插槽*（internal slot）*是通过一个经典的面向对象的镜头来呈现的。所谓“对象”，就如一个指定了很多”必须实现的**内置方法**”的接口。 普通对象提供了默认的实现方式，其中，这些实现的方式可能被外来对象部分或完全地覆写。而另一方面，**内部插槽**就像一个对象的实例的变量——包含了这个实例的实现细节。

这些关系都被总结到了下列这张UML图中（点击放大）：

[![object-uml](object-uml.svg)](https://raw.githubusercontent.com/HuaiNian1994/How-to-Read-the-ECMAScript-Specification/master/object-uml.svg?sanitize=true)





<h3 id="example-string-prototype-substring">2.6. Example: String.prototype.substring()</h3>

现在我们对ECMAScript标准的编撰有个一个不错的认识，那我们开始练习吧！

假设我现在有下列问题：

> 在不运行代码的前提下，下列码段的结果是什么？
>
> ```js
> String.prototype.substring.call(undefined, 2, 4)
> ```

这是个整人的题。它貌似会有两种输出：

1. `String.prototype.substring()`首先会把 **undefined**转化为字符串型的`"undefined"`，然后把这个字符串的从索引二到索引三的片段截出，得到   "de"。（依据substring()的功能，置入其的两个参数表示左闭右开的索引区间）
2. 另一方面， `String.prototype.substring()`也许只会直接抛出一个错误，因为其不允许**undefined** 作为输入。

不巧的是，对于这种“当**This**的值不是字符串时，substring()该如何工作”的情形，MDN没有提供回答。

那就赶紧查文档！在ECMA-262文档中键入关键词`substring`后，我们转到了21.1.2.21章节[§21.1.3.21 String.prototype.substring ( start, end )](https://tc39.github.io/ecma262/#sec-string.prototype.substring)，而正是这里规定了函数的工作方式。

在阅读算法步骤*（algorithm steps）*之前，我们先想一想自己知道什么。此处，我假定了我们知道`str.substring()`在普通传参时的工作情况：它会返回所给字符串的一部分。现在我们不确定的是，**This**值为**undefined**时substring()该怎么工作。所以，我们专门去看那些关于“**this的值**”的算法步骤就可以了。

我们很幸运，`String.prototype.substring()`算法步骤的第一步就专门写了关于**this的值**的处理：

> 1. Let *O* be ? [RequireObjectCoercible](https://tc39.github.io/ecma262/#sec-requireobjectcoercible)(**this** value).

`?`简写允许我们得出这样的结论： 抽象操作[RequireObjectCoercible](https://tc39.github.io/ecma262/#sec-requireobjectcoercible)也许会抛出异常，否则此处使用的该是`！`而不是`?`。实际上，如果它抛出了异常，那么将符合以上假设中的第二种情形。看到希望后，我们点开了 [RequireObjectCoercible](https://tc39.github.io/ecma262/#sec-requireobjectcoercible)抽象操作的链接，看看它干了啥：

 [RequireObjectCoercible](https://tc39.github.io/ecma262/#sec-requireobjectcoercible) 这个抽象操作有点奇怪。不像其他抽象操作那样，它是用一个表而不是算法步骤来定义的：

> | Argument Type | Result                           |
> | ------------- | -------------------------------- |
> | Undefined     | Throw a **TypeError** exception. |
> | ...           | ...                              |

无论如何——在undefined（我们传递给 `substring()`的**this的值**的类型）对应的那一行，表明[RequireObjectCoercible](https://tc39.github.io/ecma262/#sec-requireobjectcoercible) 要抛出一个异常。又由于在`substring()`的定义中用到了`？`符号，那么我们就知道异常的抛出将**冒泡**到substring()中。完美！

于是我们就有了答案：本题的代码段将抛出一个异常。

ECMAScript规范值只定义了所抛出的异常的类型，没规定这个异常所包含的具体内容。也就是说，一个同名异常的不同实现会有不同的内容提示，即使他们在同一个环境下。

例如，在Google的V8 6.4引擎中，（包含于 Google Chrome 64），提示信息为：

```
TypeError: String.prototype.substring called on null or undefined
```

而 Mozilla Firefox 57.0 则给了一个没啥用的提示：

```
TypeError: can’t convert undefined to object
```

同时， ChakraCore 的 1.7.5.0版本 (Microsoft Edge的JavaScript引擎 ) 走了V8的路线： 

```
TypeError: String.prototype.substring: 'this' is null or undefined
```



<h3 id="example-can-boolean-and-string-ever-throw-exceptions">2.7. Example: Boolean() 和 String() 会抛出异常吗？</h3>

编写关键任务的代码时，你必须把异常处理放到编程的首位。由此，我们有时会思考：内置方法会抛出异常吗？

在本例中，我们要尝试用`Boolean()` 和 `String()`这两个JS内置函数来这回答这个问题。注意，是他们本身而**不是**他们的实例`new Boolean()` 和`new String()`。他们很容易成为基本包装类型的对象，而这正是JS[最不受欢迎的特性](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch3.md#object-wrapper-gotchas) 。 而这在几乎所有的JS风格指南中都是不推荐的（出自[YDKJS](https://github.com/getify/You-Dont-Know-JS)）。

转到规范中关于“Boolean()”的那部分后，我们发现算法似乎相当短:

> When `Boolean` is called with argument value, the following steps are taken:
>
> 1. Let b be [ToBoolean](https://tc39.github.io/ecma262/#sec-toboolean)(value).
> 2. If NewTarget is **undefined**, return b.
> 3. Let O be ? [OrdinaryCreateFromConstructor](https://tc39.github.io/ecma262/#sec-ordinarycreatefromconstructor)(NewTarget, `"%BooleanPrototype%"`, « [[BooleanData]] »).
> 4. Set O.[[BooleanData]] to b.
> 5. Return O.

但另一方面，由于 [OrdinaryCreateFromConstructor](https://tc39.github.io/ecma262/#sec-ordinarycreatefromconstructor) 所包含的语句特殊用法，使得上述算法步骤看起来并不明确。 更重要的是，在第三步中的简写符号`?`表明他会在某些情况下抛出异常。让我们再细细看一下：

Step 1 ：把value（函数传入的形参）转化为布尔值。有趣的是，这个步骤中并没有`?`或`!`。但通常来说，一条算法步骤中没有“完成记录”的简写意为着这个简记符为`!`。所以，第一步不会抛出异常。

Step 2 ：检查这个*NewTarget*的值是否为 **undefined**。ES2015首次提出，所谓 *NewTarget*就是在ECMAScript规范中，与[`new.target`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new.target#) 属性等价的一个符号，从而使规范更好地区分 `new Boolean()` 的调用（这里是布尔值）以及 `Boolean()`的调用（这里是**undefined**）。因为我们只关注`Boolean()`的直接调用，所以我们知道 *NewTarget*总为**undefined**，算法会直接返回b，而不会有额外的处理。

因为在不使用`new`而对 `Boolean()`直接调用的情况下，只能进行到算法步骤的前两步，而这两部中都没有抛出异常。所以我们可以推断出：**不管输入为何，Boolean()都不会抛出异常**。

------

让我们来看 `String()`：

> When `String` is called with argument value, the following steps are taken:
>
> 1. If no arguments were passed to this function invocation, let s be `""`.
> 2. Else,
>    1. If NewTarget is **undefined** and [Type](https://tc39.github.io/ecma262/#sec-ecmascript-data-types-and-values)(value) is Symbol, return [SymbolDescriptiveString](https://tc39.github.io/ecma262/#sec-symboldescriptivestring)(value).
>    2. Let s be ? [ToString](https://tc39.github.io/ecma262/#sec-tostring)(value).
> 3. If NewTarget is **undefined**, return s.
> 4. Return ? [StringCreate](https://tc39.github.io/ecma262/#sec-stringcreate)(s, ? [GetPrototypeFromConstructor](https://tc39.github.io/ecma262/#sec-getprototypefromconstructor)(NewTarget, `"%StringPrototype%"`)).

依据我们对函数`Boolean()`的分析经验，我们知道当直接调用`String()`时，*NewTarget*的值只会是**undefined**。因此最后一步不在考虑范围，直接跳过。 由于 [Type](#abstract-opdef-type)和 [SymbolDescriptiveString](https://tc39.github.io/ecma262/#sec-symboldescriptivestring) 都不处理 *abrupt completion*类型的完成记录，那么这二者也不用考虑。至此，还有一个`?`在 抽象操作[ToString](https://tc39.github.io/ecma262/#sec-tostring)的调用之前。我们来细细看看：

正如之前我们研究的 [RequireObjectCoercible](https://tc39.github.io/ecma262/#sec-requireobjectcoercible)， [ToString](https://tc39.github.io/ecma262/#sec-tostring)(argument)也是由一个表定义的：

> | Argument Type | Result                                                       |
> | ------------- | ------------------------------------------------------------ |
> | Undefined     | Return `"undefined"`.                                        |
> | Null          | Return `"null"`.                                             |
> | Boolean       | If argument is **true**, return `"true"`.If argument is **false**, return `"false"`. |
> | Number        | Return [NumberToString](https://tc39.github.io/ecma262/#sec-numbertostring)(argument). |
> | String        | Return argument.                                             |
> | Symbol        | Throw a **TypeError** exception.                             |
> | Object        | Apply the following steps:Let primValue be ? [ToPrimitive](https://tc39.github.io/ecma262/#sec-toprimitive)(argument, hint String).Return ? [ToString](https://tc39.github.io/ecma262/#sec-tostring)(primValue). |

在 `String()`中调用 ToString()时，值可以是除Symbol类型的任何值（被之前的步骤过滤了）。至此，算法步骤中还留有两个`?`。我们可以通过 [ToPrimitive](https://tc39.github.io/ecma262/#sec-toprimitive)和其他链接看出，如果value为一个对象，那么它可能会抛出很多错误：

> 例十：[一些`String()` 抛出错误的例子](https://tc39.github.io/ecma262/#sec-string-constructor-string-value)

因此对于String()的结论是，它不会为原始值抛出异常，但可能会为一个对象抛出异常。

<h3 id="example-typeof-operator">2.8. Example: 操作符 typeof </h3>

至此，我们仅仅分析了API方法，让我们看看别的。

[待续。。](https://github.com/TimothyGu/es-howto/issues/2)





<h2 id="glossary">3.术语表</h2>

<h3 id="common-abstract-operations">3.1常用的抽象操作</h3>

- ArrayCreate ( length [ , proto ] ) ([spec](https://tc39.github.io/ecma262/#sec-arraycreate))

  Create an array object of length length, with proto as the value of the [[Prototype]][internal slot](#internal-slot). If proto is not specified, [%ArrayPrototype%](https://tc39.github.io/ecma262/#sec-properties-of-the-array-prototype-object) in the [current realm](https://tc39.github.io/ecma262/#current-realm) is used. Equivalent to `new Array(length)` if the `Array`constructor and all of its properties have not been monkeypatched, and proto is not specified or [%ArrayPrototype%](https://tc39.github.io/ecma262/#sec-properties-of-the-array-prototype-object) in the [current realm](https://tc39.github.io/ecma262/#current-realm).

- Call ( F, V [ , argumentsList ] ) ([spec](https://tc39.github.io/ecma262/#sec-call))

- Construct ( F [ , argumentsList [ , newTarget ] ] ) ([spec](https://tc39.github.io/ecma262/#sec-construct))

- Get ( O, P ) ([spec](https://tc39.github.io/ecma262/#sec-get-o-p))

- HasProperty ( O, P ) ([spec](https://tc39.github.io/ecma262/#sec-hasproperty))

  Call the corresponding [internal method](#internal-method) on F or O with the rest of the arguments forwarded. Equivalent to the corresponding method on the `Reflect` object.

- DefinePropertyOrThrow ( O, P, desc ) ([spec](https://tc39.github.io/ecma262/#sec-definepropertyorthrow))

- DeletePropertyOrThrow ( O, P ) ([spec](https://tc39.github.io/ecma262/#sec-deletepropertyorthrow))

  Call the corresponding [internal method](#internal-method) ([[DefineOwnProperty]] and [[Delete]], respectively) on O with the rest of the arguments forwarded, and throw an exception if the operation failed and the [internal method](#internal-method)returned **false**.

- GetV ( V, P ) ([spec](https://tc39.github.io/ecma262/#sec-getv))

  Returns [Get](#abstract-opdef-get)(V, P), with V converted to an Object with [ToObject](#abstract-opdef-toobject) first if necessary. Equivalent to `V[P]`.

- HasOwnProperty ( O, P ) ([spec](https://tc39.github.io/ecma262/#sec-hasownproperty))

  Returns whether O has an own property named P, by calling O.[[GetOwnProperty]](P). Equivalent to `Object.prototype.hasOwnProperty.call(O, P)`.

- Invoke ( V, P [ , argumentsList ] ) ([spec](https://tc39.github.io/ecma262/#sec-invoke))

  Call the method named P on V using argumentsList. Equivalent to `V[P](...argumentsList)`. Unlike in [Call](#abstract-opdef-call), P here is a property key.

- IsArray ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-isarray))

  Returns whether argument is an `Array` [exotic object](#exotic-object), or if argument is a `Proxy` [exotic object](#exotic-object), whether argument’s innermost [[ProxyTarget]][internal slot](#internal-slot) is an `Array` [exotic object](#exotic-object). Equivalent to `Array.isArray(argument)`.

- IsCallable ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-iscallable))

  Returns whether argument is a [callable object](#callable-object), otherwise known as a [function object](#function-object). Equivalent to `typeof argument === 'function'` (with the exception of `document.all`, which is an exotic object with several special behaviors; see [§B.3.7 The [[IsHTMLDDA\]] Internal Slot](https://tc39.github.io/ecma262/#sec-IsHTMLDDA-internal-slot)).

- IsConstructor ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-isconstructor))

  Returns whether argument is a [function object](#function-object) with a [[Construct]][internal method](#internal-method).

- ReturnIfAbrupt ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-returnifabrupt))

  Check if argument is an [abrupt completion](#abrupt-completion) (like a thrown exception), and if so return that [abrupt completion](#abrupt-completion) (and allows the exception to bubble up). Otherwise if argument is a [normal completion](#normal-completion), unwrap that [Completion Record](#completion-record) and set argument to argument.[[[Value\]]](#completion-record-value).

  See also: [§2.4 Completion Records; ? and !](#completion-records-and-shorthands).

- StringCreate ( value, prototype ) ([spec](https://tc39.github.io/ecma262/#sec-stringcreate))

  Returns a boxed `String` Object corresponding to the String value, with the [[Prototype]] internal slot of the resulting object being prototype. Equivalent to `new String(value)` if prototype is the [%StringPrototype%](https://tc39.github.io/ecma262/#sec-properties-of-the-string-prototype-object)of the [current realm](https://tc39.github.io/ecma262/#current-realm).

- ToBoolean ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-toboolean))

  Returns argument coerced to a Boolean. Equivalent to `!!argument`.

- ToInteger ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-tointeger))

  Returns [ToNumber](#abstract-opdef-tonumber)(argument), then truncated (i.e., rounded to 0) to become an integer. Equivalent to `Math.trunc(argument)`.

- ToInt8 ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-toint8))

- ToUint8 ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-touint8))

- ToInt16 ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-toint16))

- ToUint16 ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-touint16))

- ToInt32 ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-toint32))

- ToUint32 ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-touint32))

  Returns argument converted to an integer of the specified bits and signedness using truncation.

- ToUint8Clamp ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-touint8clamp))

  Returns argument converted to an integer of the range [0, 255] using rounding and clamping.

- ToNumber ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-tonumber))

  Returns argument coerced to a Number. Equivalent to `+argument`.

- ToObject ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-toobject))

  Returns argument coerced to an Object, using boxed primitive objects if necessary. Equivalent to `Object(argument)` other than the cases where argument is **undefined** or **null**.

- ToPrimitive ( input [ , PreferredType ] ) ([spec](https://tc39.github.io/ecma262/#sec-toprimitive))

  Returns argument coerced to a primitive (i.e., non-Object) value, optionally using the type hint given through PreferredType. Exact semantics of this abstract operation vary depending on the PreferredType.

- ToString ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-tostring))

  Returns argument coerced to a String. Equivalent to ``${argument}``.

  Care must be taken to realize that neither `String(argument)` nor `argument + ''` is fully equivalent to [ToString](#abstract-opdef-tostring). `String()` converts Symbol values to their String descriptions, while [ToString](#abstract-opdef-tostring) throws an exception on Symbol. The addition operator calls some other functions like `argument[Symbol.toPrimitive]` when attempting to convert the value to a String.

- Type ( argument ) ([spec](https://tc39.github.io/ecma262/#sec-ecmascript-data-types-and-values))

  Returns the [type](https://tc39.github.io/ecma262/#sec-ecmascript-data-types-and-values) of argument.





<h2 id="index">4.用词出处</h2>

<h3 id="index-defined-elsewhere">4.1术语的标准来源</h3>

- [CONSOLE]

   

  定义了以下术语:

  - console

- [DOM]

   

  定义了以下术语:

  - addEventListener(type, callback)

- [ECMA-262]

   

  定义了以下术语:

  - %ArrayPrototype%
  - %StringPrototype%
  - Array
  - Array.isArray()
  - ArrayBuffer
  - ArrayCreate
  - Boolean()
  - Call
  - Construct
  - DefinePropertyOrThrow
  - DeletePropertyOrThrow
  - Function
  - Get
  - GetMethod
  - GetPrototypeFromConstructor
  - GetV
  - HasOwnProperty
  - HasProperty
  - Invoke
  - IsArray
  - IsCallable
  - IsConstructor
  - IsDataDescriptor
  - Map
  - Math
  - Math.trunc()
  - Number
  - NumberToString
  - Object
  - Object()
  - Object.getOwnPropertySymbols()
  - Object.prototype.hasOwnProperty()
  - OrdinaryCreateFromConstructor
  - OrdinaryGet
  - OrdinaryGetPrototypeOf
  - OrdinaryToPrimitive
  - Promise
  - Proxy
  - Reflect
  - RegExp
  - RequireObjectCoercible
  - ReturnIfAbrupt
  - String()
  - String.prototype.substring()
  - StringCreate
  - SymbolDescriptiveString
  - ToBoolean
  - ToInt16
  - ToInt32
  - ToInt8
  - ToInteger
  - ToNumber
  - ToObject
  - ToPrimitive
  - ToString
  - ToUint16
  - ToUint32
  - ToUint8
  - ToUint8Clamp
  - Type
  - Uint8Array
  - [[GetPrototypeOf]]
  - current realm
  - property descriptor
  - type

- [HTML]

   

  定义了以下术语:

  - HTMLElement
  - WindowProxy
  - Worker
  - alert(message)
  - all
  - confirm(message)
  - document
  - window

- [NODEJS]

   

  定义了以下术语:

  - Buffer
  - __dirname
  - __filename
  - exports
  - global
  - globals
  - module
  - modules
  - process
  - require()

<h2 id="reference">5.参考资料</h2>

<h3 id="informative">5.1开卷有益</h3>

- [CONSOLE]

  Dominic Farolino; Terin Stock; Robert Kowalski. [Console Standard](https://console.spec.whatwg.org/). Living Standard. URL: <https://console.spec.whatwg.org/>

- [DOM]

  Anne van Kesteren. [DOM Standard](https://dom.spec.whatwg.org/). Living Standard. URL: <https://dom.spec.whatwg.org/>

- [ECMA-262]

  [ECMAScript Language specification](https://tc39.github.io/ecma262/). URL: <https://tc39.github.io/ecma262/>

- [ECMA-262-2018]

  [ECMAScript 2018 Language specification](https://ecma-international.org/ecma-262/9.0/index.html). URL: <https://ecma-international.org/ecma-262/9.0/index.html>

- [ECMA-262-GLOBAL]

  [tc39/proposal-global: ECMAScript Proposal, specs, and reference implementation for `global`](https://github.com/tc39/proposal-global). URL: <https://github.com/tc39/proposal-global>

- [HTML]

  Anne van Kesteren; et al. [HTML Standard](https://html.spec.whatwg.org/multipage/). Living Standard. URL: <https://html.spec.whatwg.org/multipage/>

- [ISO-16262-2011]

  [ISO/IEC 16262:2011 - Information technology – Programming languages, their environments and system software interfaces – ECMAScript language specification](https://www.iso.org/standard/55755.html). URL: <https://www.iso.org/standard/55755.html>

- [JOHNNY-FIVE]

  [Johnny-Five: The JavaScript Robotics & IoT Platform](http://johnny-five.io/). URL: <http://johnny-five.io/>

- [NODEJS]

  [Node.js](https://nodejs.org/). URL: <https://nodejs.org/>

- [NODEJS-NASA]

  [Node.js Helps NASA Keep Astronauts Safe and Data Accessible](https://foundation.nodejs.org/wp-content/uploads/sites/50/2017/09/Node_CaseStudy_Nasa_FNL.pdf). URL: <https://foundation.nodejs.org/wp-content/uploads/sites/50/2017/09/Node_CaseStudy_Nasa_FNL.pdf>

- [TC39]

  [TC39 - ECMAScript](https://www.ecma-international.org/memento/tc39.htm). URL: <https://www.ecma-international.org/memento/tc39.htm>

- [WAT]

  Gary Bernhardt. [Wat](https://www.destroyallsoftware.com/talks/wat). URL: <https://www.destroyallsoftware.com/talks/wat>

- [WHATISMYBROWSER]

  [What browser am I using?](https://www.whatsmybrowser.org/). URL: <https://www.whatsmybrowser.org/>

- [XKCD-703]

  Randall Munroe. [xkcd: Honor Societies](https://www.xkcd.com/703/). URL: <https://www.xkcd.com/703/>

- [YDKJS]

  Kyle Simpson. [You Don't Know JS (book series)](https://github.com/getify/You-Dont-Know-JS). URL: <https://github.com/getify/You-Dont-Know-JS)
