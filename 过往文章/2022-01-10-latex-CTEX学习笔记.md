---
title: latex-CTEX学习笔记
Slug:
Category:
tags:
  - CTEX
  - latex
Authors:
Date: 2022-01-10
Modified: 2022-01-10
Status: new
Summary:
---

*欢迎加入本人建的"LuaTeX ConTeXt 学习互助"群：431714622，互助学习LuaTeX ConTeXt LaTeX相关技术，以及通过Lua、Python实现格式化文本、数据的自动排版。*

**注意：为了避免本博客的jinja引擎误解析，已经在`{`+`#`之间，以及对应的`}`之前加入空格**

---

本文主要学习如下资料的笔记：

* 《一份（不太）简短的 LATEX 2ε 介绍》
* 《CTEX宏集手册？

## 环境

* TeX Live
* vscode插件LaTeX Workshop
* `pip install pylatex`

### 命令

* 用`texdoc`命令查看宏包文档，如`texdoc francyhdr`

## 中文支持

* [CTEX宏集手册](https://ctan.math.washington.edu/tex-archive/language/chinese/ctex/ctex.pdf)
* [CTEX宏集手册ctex-kit](https://github.com/CTeX-org/ctex-kit)

```python
doc = pylatex.Document()

# 引用ctex中文宏包
doc.packages.add(Package('ctex'))
# tex文件中实现：`\usepackage{ctex}%`

# 使用XeLaTeX或lualatex编译器
doc.generate_pdf('filename',compiler='latexmk', compiler_args=["-lualatex"], clean=True, clean_tex=False)
doc.generate_pdf('filename',compiler='lualatex', clean=True, clean_tex=False)
doc.generate_pdf('filename',compiler='xelatex', clean=True,  clean_tex=False)
# 插件中有配置
# tex文件中的实现：`% !TEX program = xelatex`

# 输出latex文件
doc.dumps()
```

[备考：](https://jdhao.github.io/2018/03/29/latex-chinese.zh/)

### 使用ctexart等中文文档类

> 如果用户需要在标准文档类的基础上添加中文支持和中文版式支持，我们建议用户使用 CTeX 宏集提供的四个中文文档类。CTeX 宏集提供了四个中文文档类：ctexart、ctexrep、ctexbook和ctexbeamer，分别对应 LaTeX 的标准文档类article、report、book和beamer。

```latex
\documentclass[UTF8]{ctexart}
\usepackage[T1]{fontenc}

\begin{document}
你好，这是一个测试文档。
\end{document}
```

使用 ctexart documentclass 时候，最好加上`\usepackage[T1]{fontenc}`，否则某些符号显示不正确。

ctex的ctexbook类包，适合书籍排版，参考
* [1](https://www.duguying.net/article/%E7%94%A8latex%E6%8E%92%E7%89%88%E4%B8%AD%E6%96%87%E4%B9%A6%E7%B1%8D/)


### 使用 ctex 宏包

> 用户在使用非标准文档类时，如果需要添加中文支持或中文版式支持，则可以使用ctex宏包。
> 有些文档类是建立在 LaTeX 标准文档类之上开发的。这时，给ctex宏包加上heading选项，可以将章节标题设置为中文风格。
> UTF8这个选项在 XeLaTeX 编译时写不写都无所谓，(pdf)LaTeX 编译时也即将写不写都无所谓。

```latex
\documentclass{article}
\usepackage[heading=true][UTF8]{ctex}
\begin{document}
你好，这是一个测试文档。
\end{document}
```

使用以上代码的 LaTeX 源文件可以使用 latex，pdflatex，xelatex 或者 lualatex 命令来编译生成 PDF 文件。CTeX 开发者推荐使用 xelatex 命令编译源文件。

## LaTeX Workshop配置

**不要设置编译失败后清理文件，否则影响二次编译**

```json
    // ah21 2021-07-01
    "latex-workshop.latex.tools": [
        {
            // 编译工具和命令
            "name": "xelatex",
            "command": "xelatex",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "-pdf",
                "%DOCFILE%"
            ]
        },
        {
            "name": "pdflatex",
            "command": "pdflatex",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "%DOCFILE%"
            ]
        },
        {
            "name": "bibtex",
            "command": "bibtex",
            "args": [
                "%DOCFILE%"
            ]
        }
    ],
   "latex-workshop.latex.recipes": [
    {
        "name": "xelatex",
        "tools": [
            "xelatex"
        ],
    },
    {
        "name": "pdflatex",
        "tools": [
            "pdflatex"
        ]
    },
    {
        "name": "xe->bib->xe->xe",
        "tools": [
            "xelatex",
            "bibtex",
            "xelatex",
            "xelatex"
        ]
    },
    {
        "name": "pdf->bib->pdf->pdf",
        "tools": [
            "pdflatex",
            "bibtex",
            "pdflatex",
            "pdflatex"
        ]
    }
  ],
    "latex-workshop.view.pdf.viewer": "browser",
    // 配置外部浏览器为SumatraPDF（新插件自带浏览器）
    // "latex-workshop.view.pdf.viewer": "external",
    // "latex-workshop.view.pdf.external.viewer.command": "D:/wares/SumatraPDF/SumatraPDF.exe",
    // "latex-workshop.view.pdf.external.viewer.args": [
    //     "-forward-search",
    //     "%TEX%",
    //     "%LINE%",
    //     "-reuse-instance",
    //     "-inverse-search",
    //     "\"D:/Users/ah21/AppData/Local/Programs/Microsoft VS Code/Code.exe\" \"D:/Users/ah21/AppData/Local/Programs/Microsoft VS Code/resources/app/out/cli.js\" -gr \"%f\":\"%l\"",
    //     "%PDF%"
    // ],
    // "latex-workshop.view.pdf.external.synctex.command": "D:/wares/SumatraPDF/SumatraPDF.exe",
    // "latex-workshop.view.pdf.external.synctex.args": [
    //     "-forward-search",
    //     "%TEX%",
    //     "%LINE%",
    //     "-reuse-instance",
    //     "-inverse-search",
    //     "code \"D:/Users/ah21/AppData/Local/Programs/Microsoft VS Code/resources/app/out/cli.js\" -gr \"%f\":\"%l\"",
    //     "%PDF%",
    // ],
```
## 建立新命令、新环境、宏包和文档类

```latex
\newcommand{name}[num]{definition} % 不得重名
\providecommand{name}[num]{definition} % 如果没有同名命令则提供此处定义的命令，如果有则用原来的命令；适用于多次重载防重名
\renewcommand{name}[num]{definition} % 更新命令
```

```latex
\newcommand{\txsit}[1]{
    This is the \emph{ #1 } Short Introduction to \LaTeXe
    }
% in the document body:
\begin{itemize}
\item \txsit{not so}
\item \txsit{very}
\end{itemize}
```

```latex
\newenvironment{name}[num]{before}{after}
```

```latex
\newenvironment{king}
{\rule{1ex}{1ex}%
    \hspace{\stretch{1}}}
{\hspace{\stretch{1}}%
    \rule{1ex}{1ex}}
\begin{king}
My humble subjects \ldots
\end{king}
```

```latex
% 以.sty后缀保存
\ProvidesPackage{demopack} % 定义名称，载入时如果重名会提供提示
\newcommand{\tnss}{The not so Short Introduction to \LaTeXe}
\newcommand{\txsit}[1]{The \emph{ #1 } Short Introduction to \LaTeXe}
\newenvironment{king}{\begin{quote}}{\end{quote}}
```

### 坚持使用\newcommand*

```latex
\def\examplea #1 {% #1 cannot contain \par
}
\long\def\exampleb #1 {% #1 can contain \par
}
```

\newcommand是\def的包装:

```latex
\newcommand{\examplea}[1]{% #1 can contain \par
}
\newcommand*{\exampleb}[1]{% #1 cannot contain \par
}
```

***Most of the time, \newcommand* is the best choice as you want the error-checking that it provides. That is why examples given by experienced LaTeX users normally use this form, rather than just \newcommand.***

\newenvironment的情况也是一样:

```latex
\newenvironment{examplea}[1]{% #1 can contain \par
}{}
\newenvironment*{exampleb}[1]{% #1 cannot contain \par
}{}
```

对于LaTeX3则使用杂交方法，如果使用xparse定义文档命令，那么long版用加号标记：

```latex
\NewDocumentCommand\examplea{m}{% #1 cannot contain \par
}
\NewDocumentCommand\examplab{+m}{% #1 can contain \par
}
```

### xparse 宏包更灵活

* \NewDocumentCommand\⟨name⟩{⟨arg spec⟩}{⟨definition⟩}
* \NewDocumentEnvironment{⟨name⟩}{⟨arg spec⟩}{⟨before⟩}{⟨after⟩}

## 编写宏包

写一个宏包的基本工作就是将原本在你的文档导言区里很长的内容拷贝到另一个文件中去，
这个文件需要以 .sty 作扩展名。你还需要加入一个宏包专用的命令：

\ProvidesPackage{⟨package name⟩}

```latex
% 包名要与文件名同名demopack.sty
\ProvidesPackage{demopack}
% 调用宏包
% \RequirePackage[⟨options⟩]{⟨package name⟩} %与\usepackage 一致
\newcommand{\tnss}{The not so Short Introduction to \LaTeXe}
\newcommand{\txsit}[1]{The \emph{ #1 } Short Introduction to \LaTeXe}
\newenvironment{king}{\begin{quote}}{\end{quote}}
```

## 编写文档类

```latex
% 类名要与文件名同名demopack..cls
\ProvidesClass{demopack}
% \RequirePackage[⟨options⟩]{⟨package name⟩} %与\usepackage 一致
% 调用文档类并修改
\LoadClass[⟨options⟩]{⟨package name⟩} % 与\documentclass 十分相像
...
```

## LATEX 可定制的一些命令和参数

见《一份（不太）简短的 LATEX2ε 介绍》

## pylatex

If you want to combine xelatex (for Chinese) and latexmk (for compiling multiple times) you should be able to use this:

```python
doc.generate_pdf(compiler='latexmk', compiler_args=['-xelatex'])
```

## markdown宏包

* [markdown宏包](https://ctan.org/pkg/markdown)
* [文档](https://ctan.math.illinois.edu/macros/generic/markdown/markdown.html#using-latex)
* [库](https://github.com/witiko/markdown)

编译：

```shell
latexmk -cd -lualatex -silent markdown.tex
```

## 自动化排版/datatool宏包

参考*LATEX for Administrative Work*，其目标是简化行政管理事务，如回信（邮件合并），执行重复任务，排印问题清单或试卷，等等；常涉及使用外部数据问题。

```latex
% 新建数据库
\DTLnewdb{⟨db name⟩}
% 新建全局数据库
\DTLgnewdb{⟨db name⟩}
```

```latex
% 加载数据库
\DTLloaddb[⟨options⟩]{⟨db name⟩}{⟨filename⟩}
% 保存数据库
\DTLsavedb{⟨db name⟩}{⟨filename⟩}
`
```latex
\DTLnewdb{mydata}
% 新建数据行
\DTLnewrow{mydata}%
\DTLnewdbentry{mydata}{FirstName}{John}%
\DTLnewdbentry{mydata}{Surname}{Smith, Jr}%
\DTLnewdbentry{mydata}{Score}{68}%
\DTLnewrow{mydata}%
\DTLnewdbentry{mydata}{FirstName}{Jane}%
\DTLnewdbentry{mydata}{Surname}{Brown}%
\DTLnewdbentry{mydata}{Score}{75}%
\DTLnewrow{mydata}%
\DTLnewdbentry{mydata}{FirstName}{Andy}%
\DTLnewdbentry{mydata}{Surname}{Brown}%
\DTLnewdbentry{mydata}{Score}{42}%
\DTLnewrow{mydata}%
\DTLnewdbentry{mydata}{FirstName}{Z\"oe}%
\DTLnewdbentry{mydata}{Score}{52}%
\DTLnewdbentry{mydata}{Surname}{Adams}%
\DTLsavedb{mydata}{mydata.csv}
```
``

```latex
% 迭代数据行
\DTLforeach[⟨condition⟩]{⟨db-name⟩}{⟨assign⟩}{⟨body⟩}
% 加星/只读版，更快
\DTLforeach*[⟨condition⟩]{⟨db-name⟩}{⟨assign⟩}{⟨body⟩}
% 环境版
\begin{DTLenvforeach}[⟨condition⟩]{⟨db name⟩}{⟨assign list⟩}
\begin{DTLenvforeach*}[⟨condition⟩]{⟨db name⟩}{⟨assign list⟩}

% 高级迭代（适合大数据库）：
\dtlforeachkey(⟨key cs⟩,⟨col cs⟩,⟨type cs⟩,⟨header cs⟩)\in{⟨db⟩}\do{⟨body⟩}
```




### datagidx宏包

**警告：排序、校勘效率不如外部程序，如使用`\printterms`时会排序，好处在于*内置*。**

\loadgidx[⟨options⟩]{⟨filename⟩}{⟨title⟩}
定义索引和词汇
\newgidx[⟨options⟩]{⟨label⟩}{⟨title⟩}
\newgidx{index}{Index}
定义术语
\newterm[⟨options⟩]{⟨name⟩}

## label

\label 命令可用于记录各种类型的交叉引用，使用位置分别为：

* 章节标题 在章节标题命令 \section 等之后紧接着使用。
* 行间公式 单行公式在公式内任意位置使用；多行公式在每一行公式的任意位置使用。
* 有序列表 在 enumerate 环境的每个 \item 命令之后、下一个 \item 命令之前任意位置使用。
* 图表标题 在图表标题命令 \caption 之后紧接着使用。
* 定理环境 在定理环境内部任意位置使用。

在使用不记编号的命令形式（\section*、\caption*3、带可选参数的 \item 命令等）时不要使用 \label 命令，否则生成的引用编号不正确。

## refcount引用计算宏包

\thepage，单页构造时才准确，所以只能用在页眉页脚

```latex
\label{标签}
% 标签的页码引用不能展开，含有额外信息
\pageref{标签}
% 把标签的页码引用转为数字
\getpagerefnumber{标签}
```

## 脚注

有些情况下（比如在表格环境、各种盒子内）使用 \footnote{} 并不能自动生成脚注。可以先再需要的位置用 \footnotemark 标记（计数），再在合适的位置用 \footnotetext{} 生成脚注。

## 边注

\marginpar[⟨left-margin⟩]{⟨right-margin⟩}

\marginpar{\footnotesize 边注较窄，不要写过多文字，最好设置较小的字号。}

## 列表

有序列表：

```latex
% 可定义各级行头符号
\renewcommand{\labelenumi}{\Alph{enumi}>}
\renewcommand{\labelenumii}{\Alph{enumi}>}
\begin{enumerate}
    \item An item.
    \begin{enumerate}
        \item A nested item.\label{itref}
        \item[*] A starred item.
    \end{enumerate}
    \item Reference(\ref{itref}).
\end{enumerate}
```

无序列表：

```latex
% 可定义各级行头符号
\renewcommand{\labelitemi}{\ddag}
\renewcommand{\labelitemii}{\dag}
\begin{itemize}
\item First item
\end{itemize}
```

描述列表（参数中是要描述的关键词）：

```latex
\begin{description}
    \item[⟨item title⟩] …
\end{description}
```

## 引用环境

quote 用于引用较短的文字，首行不缩进；quotation 用于
引用若干段文字，首行缩进。verse 用于排版诗歌，首行悬挂缩进。

参考 https://wiki.contextgarden.net/Verse

## 代码环境

verbatim、verbatim*（显式空格）

行内式：

\verb⟨delim⟩⟨code⟩⟨delim⟩，⟨delim⟩ 标明代码的分界位置，前后必须一致，除字母、空格或星号外，可任意选择使得不与代码本身冲突，习惯上使用 | 符号。\verb 命令对符号的处理比较复杂，一般不能用在其它命令的参数里，否则多半会出错。

还可考虑verbatim、fancyvrb、listings（带高亮）宏包。

## 表格环境

tabular 环境会与文字直接混排，所以一般会放在 table 浮动体环境中，并用 \caption 命令加标题。

## 插图

由graphicx宏包支持。

\includegraphics[⟨options⟩]{⟨filename⟩}，文件名里既不要有空格（类似 \include），也不要有多余的英文点号。

声明多个图片路径：\graphicspath{\{figures/}{logo/}\}

## 盒子

所有盒子参考：

https://tex.stackexchange.com/questions/83930/what-are-the-different-kinds-of-boxes-in-latex

盒子是 LATEX 排版的基础单元，虽然解释略有抽象：每一行是一个盒子，里面的文字从左到右依次排列；每一页也是一个盒子，各行文字从上到下依次排布。

LATEX 提供了一些命令让我们手动生成一些有特定用途的盒子。

生成水平盒子（不能断行）：

\mbox{…}
\makebox[⟨width⟩][⟨align⟩]{…}

带框的水平盒子（语法同上）：

\fbox{…}
\framebox[⟨width⟩][⟨align⟩]{…}

多行的垂直盒子/段落盒子、迷你页：

\parbox[⟨align⟩][⟨height⟩][⟨inner-align⟩]{⟨width⟩}{…}
\begin{minipage}[⟨align⟩][⟨height⟩][⟨inner-align⟩]{⟨width⟩}
…
\end{minipage}

标尺盒子：实心的矩形盒子，也可适当调整以用来画线（标尺）：
\rule[⟨raise⟩]{⟨width⟩}{⟨height⟩}

## 浮动体

LATEX 预定义了两类浮动体环境 figure 和 table

\begin{table}[⟨placement⟩]
…
\end{table}

⟨placement⟩ 参数提供了一些符号用来表示浮动体允许排版的位置，如 hbp 允许浮动体排版在当前位置、底部或者单独成页。table 和 figure 浮动体的默认设置为 tbp。

* h 当前位置（代码所处的上下文）
* t 顶部
* b 底部
* p 单独成页
* ! 在决定位置时忽视限制

排版位置的选取与参数里符号的顺序无关，LATEX 总是以 h-t-b-p 的优先级顺序决定浮动体位置。

限制包括浮动体个数（除单独成页外，默认每页不超过 3 个浮动体，其中顶部不超过 2 个，底部不超过 1 个）以及浮动体空间占页面的百分比（默认顶部不超过 70%，底部不超过 30%）。

table* 和 figure* 环境用来排版跨栏的浮动体，⟨placement⟩ 参数只能用 tp 两个位置。

标题：\caption{…}  \caption*{}（无编号）

生成两种浮动体的目录（单独的章节）：

\listoftables
\listoffigures

## 批量修改

* 通过`\includegraphics`把pdf页面作为插图插入，然后用`textpos`包添加文本，比如生成字典页眉的字头索引
    * 使用[pdfpages包](https://www.ctan.org/pkg/pdfpages)插入，相当于页面直接占位，不能添加元素
* tex文件的邮件合并，根据一个模板，每条数据生成一套页面

## 用latex3/expl3编写宏包

* [LaTeX3 教程（一）——背景知识](https://zhuanlan.zhihu.com/p/92851140)
* [LaTeX3 教程（三）——从一个例子说起](https://zhuanlan.zhihu.com/p/92854940)
* [LaTeX3 教程（二）——语法概要](https://zhuanlan.zhihu.com/p/92853481)

### 函数

LaTeX3 中的函数名包括三部分：模块名（module）、描述（description）以及参数指定（arg-spec），形如

```
\<module>_<description>:<arg-spec>
```

参数指定包括：

* n：普通（normal）参数，表示一组由大括号{...}包围的 token（记号，或者叫字元）列表，这其实就是TeX 中的标准宏参数
* N：表示单个 token，比如一个控制序列（由\开头的命令），或者一个单独的字符
* p：原始 TeX 的形参（parameter）指定。具体来说，就是我们在用\def定义新命令时所用的#1、#1#2等
* T, F：这两个是n的特殊情况，用来给出条件分支（True、False）
* D：表示不要使用（Do not use）。由D开头的命令是原语的封装，在l3kernel之外尽量不要直接使用（当然有时候不可避免）
* w：奇异型（weird）参数，表示不遵循标准参数指定的一些特例

开启 LaTeX3 语法后，单词间的空格是不起任何作用的（catcode=9，即可忽略字符）。确实要使用空格时，则用~代替。至于需要使用~的原来意思，即不可断开的空格（俗称「带子」）时，可以用原来的宏\nobreakspace

### 变量

LaTeX3 中，变量的名称包括四个部分：作用域（scope）、模块名（module）、描述（description）以及变量类型（type），形如

```
\<scope>_<module>_<description>_<type>
```

变量的作用域有三种：

* c：表示常数（constant），即一旦创建，就不应该改变它的值
* g：全局变量（global），它的值是全局有效的，也就是分组（{...}）对其无效。常见的例子比如某些计数器变量
* l：局部变量（local），顾名思义，它们的值只在局部有效。在大多数情况下，我们使用的都是l型变量

### 变量类型

我们知道，C 语言里面有int、double、char这样的数据类型。TeX 中也有类似的概念，称为寄存器（register）。寄存器共有 6 种：

* count：计数器，相当于整型变量
* toks：记号变量（**tok**en**s**）
* box：盒子变量
* dimen：刚性长度（**dimen**sion）
* skip：弹性长度
* muskip：数学弹性长度（**skip** in **m**ath **u**nit）

LaTeX3 在 TeX 的基础上做了很大的扩充，新定义了一些新的变量类型。

以下这几种直接继承了前面所说的寄存器类型：

* box
* int <-- count (**int**eger)
* dim <-- dimen
* skip
* muskip

以下是 LaTeX3 新定义的变量类型，它们大多只是一些特殊的宏：

* 数据结构：
  * tl：记号列表（**t**oken **l**ist）
  * str：字符串（**str**ing），它与tl的区别在于忽略了类别码（除空格外全部设为其他类 12，空格仍为 10）
  * seq：序列（**seq**uence），栈
  * clist：逗号分隔列表（**c**omma **list**）
  * prop：属性列表（**prop**erty list），即关联列表
  * fp：浮点数（**f**loating **p**oints）
  * intarray、fparray：整型、浮点型数组（**int**eger/**f**loating **p**oint **array**）
* 盒子的推广：
  * coffin：带「把手」的盒子
* 其他：
  * bool：布尔型变量
  * token：记号
  * ior、iow：输入、输出流（**I/O****r**ead/**w**rite）
  * regex：正则表达式（**reg**ular **ex**pression）

还有几种比较特殊的变量，它们不遵循通常的命名规则：

* quark：「夸克」，是展开到自身的宏
* mark：扫描标记

### 编程环境

之前我们就已经提到过，LaTeX3 目前为止还没有成为一个独立的格式。使用 LaTeX3，仍然需要在 LaTeX 2_ε_ 中调用expl3宏包。

如果只在一个.tex文件中使用，可以这样做：

    \documentclass{article}
    \usepackage{expl3}

    \ExplSyntaxOn   % 开启 LaTeX3 编程环境
    ...
    \ExplSyntaxOff  % 关闭 LaTeX3 编程环境

如果是要编写宏包或文档类，标准做法与在 LaTeX 2_ε_ 中类似：

    \RequirePackage{expl3}

    % 宏包使用 \ProvidesExplPackage
    % 文档类使用 \ProvidesExplClass
    % 其他文件使用 \ProvidesExplFile
    \ProvidesExplPackage{<package>}{<data>}{<version>}{<description>}

    % 之后开启 LaTeX3 语法，文件末尾处则会自动关闭



## 对齐

### 文档流对齐开关

也可以使用命令\raggedright、\centering和\raggedleft使以后的文本按指定方式对齐.

### 一行文本对齐

\leftline{左对齐}
\centerline{居中}
\rightline{右对齐}

### 多行文本或段落对齐

左对齐、居中、右对齐

\begin{flushleft}...\end{flushleft}
\begin{center}...\end{center}
\begin{flushright}...\end{flushright}

### LaTeX**公式**对齐

1. 默认情况下公式是居中对齐的，但若希望改成左对齐可以

\documentclass[a4paper,fleqn]{article}

这对整篇文章都有效。

2. 对某**一行公式**进行左对齐

 \begin{flalign}
your equation (1)
\end{flalign} \begin{flalign} your equation (1) \end{flalign}

3. 对某**一个公式**左对齐

 some text here\\

 y o u r e q u a t i o n h e r e yourequationhere

\\
and more text here.

3. 对某**几行公式**

 \begin{flalign}
\begin{split}
your equation (1)
your equation (2)
\end{split}&
\end{flalign} \begin{flalign} \begin{split} your equation (1) your equation (2) \end{split}& \end{flalign}

4.
加载amsmath宏包后，使用选项fleqn（就是声明加载宏包时使用\usepackage[fleqn]{amsmath}）可以使本该居中对齐的行间公式改为左对齐.

### \hfill 平均填充以对齐

## minipage
水平间距\hspace{2em}  \hspace \hfill
垂直间距\vspace{2em}  \vspace \vfill

\hspace*{3em}%
\begin{minipage}{0.75\textwidth}%
{\kaishu 鹅鹅鹅，

曲项向天歌。

白毛浮绿水，

红掌拨清波。

} %
\end{minipage}%

## 缩进

\setlength{\parindent}{4em}
\setlength{\parindent}{0pt}
\noindent 仅仅本段起效
\indent 仅仅本段起效

## 分栏

\onecolumn
\twocolumn[⟨one-column top material⟩]

双栏模式时\newpage是换栏，\clearpage才是换页

使用宏包multicol
\setlength{\columnsep}{1cm} % 栏间距
\setlength{\columnseprule}{1pt} % 分割线
\begin{multicols}{3}
...
\end{multicols}



## 段落格式

以下长度分别为段落的左缩进、右缩进和首行缩进：

* \setlength{\leftskip}{20pt}
* \setlength{\rightskip}{20pt}
* \setlength{\parindent}{2em}

\noindent
\indent 多个 \indent 命令的效果可以累加。

## 特殊符号

`# $ % & { } _ ^ ~ \`
`\# \$ \% \& \{ \} \_ \^{} \~{} \textbackslash`

手动换行符`\\`

分段命令`\par`

强制空格`\ `

英文单、双引号`` `' ``, ` ``'' `

英文连词、连数、连词
```tex
daughter-in-law, X-rated\\
pages 13--67\\
yes---or no?
```

断词`\-`

中文+、-号宽度不一样，减号改成行内公式`$-$`


英文省略号
\dots
\ldots

可以使用字符 ~ 输入一个不会断行的空格（高德纳称之为 tie，“带子”），通常用在英文人名、图表名称等上下文环
境

## 手动断行和分页

```tex
\\[⟨length⟩]
\\*[⟨length⟩] % 禁止断行处分页
\newline % 不能用于表格中

\newpage % 多栏模式中只是换新栏
\clearpage

% 参数0-4，表示强度，默认为4
\linebreak[⟨n⟩]
\nolinebreak[⟨n⟩]
\pagebreak[⟨n⟩]
\nopagebreak[⟨n⟩]
```

## 文档元素

**章节：**

三个标准文档类 article、report 和 book提供了划分章节的命令：

* \chapter{⟨title⟩}（只在 report 和 book 文档类有定义）；\chapter*标题不带编号，也不生成目录项和页眉页脚，但可以用\addcontentsline{toc}{⟨level⟩}{⟨title⟩}手动加上
* \section[⟨short title⟩]{⟨title⟩}；\section*{⟨title⟩}，参前
* \subsection{⟨title⟩}
* \subsubsection{⟨title⟩}
* \paragraph{⟨title⟩}
* \subparagraph{⟨title⟩}

\part 命令，用来将整个文档分割为大的分块，但不影响 \chapter或 \section 等的编号

较低层次如 \paragraph 和 \subparagraph 即使不用带星号的变体，生成的标题默认也不带编号，事实上，除 \part 外：

• article 文档类带编号的层级为 \section / \subsection / \subsubsection 三级；
• report / book 文档类带编号的层级为 \chapter / \section / \subsection 三级。

**文档结构的划分:**

\appendix 命令将正文和附录分开。book文档类还提供了结构的划分命令：
* \frontmatter 前言部分，页码使用小写罗马数字；其后的 \chapter 不编号。
* \mainmatter 正文部分，页码使用阿拉伯数字，从 1 开始计数；其后的章节编号正常。
* \backmatter 后记部分，页码格式不变，继续正常计数；其后的 \chapter 不编号。


文档结构示例：

```latex
\documentclass{book}

% 导言区，加载宏包和各项设置，包括参考文献、索引等
\usepackage{makeidx} % 调用 makeidx 宏包，用来处理索引
\makeindex % 开启索引的收集
\bibliographystyle{plain} % 指定参考文献样式为 plain

\begin{document}

\frontmatter % 前言部分
\maketitle % 标题页
\include{preface} % 前言章节 preface.tex
\tableofcontents

\mainmatter % 正文部分
\include{chapter1} % 第一章 chapter1.tex
\include{chapter2} % 第二章 chapter2.tex
...
\appendix % 附录
\include{appendixA} % 附录 A appendixA.tex
...

\backmatter % 后记部分
\include{epilogue} % 后记 epilogue.tex
\bibliography{books} % 利用 BibTeX 工具从数据库文件 books.bib 生成参考文献
\printindex % 利用 makeindex 工具生成索引

\end{document}
```

## 字号

```tex
He likes {\LARGE large and
{\small small} letters}.
He likes large and small letters.
```

**设定任意大小的字号：**
`\fontsize{⟨size⟩}{⟨base line-skip⟩}`

表 5.3: 标准文档类中的字号大小
|字号         | 10pt 选项（默认）| 11pt 选项|12pt 选项|
|-------------|-----------------|---------|---------|
|\tiny        |5pt              |6pt      |6pt      |
|\scriptsize  |7pt              |8pt      |8pt      |
|\footnotesize|8pt              |9pt      |10pt     |
|\small       |9pt              |10pt     |10.95pt  |
|\normalsize  |10pt             |10.95pt  |12pt     |
|\large       |12pt             |12pt     |14.4pt   |
|\Large       |14.4pt           |14.4pt   |17.28pt  |
|\LARGE       |17.28pt          |17.28pt  |20.74pt  |
|\huge        |20.74pt          |20.74pt  |24.88pt  |
|\Huge        |24.88pt          |24.88pt  |24.88pt  |

## 字体

两类命令、三种形式

* \bfseries 影响之后所有的字符
* {\bfseries ⟨sometext⟩}
* \textbf{}

| -           | -              | -           | -                        |
| ----------- | -------------- | ----------- | ------------------------ |
| \rmfamily   | \textrm{…}     | roman       | 衬线字体（罗马体、宋体） |
| \sffamily   | \textsf{…}     | sans serif  | 无衬线字体、微软雅黑     |
| \ttfamily   | \texttt{…}     | typewriter  | 等宽字体、仿宋           |
| \mdseries   | \textmd{…}     | medium      | 正常粗细（中等）         |
| \bfseries   | \textbf{…}     | bold face   | 粗体                     |
| \upshape    | \textup{…}     | upright     | 直立体                   |
| \itshape    | \textit{…}     | italic      | 意大利斜体、楷书         |
| \slshape    | \textsl{…}     | slanted     | 倾斜体、楷书             |
| \scshape    | \textsc{…}     | SMALL CAPS  | 小型大写字母             |
| \em         | \emph{…}       | emphasized  | 强调，默认斜体           |
| \normalfont | \textnormal{…} | normal font | 默认字体                 |


Font Family 字体族相应的数字字体族（基于 fontspec 宏包的 unicode-math 宏包）：

* \mathsf
* \mathtt
* \mathit

Font Shape 字体形状相应的数学字体系列：

* mathbb：blackboard bold，黑板粗体
* mathcal：calligraphy（美术字）
* mathrm：math roman
* mathbf：math boldface

加载更多英文字体使用的命令为 fontspec 宏包的 \newfontfamily<命令>[(可选项)]{<字体名>}。xeCJK 宏包（ctex宏包或文档类[包括我们这里的 ctexart 文档]会自动调用）中对应的命令为 \setCJKfamilyfont{<中文字体族>}[<可选项>]{字体名}。

```latex
\setCJKfamilyfont{hwhp}{华文琥珀}
\newcommand{\hwhp}{\CJKfamily{hwhp}}
\newfontfamily\tempus{Tempus Sans ITC}

{\hwhp 这是一段华文琥珀文字, english not work}
{\tempus this is Technic font, 中文不起作用}
```

另，中英文分开设置（未测试）：

```latex
% 英文字体配置部分
\setmainfont{Source Serif Pro}%Times New Roman
\setsansfont{Source Sans Pro}
\setmonofont{Source Code Pro}
% 中文字体配置部分
\usepackage{xeCJK}%中文字体
\setCJKmainfont{宋体}%正文字体
\setCJKsansfont{黑体}%无衬线字体
\setCJKmonofont{楷体}%等宽字体
\setCJKfamilyfont{boldsong}{Source Han Serif SC Heavy}
```

查看系统字体

* fc-list 命令可以列出系统中可以使用的字体
* fc-list :lang=zh 可以单独列出支持中文的字体.



## 字体设置

xelatex 和 lualatex 命令下支持用户调用字体的宏包是 fontspec。宏包提供了几个设置全局字体的命令，设置 \rmfamily 等对应命令的默认字体：

* \setmainfont{⟨font name⟩}[⟨font features⟩]
* \setsansfont{⟨font name⟩}[⟨font features⟩]
* \setmonofont{⟨font name⟩}[⟨font features⟩]

其中 ⟨font name⟩ 使用字体的文件名（带扩展名）或者字体的英文名称。⟨font features⟩ 用来手动配置对应的粗体或斜体，比如为 Windows 下的无衬线字体 Arial 配置粗体和斜体（通常情况下自动检测并设置对应的粗体和斜体，无需手动指定）：

\setsansfont{Arial}[BoldFont={Arial Bold}, ItalicFont={Arial Italic}]

fontspec 宏包会覆盖数学字体设置。需要调用表 5.4 中列出的一些数学字体宏包时，应当在调用 fontspec 宏包时指定 no-math 选项。fontspec 宏包可能被其它宏包或文档类（如 ctex 文档类）自动调用时，则在文档开头的 \documentclass 命令里指定 no-math 选项。

## CTEX字体

ctex 宏包或文档类提供了和 fontspec 宏包非常类似的语法设置中文字体3：

* \setCJKmainfont{⟨font name⟩}[⟨font features⟩]
* \setCJKsansfont{⟨font name⟩}[⟨font features⟩]
* \setCJKmonofont{⟨font name⟩}[⟨font features⟩]

由于中文字体少有对应的粗体或斜体，⟨font features⟩ 里多用其他字体来配置，比如在 Win-
dows 中设定基本字体为宋体，并设定对应的 BoldFont 为黑体，ItalicFont 为楷体：

\setCJKmainfont{SimSun}[BoldFont=SimHei, ItalicFont=KaiTi]

关闭自动字体检测，手动指定字族（方正founder、adobe、windows、ubuntu、none、⟨name⟩）：

```latex
\documentclass[fontset = none]{ctexart}
\ctexset{fontset = founder}
\begin{document}
在文档类选项中声明 \verb|fontset = none|，随后在导言区用 \verb|\ctexset|指定字体。
\end{document}
```

设置⟨name⟩会自动调用用户设置的、名为`ctex-fontset-⟨name⟩.def`的文件进行字体配置。请先保证文件的存在。可以在当前工作目录或者本地 TDS目录树下合适位置建立一个名为 ctex-fontset-⟨name⟩.def 的文件。字体配置文件的具体写法可以参考 CTEX宏集 fontset 目录下的字体配置文件。


CTEX 宏集预定义的中文字库还定义了一些字体命令。 除了在 ubuntu 字库中没有\fangsong 的定义外，所有字库都有以下四个字体命令：

* \songti 宋体，CJK 等价命令 \CJKfamily{zhsong}。
* \heiti 黑体，CJK 等价命令 \CJKfamily{zhhei}。
* \fangsong 仿宋，CJK 等价命令 \CJKfamily{zhfs}。
* \kaishu 楷书，CJK 等价命令 \CJKfamily{zhkai}。

在 windows、founder 和 macnew 字库中，额外定义了 \lishu 和 \youyuan：
* \lishu 隶书，CJK 等价命令 \CJKfamily{zhli}。
* \youyuan 圆体，CJK 等价命令 \CJKfamily{zhyou}。

在 windows 字库中还定义了 \yahei。出于兼容性的考虑，\yahei 命令在 macnew 字库中也有定义，但实际调用苹方黑体：
* \yahei 微软雅黑，CJK 等价命令 \CJKfamily{zhyahei}。

在 macnew 字库中，还定义了 \pingfang：
* \pingfang 苹方黑体，CJK 等价命令 \CJKfamily{zhpf}。

普通字体: \textnormal{} 或者 \normalfont, 相当于 \rmfamily\mdseries\upshape. 另外还有强调命令也会改变字体, 但是含义和直接对字体属性的改变不同, \emph{} 或者 \em.

**以上CTEX字体命令只能作用于汉字，\textrm{} 或 \rmfamily系列命令有关联的西文字体。**

## 自定义cjk字体

字体名称，应该是先找到字体文件，然后用字体查看器观察，用上面“信息”中显示的名字。

```latex
% 设置CJKfamily字体。注意顺序，第一个定义的就是默认字体
\setCJKfamilyfont{song}{FZShuSong-Z01S}% 方正书宋简体
\newcommand{\song}{\CJKfamily{song}}%
\setCJKfamilyfont{kaiti}{FZKai-Z03S}% 方正楷体简体
\newcommand{\kaiti}{\CJKfamily{kaiti}}%
\setCJKfamilyfont{heiti}{FZHei-B01S}% 方正黑体简体
\renewcommand{\heiti}{\CJKfamily{heiti}}% 因为原命令已被ctex中定义过，所以这里重定义
\setCJKfamilyfont{fangsong}{FZFangSong-Z02S}% 方正仿宋简体
\renewcommand{\fangsong}{\CJKfamily{fangsong}}% 因为原命令已被ctex中定义过，所以这里重定义
```

## geometry/页面设置

```latex
\documentclass[a4paper]{article}
\usepackage{geometry}
\geometry{left=2.5cm,right=2.5cm,top=2.5cm,bottom=2.5cm}
\begin{document}
test
\end{document}
```

常用的长度选项还有head（head的高度）, headsep（head与版心的距离）, foot（foot的高度），见下图

### 临时调整

在需要调整页边距的页面，加入下面命令：

\newgeometry{left=3cm,bottom=1cm}

如果需要恢复到原来的页边距，用下面的命令：

\restoregeometry

## pythontex

```latex
%!TEX TS-program = Arara
% arara: pdflatex: {shell: yes}
% arara: pythontex
% arara: pdflatex: {shell: yes}
\documentclass[12pt]{article}
\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\usepackage{pythontex} % <--
\begin{document}

\py{2+2}

\end{document}
```

PythonTEX offers various inline commands:

* `\py{<expression>}` prints value of expression
* `\pyc{<code>}` executes code, output goes to STDOUT
* `\pys{<code>}` supports variable and expression substitution
* `\pyb{<code>}` execute code and prettyprint result
* `\pyv{<code>}` prettyprint code

PythonTEX also offers various environments:

* `pycode` executed, but not typeset
* `pysub` variable and expression substitution
* `pyverbatim` typeset, but not executed
* `pyblock` executed and typeset
* `pyconsole` simulates an interactive Python-console

```latex
\documentclass[12pt]{article}
\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\usepackage{pythontex}
\usepackage{booktabs}
\begin{document}

\pyc{from yahoo_fin import stock_info as si}

\begin{tabular}{lr} \toprule
Company & Latest quote \\ \midrule
Apple & \py{round(si.get_live_price("aapl"),2)} \\
Amazon & \py{round(si.get_live_price("amzn"),2)} \\
Facebook & \py{round(si.get_live_price("fb"),2)} \\ \bottomrule
\end{tabular}

\end{document}
```


### 自定义

```latex
\usepackage{fancyvrb}
\makeatletter
\newenvironment{pycode}[1]%
{\xdef\d@tn@me{ #1 }\xdef\r@ncmd{python #1.py > #1.plog}%
\typeout{Writing file #1}\VerbatimOut{ #1.py}%
}
{\endVerbatimOut %
\toks0{\immediate\write18}%
\expandafter\toks\expandafter1\expandafter{\r@ncmd}%
\edef\d@r@ncmd{\the\toks0{\the\toks1}}\d@r@ncmd %
\noindent Input
\inputminted{python}{\d@tn@me.py}%
\noindent Output
\inputminted{text}{\d@tn@me.plog}%
}
\makeatother

\begin{document}

\begin{pycode}{abc}
import pandas as pd
print(pd.__version__);
print(1+123424)
\end{pycode}


\end{document}
```



## 在天头中通过页码总计每页counter计数

将每页对象的展开数量存储在一个\prop列表（l3prop属性列表，键值对列表）中，并在天头中提取该列表的值（l3keys键值对列表），天头中属性名称只有页码（希望中间没有重置）；不过对象的数量必须被评估一次以上（在一个循环中），这只更新键-值（因为属性-列表有一个唯一的键-值关系，旧值会被覆盖）。

```latex
\documentclass{article}

\usepackage{fancyhdr}
\pagestyle{fancy}

\usepackage{zref-perpage,expl3}
\newcounter{mycounter}
\zmakeperpage{mycounter}


\lhead{on this page are \propextract\ objects}



\begin{document}\pdfsetrandomseed 4

\ExplSyntaxOn
\cs_generate_variant:Nn \prop_item:Nn {No,Nx}
\cs_generate_variant:Nn \prop_gput:Nnn {Nxx}
\cs_new:Npn \propextract{%
    \prop_item:Nx \g_ufischer_object_counter {\thepage}
}
\prop_new:N \g_ufischer_object_counter
\int_step_inline:nnnn {1 }{1}{80}
{\refstepcounter{mycounter}\themycounter.~an~object \\
    \int_if_odd:nT {\fp_eval:n { randint( 9 ) }}{some~randomness\\}
    \prop_gput:Nxx \g_ufischer_object_counter {\thezpage} {\themycounter}

}
\ExplSyntaxOff
\end{document}
```

## 计算文本的实际行数/高度以便文本适配

* https://stackoverflow.com/questions/2939450/get-height-on-a-block-of-latex-output
* https://tex.stackexchange.com/questions/317713/count-lines-in-a-block-of-text

通过盒子测量高度：

```latex
\newdimen\height
\setbox0=\hbox{\Huge Hello, World!}
\height=\ht0 \advance\height by \dp0
The height is: \the\height
```

使用`adjustbox`把内容包括在给定宽度`minipage`中，然后存储它的高度，然后用计算`baselineskip的`个数，最后废弃内容。

```latex
\documentclass{article}
\usepackage{adjustbox}

\newlength\mycalclength

\newcommand\CalcNumber[3]{%
    \adjustbox{minipage={ #2 }, gstore totalheight=\mycalclength, discard}{ #1 }%
    \edef#3{\the\numexpr\dimexpr \mycalclength/\baselineskip\relax\relax}%
}

\begin{document}

\CalcNumber{A}{\columnwidth}{\AHeight}\AHeight

\CalcNumber{M M M M M M M}{1em}{\MMMHeight}\MMMHeight

\end{document}
```

If you rearrange the order of the arguments and have the content last, you can use the feature of \adjustbox to read the last argument as box, not as argument, allowing for verbatim and other special content. Note that the assignment is then global as it happens in a group.

```latex
\documentclass{article}
\usepackage{adjustbox}

\newlength\mycalclength

\newcommand\CalcNumber[2]{%
    \adjustbox{minipage={ #1 }, gstore totalheight=\mycalclength, execute={\gdef#2{\the\numexpr\dimexpr \mycalclength/\baselineskip\relax\relax}}, discard}
}

\begin{document}

\CalcNumber{\columnwidth}{\AHeight}{A}\AHeight

\CalcNumber{1em}{\MMMHeight}{M M M M M M M}\MMMHeight

\end{document}
```

FWIW, ConTeXt provides a macro `\determinenofline{...}` that determines the number of lines of the content (when typeset in a \vbox). The result is stored in the counter: `\noflines`

## 文件/模块引入、文档组织

* \input{⟨filename⟩}，纯粹是把文件里的内容插入，适合用在导言区
* \include{⟨filename⟩}, 在读入文件之前会另起一页，适合章节
* 导言区\includeonly指定那些文件可导入，其余文件\include无效：\includeonly{⟨filename1⟩,⟨filename2⟩,…}
* \registerctxluafile{luafilename}{} % 注册lua文件

## 超链接和书签

\usepackage{hyperref}

## 拼音

\usepackage{pxrubrica}

或

\usepackage{luatexja-ruby}

或

\overset{\makebox[0pt]{(\scriptsize animal)}}{Bears}

