---
layout: post
title: 旋转一个矩阵
category: java
date: 2016-11-06
---

在做流（Stream）练习时的偶然产物，其实就是一些集合操作，非常有意思。

假设我们有数个 list ，其中一个 list 包括了所有人的名字，其他的 list 包括了所有人的 kudosu ，然后我们把这几个 list 组合成一个矩阵，它类似于一个二维数组，也就是二维列表，就像这样子：

{% highlight java %}

static List<List<String>> kudosuMatrix() {
    return Arrays.asList(
            Arrays.asList("Andrea", "Leorda", "Garven", "Bakari", "wmfchris", "Sonnyc", "James2250", "CDFA", "Mafiamaster", "Breeze"),
            Arrays.asList("2303", "2124", "1931", "1862", "1777", "1319", "1236", "1136", "1122", "1021"),
            Arrays.asList("35", "1665", "1140", "21", "1139", "115", "1135", "16", "48", "382"),
            Arrays.asList("2268", "459", "791", "1841", "638", "1204", "101", "1120", "1074", "639"));
}

{% endhighlight %}

在这个例子里如果我要增加一个 country 列非常简单， `add(countryList)` 即可：

{% highlight java %}

...
    List<List<String>> matrix = kudosuMatrix();
    matrix.add(countryList);
...

{% endhighlight %}

当我要插入一整行的数据，比如在 Andrea 所在行的后面加上一个名字叫 "Hollow Wings"，kudosu 分别是 518, 29, 396 的人，这意味着我找到所在行数以后需要分别在四个 list 中的某一行插入相应的值，这小小的 List 仿佛是在嘲笑我。

表中的每一列存放的数据变成了个人信息。这样我们从插入行变成了插入列，直接找到了 Andrea 所在列后即可插入一个 Hollow Wings 信息的 list。

实现的思路是这样的，首先得到 Names 列成员数 n ，创建 n 个 kudosuInfo ，kudosuInfo 也是一个 List ，里面的值除了成员 Name 本身还有他的各项kudosu 指标。

Java 实现：

{% highlight java %}

static List<List<String>> rotate(List<List<String>> matrix) {
    return getNumbersFofInfoList()
            .stream()
            .map(i -> matrix
                    .stream()
                    .map(list -> list.get(i))
                    .collect(Collectors.toList()))
                    .collect(Collectors.toList());
}

{% endhighlight %}

测试一下：

{% highlight java %}

...
    List<List<String>> matrix = rotate(kudosuMatrix());
    matrix.forEach(System.out::println);
    matrix.add(Arrays.asList(
                "Hollow Wings",
                "518",
                "29",
                "396"
    ));
    lists.forEach(System.out::println);
...

{% endhighlight %}

既然能够将表格进行旋转插入一整条 info ，当然也可以再旋转一次，然后插入 sex, country 列之类的。
