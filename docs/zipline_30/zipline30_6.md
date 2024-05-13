# 开发

> 原文：[`zipline.ml4trading.io/development-guidelines.html`](https://zipline.ml4trading.io/development-guidelines.html)

本页面旨在为 Zipline 的开发者、希望为 Zipline 代码库或文档做出贡献的人，或希望从源代码安装并对其 Zipline 副本进行本地更改的人提供指导。

我们欢迎所有贡献，包括错误报告、错误修复、文档改进、增强功能和想法。我们在[GitHub](https://github.com/)上[跟踪问题](https://github.com/stefan-jansen/zipline-reloaded/issues)，并且还有一个[邮件列表](https://exchange.ml4trading.io/)，您可以在那里提问。

## 创建开发环境

首先，您需要通过运行以下命令克隆 Zipline：

```py
$  git  clone  git@github.com:stefan-jansen/zipline-reloaded.git 
```

然后检出到一个新分支，您可以在那里进行更改：

```py
$  cd  zipline-reloaded
$  git  checkout  -b  some-short-descriptive-name 
```

如果您还没有这些依赖，您将需要一些 C 库依赖。您可以按照安装指南获取适当的依赖。

一旦您创建并激活了一个[虚拟环境](https://docs.python.org/3/library/venv.html)

```py
$  python3  -m  venv  venv
$  source  venv/bin/activate 
```

或者，使用[virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/)：

```py
$  mkvirtualenv  zipline 
```

运行`pip install -e .[test]`以安装：

安装后，您应该能够从虚拟环境中使用`zipline`命令行界面：

```py
$  zipline  --help 
```

最后，确保测试通过。

在开发过程中，您可以通过运行以下命令重新构建 C 扩展：

```py
$  ./rebuid-cython.sh 
```

## 风格指南与运行测试

我们使用[flake8](https://flake8.pycqa.org/en/latest/)来检查风格要求，使用[black](https://black.readthedocs.io/en/stable/)进行代码格式化，并使用[pytest](https://docs.pytest.org/en/latest/)来运行 Zipline 测试。我们的[持续集成](https://en.wikipedia.org/wiki/Continuous_integration)工具将运行这些命令。

在提交补丁或拉取请求之前，请确保您的更改在运行以下命令时通过：

```py
$  flake8  src/zipline  tests 
```

为了在本地运行测试，您需要[TA-lib](https://mrjbq7.github.io/ta-lib/install.html)，您可以通过运行以下命令在 Linux 上安装：

```py
$  wget  http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz
$  tar  -xvzf  ta-lib-0.4.0-src.tar.gz
$  cd  ta-lib/
$  ./configure  --prefix=/usr
$  make
$  sudo  make  install 
```

对于 OS X 上的`TA-lib`，您只需运行：

```py
$  brew  install  ta-lib 
```

然后运行`pip install` TA-lib：

您现在应该可以自由运行测试：

```py
$  pytest  tests 
```

## 持续集成

[TODO]

## 打包

[TODO]

## 为文档做出贡献

如果您想为 zipline.io 上的文档做出贡献，您可以导航到`docs/source/`，其中每个[reStructuredText](https://en.wikipedia.org/wiki/ReStructuredText)（`.rst`）文件都是一个单独的部分。要添加一个部分，创建一个名为`some-descriptive-name.rst`的新文件，并将`some-descriptive-name`添加到`appendix.rst`中。要编辑一个部分，只需打开现有的文件，进行更改，然后保存。

我们使用[Sphinx](https://www.sphinx-doc.org/en/master/)为 Zipline 生成文档，您需要通过运行以下命令来安装：

如果您想使用 Anaconda，请按照安装指南创建并激活一个环境，然后运行上述命令。

要构建并在本地查看文档，请运行：

```py
# assuming you're in the Zipline root directory
$  cd  docs
$  make  html
$  {BROWSER}  build/html/index.html 
```

## 提交消息

标准的前缀来开始一个提交消息：

```py
BLD: change related to building Zipline
BUG: bug fix
DEP: deprecate something, or remove a deprecated object
DEV: development tool or utility
DOC: documentation
ENH: enhancement
MAINT: maintenance commit (refactoring, typos, etc)
REV: revert an earlier commit
STY: style fix (whitespace, PEP8, flake8, etc)
TST: addition or modification of tests
REL: related to releasing Zipline
PERF: performance enhancements 
```

一些提交样式指南：

提交行不应超过[72 个字符](https://git-scm.com/book/en/v2/Distributed-Git-Contributing-to-a-Project)。提交的第一行应包含上述前缀之一。提交主题和提交正文之间应有空行。通常，消息应以祈使语气编写。最佳实践是不仅包括更改的内容，还包括更改的原因。

**示例：**

```py
MAINT: Remove unused calculations of max_leverage, et al.

In the performance period the max_leverage, max_capital_used,
cumulative_capital_used were calculated but not used.

At least one of those calculations, max_leverage, was causing a
divide by zero error.

Instead of papering over that error, the entire calculation was
a bit suspect so removing, with possibility of adding it back in
later with handling the case (or raising appropriate errors) when
the algorithm has little cash on hand. 
```

## 格式化文档字符串

在为类、函数等添加或编辑文档字符串时，我们使用[numpy](https://github.com/numpy/numpy/blob/master/doc/HOWTO_DOCUMENT.rst.txt)作为权威参考。

## 更新 Whatsnew

我们有一套[Whatsnew](https://github.com/stefan-jansen/zipline-reloaded/tree/main/docs/source/whatsnew)文件，用于记录 Zipline 不同版本之间发生的更改。一旦你对 Zipline 进行了更改，在你的拉取请求中，请更新最近的`Whatsnew`文件，并添加一条关于你所做更改的评论。你可以在之前的`Whatsnew`文件中找到示例。

## 创建开发环境

首先，你需要通过运行以下命令来克隆 Zipline：

```py
$  git  clone  git@github.com:stefan-jansen/zipline-reloaded.git 
```

然后切换到一个新分支，你可以在那里进行更改：

```py
$  cd  zipline-reloaded
$  git  checkout  -b  some-short-descriptive-name 
```

如果你还没有它们，你需要一些 C 库依赖项。你可以按照安装指南来获取适当的依赖项。

一旦你创建并激活了一个[虚拟环境](https://docs.python.org/3/library/venv.html)

```py
$  python3  -m  venv  venv
$  source  venv/bin/activate 
```

或者，使用[virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/)：

```py
$  mkvirtualenv  zipline 
```

运行`pip install -e .[test]`来安装：

安装完成后，你应该能够从虚拟环境中使用`zipline`命令行界面：

```py
$  zipline  --help 
```

最后，确保测试通过。

在开发过程中，你可以通过运行以下命令来重建 C 扩展：

```py
$  ./rebuid-cython.sh 
```

## 样式指南与运行测试

我们使用[flake8](https://flake8.pycqa.org/en/latest/)来检查样式要求，使用[black](https://black.readthedocs.io/en/stable/)来格式化代码，并使用[pytest](https://docs.pytest.org/en/latest/)来运行 Zipline 测试。我们的[持续集成](https://en.wikipedia.org/wiki/Continuous_integration)工具将执行这些命令。

在提交补丁或拉取请求之前，请确保你的更改在运行时通过：

```py
$  flake8  src/zipline  tests 
```

要在本地运行测试，你需要[TA-lib](https://mrjbq7.github.io/ta-lib/install.html)，在 Linux 上你可以通过运行以下命令来安装：

```py
$  wget  http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz
$  tar  -xvzf  ta-lib-0.4.0-src.tar.gz
$  cd  ta-lib/
$  ./configure  --prefix=/usr
$  make
$  sudo  make  install 
```

对于 OS X 上的`TA-lib`，你只需运行：

```py
$  brew  install  ta-lib 
```

然后运行`pip install` TA-lib：

你现在应该可以自由运行测试了：

```py
$  pytest  tests 
```

## 持续集成

[TODO]

## 打包

[TODO]

## 贡献文档

如果你想为 zipline.io 上的文档做出贡献，你可以导航到`docs/source/`，其中每个[reStructuredText](https://en.wikipedia.org/wiki/ReStructuredText)（`.rst`）文件都是一个单独的部分。要添加一个部分，创建一个名为`some-descriptive-name.rst`的新文件，并将`some-descriptive-name`添加到`appendix.rst`中。要编辑一个部分，只需打开现有的文件，进行更改，然后保存。

我们使用[Sphinx](https://www.sphinx-doc.org/en/master/)来为 Zipline 生成文档，你需要通过运行以下命令来安装它：

如果你想要使用 Anaconda，请按照安装指南创建并激活一个环境，然后运行上述命令。

要在本地构建并查看文档，运行：

```py
# assuming you're in the Zipline root directory
$  cd  docs
$  make  html
$  {BROWSER}  build/html/index.html 
```

## 提交消息

开始提交消息的标准前缀：

```py
BLD: change related to building Zipline
BUG: bug fix
DEP: deprecate something, or remove a deprecated object
DEV: development tool or utility
DOC: documentation
ENH: enhancement
MAINT: maintenance commit (refactoring, typos, etc)
REV: revert an earlier commit
STY: style fix (whitespace, PEP8, flake8, etc)
TST: addition or modification of tests
REL: related to releasing Zipline
PERF: performance enhancements 
```

一些提交样式指南：

提交行的长度不应超过[72 个字符](https://git-scm.com/book/en/v2/Distributed-Git-Contributing-to-a-Project)。提交的第一行应包含上述前缀之一。提交主题和提交正文之间应有空行。通常，消息应以祈使语气编写。最佳实践是不仅包括更改的内容，还应包括更改的原因。

**示例：**

```py
MAINT: Remove unused calculations of max_leverage, et al.

In the performance period the max_leverage, max_capital_used,
cumulative_capital_used were calculated but not used.

At least one of those calculations, max_leverage, was causing a
divide by zero error.

Instead of papering over that error, the entire calculation was
a bit suspect so removing, with possibility of adding it back in
later with handling the case (or raising appropriate errors) when
the algorithm has little cash on hand. 
```

## 文档字符串格式化

在为类、函数等添加或编辑文档字符串时，我们使用[numpy](https://github.com/numpy/numpy/blob/master/doc/HOWTO_DOCUMENT.rst.txt)作为权威参考。

## 更新 Whatsnew

我们有一套[whatsnew](https://github.com/stefan-jansen/zipline-reloaded/tree/main/docs/source/whatsnew)文件，用于记录 Zipline 不同版本之间的变化。一旦你对 Zipline 进行了更改，在你的拉取请求中，请更新最近的`whatsnew`文件，并添加关于你所做更改的评论。你可以在之前的`whatsnew`文件中找到示例。
