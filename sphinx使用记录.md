## sphinx使用记录

### 注意事项

1.[官网](http://www.sphinx-doc.org/en/master/index.html)

2.注意：配置conf.py中和使用`sphinx-apidoc`的路径时，是按照Makefile的文件位置来配置。或者直接使用绝对路径。`sys.path.append('/home/workspace/myproj/myproj')`

## 简介

Sphinx 是一种工具，它允许开发人员以纯文本格式编写文档，以便采用满足不同需求的格式轻松生成输出。这在使用 Version Control System 追踪变更时非常有用。纯文本文档对不同系统之间的协作者也非常有用。纯文本是当前可以采用的最便捷的格式之一。

虽然 Sphinx 是用 Python 编写的，并且最初是为 Python 语言文档而创建，但它并不一定是以语言为中心，在某些情况下，甚至不是以程序员为中心。Sphinx 有许多用处，比如可以用它来编写整本书！

> **默认情况下，Sphinx 会为 Python 突出显示代码，但它还允许定义其他编程语言，比如 C 和 Ruby。**

可以将 Sphinx 想像成为一种*文档框架*：它会抽象化比较单调的部分，并提供自动函数来解决一些常见问题，比如突出显示标题索引和特殊代码（在显示代码示例时），以及突出显示适当的语法。

### 要求

您应该能轻车熟路地使用 Linux® 或 UNIX® 终端（也称为控制台或终端仿真器），因为命令行界面是与 Sphinx 进行互动的主要方式。

您需要安装 Python。在所有主要的 Linux 发行版和一些基于 UNIX 的操作系统（如 Mac OSX）上预先安装 Python 并做好使用它的准备。Sphinx 支持 Python V 2.4、2.5 和 2.6。要确定您已经安装了 Python 并且安装的是有效版本，请运行 [清单 1](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#listing1) 中的代码。

##### 清单 1. 检查 Python 的版本

````shell
$ python --version

Python 3.5.1

````

### 语法

Sphinx 使用 reStructuredText 标记语法（和其他一些语法）来提供文档控制。如果您之前编写过纯文本文件，那么您可能非常了解精通 Sphinx 所需的语法。

标记允许为适当的输出实现文本的定义和结构。开始之前，请参见 [清单 2](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#listing2) 中的一个小的标记语法示例。

##### 清单 2. Sphinx 标记语法示例

```rst
This is a Title
===============
That has a paragraph about a main subject and is set when the '='
is at least the same length of the title itself.
 
Subject Subtitle
----------------
Subtitles are set with '-' and are required to have the same length 
of the subtitle itself, just like titles.
 
Lists can be unnumbered like:
 
 * Item Foo
 * Item Bar
 
Or automatically numbered:
 
 #. Item 1
 #. Item 2
 
Inline Markup
-------------
Words can have *emphasis in italics* or be **bold** and you can define
code samples with back quotes, like when you talk about a command: ``sudo`` 
gives you super user powers!
```



正如您所看到的，纯文本格式的语法非常容易读懂。在创建特定格式（如 HTML）时，标题会成为主要目标，其字体会比副标题大一些（理应如此），并且会对编号列表进行适当的编号。您已经拥有一些非常强大的功能。添加更多的项或更改编号列表中的顺序不会影响到编号，而通过替换使用的下划线可以改变标题的重要性。

## 安装和配置

安装是通过命令行进行的，非常简单明了，如 [清单 3](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#listing3) 所示。

##### 清单 3. 安装 Sphinx

```shell
$ pip install sphinx
```

为了简便起见，[清单 3](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#listing3) 中的内容有所缩减，但它提供了在一个在安装 Sphinx 时应执行的操作的示例。

框架使用了一个目录结构来分离源文件（纯文本文件）和构建（指生成的输出）。例如，如果使用 Sphinx 从文档源中生成一个 PDF，那么该文件会放置在构建目录中。您可以更改此行为，但为了获得一致性，我们还是保留了默认格式。

让我们快速启动 [清单 4](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#listing4) 的一个新的文档项目，系统会通过一些问题提示您如何操作。请按下 **Enter** 键接受所有的默认值。

##### 清单 4. 执行 sphinx-quickstart

```shell
$ sphinx-quickstart
Welcome to the Sphinx 1.6.3 quickstart utility.

Please enter values for the following settings (just press Enter to
accept a default value, if one is given in brackets).

Selected root path: sphinx-test

You have two options for placing the build directory for Sphinx output.
Either, you use a directory "_build" within the root path, or you separate
"source" and "build" directories within the root path.
> Separate source and build directories (y/n) [n]:

Inside the root directory, two more directories will be created; "_templates"
for custom HTML templates and "_static" for custom stylesheets and other static
files. You can enter another prefix (such as ".") to replace the underscore.
> Name prefix for templates and static dir [_]:

The project name will occur in several places in the built documentation.
> Project name: SphinxTest
> Author name(s): hymanlu

Sphinx has the notion of a "version" and a "release" for the
software. Each version can have multiple releases. For example, for
Python the version is something like 2.5 or 3.0, while the release is
something like 2.5.1 or 3.0a1.  If you don't need this dual structure,
just set both to the same value.
> Project version []: 1.0.0
> Project release [1.0.0]:

If the documents are to be written in a language other than English,
you can select a language here by its language code. Sphinx will then
translate text that it generates into that language.

For a list of supported codes, see
http://sphinx-doc.org/config.html#confval-language.
> Project language [en]: zh_CN

The file name suffix for source files. Commonly, this is either ".txt"
or ".rst".  Only files with this suffix are considered documents.
> Source file suffix [.rst]:

One document is special in that it is considered the top node of the
"contents tree", that is, it is the root of the hierarchical structure
of the documents. Normally, this is "index", but if your "index"
document is a custom template, you can also set this to another filename.
> Name of your master document (without suffix) [index]:

Sphinx can also add configuration for epub output:
> Do you want to use the epub builder (y/n) [n]:

Please indicate if you want to use one of the following Sphinx extensions:
> autodoc: automatically insert docstrings from modules (y/n) [n]:
> doctest: automatically test code snippets in doctest blocks (y/n) [n]:
> intersphinx: link between Sphinx documentation of different projects (y/n) [n]:
> todo: write "todo" entries that can be shown or hidden on build (y/n) [n]:
> coverage: checks for documentation coverage (y/n) [n]:
> imgmath: include math, rendered as PNG or SVG images (y/n) [n]:
> mathjax: include math, rendered in the browser by MathJax (y/n) [n]:
> ifconfig: conditional inclusion of content based on config values (y/n) [n]:
> viewcode: include links to the source code of documented Python objects (y/n) [n]:
> githubpages: create .nojekyll file to publish the document on GitHub pages (y/n) [n]:

A Makefile and a Windows command file can be generated for you so that you
only have to run e.g. `make html' instead of invoking sphinx-build
directly.
> Create Makefile? (y/n) [y]:
> Create Windows command file? (y/n) [y]:

Creating file sphinx-test\conf.py.
Creating file sphinx-test\index.rst.
Creating file sphinx-test\Makefile.
Creating file sphinx-test\make.bat.

Finished: An initial directory structure has been created.

You should now populate your master file sphinx-test\index.rst and create other documentation
source files. Use the Makefile to build the docs, like so:
   make builder
where "builder" is one of the supported builders, e.g. html, latex or linkcheck.
```

通常下面两个不用默认项，其他均可用默认项

```python
> Separate source and build directories (y/n) [n] y
> autodoc: automatically insert docstrings from modules (y/n) [n]: y
```

运行 `sphinx-quickstart` 命令后，在工作目录中会出现类似 [清单 5](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#listing5) 的文件。

##### 清单 5. 工作目录的列表

```shell

- yourdir/ # 刚才新建的目录
    - source/ # 存放Sphinx工程源码
    - build/ # 存放生成的文档
    Makefile
    conf.py
    index.rst
```

让我们详细研究一下每个文件。

- **Makefile**：编译过代码的开发人员应该非常熟悉这个文件，如果不熟悉，那么可以将它看作是一个包含指令的文件，在使用 `make` 命令时，可以使用这些指令来构建文档输出。
- **build**：这是触发特定输出后用来存放所生成的文件的目录。
- **static**：可以将所有不属于源代码（如图像）一部分的文件均存放于此处，稍后会在构建目录中将它们链接在一起。
- **conf.py**：这是一个 Python 文件，用于存放 Sphinx 的配置值，包括在终端执行 `sphinx-quickstart` 时选中的那些值。
- **index.rst**：文档项目的 root 目录。如果将文档划分为其他文件，该目录会连接这些文件。

## 入门指南

此时，我们已经正确安装了 Sphinx，查看了默认结构，并了解了一些基本语法。不要直接开始编写文档。缺乏布局和输出方面的知识会让您产生混淆，可能耽误您的整个进程。

现在来深入了解一下 `index.rst` 文件。它包含大量的信息和其他一些复杂的语法。为了更顺利地完成任务并避免干扰，我们将合并一个新文件，将它列在主要章节中。

在 `index.rst` 文件中的主标题之后，有一个内容清单，其中包括 `toctree` 声明。`toctree` 是将所有文档汇集到文档中的中心元素。如果有其他文件存在，但没有将它们列在此指令下，那么在构建的时候，这些文件不会随文档一起生成。

我们想将一个新文件添加到文档中，并打算将其命名为 `example.rst`。还需要将它列在 `toctree` 中，但要谨慎操作。文件名后面需要有一个间隔，这样文件名清单才会有效，该文件不需要文件扩展名（在本例中为 `.rst`）。[清单 6](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#listing6) 显示该列表的外观。在文件名距离左边距有三个空格的距离，`maxdepth` 选项后面有一个空白行。

##### 清单 6. index.rst 中的 toctree 示例

```rst
Contents:
 
.. toctree::
   :maxdepth: 2
 
   example
```

此时，不用担心其他选项。目前，注意到了有一个列出其他单独的文件的索引文件，该文件可存储有效文档，因此，该列表有一定的顺序和空格，才能使该列表变得有效。

还记得 [清单 2](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#listing2) 中的示例语法吗? 请复制该示例，将它粘贴到 `example.rst` 文件中并保存它。现在我们准备生成输出。

运行 `make` 命运，并将 HTML 指定为输出格式。可直接将该输出用作网站，因为它包含了生成的所有内容，包括 JavaScript 和 CSS 文件。请参见 [清单 7](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#listing7)。

##### 清单 7. `make html` 命令的输出

```shell
$ make html
sphinx-build -b html -d _build/doctrees   . _build/html
Making output directory...
Running Sphinx v1.0.5
loading pickled environment... not yet created
building [html]: targets for 2 source files that are out of date
updating environment: 2 added, 0 changed, 0 removed
reading sources... [100%] index
looking for now-outdated files... none found
pickling environment... done
checking consistency... done
preparing documents... done
writing output... [100%] index 
writing additional files... genindex search
copying static files... done
dumping search index... done
dumping object inventory... done
build succeeded.
 
Build finished. The HTML pages are in _build/html.
```

如果您对 `make` 命令提供的其他选项感兴趣，请参见 [清单 8](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#listing8)，将帮助标志传至此处，并查看完整的描述。

##### 清单 8. 列示 make 选项

```shell
$ make -h
Usage: make [options] [target] ...
Options:
[...]
```

### 生成静态网站

随着我们完成第一步操作，从两个文件中生成 HTML 之后，我们就拥有一个完整的函数式（静态）网站。

在 `_build` 目录内，现在应该有两个目录：`doctrees` 和 `HTML`。我们对于这个存储了文档网站所需的全部文件的 HTML 目录很感兴趣。使用浏览器打开 index.html 文件，就会发现如 [图 1](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#fig1) 所示的内容。

##### 图 1. 静态 HTML 形式的主页

虽然信息很少，但 Sphinx 能够创建很多内容。我们拥有一个基本布局，该布局包含有关项目文档、搜索部分、内容表、附带名称和日期的版权声明、页码的一些信息。

搜索部分非常有趣，因为 Sphinx 已经为所有文件建立索引，并使用 JavaScript 的一些强大功能创建了一个可搜索的静态网站。

还记得我们已将 `example` 作为一个单独的文件添加至 [清单 6](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#listing6) 的 `toctree` 中的文档吗？您可以看到，主标题显示为内容索引中的主要项目符号，副标题显示为二级项目符号。Sphinx 小心维护着让整个结构保持正确。

> **如果一开始就没有成功...**

进行一些修改后，只需再次运行 `make` 命令，即可生成这些文件。

所有的链接都指向文档中的正确位置，并且标题和副标题均有定位点，允许直接进行链接。比如，`Subject Subtitle` 部分在浏览器中有一个类似 `../example.html#subject-subtitle` 的定位点。如前所述，该工具消除了我们对这些琐碎的、重复的需求的顾虑。

[图 2](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#fig2) 显示了 `example.rst` 如何显示为静态网站中的 HTML 文件。

##### 图2. HTML 页面示例

### 添加图形

简明的段落、图像和图形都为项目文档增加趣味性和可读性。Sphinx 有助于利用这些有可能添加了静态文件的主要元素来吸引读者的注意。

添加静态文件的正确语法很容易记忆。只要将静态文件放置 `_static` 目录（Sphinx 在创建文档布局时创建了该目录）中，就可以轻松地对其进行引用。在 [清单 9](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#listing9)，查看 reStructuredTex 文件中的引用应该是什么样子的。在本例中，我将其添加在 `example.rst` 的底部。

##### 清单 9. example.rst 的静态清单

```shell
.. image:: _static/system_activity.jpg
```

生成文档之后，应将图像正确放置在我们为有关系统活动的 JPEG 小图像指定的地方。它看上起应该类似于 [图 3](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#fig3)。

##### 图 3. 系统活动图像

### 例1

假设现在我们有一个叫run.py的文件，如下

```
# run.py
def run(name):
    """
    this is how we run

    :param name name of people who runs
    """
    print name, 'is running'
123456789
```

那么需要如何生成文档呢？下面一步步带你完成 
\- 创建一个文件夹demo/，并将run.py放到里面 
\- 在demo里创建一个docs目录，进入docs/目录，当然这里你可以随意选定文件夹，只是这样更规范一些 
\- 生成Sphinx默认模板，设置项目名为demo，并开启autodoc 
运行`sphinx-quickstart` 
\- 进入source目录，打开index.rst 
\- 将index.rst 修改为如下，实际上这里面可以写任何满足rst规范的内容

```
Welcome to demo's API Documentation
======================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:

Introduction
============
This is the introduction of demo。

API
===
.. automodule:: run
   :members:

Indices and tables
==================
* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
12345678910111213141516171819202122
```

这个是用于自动从模块读取docstring的语句，可以自动从run模块读取文档

```
.. automodule:: run
   :members:12
```

- 但是现在还差一步，如果你现在直接生成文档，会告诉你找不到run模块，因为Sphinx默认只会从sys.path里加载模块，所以需要将demo目录加入sys.path，所以现在打开conf.py，添加如下内容

```
import os
import sys
sys.path.insert(0, os.path.abspath('../..'))123
```

- 运行Sphinx生成html文档

```
cd ..
sphinx-build -b html source build
make html123
```

现在，打开build/html/index.html就可以看到如下界面了

![demo](http://img.blog.csdn.net/20170623150124967?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJleXRh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 格式进一步优化

上面的例子涵盖了大多数常用操作，但是通常文档都不是扁平的，而是层次化的，那么要如何将文档层次化的分门别类。 
实际上在rst文档中是可以以链接的形式引用其他rst文档的，也就是说我们可以自由的对文档结构进行组织使其更易读

下面我们把run的文档移动到一个单独的页面，只在index.rst里保留跳转链接。在source目录下创建run.rst

```
API
===
.. automodule:: run
   :members:

Indices and tables
==================
* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`12345678910
```

然后将index.rst对应位置的内容删掉，并进行修改

```
Welcome to demo's API Documentation
===================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:

Introduction
============
This is the introduction of demo。

API
===
:doc:'Run API</run>'

Indices and tables
==================
* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`1234567891011121314151617181920
```

`:doc:'Run API</run>'`表示对一个文档的引用，这里引用了当前目录的run.rst，现在重新生成html，就会看到页面显示上的变化了。

### 例子2

#### 建立sphinx项目

在代码目录下安装shift+鼠标右键，打开cmd窗口，我的目录地址是 `E:\pycharm` 
然后依次输入如下命令，注意，如果没有输入直接回车，则是使用默认选型

```
E:\pycharm> sphinx-quickstart
> Root path for the documentation [.]: doc  # 在当前目录下新建doc文件夹存放sphinx相关信息
> Separate source and build directories (y/n) [n]:   # 默认，直接回车
> Name prefix for templates and static dir [_]: 
> Project name: python123   # 输入项目名称
> Author name(s): 123   # 作者
> Project version: 1.0  # 项目版本
> Project release [1.0]:
> Project language [en]:   # 默认，回车
> Source file suffix [.rst]: 
> Name of your master document (without suffix) [index]:
> Do you want to use the epub builder (y/n) [n]:
> autodoc: automatically insert docstrings from modules (y/n) [n]: y  # 这个很重要，输入y
> doctest: automatically test code snippets in doctest blocks (y/n) [n]:
> intersphinx: link between Sphinx documentation of different projects (y/n) [n]:
> todo: write "todo" entries that can be shown or hidden on build (y/n) [n]:
> coverage: checks for documentation coverage (y/n) [n]:
> pngmath: include math, rendered as PNG images (y/n) [n]:
> mathjax: include math, rendered in the browser by MathJax (y/n) [n]:
> ifconfig: conditional inclusion of content based on config values (y/n) [n]:
> viewcode: include links to the source code of documented Python objects (y/n) [n]: y  # 很重要，输入y，表示将源码也放到文档中，你看很多python的模块的文档，其实都是包含代码的。
> Create Makefile? (y/n) [y]:
> Create Windows command file? (y/n) [y]:

# 之后会在doc目录下生成如下文件
E:.
│  conf.py
│  index.rst
│  make.bat
│  Makefile
│
├─_build
├─_static
└─_templates12345678910111213141516171819202122232425262728293031323334
```

#### 生成apidoc

还是在刚才的目录下 `E:\pycharm` 打开cmd

```
# 对当前目录的每一个文件夹及子文件夹生成一个rst文件，对应python的包，存放在./doc目录下
sphinx-apidoc -o ./doc/ .

# 之后会在doc文件夹下看到很多刚才生成的rst文件
E:.
│  ahp_model_ys.rst
│  conf.py
│  config.rst
│  control_all_data_and_process.rst
│  data_etl.rst
│  dynamic_proc.rst
│  ex_data.oracle_imp_exp.rst
│  ex_data.rst
│  index.rst
│  keywords.rst
│  log_ys.rst
│  make.bat
│  Makefile
│  modules.rst
│  predict_by_rule_ys.rst
│  similar_prison_ys.rst
│  tools_py.backup_ys.rst
│  tools_py.cmd_py.rst
│  tools_py.database.rst
│  tools_py.email_ys.rst
│  tools_py.linux.rst
│  tools_py.rst
│  tools_py.sched_py.rst
│  tools_py.time_py.rst
│  trace_sql_info.rst
12345678910111213141516171819202122232425262728293031
```

#### 修改conf

进入doc目录，修改conf.py文件。

```
# 1.增加搜索目录
# 找到 #sys.path.insert(0, os.path.abspath('.'))，去掉注释并修改为
sys.path.insert(0, os.path.abspath('..'))

# 2.修改主题
# 在文件的开头导入刚才安装的主题
import sphinx_bootstrap_theme
# 找到html_theme，修改为
html_theme = 'bootstrap'
# 找到html_theme_path，修改为
html_theme_path = sphinx_bootstrap_theme.get_html_theme_path()
# 找到html_theme_options，修改为
html_theme_options = {  
    'navbar_title': "你的项目名称",  
    'globaltoc_depth': 2,  
    'globaltoc_includehidden': "true",  
    'navbar_class': "navbar navbar-inverse",  
    'navbar_fixed_top': "true",  
    'bootswatch_theme': "united",  
    'bootstrap_version': "3",  
}123456789101112131415161718192021
```

sphinx_bootstrap_theme的参数调节请参考其官方文档。

#### 修改index.rst

打开doc目录下的index.rst文件，你可以在这里写上一起其他的欢迎信息，简要说明，或者注意事项。需要注意的是，sphinx的语法和markdown差不多，使用空行表示换行。

在 :maxdepth: 2 下面增加modules.rst，表示目录

最后的我修改的样子如下，注意要去掉括号里面的注释。

```
.. python123 documentation master file, created by
   sphinx-quickstart on Tue Oct 25 14:30:26 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

你好啊，这是一级标题
==================  （注意，可以用=或者-表示标题，只要长度大于标题就可以了）

空一行，这里写些其他内容，简介或者注意事项。

当然你也可以加入代码，后面加两个冒号，然后缩进 :: 
    import pandas as pd
    pd.DataFrame()

更多格式，可以参考：
http://blog.csdn.net/jianhong1990/article/details/8256598

Contents:

.. toctree::
   :maxdepth: 2  （目录树深度，不要展开太多）

   modules.rst  （这里空一行加入目录，当然也可以加其他rst。注意要和上面的平级，）

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
12345678910111213141516171819202122232425262728293031
```

#### 修改modules.rst

```
# 将modules.rst第一行的点号修改为 '显示目录'，当然可以不改。
# 第一行的等号多写几个，长度大于第一行
# 树深度改为3

显示目录
=========

.. toctree::
   :maxdepth: 3123456789
```

#### 编译

在doc目录下打开cmd，执行 `make html`，过一会就可以在 `doc\_build\html`目录下看到编译生成的网页文件了。

#### 严重的排版问题

由于sphinx使用空行表示换行，这就会导致，如果想要在网页端可到好看的结果，那么代码文件中的注释就需要很多空行，如果想在代码文件中把注释写得好看点，那么网页端排版可能就不好看了。 
另一个问题是缩进问题，一个大问题总会分解成几个小问题吧，或者一个问题有几个小步骤，这时候分几点然后缩进排版是很正常的。 
比如下面的源码中的注释：

```
为了保持网页端的排版好看，我可能需要这样写注释。

上面空了一行，否则你在源码中看到的换行，在网页端其实并没有换行。

又比如缩进问题：

    又要空一行，真的是浪费啊，而且也不是那么好看了。如果这里缩进，就会变成列表了，其实在网页端是不好看的。

    不信你试试123456789
```

但是在正常的情况下，你肯定不会写这么多空行吧，真实的情况只有在两个段落差异比较大的情况才会这样换行吧，下面是更多见的注释情况吧

```
为了保持网页端的排版好看，我可能需要这样写注释。
一般情况下谁没事会经常空一行表示换行呢？直接回车就行了嘛，代码已经不好看了，注释还不能好看。
(下面空一行，表示两个段落的分割)

又比如缩进问题：
    我这里不想空一行再缩进，想要直接缩进，就像这样子。
    可是它会变成列表那样子，排版上其实是不好看的。
    不信你试试12345678
```

那么有没有办法既能兼顾代码注释的美观，又能使生成的网页比较好看呢？ 
答案是不能的，只能曲线处理，就是牺牲代码注释一点点的美观，换取不要写那么多空行。

#### 使用特殊的换行标记

比如在注释中，每一行的末尾都加入`<p>`，这样生成网页后，将所有网页的这个`<p>`标记替换成`</p><p>`实现换行。 
虽然不是高明，但是也还过得去。 
缩进问题，比如在行首加上`.`号防止被识别为列表缩进，然后空4个空格接着写注释，对生成的网页文件，将这个（点号+4个空格）替换成网页能识别的空格`&nbsp`即可。 
那么代码注释就会变成这样了：

```
为了保持网页端的排版好看，我可能需要这样写注释。 <p>
在每一行的后面都加一个 <p>，这样就可以后期直接修改网页源码使之变成换行符<p>
(下面空一行，表示两个段落的分割) 

又比如缩进问题： <p>
.   我在前面加上点号，然后几个空格，后期可以直接对网页源码修改为几个空格即可实现缩进。<p>
.   不信你试试<p>1234567
```

#### 使用块缩进

上面的方法虽然可行，但是步骤还是多了写，而且需要在生成网页后批量修改网页源代码。 
有一个比较笨，但是也还不错的方法，就是所用块缩进，也就是在每一行起始用`|`（竖线+空格）表示块，这样就间接解决了换行问题，唯一不足的地方是，没有那么好看了。 
修改后的注释是这样的：

```
| 为了保持网页端的排版好看，我可能需要这样写注释。 
| 在每一行的前面都加上竖线和空格，这样每一行都是一块，不需要使用空行换行
| 而且也不是那么难看，勉强可以接受
| (下面空一行，表示两个段落的分割) 
| 
| 又比如缩进问题： 
|   这里想缩进多少就缩进多少，最终网页就是你所看到的这个样子 
|   不信你试试 
| 
| 此时不同的缩进层次就明朗了12345678910
```

## 结束语

本文介绍了开始使用 Sphinx 的一些基础知识，但仍有许多内容有待我们探索。Sphinx 能够用不同的格式导出文档，但要求安装额外的库和软件。可生成的格式包括：PDF、epub、man (UNIX Manual Pages) 和 LaTeX。

对于复杂的图形，有一个插件可将 Graphviz 图形添加至您的文档项目。我曾经不得不为一个小型办公网络地图创建一个插件，但它表现相当出色，无需使用其他工具，便可在同一文档中获取所有的东西。与 Graphviz 插件类似，有大量可用于 Sphinx 的插件（亦称为扩展）。Sphinx 提供了一些插件，比如 interSphinx，该插件允许您链接不同的 Sphinx 项目。

如果生成的输出的外观不符合您的喜好，Sphinx 还提供了许多主题，可应用它们来完全改变 HTML 文件呈现文档的方式。一些重要的开源项目，如 Celery 和 Lettuce，通过更改 CSS 并扩展模板完全更改了 HTML 的外观。请参阅 [参考资料](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/index.html#artrelatedtopics) 部分，以获取这些项目的链接、解释如何扩展的文档的链接以及修改默认 CSS 和布局的链接。

Sphinx 改变了我对编写文档的看法。从一开始的毫无灵感，到现在能够轻易编制我的几乎所有的个人开源项目以及少数内部项目，我感到非常兴奋。使用 Sphinx 可轻松检索遗忘在您自己文档中的信息。

#### 下载资源

- [本文的样例 Sphinx 项目](http://www.ibm.com/developerworks/apps/download/index.jsp?contentid=788234&filename=example_sphinx_project.zip&method=http&locale=zh_CN) (example_sphinx_project.zip | 142KB)

#### 相关主题

- [Sphinx 项目文档](http://sphinx.pocoo.org/)
- [Sphinx 扩展](http://sphinx.pocoo.org/extensions.html)：查找第三方扩展的完整列表和一些外部引用。
- [Theming and Templating](http://sphinx.pocoo.org/theming.html)：浏览解释扩展当前主题和应用新主题的部分。
- [Celery 项目文档](http://celeryq.org/docs/) 使用 Sphinx 修改 CSS 和 Font 的默认值。
- [Python Programming Language 文档](http://docs.python.org/)：构建 Sphinx 是为了支持 Python 上的完整文档。
- [Lettuce](http://lettuce.it/) 是另一个开源项目，它（极大地）修改了 Sphinx 生成 HTML 的方式。
- [Pacha: System Configuration Engine](http://pacha.cafepais.com/docs/index.html) 是我使用 Sphinx 实现的开源项目之一。
- [developerWorks 中国网站开源技术专区](http://www.ibm.com/developerworks/cn/opensource/)：获取大量 how-to 信息、工具和项目更新，以帮助您了解如何使用开源技术进行开发，以及如何同 IBM 产品相结合使用。
- [Twitter 上的 developerWorks](http://twitter.com/developerworks)：关注我们最新的新闻。
- 随时关注 developerWorks [技术活动](http://www.ibm.com/developerworks/cn/offers/techbriefings/)和[网络广播](http://www.ibm.com/developerworks/cn/swi/)。
- 访问 developerWorks [Open source 专区](http://www.ibm.com/developerworks/cn/opensource/)获得丰富的 how-to 信息、工具和项目更新以及[最受欢迎的文章和教程](http://www.ibm.com/developerworks/cn/opensource/best2009/index.html)，帮助您用开放源码技术进行开发，并将它们与 IBM 产品结合使用。