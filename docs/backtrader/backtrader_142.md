# Python 的隐藏力量（2）

> 原文：[`www.backtrader.com/blog/posts/2016-11-23-hidden-powers-2/hidden-powers/`](https://www.backtrader.com/blog/posts/2016-11-23-hidden-powers-2/hidden-powers/)

让我们更深入地探讨 *Python 的隐藏力量* 如何在 *backtrader* 中使用，以及如何实现这一点以尝试达到主要目标：*易用性*

## 这些定义是什么？

例如一个指标：

```py
`import backtrader as bt

class MyIndicator(bt.Indicator):

    lines = ('myline',)
    params = (('period', 20),)

    ...` 
```

任何能够阅读 Python 的人都会说：

+   `lines` 是一个 `tuple`，实际上包含一个字符串项目

+   `params` 也是一个 `tuple`，包含另一个包含 2 个项目的 `tuple`

## 但是后来

扩展示例：

```py
`import backtrader as bt

class MyIndicator(bt.Indicator):

    lines = ('myline',)
    params = (('period', 20),)

    def __init__(self):

        self.lines.myline = (self.data.high - self.data.low) / self.p.period` 
```

对于任何人来说，这里应该很明显：

+   在*类*中的 `lines` 的定义已经转换为可以通过 `self.lines` 访问的属性，并且依次包含定义中指定的 `myline` 属性

而且

+   在*类*中的 `params` 的定义已经转换为可以通过 `self.p`（或 `self.params`）访问的属性，并依次包含定义中指定的 `period` 属性

    而 `self.p.period` 似乎有一个值，因为它直接在算术运算中使用（显然该值是定义中的值：`20`）

## 答案：元类

`bt.Indicator` 和因此也有 `MyIndicator` 的 *元类*，这允许应用 *元编程* 概念。

在这种情况下，*拦截对 `lines` 和 `params` 定义的*方式是这样的：

+   *实例* 的属性，即：可通过 `self.lines` 和 `self.params` 访问

+   *类* 的属性

+   包含其中定义的 `属性`（和已定义的值）

## 部分的秘密

对于那些不熟悉*元类*的人，它或多或少是这样实现的：

```py
`class MyMetaClass(type):

    def __new__(meta, name, bases, dct):
        ...

        lines = dct.pop('lines', ())
        params = dct.pop('params', ())

        # Some processing of lines and params ... takes place here

        ...

        dct['lines'] = MyLinesClass(info_from_lines)
        dct['params'] = MyParamsClass(info_from_params)

        ...` 
```

这里拦截了*类*的创建，并用从定义中提取的信息替换了 `lines` 和 `params` 的定义。

这单独是不够的，所以还拦截了实例的创建。 使用 Pyton 3.x 语法：

```py
`class MyClass(Parent, metaclass=MyMetaClass):

    def __new__(cls, *args, **kwargs):

        obj = super(MyClass, cls).__new__(cls, *args, **kwargs)
        obj.lines = cls.lines()
        obj.params = cls.params()

        return obj` 
```

在这里，*上面定义的* `MyLinesClass` 和 `MyParamsClass` 的实例已经被放置到 `MyClass` 的实例中。

不，没有冲突：

+   *类* 可以说是：“系统范围内”，并包含其自己的 `lines` 和 `params` 的属性，它们是类

+   *实例* 可以说是：“系统本地”，每个实例都包含 `lines` 和 `params` 的实例（每次都不同）

通常，人们会使用 `self.lines` 访问实例，但也可以使用 `MyClass.lines` 访问类。

后者给用户访问方法的权限，这些方法并不是为了一般使用，但这是 Python，没有什么可以禁止的，甚至更不用说 *开源* 了

## 结论

元类在幕后工作，提供了一种机制，通过处理诸如 `lines` 和 `params` 的 `tuple` 定义之类的东西，从而使几乎成为一种元语言

目标是让任何使用该平台的人的生活更加轻松
