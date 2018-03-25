---
layout: post
title: 实现一个解释器
category: racket
date: 2018-02-25
---

解释器是什么，一般来说，我输入一个表达式，解释器对表达式进行求值，然后返回这个表达式的结果，这听起来和函数差不多，也与函数具有相同的功能，那么理所当然解释器就是一个函数。一个程序语言或一个编译器其实都是一个解释器，而现实生活中也有不少解释器的例子，比如翻译员、变频器、CPU等等。

<br />

---

<br />

## 关于这个解释器

### 目标

这篇文章已经鸽了半年有多了，尽管已经过了半年多了，可我的水平仍然停留在半年以前（半年多的空白期，甚至更菜了），所以这里能够实现一些基本功能就够了，比如：

1. 变量
2. 绑定
3. 函数
4. 调用
5. 分支
4. 类型推导

### 工具

最近一直在补 racket，所以这里选用 racket 作为我们的编程语言。编辑器可以选 DrRacket 或 Eamcs，或者平常用 DrRacket 写代码，看代码时换成 Emacs。

DrRacket 自带了一个词法闭包的背景 hl，非常醒目，对关键字的 hl 比较少（根本没有）；Emacs 对关键字的 hl 还是比较好的。如果你和我一样使用 racket 则需要另外自行配置，不然 REPL 和 eval-buffer 都用不了，要不再另外开一个终端，还不如前者的 interaction 方便。

![drrkt lexical hl][drrkt lexical hl] ![emcas hl][emacs hl]

你可以通过访问 <http://racket-lang.org/> 进行免费下载。

<br />

---

<br />

## R3 解释器

有人说得好，学习实现语言，最好是从最简单的语言和最简单的语法开始。先实现一个解释器，然后再慢慢往里面添加特性，这样才能有条不紊地制造出复杂语言。为什么这个解释器要叫 R3 呢，你可能听说过 [R3RS][r3rs]。当然，这里做的这个玩具是远不如 R3RS。

[上一次][arithmetic]我们实现了一个计算器，计算器也是一个解释器，只不过它只能用来解释算数表达式，我们只要在这个计算器上继续添加一些程序语言中的功能，就可以得到一个程序语言的解释器。

为了将这个计算器变成程序语言，这里首先把这个计算器给封装一下，并做一些抽象，把我们 R3 的**大致模型**给打造出来。

先看一下被改造后的计算器的代码以及注释： 

{% highlight racket %}

;; 初始化一个默认的环境
(define denv
  (map cons
       (list '+ '- '* '/)
       (list  +  -  *  /)))

;; 在环境中查找 s 对应的值
(define lookup
  (λ (s env)
    (let ([v (assq s env)])
      (cond
        [(not v) (error "unbound variable" )]
        [else (cdr v)]))))

;; 拓展这个环境
(define ext-env
  (λ (s v env)
    (cons (cons s v) env)))

;; 解释器对树的遍历部分
(define interp
  (λ (exp env)
    (match exp
      [(? number?) exp]
      [(? symbol?)
       (lookup exp env)]
      [(list e1 e2 ...)
       (let ([func (interp e1 env)]
             [vars (map (λ (x) (interp x env)) e2)])
         (apply func vars))])))

;; 对解释器的封装
(define r3
  (λ (exp)
    (interp exp denv)))

{% endhighlight %}

### 环境

实际上，构造一个复杂的程序，也就是为了去一步步地创建出越来越复杂的计算性对象，为此需要创建所需要的名字，也就是对象关联。

我们将值与符号相关联，而后又能提取出这些值，这意味着解释器必须拥有某种存储能力，并且能够随时找到这些由 **symbol-value** 组成的序对，这种存储被称为**环境（environment）**。在这里，我把一些基本的算术运算函数以序对的形式存进了默认的环境中，它看起来是这样的：

{% highlight racket %}

> (map cons
       (list '+ '- '* '/)
       (list  +  -  *  /))
'((+ . #<procedure:+>)
  (- . #<procedure:->)
  (* . #<procedure:*>) 
  (/ . #<procedure:/>))

{% endhighlight %}

`#<procedure:x>` 代表这是一个过程（函数），这种由序对组成的链表有一个术语叫作**关联表**，racket 提供了一些用于从关联表中查找序对的[函数][ass stuff]，分别叫 `assoc` `assv` `assq` `assf`（ass 是 association 的简写）。它们都具有相同的功能，只不过比较元素的方式不一样。

{% highlight racket %}

(let ([v (assq s env)])
  (cond
    [(not v) (error "unbound variable" )]
    [else (cdr v)]))

{% endhighlight %}

在 `lookup` 中，我使用了 `assq` 在关联表中进行寻值，如果传入的 symbol 是 `'+`，那 `v` 将会是 `(+ . #<procedure:+>)`，而它的 cdr 部分正是我们所需要的。如果没有找到关联的值则会返回 `#f`，在这里我直接引发了一个异常（Raising Exceptions）。

关联表本质上也是一个链表，所以要拓展这个环境，直接 cons 这个序对就可以了。

由于链表具有**堆栈（stack）**的性质，所以我们在环境中寻值的时候只能找到环境中最迟被压栈的值，考虑到这种情况：

{% highlight racket %}

'((+ . 123)
  (+ . #<procedure:+>)
  (- . #<procedure:->)
  (* . #<procedure:*>) 
  (/ . #<procedure:/>))

{% endhighlight %}

之后再去用 `'+` 寻值则只会返回 `(+ . 123)`，这种现象称之为 **shadow**。shadow 现象非常常见，举个例子，我们已经拥有一个变量 x 的值是 5，然后我们在 `let` 中又用 x 这个名字绑定了另外一个值 3，那在 `let` 的作用域中 x 的值只能是 3。关于这点在后面的闭包中另外细说。

{% highlight racket %}

> (define x 5)
> (let ([y 3])
    (+ x y))
8
> (let ([x 3])
    (+ x x))
6

{% endhighlight %}

### 模式匹配

模式匹配（Pattern Matching）是用来控制条件分支的一种很好的手段。

在条件表达式中，需要通过**谓词**去控制分支，而模式匹配则是通过各种模式去匹配这个表达式来完成分支，各种各样的模式之间可以任意搭配组合。在模式中可以使用谓词。

>
我们用术语**谓词**指代那些返回真或假的函数，也指那种能够求出真或者假的值的表达式。

我在对语法树的遍历中使用了一个叫 [match][match] 的函数控制递归的分支，你也可以使用其它条件表达式比如 `cond` `if` 进行遍历，但可能没有模式匹配来得方便与直观。

先引用一段文档上的语法介绍：

{% highlight racket%}

(match val-expr 
  clause ...)

clause = [pat body ...]
       | [pat #:when cond-expr body ...+]
       ...

{% endhighlight %}

如果表达式与这个 pat 相匹配的话则执行 body 语句块，`#:when` 是模式的卫语句。先来回顾一下上面那段 `match` 代码：

{% highlight racket %}

(match exp
  [(? symbol?)
   (lookup exp env)]
  [(? number?) exp]
  [(list e1 e2 ...)
   (let ([func (interp e1 env)]
         [vars (map (λ (x) (interp x env)) e2)])
     (apply func vars))])

{% endhighlight %}

其中用到了这么一个模式 `(? pred)`， pred 可以是任意谓词，比如 `symbol?` `number?` `procedure?`，这个模式表示将这个谓词应用于要匹配的表达式，并且结果为真。它与以下条件表达式或卫语句写法是等价的：

{% highlight racket %}

(cond
  [(pred exp) ...])

(match exp
  [exp #:when (pred exp) ...])

{% endhighlight %}

此外我还用到了另外一种模式 `(list e1 e2 ...)`，如果匹配的话，exp 将是一个链表，e1 是链表的 car 部分，e2 是链表的 cdr 部分，e1 与 e2 只是我随意取的变量名。

e1 与 e2 之间可插入多个元素，比如我用 `(list e1 e2 e3 e4 ...)` 去匹配表达式 `'(1 2 3 4 5 6 7)`，则 e1 的值对应了表达式的 car 部分 `1`，e2 是 cdr 部分的 car 部分也就是 `2`，e3 是 `3`，e4 是剩下的 cdr 部分 `'(4 5 6 7)`。

最后一个元素的后面没有 `...` 时，这个模式则只匹配指定元素数量的链表。

### 函数调用

最后一段代码是对语法树的遍历操作，此前必须匹配到一个至少有 1 个元素的链表，它的 car 部分是 e1，cdr 部分是 e2，那么来看一下对 e1 和 e2 的处理：

{% highlight racket %}

(let ([func (interp e1 env)]
      [vars (map (λ (x) (interp x env)) e2)])
  (apply func vars))

{% endhighlight %}

我们的语法 "S表达式" 规定了被括号包裹着的第一个元素必须是函数，实际上我们的表达式是由一堆符号与数字组合而成的链表，比如表达式 `'(+ 1 2 3)` 就是由符号 `'+` 和数字 `1` `2` `3` 组成的链表。那么 e1 可能的值就被缩小到好几种：

1. e1 是一个符号，尝试从环境中获取关联的函数
2. e1 是一个可被调用的 λ 匿名函数
3. e1 仍是一个表达式，但在经过解释后最终会返回一个函数

现阶段只对第一种情况做了处理，后面会继续拓展这个解释器以处理更多的情况。

e2 是调用 e1 所需要的参数，其中的元素可以是任意数据类型，处理方式如 e1 炮制，每当需要处理新的数据类型只要拓展这个解释器即可。

这里用到了一个叫 `map` 的函数，它接受一个函数与一个链表，之后（多个）链表中的每个元素分别调用这个函数，并将各自调用返回的结果组合成一个新链表。也就是将一（多）个链表通过一个函数**映射**为另一个链表。

### 用户界面

最后，我对解释器做了一下装箱的操作：

{% highlight racket %}

(define r3
  (λ (exp)
    (interp exp denv)))

{% endhighlight %}

r3 是实际解释器的用户界面函数，也就是解释器的入口，exp 是表达式（以链表形式），然后我让解释器使用一个事先配置好的环境作为默认环境，也就是最上面的 `denv`。

输入流也需要一个友好的用户界面，这里我先随便糊一个出来：

{% highlight racket %}

(define repl 
  (λ ()
    (display "REPL ⇒ ")
    (let ([exp (read)])
      (cond
        [(equal? exp '(exit)) "exit"]
        [else
         (printf "REPL ⇒ ~a~n" (r3 exp))
         (repl)]))))

{% endhighlight %}

你可以自行验证这个改造后的计算器的正确性，那么接下来我要对这个解释器进行一步改造。

<br />

---

<br />

## 函数与作用域

这里的函数包括匿名函数和已经被定义好了的函数。在我将要实现的语言里，函数是要像数字、符号等一样的存在，那么就有必要完全把它当成一种数据来处理。

作用域与环境之间只隔了一条模糊的界限，作用域纯粹只是用来描述函数或者变量作用范围的抽象概念。并且由于所有变量的值都被绑定到环境中，那么一个函数的所有可用变量便可以在轻易在相关联的环境中获取，你甚至可以通过环境来实现 IDE 中的代码提示/自动补全功能。

既然函数与作用域都能作为一种数据来使用，那么为了能够方便去控制某种特定的数据类型以及保持其完整性（你也可以不这样做，那么下面就可以跳过了），racket 允许我们通过 [struct][struct] 表达式来创建一种新的结构，下面做一些简单的介绍。

### 数据结构

通过 struct 创建一种新的结构类型：

{% highlight racket %}

;; 结构 foo 的两个成员分别是 bar baz
(struct foo (bar baz))

{% endhighlight %}

然后 racket 就会自动帮我们创建相关的构造函数（constructor）及选择函数（selectors）:

{% highlight racket %}

;; 构造函数默认就是它的 id 本身
> (foo 29 'qwe)
#<foo>

;; 选择函数则是 id-字段id 的形式，字段id 就是成员的名字
> (define my-foo (foo 'hello 'foo))
> (foo-bar my-foo)
'hello
> (foo-baz my-foo)
'foo

;; 还可以通过一个谓词函数来判断某种数据是否为该种结构类型的实例
> (foo? my-foo)
#t

{% endhighlight %}

struct 的功能远不止如此，但对于 R3 而言已经够用了，试着将这种结构嵌入到我们的解释器中：

{% highlight racket %}

(struct closure (fun env))

;; 我用 id* 的形式命名表示这个函数或变量能够处理一组数据或者存储了一组数据
(define ext-env*
  (λ (a* v* env*)
    `(,@(map cons a* v*) ,@env*)))

;; 匹配了一种新的数据结构 closure
(define interp
  (λ (exp env)
    (match exp
      [(? number?) exp]
      [(? symbol?)
       (lookup exp env)]
      [`(λ ,a ,e ...)
       (closure exp env)]
      [`(,f ,a* ...)
       (let ([funv (interp f env)]
             [arg* (map (λ (a) (interp a env)) a*)])
         (match funv
           [(? procedure?)
            (apply funv arg*)]
           [(closure `(λ ,a* ,e*) env-save)
            (interp e* (ext-env* a* arg* env-save))]
           [_ (error "badmatch" funv)]))])))

{% endhighlight %}

### 闭包

闭包说起来有点麻烦，这里可以简单理解为：每一个函数都有一一相对应的环境，使得各函数作用域的变量之间不会造成混淆，那么这种做法就可以称作**闭包（Closure）**。我们已经将一个函数与一个环境通过 `struct` 联系到一起籍此实现闭包。

>
闭包的方式也有多种，其中较为常人所知晓的两种 Dynamic Closure 以及 Lexical Closure，各自所造成的结果也就是 Dynamic Scoping 和 Lexical Scoping，是不是很好记。两种闭包各有其特色，其中的恩怨情仇我也不特意去考古了。

用其他语言中的闭包来举个例子，比如拿 java 和我们 R3 解释器的原型 scheme 来比较：

1. 首先把 java 中的“类”想象成 scheme 中的“函数”；
2. 如此一来使用 `new 构造函数` 创建对象返回的一份实例就相当于调用函数后返回了一个新的函数；
3. 对象中可以拥有成员和方法，并且只能通过该对象的实例获取这些闭包内的数据；
4. 函数中可以定义一些临时的变量或函数，由于闭包将函数与环境相关联，最后返回的函数也自然而然地拥有了这些数据的引用。

{% highlight java %}

class A {
    int a = 10;
    int plusA(int b) { a + b; }
}

A a = new A();
a.a;         // ⇒ 10
a.plusA(6);  // ⇒ 16

{% endhighlight %}

{% highlight racket %}

(define (A)
  (define a 10)
  (define (plusA b) (+ a b))
  (λ (pick)
    (match pick
      ['a a]
      ['plusA plusA])))
	  
(define a (A))
(a 'a)         ; ⇒ 10
((a 'plusA) 6) ; ⇒ 16

{% endhighlight %}

函数与类、对象之间的界限变得越来越模糊了，实际上它们都是一种拥有 closure 行为的数据结构而已，说到这里我还想聊一下 javascript。

如今大多数语言都已经把函数当成头等公民，也就是允许把函数当作参数传递、作为值返回，甚至还有函数类型，也就是名副其实的是一种数据。但在 javascript 中，函数同时也是一个**对象**，直接来看下代码。

{% highlight javascript %}

function p(name) {
    this.name = name;
    this.getName = function() {
        this.prefix = 'Name is ';
        return this.prefix + this.name;
    }
}

p                          // [Function: p]
p1 = new p('ldc')          // p { name: 'ldc', getName: [Function] }
p1.getName()               // 'Name is ldc'
p1g = new p1.getName()     // { prefix: 'Name is ' }
p1g.suffix = 'end...'; p1g // { prefix: 'Name is ', suffix: 'end...' }

{% endhighlight %}

发生了什么事情？？？

javascript 的函数通过 `new` 操作变成了一个具有 closure 的数据，然后通过 `.` 对这个 closure 的 env 进行访问（读取修改）。

对比一下 java 中获取闭包内数据的方法，再对比一下 scheme 中获取闭包内数据的方法。

java 需要事先把结构给声明好（它拥有哪些成员、方法）。scheme 中获取数据的方法非常不方便，使用时也非常不方便。javascript 看起来就像是结合了两者的优点一样，并且还可以随时给自己的 env 添加关联数据（随时随地添加成员或者方法）。

### 变量绑定



{% highlight racket %}



{% endhighlight %}


<br />

---

<br />


## 未完待续。。。

{% assign img_url = "/images/r3-interpreter" %}

[drrkt lexical hl]: {{img_url}}/drrkt-lexical-hl.png
[emacs hl]: {{img_url}}/emacs-hl.png

[arithmetic]: /interpreter/2018/02/04/basic-arithmetic.html
[r3rs]: https://practical-scheme.net/wiliki/schemexref.cgi?R3RS
[ass stuff]: https://docs.racket-lang.org/reference/pairs.html?q=assoc#%28def._%28%28lib._racket%2Fprivate%2Flist..rkt%29._assoc%29%29
[match]: https://docs.racket-lang.org/reference/match.html?q=ass
[struct]: https://docs.racket-lang.org/reference/define-struct.html
