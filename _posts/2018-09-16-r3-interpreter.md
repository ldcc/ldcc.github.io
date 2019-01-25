---
layout: post
title: Lisp 解释器(Basic)
category: INTERP
date: 2018-09-16
---

一个程序语言或一个编译器其实都是一个解释器，那么解释器是什么？一般来说，我输入一个表达式，解释器对表达式进行求值，然后返回这个表达式的结果。
这听起来和函数差不多，也与函数具有相同的功能，那么理所当然解释器就是一个函数。现实生活中也有不少解释器的例子，比如翻译员、变频器、CPU等等。

<br />

---

<br />

## 关于这个解释器

### 目标

这篇文章已经鸽了半年有多了，尽管已经过了半年多了，可我的水平仍然停留在半年以前（半年多的空白期，甚至更菜了（写了一半想摸几天，现在又鸽了半年））。
所以这里能够实现一些基本功能就够了：

基本功能：
- [x] 算术运算(arithmetic)
- [x] 环境(environment)
- [x] 用户界面(repl)
- [x] 函数调用(invoke)
- [x] 闭包(closure)
- [x] 变量绑定(bind)
- [ ] 块结构(block structure)
- [ ] 赋值(definition)
- [ ] 分支(branch)

进阶功能（将来也许会做）：
- [ ] 面向对象(oop)
- [ ] 类型推导(type inference)
- [ ] 模式匹配(pattern matching)
- [ ] 柯里化(currying)
- [ ] 惰性求值(lazy evaluation)

### 工具

最近一直在补 racket，所以这里选用 racket 作为我们的编程语言。编辑器我推荐 DrRacket 或 Emacs 中选一个。

DrRacket 自带了一个词法闭包的背景 shadow，非常醒目，对关键字的 hl 比较少。
如果你和我一样使用 racket 则 Emacs 需要另外自行配置，不装插件时 REPL 和 eval-buffer 都用不了。
如果另开一个终端，还不如前者的 interaction 方便。

![drrkt lexical hl][drrkt lexical hl] ![emcas hl][emacs hl]

你可以通过访问 <http://racket-lang.org/> 进行免费下载。

<br />

---

<br />

## R3 解释器

有人说得好，学习实现语言，最好是从最简单的语言和最简单的语法开始。先实现一个解释器，然后再慢慢往里面添加特性，这样才能有条不紊地制造出复杂语言。
为什么这个解释器要叫 R3 呢，你可能听说过 [R3RS][r3rs]。当然，这里做的这个玩具是远不如 R3RS。

[上一次][arithmetic]我实现了一个计算器，计算器也是一个解释器，只不过它只能用来解释算数表达式，我们只要在这个计算器上继续添加一些程序语言中的功能，就可以得到一个程序语言的解释器。

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
       (let ([funv (interp e1 env)]
             [vars (map (λ (x) (interp x env)) e2)])
         (apply funv vars))])))

;; 对解释器的封装
(define r3
  (λ (exp)
    (interp exp denv)))

{% endhighlight %}

<br />

### 环境

实际上，构造一个复杂的程序，也就是为了去一步步地创建出越来越复杂的计算性对象，为此需要创建所需要的名字，也就是对象关联。

我们将值与符号相关联，而后又能提取出这些值，这意味着解释器必须拥有某种存储能力，并且能够随时找到这些由 **symbol-value** 组成的序对，这种存储被称为**环境（environment）**。
在这里，我把一些基本的算术运算函数以序对的形式存进了默认的环境中，它看起来是这样的：

{% highlight racket %}

> (map cons
       (list '+ '- '* '/)
       (list  +  -  *  /))
'((+ . #<procedure:+>)
  (- . #<procedure:->)
  (* . #<procedure:*>) 
  (/ . #<procedure:/>))

{% endhighlight %}

`#<procedure:x>` 代表这是一个过程（函数），这种由序对组成的链表有一个术语叫作**关联表**。
racket 提供了一些用于从关联表中查找序对的[函数][ass stuff]，分别叫 `assoc` `assv` `assq` `assf`（ass 是 association 的简写）。
它们都具有相同的功能，只不过比较元素的方式不一样。

{% highlight racket %}

(let ([v (assq s env)])
  (cond
    [(not v) (error "unbound variable" )]
    [else (cdr v)]))

{% endhighlight %}

在 `lookup` 中，我使用了 `assq` 在关联表中进行寻值，如果传入的 symbol 是 `'+`，那 `v` 将会是 `(+ . #<procedure:+>)`，而它的 cdr 部分正是我们所需要的。
如果没有找到关联的值则会返回 `#f`，在这里我直接引发了一个异常（Raising Exceptions）。

关联表本质上也是一个链表，所以要拓展这个环境，直接 cons 这个序对就可以了。

由于链表具有**堆栈（stack）**的性质，所以我们在环境中寻值的时候只能找到环境中最迟被压栈的值，考虑到这种情况：

{% highlight racket %}

'((+ . 123)
  (+ . #<procedure:+>)
  (- . #<procedure:->)
  (* . #<procedure:*>) 
  (/ . #<procedure:/>))

{% endhighlight %}

之后再去用 `'+` 寻值则只会返回 `(+ . 123)`，这种现象称之为 **shadow**。shadow 现象非常常见，举个例子，
我们已经拥有一个变量 x 的值是 5，然后我们在 `let` 中又用 x 这个名字绑定了另外一个值 3，那在 `let` 的作用域中 x 的值只能是 3。关于这点在后面的闭包中另外细说。

{% highlight racket %}

> (define x 5)
> (let ([y 3])
    (+ x y))
8
> (let ([x 3])
    (+ x x))
6

{% endhighlight %}

<br />

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

如果表达式与这个 pat 相匹配的话则执行 body 语句块，`#:when` 是模式的卫语句。先来回顾一下上面那段模式匹配代码：

{% highlight racket %}

(match exp
  [(? symbol?)
   (lookup exp env)]
  [(? number?) exp]
  [(list e1 e2 ...)
   (let ([funv (interp e1 env)]
         [vars (map (λ (x) (interp x env)) e2)])
     (apply funv vars))])

{% endhighlight %}

其中用到了这么一个模式 `(? pred)`， pred 可以是任意谓词，比如 `symbol?` `number?` `procedure?`，这个模式表示将这个谓词应用于要匹配的表达式，并且结果为真。
它与以下条件表达式或卫语句写法是等价的：

{% highlight racket %}

(cond
  [(pred exp) ...])

(match exp
  [exp #:when (pred exp) ...])

{% endhighlight %}

此外我还用到了另外一种模式 `(list e1 e2 ...)`，如果匹配的话，exp 将是一个链表，e1 是链表的 car 部分，e2 是链表的 cdr 部分，e1 与 e2 只是我随意取的变量名。

e1 与 e2 之间可插入多个元素，比如我用 `(list e1 e2 e3 e4 ...)` 去匹配表达式 `'(1 2 3 4 5 6 7)`，
则 e1 的值对应了表达式的 car 部分 `1`，e2 是 cdr 部分的 car 部分也就是 `2`，e3 是 `3`，e4 是剩下的 cdr 部分 `'(4 5 6 7)`。

最后一个元素的后面没有 `...` 时，这个模式则只匹配指定元素数量的链表。

<br />

### 函数调用

最后一段代码是对语法树的遍历操作，此前必须匹配到一个至少有 1 个元素的链表，它的 car 部分是 e1，cdr 部分是 e2，那么来看一下对 e1 和 e2 的处理：

{% highlight racket %}

(let ([funv (interp e1 env)]
      [vars (map (λ (x) (interp x env)) e2)])
  (apply funv vars))

{% endhighlight %}

我们的语法 "S表达式" 规定了被括号包裹着的第一个元素必须是函数，实际上我们的表达式是由一堆符号与数字组合而成的链表。
比如表达式 `'(+ 1 2 3)` 就是由符号 `'+` 和数字 `1` `2` `3` 组成的链表。那么 e1 可能的值就被缩小到好几种：

1. e1 是一个符号，尝试从环境中获取关联的函数
2. e1 是一个可被调用的 λ 匿名函数
3. e1 仍是一个表达式，但在经过解释后最终会返回一个函数

现阶段只对第一种情况做了处理，后面会继续拓展这个解释器以处理更多的情况。

e2 是调用 e1 所需要的参数，其中的元素可以是任意数据类型，处理方式如 e1 炮制，每当需要处理新的数据类型只要拓展这个解释器即可。

这里用到了一个叫 `map` 的函数，它接受一个函数与一个链表，之后（多个）链表中的每个元素分别调用这个函数，并将各自调用返回的结果组合成一个新链表。
也就是将一（多）个链表通过一个函数**映射**为另一个链表。

<br />

### 用户界面

首先，我对解释器做了一个装箱的操作：

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

作用域与环境之间只隔了一条模糊的界限，作用域纯粹只是用来描述函数或者变量作用范围的抽象概念。
由于所有变量的值都被绑定到环境中，那么一个函数的所有可用变量便可以在轻易在相关联的环境中获取，你甚至可以通过环境来实现 IDE 中的代码提示/自动补全功能。

既然函数与环境都能作为一种数据来使用，为了能够方便去控制某种特定的数据类型以及保持其完整性（你也可以不这样做，那么下面就可以跳过了）。
racket 允许我们通过 [struct][struct] 表达式来创建一种新的结构，下面做一些简单的介绍。

<br />

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

;; 还可以通过一个谓词来判断某些数据是否为该种结构类型的实例
> (foo? my-foo)
#t

{% endhighlight %}

struct 的功能远不止如此，但对于 R3 而言已经够用了，现在将这种结构嵌入到我们的解释器中：

{% highlight racket %}

;; 一种叫 closure 的结构类型，包含了一个函数和与之相对应的环境
(struct closure (fun env))

;; 我用 id* 的形式命名表示这个函数或变量能够处理一组数据或者存储了一组数据（个人喜好）
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

你可能会对我在模式匹配中使用的一些符号感到迷惑，由于会涉及到 lisp 的符号系统，这里考虑到篇幅问题就简单说明一下：

{% highlight racket %}

(match exp
  [(list 'λ e1 e2 ...)
   ;; 'λ 是一个符号
   ;; e1 ⇒ (car exp)
   ;; e2 ⇒ (cdr exp)
   ...]
  [`(λ ,e1 ,e2 ...)
   ;; 这段模式与上面的模式完全是等价的
   ...])

{% endhighlight %}

想要匹配这个模式则 `exp` 是一个至少有三个元素的链表，且第一个元素必须是符号 `'λ`。

<br />

### 闭包

闭包说起来有点麻烦，这里可以简单理解为：每一个函数都有一一相对应的环境，使得各函数作用域的变量之间不会造成混淆，那么这种做法就可以称作**闭包（Closure）**。
我们已经将一个函数与一个环境通过 `struct` 联系到一起籍此实现闭包。

>
闭包的方式也有多种，其中较为常人所知晓的两种 Dynamic Closure 以及 Lexical Closure。
两种闭包各有其特色，其中的恩怨情仇我也不特意去考古了。

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

函数与类、对象之间的界限是不是变得越来越模糊了，说到这里我还想聊一下 javascript。

如今大多数语言都已经把函数当成头等公民，也就是允许把函数当作参数传递、作为值返回，甚至还有函数类型，名副其实的是一种非常具体的数据。
但在 javascript 中，函数同时也是一个**对象**，直接来看下代码。

{% highlight javascript %}

function p(name) {
    this.name = name;
    this.getName = function() {
        this.prefix = 'Name is ';
        return this.prefix + this.name;
    }
}

p                          // ⇒ [Function: p]
p1 = new p('ldc')          // ⇒ p { name: 'ldc', getName: [Function] }
p1.getName()               // ⇒ 'Name is ldc'
p1g = new p1.getName()     // ⇒ { prefix: 'Name is ' }
p1g.suffix = 'end...'; p1g // ⇒ { prefix: 'Name is ', suffix: 'end...' }

{% endhighlight %}

发生了什么事情？？？

javascript 的函数通过 `new` 操作后变成了一个具有 closure 性质的数据，并且你可以通过 `.` 对这个数据的 env 进行访问（读取修改）。

对比一下 java 中获取闭包内数据的方法，再对比一下 scheme 中获取闭包内数据的方法。

java 需要事先把结构给声明好（它拥有哪些成员、方法）。scheme 中获取数据的方法非常不方便，使用时也非常不方便。
javascript 看起来就像是结合了两者的优点一样，并且还可以随时给自己的 env 添加关联数据（随时随地添加成员或者方法）。

<br />

### 变量绑定

这里要实现的是也就是将一个 id（symbol）与一个具体的值相关联并储存到 env 的操作。

我们先回顾一下上面那段代码，首先是对匿名函数 `λ` 的解释：
 
{% highlight racket %}

(struct closure (fun env))

...
  ...  
    ...
      ...
      [`(λ ,a ,e ...)
       (closure exp env)]
      ...

{% endhighlight %}

一般来说，当一个匿名函数出现在程序语言中的时候，编译器很难去断言现在的这个匿名函数最终会被赋值还是会立即调用。
而由于匿名函数本身就能够当作一种数据进行储存，所以这里要做的事情非常简单：直接返回一个 `closure` 即可。

除此以外我还故意把 lambda 的匹配写在了 invoke 的匹配上面，使得对 `f` 进一步解释的时候有可能会返回一个 `closure` 而不是直接对 `f` 本身进行匹配，
是因为我想把 Data 和 Invoke 这两种截然不同的操作从代码层面进行剥离。

接下来还有对函数的解释：

{% highlight racket %}

(struct closure (fun env))

...
  ...  
    ...
      ...
      [`(,f ,a* ...)
       (let ([funv (interp f env)]
             [arg* (map (λ (a) (interp a env)) a*)])
         (match funv
           [(? procedure?)
            (apply funv arg*)]
           [(closure `(λ ,a* ,e*) env-save)
            (interp e* (ext-env* a* arg* env-save))]
           [_ (error "badmatch" funv)]))]...

{% endhighlight %}

在之前，我们的环境（也就是默认环境 denv）中只存了一些基本的算术运算符，也就是肯定可以被 `apply` 的函数。
但现在多了一种叫作匿名函数的数据，也就是上面定义的结构类型 `closure`。

首先，在 r3 的语法中，`a*` 必须是一个列表，它代表了函数被定义时的形参，也就是函数被 invoke 时被绑定到函数体（环境）的变量。
而 `e*` 是匿名函数的实体（body），它可以是任意符合语法的表达式。

接下来就是闭包的精髓所在了，在大多数情况下，一个函数在定义和调用时的作用域往往都由两个差别各异的环境所决定，举个例子：

{% highlight racket %}

(define getλ
  (let ([a 1])
    (λ (b) (+ a b))))

(define f (getλ))

(f 3)

{% endhighlight %}

不难看出 `f` 是一个这样的函数 `(λ (b) (+ a b))`，如果忽略 `getλ` 中的上下文并且这时不存在一个全局变量 `a`，显然程序到这里是无法运行下去的。
更极端一点，`f` 定义的地方也许隔着几百行代码，甚至在另外一个文件里面。而且调用函数的人凭什么应该知道，`f` 的定义里面有一个自由变量，它的名字叫做 `a`？
所以这里在给到匿名函数生成闭包时，同时还将程序运行到这一步时的环境 `env-save` 给绑定到一起，这种操作被叫做词法闭包（lexical closure）。

形参有了，实参有了，那么只要将其双双绑定再扩展到 `env-save` 就可以进行调用了。

<!--
<br />

---

<br />

## 块结构
-->

<br />

---

<br />

## 未完待续。。。

{% highlight racket %}

;;; structure
(struct closure (fun env))

;;; env
(define denv
  (make-hash
   (map cons
        '(+   -  *  /)
        `(,+ ,- ,* ,/))))

(define lookup
  (λ (a env)
    (let ([v? (hash-ref env a #f)])
      (cond
        [(not v?) (error (format "unbound variable"))]
        [else v?]))))

(define ext-env
  (λ (a v env)
    (hash-set! env a v)
    env))

(define ext-env*
  (λ (a* v* env*)
    (for ([a a*]
          [v v*])
      (hash-set! env* a v))
    env*))

;;; main code
(define interp
  (λ (exp env)
    (match exp
      [(? number?) exp]
      [(? symbol?)
       (lookup exp env)]
      [`(begin ,e* ... ,en)
       (for ([e e*]) (interp e env))
       (interp en env)]
      [`(define ,a ,e)
       (ext-env a (interp e env) env)]
      [`(λ ,a ,e ...)
       (closure `(λ ,a (begin ,@e)) env)]
      [`(,f ,a* ...)
       (let ([funv (interp f env)]
             [arg* (map (λ (a) (interp a env)) a*)])
         (match funv
           [(? procedure?)
            (apply funv arg*)]
           [(closure `(λ ,a* ,e*) env-save)
            (interp e* (ext-env* a* arg* env-save))]
           [_ (error "badmatch" funv)]))])))

;; user interface
(define r3
  (λ (exp)
    (interp exp denv)))

(define repl 
  (λ ()
    (display "REPL ⇒ ")
    (let ([exp (read)])
      (cond
        [(equal? exp '(exit)) "exit"]
        [else
         (printf "REPL ⇒ ~a~n" (r3 exp))
         (repl)]))))

(r3
 '(begin
    (define c 20)
    (define a 10)
    (+ a c)))

{% endhighlight %}

有缘再见。。。

{% assign img_url = "/images/r3-interpreter/" %}

[drrkt lexical hl]: {{img_url}}/drrkt-lexical-hl.png
[emacs hl]: {{img_url}}/emacs-hl.png

[arithmetic]: /interpreter/2018/02/04/basic-arithmetic.html
[r3rs]: https://practical-scheme.net/wiliki/schemexref.cgi?R3RS
[ass stuff]: https://docs.racket-lang.org/reference/pairs.html?q=assoc#%28def._%28%28lib._racket%2Fprivate%2Flist..rkt%29._assoc%29%29
[match]: https://docs.racket-lang.org/reference/match.html?q=ass
[struct]: https://docs.racket-lang.org/reference/define-struct.html
