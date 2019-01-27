# 如何阅读 ECMAScript 规范

- 原文链接:

  <https://timothygu.me/es-howto/>

- 原作者:

  [Timothy Gu](https://timothygu.me/) [timothygu99@gmail.com](mailto:timothygu99@gmail.com)

- 说明：这篇文档由我本人（槐念，微信公众号Huainian_1994）逐字翻译而得，有些许删改，力求保留原意，包括感情色彩。本文不得用于商业用途，而对于其他用途，在注明出处和作者的前提下可随意转载、删改本翻译文档，同时需要遵守原文的版权规范。
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

   ​	[2.3.1它是一个记录的一个字段（ A field of a Record）](#它是一个记录的一个字段（ A field of a Record）)	

   ​	[2.3.2它表示了JS对象的“内部插槽”](#它表示了JS对象的"内部插槽")

   ​	[2.3.3它表示了JS对象的一个内置方法](#它表示了JS对象的一个内置方法)

   [2.4Completion Records; `?` and `!`](#completion-records-and-shorthands)

   [2.5JavaScript Objects](#javascript-objects)

   [2.6Example: `String.prototype.substring()`](#example-string-prototype-substring)

   [2.7Example: Can `Boolean()` and `String()` ever throw exceptions?](#example-can-boolean-and-string-ever-throw-exceptions)

   [2.8Example: `typeof` operator](#example-typeof-operator)

 <h3 id="prelude" >1. 前言</h3>

现在，你决定每天阅读一点ECMAScript规范。也许是为了陶冶情操，或者这是新年立的FLAG，亦或你老妈觉得你需要读。哎，不管是什么原因，欢迎入坑!

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





<h3 id="Runtime-sematics" >2、执行语义（Runtime sematics） </h3>

JS语言和大量API的执行语义占据了ECMAScript规范的很大篇幅。通常，它描述了人们最关心的问题。

（译者注：所谓执行语义，就是这段代码执行后会干啥。相同一段代码，放到不同代码块中执行的流程、结果会有很大的差异。直接从一段代码可阅读出的意思称为所谓的**静态语义**，而表示了一段代码的执行流程、执行结果的称为**执行语义**，也成为**动态语义**。）

大体说，执行语义相关章节的表述非常直接，不绕弯子。然鹅蛋碎的是， ECMAScript规范使用了大量的速记词法，对于刚起步的人来说这简直讨厌至极（至少对我来说是如此）。为了了解一些语句的工作方式，我会尝试解释其中的一些约定，并将它们应用到我的日常工作流程中。



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



> 延伸阅读: [§5.2 Algorithm Conventions](https://tc39.github.io/ecma262/#sec-algorithm-conventions)



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



#### 2.3.1. 它表示了一对键值对（ A field of a Record）

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



#### 2.3.2. 它表示了JS对象的“内部插槽”

根据ECMAScript规范，JavaScript对象中可能会有所谓的用于**保存数据**的“内部插槽”（internal slot）。 正如记录`Record`的字段*(field)*，这些所谓的内部插槽在使用JS的过程中是不可见的，它不会体现于代码中。

（译者注：但是它会体现于代码结果上：例如内置对象**Data**使用了*[[DateValue]]*这个“内部插槽”，用于存储以世界协调时为基准的当前时间的时间戳。码代码时不可见，但你可以把它打印出来。）

但是，他们会暴露于那些实现了特定细节的工具中，例如谷歌Chrome自带的开发者调试工具。因此，使用形如*[[Notation]]*的结构来描述“内部插槽”是有意义的。关于“内置插槽”的规定在2.2章节中 [§2.5 JavaScript Objects](#javascript-objects)。

现在，你专心看以下的例子就好，不要老想着内部插槽到底是干嘛的：

> 例三：大部分JS对象都会拥有这个内部插槽*[[Prototype]]* ，它指向了这个JS对象的继承来源。这个内部插槽的值正是方法 `Object.getPrototypeOf()` 的返回值。在抽象操作 [OrdinaryGetPrototypeOf](https://tc39.github.io/ecma262/#sec-ordinarygetprototypeof) 中，内部插槽*[[Prototype]]*的值被访问了：
>
> <hr>
>
> When the abstract operation OrdinaryGetPrototypeOf is called with Object O, the following steps are taken:
>
> 1. Return O.[[Prototype]].

心得：一个JS对象的内部插槽和记录字段的样子看起来一样，但看一眼*[[Notation]]*之前的的内容即可立辨雌雄（在`.`之前的部分）。



#### 2.3.3. 它表示了JS对象的一个内置方法

JavaScript对象中也可能会有所谓的”内置方法”（internal method） 。正如“内部插槽”，这些内置方法也不能通过JS代码直接观察到。 因此，使用形如*[[Notation]]*的结构来表示内置方法也是有意义的。

第2.5章节阐述了内置方法的相关细节 [§2.5 JavaScript Objects](#javascript-objects).。现在，你专心看以下的例子就好，不要老想着内置方法用来干嘛：

> 例四：All JavaScript functions have an internal method [[Call]] that runs that function. The [Call](https://tc39.github.io/ecma262/#sec-call) abstract operation has the following step:
>
> 1. Return ? F.[[Call]] (V, argumentsList).
>
> 其中F为JavaScript函数对象。在本例中，F使用形参*V*和*argumentsList*对内置方法*[[Call]]*进行了调用。

心得： 我们可以根据这个*[[Notation]]*周围的内容看出它是否长得像一个函数。从而判定它是否为一个内置函数。



### 2.4. “完成记录” 与 符号 `?` 、 `!`

每段ECMAScript规范中的执行语义都显式或隐式地返回了一个描述代码输出结果的**完成记录**（Completion Records）。这个完成记录有三个可能的字段：

> - 首先，这个完成记录形如：[[Type]]
>   - (可能的类型有`normal`、 `return`、`throw`、`break`或者`continue`。)
> - 如果 [[Type]]是 `normal`、`return`、或 `throw`，那么它还会拥有 [[Value]] ("抛出了什么/返回值是啥")。
>
> - 如果 [[Type]]是  `break` 或者 `continue`，那么它还会视情况带上一个  [**标记语句**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label#) [[Target]]，来标记这个语句中断的地方 ，或表示中断后从何处继续执行。

注： 形如`[[ ]]`的双括号常用来表示一个记录的字段（即键值对）。

一个[[Type]]是`normal` 的完成记录称作“正常完成”*（normal completion）*。除了“正常完成”之外，其他的完成记录称为“突然完成”*（abrupt completion）*。

大多情况下，你会处理到的*abrupt completion*的记录其 [[[Type\]]](#completion-record-type) 为 `throw`。其他三种*abrupt completion*的完成记录只会在你观察“特定语法元素的计算过程”时有点作用。实际上，你并不会在内置函数的定义中看到其他类型，因为关键词`break`/`continue`/`return` 不会跨函数域工作。

延伸阅读： [§6.2.3 The Completion Record Specification Type](https://tc39.github.io/ecma262/#sec-completion-record-规范-type)

------

Because of the definition of [Completion Records](#completion-record), niceties in JavaScript like bubbling errors until a `try`-`catch` block don’t exist in the spec. In fact, errors (or more precisely abrupt completions) are handled explicitly.

Without any shorthands, the spec text for an ordinary call to an abstract operation that may either return a computation result or throw an error would look like:



A few steps that call an abstract operation that may throw **without any shorthands**:

1. Let resultCompletionRecord be AbstractOp().

   Note: resultCompletionRecord is a [Completion Record](#completion-record).

2. If resultCompletionRecord is an abrupt completion, return resultCompletionRecord.

   Note: Here, resultCompletionRecord is directly returned if it is an [abrupt completion](#abrupt-completion). In other words, errors thrown in AbstractOp are forwarded, and the remaining steps aborted.

3. Let result be resultCompletionRecord.[[Value]].

   Note: After ensuring we got a [normal completion](#normal-completion), we can now unwrap the [Completion Record](#completion-record) to get the actual result of the computation we need.

4. result is the result we need. We can now do more things with it.

This may possibly vaguely remind you of manual error handling in C:

```c
int result = abstractOp();              // Step 1
if (result < 0)                         // Step 2
  return result;                        // Step 2 (continued)
                                        // Step 3 is unneeded
// func() succeeded; carrying on...     // Step 4
```

But to reduce these heavily boilerplated steps, editors of the ECMAScript spec added a few shorthands. Since ES2016, the same spec text can instead be written in the following two equivalent ways:



A few steps that call an abstract operation that may throw **with ReturnIfAbrupt**:

1. Let result be AbstractOp().

   Note: Here, just like the step 1 in the previous example, result is a [Completion Record](#completion-record).

2. [ReturnIfAbrupt](https://tc39.github.io/ecma262/#sec-returnifabrupt)(result).

   Note: [ReturnIfAbrupt](#abstract-opdef-returnifabrupt) deals with any possible [abrupt completions](#abrupt-completion)by forwarding it, and unwraps the result to its [[[Value\]]](#completion-record-value)automatically.

3. result is the result we need. We can now do more things with it.

or, even more concisely, with a special question mark (?) notation:



A few steps that call an abstract operation that may throw **with a question mark (?)**:

1. Let result be ? AbstractOp().

   Note: In this notation we don’t deal with [Completion Records](#completion-record) at all. The [?](#question-mark) shorthand handles *everything* for us, and result is ready to use immediately after.

2. result is the result we need. We can now do more things with it.

------

Sometimes, it can convey more information to the reader about the spec’s intent if we know that a particular call to AbstractOp will never return an abrupt completion. In those cases, an exclamation mark (!) is used:



A few steps that call an abstract operation that cannot ever throw **with an exclamation mark (!)**:

1. Let result be ! AbstractOp().

   Note: While [?](#question-mark) forwards any errors we may have gotten, [!](#exclamation-mark) asserts that we *never* get any abrupt completions from this call, and it would be a bug in the 规范 if we did. Like the case with [?](#question-mark), we don’t deal with [Completion Records](#completion-record) at all. result is ready to use immediately after.

2. result is the result we need. We can now do more things with it.

CAUTION

The [!](#exclamation-mark) can admittedly become pretty confusing if it is part of a comparison:

> 1. ..., return the result of the comparison ! [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber)(x) == y.
>
> — Excerpted from [§7.2.14 Abstract Equality Comparison](https://tc39.github.io/ecma262/#sec-abstract-equality-comparison).

Here, the [!](#exclamation-mark) just means that we are certain that this call to [ToNumber](#abstract-opdef-tonumber) will never return an exception, *not* that the comparison is inverted!

Further reading: [§5.2.3.4 ReturnIfAbrupt Shorthands](https://tc39.github.io/ecma262/#sec-returnifabrupt-shorthands).

### 2.5. JavaScript Objects

In ECMAScript, every Object has a set of internal methods that the rest of the 规范 call on to do certain tasks. A few of these [internal methods](#internal-method)that all Objects have are:

- [[Get]], which gets a property on an Object (e.g. `obj.prop`)
- [[Set]], which sets a property on an Object (e.g. `obj.prop = 42;`)
- [[GetPrototypeOf]], which gets the Object’s prototype (i.e., `Object.getPrototypeOf(obj)`)
- [[GetOwnProperty]], which gets the Property Descriptor of an own property of an Object (i.e., `Object.getOwnPropertyDescriptor(obj,"prop")`)
- [[Delete]], which deletes a property on an Object (e.g. `delete obj.prop`)

(an exhaustive list is available in [§6.1.7.2 Object Internal Methods and Internal Slots](https://tc39.github.io/ecma262/#sec-object-internal-methods-and-internal-slots)).

Based on this definition, function objects (or just "functions") are simply Objects that additionally have the [[Call]] internal method, and possibly the [[Construct]] internal method too; for this reason they are also known as callable objects.

The spec then divides all Objects into two camps: [ordinary objects](#ordinary-object) and [exotic objects](#exotic-object). Most of the objects you encounter are ordinary objects, which means that all of their [internal methods](#internal-method) are the default ones specified in [§9.1 Ordinary Object Internal Methods and Internal Slots](https://tc39.github.io/ecma262/#sec-ordinary-object-internal-methods-and-internal-slots).

However, ECMAScript spec also defines a few kinds of exotic objects, which may override the default implementations of those internal methods. There are certain minimal constraints put on what exotic objects are allowed to do, but in general the overriden internal methods can do a lot of acrobatics without going against the spec.



`Array` objects are one kind of these [exotic objects](#exotic-object). Some special semantics around the `length` property of `Array` objects cannot be achieved using the instruments available to [ordinary objects](#ordinary-object).

One of them is the fact that setting the `length` property of an `Array`object can remove properties from the object, but the `length` property seems to be just an ordinary data property. In contrast, `new Map().size` is only a getter function specified on `Map.prototype`, and does not have the magical properties `[].length` does.

```
> const arr = [0, 1, 2, 3];
> console.log(arr);
[ 0, 1, 2, 3 ]
> arr.length = 1;
> console.log(arr);
[ 0 ]
> console.log(Object.getOwnPropertyDescriptor([], "length"));
{ value: 1,
  writable: true,
  enumerable: false,
  configurable: false }
> console.log(Object.getOwnPropertyDescriptor(new Map(), "size"));
undefined
> console.log(Object.getOwnPropertyDescriptor(Map.prototype, "size"));
{ get: [Function: get size],
  set: undefined,
  enumerable: false,
  configurable: true }
```

This behavior is achieved by overriding the [[DefineOwnProperty]] internal method. See [§9.4.2 Array Exotic Objects](https://tc39.github.io/ecma262/#sec-array-exotic-objects) for details.

The ECMAScript spec also allows other specs to define their own exotic objects. It is through this mechanism the limitations browsers put on [cross-origin API access](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy#Cross-origin_script_API_access) are specified (see `WindowProxy`) [[HTML\]](#biblio-html). It is also possible for JavaScript programmers to create their own exotic objects through the `Proxy` API.

------

JavaScript Objects may also have internal slots defined to contain certain types of values. I tend to think of [internal slots](#internal-slot) as Symbol-named properties that are hidden even to `Object.getOwnPropertySymbols()`. Both [ordinary objects](#ordinary-object) and [exotic objects](#exotic-object) are allowed to have [internal slots](#internal-slot).

In [§2.3.2 An internal slot of a JavaScript Object](#double-brackets-internal-slot-of-javascript-object), I mentioned an [internal slot](#internal-slot) called [[Prototype]] that most Objects have. (In fact, all [ordinary objects](#ordinary-object) and even some [exotic objects](#exotic-object) like `Array` objects have it.) But we also know that there is an [internal method](#internal-method) called [[GetPrototypeOf]] that I briefly described above. What is the difference?

The keyword here is *most*: while *most* objects have the [[Prototype]] internal slot, *all* objects implement the [[GetPrototypeOf]] internal method. Notably, `Proxy` objects do not have their own [[Prototype]], and its [[[GetPrototypeOf\]]](https://tc39.github.io/ecma262/#sec-proxy-object-internal-methods-and-internal-slots-getprototypeof) internal method instead defer to either the registered handler or the prototype of its target, stored in the [[ProxyTarget]] internal slot of the `Proxy` object.

For this reason, when dealing with Objects, it is almost always a good idea to refer to the appropriate [internal method](#internal-method) rather than directly looking at the value of an [internal slot](#internal-slot).

------

Another way of thinking about the relations between Objects, [internal methods](#internal-method), and [internal slots](#internal-slot) are through a classical object-oriented lens. "Object" is like an interface specifying several [internal methods](#internal-method) that must be implemented. [Ordinary objects](#ordinary-object) provide default implementations, which [exotic objects](#exotic-object) may override either partially or fully. On the other hand, [internal slots](#internal-slot) are like instance variables of an Object – the implementation details of that Object.

All of these relations are summarized by the following UML diagram (click to enlarge):

[![Boxes denoting concepts and connections between them denoting hierarchy]()](object-uml.svg)

### 2.6. Example: `String.prototype.substring()`

Now that we have a pretty good understanding of how the spec is organized and written, let’s practice!

Suppose I now have the following question:

> Without running the code, what does the following code fragment return?
>
> ```js
> String.prototype.substring.call(undefined, 2, 4)
> ```

This is a pretty tricky question. It seems that there are two plausible outcomes:

1. `String.prototype.substring()` could first cast **undefined** to the string `"undefined"`, and then take characters at positions two and three (i.e., the interval [2, 4)) of that string to **result in "de"**
2. On the other hand, `String.prototype.substring()` may just as reasonably **throw an error**, thus rejecting **undefined** as an input.

Unfortunately, [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/substring#) also doesn’t really offer any insights on how the function behaves when the **this** value isn’t a string.

*Spec to the rescue!* After typing in `substring` in the search box in the top-left corner on the spec [[ECMA-262\]](#biblio-ecma-262), we arrive at [§21.1.3.21 String.prototype.substring ( start, end )](https://tc39.github.io/ecma262/#sec-string.prototype.substring), which is the normative 规范 of how the function works.

Before reading the algorithm steps, let’s think about what we know first. I assume we have a basic understanding of how `str.substring()` ordinarily works, which is to return a part of the given string. What we are not really sure of right now, is how it acts with the **this** value being **undefined**. So, we would specifically look for algorithm steps that address the **this** value.

Lucky for us, the first step of the algorithm for `String.prototype.substring()` deals specifically with the **this** value:

> 1. Let *O* be ? [RequireObjectCoercible](https://tc39.github.io/ecma262/#sec-requireobjectcoercible)(**this** value).

The [?](#question-mark) shorthand allows us to conclude that there may be some cases where the [RequireObjectCoercible](https://tc39.github.io/ecma262/#sec-requireobjectcoercible) abstract operation may actually throw exceptions, since otherwise [!](#exclamation-mark) would have been used instead. In fact, if it throws an error it would correspond with our second hypothesis above! With hope, we look into what [RequireObjectCoercible](https://tc39.github.io/ecma262/#sec-requireobjectcoercible) does by clicking on the hyperlink.

The [RequireObjectCoercible](https://tc39.github.io/ecma262/#sec-requireobjectcoercible) abstract operation is a little odd. Unlike most abstract operations, it is defined through a table rather than steps:

> | Argument Type | Result                           |
> | ------------- | -------------------------------- |
> | Undefined     | Throw a **TypeError** exception. |
> | ...           | ...                              |

No matter though – in the row corresponding to Undefined (the type of the **this** value we passed to `substring()`) the spec says that [RequireObjectCoercible](https://tc39.github.io/ecma262/#sec-requireobjectcoercible) should throw an exception. And because the [?](#question-mark)notation is used in the definition of the function, we know that the thrown exception must bubble up to the caller of the function. Bingo!

And there we have our answer: **the given code fragment throws a TypeError exception.**

The spec only specifies the type of the Error thrown, not what message it contains. This means that implementations can have different error messages, maybe even localized ones.

On Google’s V8 6.4 (included in Google Chrome 64), for example, the message is

```
TypeError: String.prototype.substring called on null or undefined
```

while Mozilla Firefox 57.0 gives a somewhat less helpful

```
TypeError: can’t convert undefined to object
```

At the same time, ChakraCore version 1.7.5.0 (the JavaScript engine in Microsoft Edge) takes V8’s route and throws

```
TypeError: String.prototype.substring: 'this' is null or undefined
```

### 2.7. Example: Can `Boolean()` and `String()` ever throw exceptions?

When writing mission-critical code, one must put exception handling at the forefront in programming. As such, the question of "can *some built-in function* ever throw an exception?" may be oft-pondered.

In this example, we shall try to answer this question for two language built-in functions, `Boolean()` and `String()`. We will only look at direct calls to those functions, not the cases of `new Boolean()` and `new String()` which form boxed objects – easily one of the [most undesirable features in JavaScript](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch3.md#object-wrapper-gotchas) and a very much discouraged practice in pretty much all JS style guides out there [[YDKJS\]](#biblio-ydkjs).

After navigating to the section for `Boolean()` in the spec, we see that the algorithm seems to be fairly short:

When `Boolean` is called with argument value, the following steps are taken:

1. Let b be [ToBoolean](https://tc39.github.io/ecma262/#sec-toboolean)(value).
2. If NewTarget is **undefined**, return b.
3. Let O be ? [OrdinaryCreateFromConstructor](https://tc39.github.io/ecma262/#sec-ordinarycreatefromconstructor)(NewTarget, `"%BooleanPrototype%"`, « [[BooleanData]] »).
4. Set O.[[BooleanData]] to b.
5. Return O.

But on the other hand, it’s not totally straightforward, with some complex acrobatics around [OrdinaryCreateFromConstructor](https://tc39.github.io/ecma262/#sec-ordinarycreatefromconstructor) involved. More importantly, there is a [?](#question-mark) shorthand in step 3 that *may* indicate this function can throw errors in certain cases. Let’s take a closer look.

Step 1 casts value (the function argument) to a Boolean value. Interestingly there isn’t a [?](#question-mark) or [!](#exclamation-mark) shorthand for this step, but usually not having a Completion Record shorthand means the same thing as [!](#exclamation-mark). So step 1 cannot throw an exception.

Step 2 checks if something called [NewTarget](#abstract-opdef-newtarget) is **undefined**. NewTarget is the spec equivalent for the `new.target` meta property that was first added in ES2015, allowing the spec to distinguish between a `new Boolean()` call (where it is `Boolean`) and a `Boolean()` call (where it is **undefined**). Because we are only looking at direct calls to `Boolean()` at this moment, we know that [NewTarget](#abstract-opdef-newtarget) is always going to be **undefined**, and the algorithm will always return b straightaway without any additional processing.

Because calling `Boolean()` without `new` can only access the first two steps in the algorithm for `Boolean()`, neither of which can throw exceptions, we conclude that **Boolean() never throws exceptions** no matter what the input is.

------

Let’s turn our attention to `String()`:

When `String` is called with argument value, the following steps are taken:

1. If no arguments were passed to this function invocation, let s be `""`.
2. Else,
   1. If NewTarget is **undefined** and [Type](https://tc39.github.io/ecma262/#sec-ecmascript-data-types-and-values)(value) is Symbol, return [SymbolDescriptiveString](https://tc39.github.io/ecma262/#sec-symboldescriptivestring)(value).
   2. Let s be ? [ToString](https://tc39.github.io/ecma262/#sec-tostring)(value).
3. If NewTarget is **undefined**, return s.
4. Return ? [StringCreate](https://tc39.github.io/ecma262/#sec-stringcreate)(s, ? [GetPrototypeFromConstructor](https://tc39.github.io/ecma262/#sec-getprototypefromconstructor)(NewTarget, `"%StringPrototype%"`)).

From our experience with doing the same kind of analysis with the `Boolean()` function, we know that [NewTarget](#abstract-opdef-newtarget) will always be **undefined** for our case, and therefore the last step can be skipped from our consideration. We also know that [Type](#abstract-opdef-type) and [SymbolDescriptiveString](https://tc39.github.io/ecma262/#sec-symboldescriptivestring) are safe as well, since abrupt completions are not handled for either of them. Yet, there is still a tell-tale [?](#question-mark) preceding the call to the [ToString](#abstract-opdef-tostring) abstract operation. Let’s take a closer look.

Like [RequireObjectCoercible](https://tc39.github.io/ecma262/#sec-requireobjectcoercible) we looked at earlier, [ToString](https://tc39.github.io/ecma262/#sec-tostring)(argument) is also defined with a table:

> | Argument Type | Result                                                       |
> | ------------- | ------------------------------------------------------------ |
> | Undefined     | Return `"undefined"`.                                        |
> | Null          | Return `"null"`.                                             |
> | Boolean       | If argument is **true**, return `"true"`.If argument is **false**, return `"false"`. |
> | Number        | Return [NumberToString](https://tc39.github.io/ecma262/#sec-numbertostring)(argument). |
> | String        | Return argument.                                             |
> | Symbol        | Throw a **TypeError** exception.                             |
> | Object        | Apply the following steps:Let primValue be ? [ToPrimitive](https://tc39.github.io/ecma262/#sec-toprimitive)(argument, hint String).Return ? [ToString](https://tc39.github.io/ecma262/#sec-tostring)(primValue). |

At the point where [ToString](#abstract-opdef-tostring) is called in `String()`, value can be any value other than a Symbol (which is filtered out in the step immediately before). Yet, there still remain two [?](#question-mark) in the row for Object. We can follow the link to [ToPrimitive](https://tc39.github.io/ecma262/#sec-toprimitive) and beyond, and see that there are in fact a lot of opportunities for an error to be thrown if value is an Object:

Several examples where `String()` throws

So for `String()`, our conclusion is that **it never throws exceptions for primitive values, but may throw errors for Objects.**

### 2.8. Example: `typeof` operator

So far, we’ve only analyzed API functions, let’s try something different.

 To be written. [](https://github.com/TimothyGu/es-howto/issues/2)

## Glossary

### Common abstract operations

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

## Index

### Terms defined by this specification

- [!](#exclamation-mark), in §2.4
- [?](#question-mark), in §2.4
- [abrupt completion](#abrupt-completion), in §2.4
- [abstract operation](#abstract-operation), in §2.2
- [ArrayCreate](#abstract-opdef-arraycreate), in §Unnumbered section
- [break](#dom-completion-record-type-break), in §2.4
- [Call](#abstract-opdef-call), in §Unnumbered section
- [callable object](#callable-object), in §2.5
- [Completion Record](#completion-record), in §2.4
- [Construct](#abstract-opdef-construct), in §Unnumbered section
- [continue](#dom-completion-record-type-continue), in §2.4
- [DefinePropertyOrThrow](#abstract-opdef-definepropertyorthrow), in §Unnumbered section
- [DeletePropertyOrThrow](#abstract-opdef-deletepropertyorthrow), in §Unnumbered section
- [double brackets notation](#double-brackets-notation), in §2.3
- [exotic object](#exotic-object), in §2.5
- [field](#record-field), in §2.3.1
- [function object](#function-object), in §2.5
- [Get](#abstract-opdef-get), in §Unnumbered section
- [GetV](#abstract-opdef-getv), in §Unnumbered section
- [HasOwnProperty](#abstract-opdef-hasownproperty), in §Unnumbered section
- [HasProperty](#abstract-opdef-hasproperty), in §Unnumbered section
- [internal method](#internal-method), in §2.5
- [internal slot](#internal-slot), in §2.5
- [Invoke](#abstract-opdef-invoke), in §Unnumbered section
- [IsArray](#abstract-opdef-isarray), in §Unnumbered section
- [IsCallable](#abstract-opdef-iscallable), in §Unnumbered section
- [IsConstructor](#abstract-opdef-isconstructor), in §Unnumbered section
- [NewTarget](#abstract-opdef-newtarget), in §2.7
- [normal](#dom-completion-record-type-normal), in §2.4
- [normal completion](#normal-completion), in §2.4
- [ordinary object](#ordinary-object), in §2.5
- [Record](#record), in §2.3.1
- [return](#dom-completion-record-type-return), in §2.4
- [ReturnIfAbrupt](#abstract-opdef-returnifabrupt), in §Unnumbered section
- [StringCreate](#abstract-opdef-stringcreate), in §Unnumbered section
- [[[Target\]]](#completion-record-target), in §2.4
- [throw](#dom-completion-record-type-throw), in §2.4
- [ToBoolean](#abstract-opdef-toboolean), in §Unnumbered section
- [ToInt16](#abstract-opdef-toint16), in §Unnumbered section
- [ToInt32](#abstract-opdef-toint32), in §Unnumbered section
- [ToInt8](#abstract-opdef-toint8), in §Unnumbered section
- [ToInteger](#abstract-opdef-tointeger), in §Unnumbered section
- [ToNumber](#abstract-opdef-tonumber), in §Unnumbered section
- [ToObject](#abstract-opdef-toobject), in §Unnumbered section
- [ToPrimitive](#abstract-opdef-toprimitive), in §Unnumbered section
- [ToString](#abstract-opdef-tostring), in §Unnumbered section
- [ToUint16](#abstract-opdef-touint16), in §Unnumbered section
- [ToUint32](#abstract-opdef-touint32), in §Unnumbered section
- [ToUint8](#abstract-opdef-touint8), in §Unnumbered section
- [ToUint8Clamp](#abstract-opdef-touint8clamp), in §Unnumbered section
- [Type](#abstract-opdef-type), in §Unnumbered section
- [[[Type\]]](#completion-record-type), in §2.4
- [[[Value\]]](#completion-record-value), in §2.4

### Terms defined by reference

- [CONSOLE]

   

  defines the following terms:

  - console

- [DOM]

   

  defines the following terms:

  - addEventListener(type, callback)

- [ECMA-262]

   

  defines the following terms:

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

   

  defines the following terms:

  - HTMLElement
  - WindowProxy
  - Worker
  - alert(message)
  - all
  - confirm(message)
  - document
  - window

- [NODEJS]

   

  defines the following terms:

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

## References

### Informative References

- [CONSOLE]

  Dominic Farolino; Terin Stock; Robert Kowalski. [Console Standard](https://console.spec.whatwg.org/). Living Standard. URL: <https://console.spec.whatwg.org/>

- [DOM]

  Anne van Kesteren. [DOM Standard](https://dom.spec.whatwg.org/). Living Standard. URL: <https://dom.spec.whatwg.org/>

- [ECMA-262]

  [ECMAScript Language 规范](https://tc39.github.io/ecma262/). URL: <https://tc39.github.io/ecma262/>

- [ECMA-262-2018]

  [ECMAScript 2018 Language 规范](https://ecma-international.org/ecma-262/9.0/index.html). URL: <https://ecma-international.org/ecma-262/9.0/index.html>

- [ECMA-262-GLOBAL]

  [tc39/proposal-global: ECMAScript Proposal, specs, and reference implementation for `global`](https://github.com/tc39/proposal-global). URL: <https://github.com/tc39/proposal-global>

- [HTML]

  Anne van Kesteren; et al. [HTML Standard](https://html.spec.whatwg.org/multipage/). Living Standard. URL: <https://html.spec.whatwg.org/multipage/>

- [ISO-16262-2011]

  [ISO/IEC 16262:2011 - Information technology – Programming languages, their environments and system software interfaces – ECMAScript language 规范](https://www.iso.org/standard/55755.html). URL: <https://www.iso.org/standard/55755.html>

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

  Kyle Simpson. [You Don't Know JS (book series)](https://github.com/getify/You-Dont-Know-JS). URL: <https://github.com/getify/You-Dont-Know-JS>

## Issues Index

 To be written. [](https://github.com/TimothyGu/es-howto/issues/2)[ ↵](#issue-c1ad3571)