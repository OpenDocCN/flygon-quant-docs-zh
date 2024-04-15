# 安装

> 原文：[`www.backtrader.com/docu/installation/`](https://www.backtrader.com/docu/installation/)

## 要求和版本

`backtrader` 是自包含的，没有外部依赖项（除非你想要绘图）

基本要求是：

+   Python 2.7

+   Python 3.2 / 3.3 / 3.4 / 3.5

+   pypy/pypy3

若需要绘图功能，还需要额外的要求：

+   Matplotlib >= 1.4.1

    其他版本可能也可以，但这是用于开发的版本

**注意**：在撰写本文时，Matplotlib 不支持 *pypy/pypy3*

### Python 2.x/3.x 兼容

开发工作在 Python 2.7 下进行，有时也在 3.4 下进行。本地同时运行两个版本的测试。

在 Travis 下，使用连续集成检查与 3.2 / 3.3 / 3.5 以及 pypy/pyp3 的兼容性

## 从 pypi 安装

例如使用 pip：

```py
pip install backtrader
```

使用相同语法也可以应用 *easy_install*

## 从 pypi 安装（包括 *matplotlib*）

若需要绘图功能，请使用此选项：

```py
pip install backtrader[plotting]
```

这会引入 matplotlib，它将进一步引入其他依赖项。

你可能更喜欢（或只能使用...）`easy_install`

## 从源码安装

首先从 github 网站下载一个发布版或最新的压缩包：

+   [`github.com/mementum/backtrader`](https://github.com/mementum/backtrader)

解压后运行以下命令：

```py
python setup.py install
```

## 从源码在你的项目中运行

从 github 网站下载一个发布版或最新的压缩包：

+   [`github.com/mementum/backtrader`](https://github.com/mementum/backtrader)

然后将 *backtrader* 包目录复制到你自己的项目中。例如，在类 Unix 操作系统下：

```py
tar xzf backtrader.tgz
cd backtrader
cp -r backtrader project_directory
```

请记住，你随后需要手动安装 `matplotlib` 以进行绘图。
