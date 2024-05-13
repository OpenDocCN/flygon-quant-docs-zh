# 安装

> 原文：[`zipline.ml4trading.io/install.html`](https://zipline.ml4trading.io/install.html)

您可以使用[pip](https://pip.pypa.io/en/stable/)，Python 包安装程序，或[conda](https://docs.conda.io/projects/conda/en/latest/index.html)，跨 Windows、macOS 和 Linux 的包和环境管理系统来安装 Zipline。如果您在安装 zipline-reloaded 的同时安装其他包并遇到[冲突错误](https://github.com/conda/conda/issues/9707)，请考虑使用[mamba](https://github.com/mamba-org/mamba)代替。

Zipline 支持 Python 3.8、3.9、3.10 和 3.11。要安装和并行使用不同版本的 Python 以及创建虚拟环境，您可能希望使用[pyenv](https://github.com/pyenv/pyenv)。

## 使用`pip`安装

通过`pip`安装 Zipline 比一般的 Python 包稍微复杂一些。

增加复杂性的原因有两个：

1.  Zipline 提供了几个需要访问 CPython C API 的 C 扩展。为了构建这些 C 扩展，`pip`需要访问您的 Python 安装的 CPython 头文件。

1.  Zipline 依赖于[NumPy](https://www.numpy.org/)，这是 Python 中用于数值数组计算的核心库。而 NumPy 又依赖于[LAPACK](https://www.netlib.org/lapack)线性代数例程。

由于 LAPACK 和 CPython 头文件是非 Python 依赖项，因此安装它们的方法因平台而异。如果您更愿意使用单一工具来安装 Python 和非 Python 依赖项，或者如果您已经在使用[Anaconda](https://www.anaconda.com/distribution/)作为您的 Python 发行版，您可以直接跳到:ref: conda 部分。

一旦您安装了必要的额外依赖项（请参阅下面针对您特定平台的说明），您应该能够简单地运行（最好在激活的虚拟环境中）：

```py
$  pip  install  zipline-reloaded 
```

如果您使用 Python 进行除 Zipline 之外的任何操作，我们**强烈**建议您在[virtualenv](https://virtualenv.readthedocs.org/en/latest/)中安装。《Python 编程指南》提供了一个[关于 virtualenv 的优秀教程](https://docs.python-guide.org/en/latest/dev/virtualenvs/)。

### GNU/Linux

#### 依赖项

在[Debian 衍生](https://www.debian.org/derivatives/)的 Linux 发行版上，您可以通过运行以下命令从`apt`获取所有必要的二进制依赖项：

```py
$  sudo  apt  install  libatlas-base-dev  python-dev  gfortran  pkg-config  libfreetype6-dev  hdf5-tools 
```

在最近的[RHEL 衍生](https://en.wikipedia.org/wiki/Red_Hat_Enterprise_Linux_derivatives)Linux 发行版（例如 Fedora）上，以下步骤应该足以获取必要的额外依赖项：

```py
$  sudo  dnf  install  atlas-devel  gcc-c++  gcc-gfortran  libgfortran  python-devel  redhat-rpm-config  hdf5 
```

在[Arch Linux](https://www.archlinux.org/)上，您可以通过`pacman`获取额外的依赖项：

```py
$  pacman  -S  lapack  gcc  gcc-fortran  pkg-config  hdf5 
```

还有适用于安装[ta-lib](https://aur.archlinux.org/packages/ta-lib/)的 AUR 包。Python 3 也可以通过以下方式安装：

```py
$  pacman  -S  python3 
```

#### 编译 TA-Lib

你还需要编译[TA-Lib](https://www.ta-lib.org/)库以进行技术分析，以便其头文件可用。

你可以按照以下步骤进行操作：

```py
$  wget  http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz
$  tar  -xzf  ta-lib-0.4.0-src.tar.gz
$  cd  ta-lib/
$  sudo  ./configure
$  sudo  make
$  sudo  make  install 
```

这将允许你使用`pip`安装 Python 包装器，正如二进制轮子所预期的那样。

### macOS

macOS 随附的 Python 版本通常已过时，并且由于操作系统直接使用它而存在一些怪癖。出于这些原因，许多开发人员选择安装并使用单独的 Python 安装。

[Python 搭车指南](https://docs.python-guide.org/en/latest/)提供了关于[在 macOS 上安装 Python](https://docs.python-guide.org/en/latest/)的优秀指南，该指南解释了如何使用[Homebrew](https://brew.sh/)管理器安装 Python。或者，你也可以使用[pyenv](https://github.com/pyenv/pyenv)。

假设你已经使用`brew`安装了 Python，那么你可能还需要以下包：

```py
$  brew  install  freetype  pkg-config  gcc  openssl  hdf5  ta-lib 
```

### Windows

对于 Windows，安装 Zipline 最简单且最支持的方法是使用`conda`。

## 使用`conda`安装

另一种安装 Zipline 的方法是通过`conda`包管理器，它是[Anaconda](https://www.anaconda.com/distribution/)发行版的一部分。或者，你可以使用相关但更轻量级的[Miniconda](https://docs.conda.io/en/latest/miniconda.html#)或[Miniforge](https://github.com/conda-forge/miniforge)安装程序。

使用 Conda 而不是`pip`的主要优点是，`conda`原生理解像`numpy`和`scipy`这样的包的复杂二进制依赖关系。这意味着`conda`可以安装 Zipline 及其依赖项，而不需要使用第二个工具来获取 Zipline 的非 Python 依赖项。

有关如何安装`conda`的说明，请参阅[Conda 安装文档](https://conda.io/projects/conda/en/latest/user-guide/install/index.html)。

一旦设置了`conda`，你就可以从`conda-forge`频道安装 Zipline。

请参阅[此处](https://github.com/conda-forge/zipline-reloaded-feedstock)了解最新的安装详细信息。

### 管理`conda`环境

建议在隔离的`conda`环境中安装 Zipline。在`conda`环境中安装 Zipline 不会干扰你的默认 Python 部署或 site-packages，这将防止与你的全局库发生任何可能的冲突。有关`conda`环境的更多信息，请参阅[Conda 用户指南](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)。

假设已经设置了`conda`，你可以创建一个`conda`环境：

```py
$  conda  create  -n  env_zipline  python=3.10 
```

现在你已经设置了一个名为`env_zipline`的隔离环境，这是一个类似沙盒的结构，用于安装 Zipline。然后你应该使用以下命令激活 conda 环境：

```py
$  conda  activate  env_zipline 
```

你可以通过运行以下命令来安装 Zipline：

```py
(env_zipline)  $  conda  install  -c  ml4t  zipline-reloaded 
```

要停用`conda`环境：

```py
(env_zipline)  $  conda  deactivate 
```

注意

`conda activate` 和 `conda deactivate` 仅适用于 conda 4.6 及更高版本。对于 conda 4.6 之前的版本，请运行：

> +   Windows：`activate` 或 `deactivate`
> +   
> +   Linux 和 macOS：`source activate` 或 `source deactivate`

## 使用 `pip` 安装

通过 `pip` 安装 Zipline 比一般的 Python 包稍微复杂一些。

增加复杂性的原因有两个：

1.  Zipline 提供了多个需要访问 CPython C API 的 C 扩展。为了构建这些 C 扩展，`pip` 需要访问您 Python 安装的 CPython 头文件。

1.  Zipline 依赖于 [NumPy](https://www.numpy.org/)，这是 Python 中用于数值数组计算的核心库。NumPy 反过来又依赖于 [LAPACK](https://www.netlib.org/lapack) 线性代数例程。

由于 LAPACK 和 CPython 头文件是非 Python 依赖项，因此安装它们的方法因平台而异。如果您更愿意使用单个工具来安装 Python 和非 Python 依赖项，或者如果您已经在使用 [Anaconda](https://www.anaconda.com/distribution/) 作为您的 Python 发行版，则可以跳至 :ref: conda 部分。

一旦您安装了必要的额外依赖项（请参阅下面的特定平台），您应该能够简单地运行（最好在激活的虚拟环境中）：

```py
$  pip  install  zipline-reloaded 
```

如果您使用 Python 进行除 Zipline 之外的任何操作，我们**强烈**建议您在 [virtualenv](https://virtualenv.readthedocs.org/en/latest/) 中安装。《Python 漫游者指南》提供了一个关于 virtualenv 的[优秀教程](https://docs.python-guide.org/en/latest/dev/virtualenvs/)。

### GNU/Linux

#### 依赖项

在 [Debian 派生](https://www.debian.org/derivatives/)的 Linux 发行版上，您可以通过运行以下命令从 `apt` 获取所有必要的二进制依赖项：

```py
$  sudo  apt  install  libatlas-base-dev  python-dev  gfortran  pkg-config  libfreetype6-dev  hdf5-tools 
```

在最近的 [RHEL 派生](https://en.wikipedia.org/wiki/Red_Hat_Enterprise_Linux_derivatives)的 Linux 发行版（例如 Fedora）上，以下内容应该足以获取必要的额外依赖项：

```py
$  sudo  dnf  install  atlas-devel  gcc-c++  gcc-gfortran  libgfortran  python-devel  redhat-rpm-config  hdf5 
```

在 [Arch Linux](https://www.archlinux.org/) 上，您可以通过 `pacman` 获取额外的依赖项：

```py
$  pacman  -S  lapack  gcc  gcc-fortran  pkg-config  hdf5 
```

也有 AUR 包可用于安装 [ta-lib](https://aur.archlinux.org/packages/ta-lib/)。Python 3 也可以通过以下方式安装：

```py
$  pacman  -S  python3 
```

#### 编译 TA-Lib

您还需要编译 [TA-Lib](https://www.ta-lib.org/) 库以进行技术分析，以便其头文件可用。

您可以按照以下步骤操作：

```py
$  wget  http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz
$  tar  -xzf  ta-lib-0.4.0-src.tar.gz
$  cd  ta-lib/
$  sudo  ./configure
$  sudo  make
$  sudo  make  install 
```

这将允许您按照二进制轮子的预期使用 `pip` 安装 Python 包装器。

### macOS

macOS 随附的 Python 版本通常已过时，并且由于操作系统直接使用它而存在一些特殊情况。出于这些原因，许多开发者选择安装并使用单独的 Python 安装。

[Python 漫游指南](https://docs.python-guide.org/en/latest/)提供了[在 macOS 上安装 Python](https://docs.python-guide.org/en/latest/)的优秀指南，解释了如何使用[Homebrew](https://brew.sh/)管理器安装 Python。或者，您可以使用[pyenv](https://github.com/pyenv/pyenv)。

假设您已经使用`brew`安装了 Python，您可能还需要以下包：

```py
$  brew  install  freetype  pkg-config  gcc  openssl  hdf5  ta-lib 
```

### Windows

对于 Windows，安装 Zipline 最简单且最支持的方法是使用`conda`。

### GNU/Linux

#### 依赖项

在[Debian 衍生](https://www.debian.org/derivatives/)的 Linux 发行版上，您可以通过运行以下命令从`apt`获取所有必要的二进制依赖项：

```py
$  sudo  apt  install  libatlas-base-dev  python-dev  gfortran  pkg-config  libfreetype6-dev  hdf5-tools 
```

在最近的[RHEL 衍生](https://en.wikipedia.org/wiki/Red_Hat_Enterprise_Linux_derivatives)的 Linux 发行版（例如 Fedora）上，以下步骤应该足以获取必要的额外依赖项：

```py
$  sudo  dnf  install  atlas-devel  gcc-c++  gcc-gfortran  libgfortran  python-devel  redhat-rpm-config  hdf5 
```

在[Arch Linux](https://www.archlinux.org/)上，您可以通过`pacman`获取额外的依赖项：

```py
$  pacman  -S  lapack  gcc  gcc-fortran  pkg-config  hdf5 
```

也有 AUR 包可用于安装[ta-lib](https://aur.archlinux.org/packages/ta-lib/)。Python 3 也可以通过以下方式安装：

```py
$  pacman  -S  python3 
```

#### 编译 TA-Lib

您还需要编译[TA-Lib](https://www.ta-lib.org/)技术分析库，以便其头文件可用。

您可以按照以下步骤操作：

```py
$  wget  http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz
$  tar  -xzf  ta-lib-0.4.0-src.tar.gz
$  cd  ta-lib/
$  sudo  ./configure
$  sudo  make
$  sudo  make  install 
```

这将允许您按照二进制轮子的预期使用`pip`安装 Python 包装器。

#### 依赖项

在[Debian 衍生](https://www.debian.org/derivatives/)的 Linux 发行版上，您可以通过运行以下命令从`apt`获取所有必要的二进制依赖项：

```py
$  sudo  apt  install  libatlas-base-dev  python-dev  gfortran  pkg-config  libfreetype6-dev  hdf5-tools 
```

在最近的[RHEL 衍生](https://en.wikipedia.org/wiki/Red_Hat_Enterprise_Linux_derivatives)的 Linux 发行版（例如 Fedora）上，以下步骤应该足以获取必要的额外依赖项：

```py
$  sudo  dnf  install  atlas-devel  gcc-c++  gcc-gfortran  libgfortran  python-devel  redhat-rpm-config  hdf5 
```

在[Arch Linux](https://www.archlinux.org/)上，您可以通过`pacman`获取额外的依赖项：

```py
$  pacman  -S  lapack  gcc  gcc-fortran  pkg-config  hdf5 
```

也有 AUR 包可用于安装[ta-lib](https://aur.archlinux.org/packages/ta-lib/)。Python 3 也可以通过以下方式安装：

```py
$  pacman  -S  python3 
```

#### 编译 TA-Lib

您还需要编译[TA-Lib](https://www.ta-lib.org/)技术分析库，以便其头文件可用。

您可以按照以下步骤操作：

```py
$  wget  http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz
$  tar  -xzf  ta-lib-0.4.0-src.tar.gz
$  cd  ta-lib/
$  sudo  ./configure
$  sudo  make
$  sudo  make  install 
```

这将允许您按照二进制轮子的预期使用`pip`安装 Python 包装器。

### macOS

macOS 随附的 Python 版本通常已过时，并且由于操作系统直接使用它而存在一些怪癖。出于这些原因，许多开发人员选择安装并使用单独的 Python 安装。

[Python 漫游指南](https://docs.python-guide.org/en/latest/)提供了[在 macOS 上安装 Python](https://docs.python-guide.org/en/latest/)的优秀指南，解释了如何使用[Homebrew](https://brew.sh/)管理器安装 Python。或者，您可以使用[pyenv](https://github.com/pyenv/pyenv)。

假设您已使用`brew`安装了 Python，您可能还需要以下包：

```py
$  brew  install  freetype  pkg-config  gcc  openssl  hdf5  ta-lib 
```

### Windows

对于 Windows，安装 Zipline 最简单且最支持的方法是使用`conda`。

## 使用`conda`安装

另一种安装 Zipline 的方法是通过`conda`包管理器，该管理器包含在[Anaconda](https://www.anaconda.com/distribution/)发行版中。或者，您可以使用相关但更轻量级的[Miniconda](https://docs.conda.io/en/latest/miniconda.html#)或[Miniforge](https://github.com/conda-forge/miniforge)安装程序。

使用 Conda 而非`pip`的主要优势在于，`conda`原生理解`numpy`和`scipy`等包的复杂二进制依赖关系。这意味着`conda`可以安装 Zipline 及其依赖项，而无需使用第二个工具来获取 Zipline 的非 Python 依赖项。

有关如何安装`conda`的说明，请参阅[Conda 安装文档](https://conda.io/projects/conda/en/latest/user-guide/install/index.html)。

一旦设置了`conda`，您就可以从`conda-forge`频道安装 Zipline。

有关最新安装详细信息，请参见[此处](https://github.com/conda-forge/zipline-reloaded-feedstock)。

### 管理`conda`环境

建议在隔离的`conda`环境中安装 Zipline。在`conda`环境中安装 Zipline 不会干扰您的默认 Python 部署或 site-packages，这将防止与您的全局库发生任何可能的冲突。有关`conda`环境的更多信息，请参阅[Conda 用户指南](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)。

假设已经设置了`conda`，您可以创建一个`conda`环境：

```py
$  conda  create  -n  env_zipline  python=3.10 
```

现在，您已经设置了一个名为`env_zipline`的隔离环境，这是一个类似沙箱的结构，用于安装 Zipline。然后，您应该通过使用以下命令来激活 conda 环境：

```py
$  conda  activate  env_zipline 
```

您可以通过运行以下命令来安装 Zipline：

```py
(env_zipline)  $  conda  install  -c  ml4t  zipline-reloaded 
```

要停用`conda`环境：

```py
(env_zipline)  $  conda  deactivate 
```

注意

`conda activate` 和 `conda deactivate` 仅适用于 conda 4.6 及更高版本。对于 conda 4.6 之前的版本，请运行：

> +   Windows: `activate` 或 `deactivate`
> +   
> +   Linux 和 macOS: `source activate` 或 `source deactivate`  ### 管理`conda`环境

建议在隔离的`conda`环境中安装 Zipline。在`conda`环境中安装 Zipline 不会干扰您的默认 Python 部署或 site-packages，这将防止与您的全局库发生任何可能的冲突。有关`conda`环境的更多信息，请参阅[Conda 用户指南](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)。

假设已经设置了`conda`，您可以创建一个`conda`环境：

```py
$  conda  create  -n  env_zipline  python=3.10 
```

现在，您已经设置了一个名为`env_zipline`的隔离环境，这是一个类似沙箱的结构，用于安装 Zipline。然后，您应该通过使用以下命令来激活 conda 环境：

```py
$  conda  activate  env_zipline 
```

您可以通过运行以下命令来安装 Zipline：

```py
(env_zipline)  $  conda  install  -c  ml4t  zipline-reloaded 
```

要停用 `conda` 环境：

```py
(env_zipline)  $  conda  deactivate 
```

注意

`conda activate` 和 `conda deactivate` 仅适用于 conda 4.6 及更高版本。对于 conda 4.6 之前的版本，请运行：

> +   Windows：`activate` 或 `deactivate`
> +   
> +   Linux 和 macOS：`source activate` 或 `source deactivate`
