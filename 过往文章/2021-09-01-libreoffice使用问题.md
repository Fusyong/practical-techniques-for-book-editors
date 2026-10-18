---
title: LibreOfficeWriter使用问题
Slug: 
Category: 
tags: 
  - 编辑工具
Authors: 
Date: 2021-09-01
Modified: 2023-02-11
Status: 
Summary: 
---

## 总体心得

* 现在用LibreOffice Writer替代微软Word不会有大问题；
* 尽可能只在默认的odt格式上编辑
    1. **第一次打开docx文件时，不要有任何修改动作，** 注意观察字数（**doc文件要先用MS Word另存为docx，而不是用Writer直接转**）；
    1. 另存为默认的odt格式，关闭，再打开，核对字数是否有误；
    1. **只在odt上改动，**
    1. 必要时才通过docx等格式交换文件（Word能直接编辑odt文件）；
* 好用的功能：
    * 支持正则表达式的查找与替换，安装插件后能跨行处理；
    * 全屏模式。

## 常用快捷键

[官方文档](https://help.libreoffice.org/latest/zh-CN/text/swriter/04/01020000.html)

**LibreOffice Writer的菜单命令都可以定制快捷键，通过`工具 | 自定义`菜单。** 以下是我常用的快捷键：

* Ctrl+0到Ctrl+5，设置标题各级别样式；Ctrl+0取消标题样式而应用默认样式
* Ctrl+Shift+i（自定义），显示/隐藏追踪修订，协同编辑常用
* Ctrl+m，清除直接格式
* F11（自定义），全屏
* F5（自定义），刷新
* Ctrl+Alt+Shift+V，以无格式文本粘贴剪贴板中的内容

以及各种编辑器通用的：Ctrl+z，编辑记录回退；+y，编辑记录前进；+f，查找；+h，替换；复制；粘贴；剪切；……

以及用得最频繁、常是下意识使用的Ctrl+s。Writer有奔溃问题，随手保存很重要！

## 破折号和省略号的问题

不使用“字符间距”压缩可能导致行尾破折号断成两截；使用压缩则破折号、省略号都可能有这样的问题。

## LibreOffice Writer审阅所致错乱

LibreOfficeWriter多人协作“追踪修订”会出错，主要表现为：改动某处后，显示无异常；但保存文件，再次打开后，全文各处很多以前删除的内容被显示为未删除，导致全文混乱。

如下原文：

![](img/libreofficeWriter审阅所致错误/2021-08-11-12-08-59.png)

修改绿色两处：

![](img/libreofficeWriter审阅所致错误/2021-08-11-12-09-44.png)

保存文件再打开，下面的“大多数”以下大段文字并意外恢复：

![](img/libreofficeWriter审阅所致错误/2021-08-11-12-10-33.png)


### 已知文档特征

* 最近在wps中处理过的，docx格式；
* **最直接的检查方法：用writer“另存为”docx或doc，关闭后重新打开，看前后字数是否一致。**

按理应当检查原文件的xml标签，可惜我不熟悉。

### 目前可能的解决办法

1. 收到的文件后，在未作任何修改前，用Writer转为首选格式odt；
    * **doc文件要先用MS Word另存为docx，而不是用Writer直接转**
    * 注意检查文档一致性：观察转换格式前后的字数是否一致（Writer有时要保存后，字数才是正确的，但有时保存后也不一定正确，所以需要通过前后变动来确认），如有不一致，可以把转换前后的全文纯文本拷贝至EmEditor等软件中进行比较（常有空格不一致的情况）。
1. **只在odt文中进行改动**
    * （通过odt再导出的docx，也许可以在Writer中修改，但只经过少量验证）；
1. 改定后再通过Writer导出需要的格式，并同前检查文档一致性；

!!! note ODT vs OOXML
    有人推荐微软维护的ISO标准“Office Open XML文本（过渡格式）”(或叫OOXML、OpenXML，不同于Word 2007-365版的docx），认为兼容性好，并方便与MS Word协作。但有人认为“过渡格式”（即未完全落实ISO标准）中隐藏着一些“非标准”陷阱。[参考](https://ask.libreoffice.org/t/tutorial-differences-between-ms-word-eg-doc-and-docx-and-lo-files-eg-odt/24585)

### 未证实的问题

未经证实的问题：如果审阅人没有名称(编辑软件中没有设置用户名称所致)，那么可能造成审阅标记混乱，比如错误恢复、大量时间归零的标记。

## LibreOffice Writer审阅导致无法应用段落样式

如下图，连续删除段落后，不管是展开还是关闭审阅标记，都不能在第三处应用段落样式，而实际上已经删除的一、二两处可以。解决方法是把文字移动到第一处。

![](img/2021-09-01-libreoffice使用问题/2021-10-25-09-54-09.png)

## 显示更新不及时的问题

替换后不会自动刷新，导致旧文本和新文本同时呈现。更新菜单为：`工具>更新>全部更新`，可以设置快捷方式，如F5；保存文档通常也能激发刷新。

编辑文字时也可能更新不及时，导致显示错乱、光标位置不是实际位置（导致增删错误），需要手动刷新。

## 保存修改然后回退导致奔溃

个别文档（含有审阅标记），在文档的某个特定区间，删除并保存后再回退，马上导致奔溃。插入并保存后再回退，或删除后不保存即回退，都没有问题。

##  正则表达式查找替换增强插件

[Alternative Find & Replace for Writer (AltSearch)](https://extensions.libreoffice.org/en/extensions/show/alternative-dialog-find-replace-for-writer)

有很多增强功能，例如：

* 跨行/跨段落查找、替换
* 在替换中插入计数器和页码
* 计算找的项的出现次数
* 多命令、多文件批处理模式
* 为批处理分配快捷键

界面举例：

![](img/2021-09-01-libreoffice使用问题/2021-10-07-17-47-57.png)

![](img/2021-09-01-libreoffice使用问题/2021-10-07-17-48-47.png)
*预制正则表达式*

![](img/2021-09-01-libreoffice使用问题/2021-10-07-18-02-03.png)
*批处理*

## 命令行运行，批处理

可以命令行无头/无界面运行（`--headless`，可结合python等脚本语言使用），如`--convert-to`批量转换文档格式、`-p`批量打印。用例如：

```shell
# 临时添加环境变量，分号后面应是你的Libreoffice(soffice.exe)实际安装路径
set path=%path%;D:\Program Files\LibreOffice\program
# 把当前路径下所有odt文件转换docx文件
soffice --convert-to docx *.odt
# 把当前路径下所有ppt文件转换pptx文件
soffice --convert-to pptx *.ppt
```

参见[官方文档](https://help.libreoffice.org/7.2/zh-CN/text/shared/guide/start_parameters.html)
