---
layout: post
title: 日本语初学心得
category: LANG
date: 2019-01-23
---

某天lashi的时候收到一封来自 Play Store 的神秘邮件，说送了我一张『5$ credit for any ebook over 5$』的礼品卡，然后我就用几乎白嫖的价格买了一本日本语的入门教材，于是我就从持续摸鱼的状态转换到了一边看狗摸鱼一边学日本语的状态。学了也有一段时间了，所以想要分享一些学习心得作为阶段性的总结。

万事入门难，尤其是语言的学习，日本语对于初学者而言也实在算不上友好，毕竟还是个刚进门就要你去熟悉几乎 100 个
<ruby>符号<rt>$$letter \in symbol$$</rt></ruby>
的语言，可不是开玩笑的。另一方面，日语作为一门黏着语，尽管规则繁琐，但学习难度会随着你不断深入而降低，学习的效率正如一个指数函数的执行过程。
$$
\newcommand{romaji}[1]{\mathrm{#1}}
$$

<br />

---

<br />


## 日本语书写系统

**日本语书写系统**是指使用文字来记载或写作等的系统方法。现代日语书写系统由几种文字构成：

- ### <ruby>漢字<rt>かんじ</rt>（$$\romaji{Ka\bar{n}ji}$$）</ruby>

	日本语的汉字多用在文字的语干上，并且常常与平假名搭配进行使用，这是标准的和最常用的书写日本语的方式。
	
	华人学习日本语的其中一个优势就是，本该是最难记忆的汉字，对共存于汉字文化圈中心的我们来说，却成为了相对简单的其中一环。

- ### <ruby>平仮名<rt>ひらがな</rt>（$$\romaji{Hiragana}$$）</ruby>
	
	平假名是一种**音节文字**，常常与汉字搭配用在动词结尾、介词等位置。
	
	抛开发音部分不谈，我个人觉得平假名更像是英语的拉丁字母或者汉语的笔画一样的存在，是整套文字系统中的原子部分。
	
	说起来我（默）读假名的时候有一个坏习惯，就是在看到一个假名的时候总是要先想象一下它的罗马音，这应该算是不熟练的表现吧。如果假名和拉丁字母是位于同格的存在，那么我们在看到一个假名时浮现出来的应该是这个符号所代表的声音或者是一张嘴的形状才对。
	
	我想表达的是，比如 $$あ$$ 这个符号它就是读 $$あ$$，而不是读 $$\romaji{A}$$，$$しょ$$ 就是读 $$しょ$$ 而不是 $$\romaji{SHO}$$ 或者 $$\romaji{SYO}$$。。。
	
	使用罗马音去注释平假名，这种用一套发音系统去解释另一套发音系统的方法，就像早期的 Lisp 编译器，仅仅只是把 Lisp 编译成 C，再调用 C 语言的编译器编译成汇编，是效率低下的且存在误差的。
	
- ### <ruby>片仮名<rt>カタカナ</rt></ruby>（$$\romaji{Katakana}$$）：

	片假名是另一种**音节文字**，主要用于将外来词转换成日语音节。

- ### ロウマじ（$$\romaji{R\bar{o}maji}$$）

	日本语的罗马字是一种使用拉丁字母转写为日语的发音进行标注的拼音方式，转写规则有不少变种，本文采用了一种传统的平文式的规则。
	
	关于日本语的罗马字已有不少文章作过详细的介绍，这里就不再过多阐述。

## 自然语言与计算机语言

对于计算机而言，当我们尝试使用某种编程语言去表达某种想法的时候，**我们应该特别注意语言本身提供的将简单的想法组合形成更复杂的想法的手段**。而任何强大的语言都应该具有三种机制去达成这项目的：

- 基本元素：代表与语言所关注的最简单的实体。
- 组合的方式：将基本元素构建成复合元素的手段。
- 抽象的方法：将复合对象进行命名和操纵的方法。

这里的基本元素对应了自然语言中的**『音素』**。

而 Chromsky 则倾向于将语言看作是一个（有限的或无限的）的句子集，每个句子的长度都是有限的，并且是由有限的音素集构成。所有语言或书面形式的自然语言都是这种意义上的语言，因为每种自然语言都有有限数量的音素，尽管有无限多的句子，但每个句子都可以表现为这些音素的有限序列。类似地，“句子”的集合在一些形式化的数学系统中可以也被认为是一种语言。

虽然自然语言与计算机语言有所区别，但我认为这三种机制对于自然语言同样是说的通的。

回到正题，对日本语来说最基本的元素当属五个元音素『あ、い、う、え、お』了，然后这五个元音作为五个段又与其它辅音共同构成了我们现在使用的『五十音』、『浊音』、『拗音』等等。关键在于，这些子音由母音演变过来后，我可以把它们都当作基本元素来使用。

紧接着我们用一个或多个音素组合成了复合音素，最后我们将这个复合音素抽象成具体的概念，赋予了它实际意义。

举个实际的例子，比如说**『<ruby>私<rt>わたし</rt>（$$\romaji{Watashi}$$）</ruby>』**这个词组，它是由三个音素『わ、た、し』组成，是一个代词，表达的是**“我”**。事实上，如果打破砂锅问到底的话，『私』不是我，它只是一个符号，只是这个符号被用来代表柏拉图概念下的“我”。我们把『わ、た、し』这三个音素按顺序组合成『わたし』，为了避免这个『わたし』和代表了其它概念但也是读『わたし』的复合音素相混淆，又把这个『わたし』写成了『私』。

但更重要的则是我现在可以自由地使用『私』，让『私』与其它音素组合，或者把它当作一个基本元素用在更复杂的组合中，一个句子就是这么组成的。当然一个合乎文法的句子还是需要按照既定的语法规则进行组合，一个不合文法的句子一般都会使人感到浑惑。

> 说个题外话，我认为对黏着语进行 NLP 的研究比起其它语言具有一定的优势。

<br />

---

<br />

## 句法的结构

todo

<ruby><rt></rt></ruby>