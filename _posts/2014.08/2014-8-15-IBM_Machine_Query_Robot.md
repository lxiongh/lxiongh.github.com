---
layout: post
title:  IBM Machine Query Robot
category : IBM
tagline: 
tags : [robot,vb,ibm]
---

## 前提

IBM SSR 经常需要在 PCOM (Personal Communications) 中的 RCMS (RETAIN-linked call management system) 上进行 CALL 信息的查询，	通常这些信息需要人工逐条查询并记录，当面对大量数据的查询时，往往就力不从心了。现迫切需要开发出一个 Robot，可以做到自动从 PCOM-RCMS 上 Load 数据下来，并记录在 EXCEL 表格中。

幸运的是，之前已经有人写过 IBM Machine Robot，根据 CALL NO.， 从 RCMS 上 load 出这些 CALL 的详情信息，并记录在EXCEL表格的。而现在还有另外一种需求：根据 customer id，在 RCMS 上 Load 出某段时间内的所有 CALLS 信息。

可用 C++,JAVA,或者 VB 进行 PCOM-RCMS编程，以命令的形式向 PCOM-RCMS 上填入及发送命令
> The Host Access Class Library Automation Objects allow the Personal Communications product to support Microsoft COM-based automation technology (formerly known as OLE automation). The ECL Automation Objects are a series of automation servers that allow automation controllers, for example, Microsoft Visual Basic, to programmatically access Personal Communications data and functionality.

> In other words, applications that are enabled for controlling the automation protocol (automation controller) can control some Personal Communications operations (automation server). Personal Communications supports Visual Basic Script, which uses ECL Automation objects. Refer to the Personal Communications Macro/Script support for more details. 
Refer to [Host Access Class Library Automation Objects](http://publib.boulder.ibm.com/infocenter/pcomhelp/v5r9/index.jsp?topic=/com.ibm.pcomm.doc/books/html/host_access08.htm) for more details.

---

## 初始化

利用 autECLPS Class 建立 VB 与 PCOM-RCMS 通信，初始化代码如下：

<pre class="prettyprint">
Dim  autECLPSObj as Object
Dim  autECLConnList as Object
Set autECLPSObj = CreateObject("PCOMM.autECLPS")
Set autECLConnList = CreateObject("PCOMM.autECLConnList")
 
' Initialize the connection
autECLConnList.Refresh
autECLPSObj.SetConnectionByHandle(autECLConnList(1).Handle)
 
' Build the field list
CurPosCol = autECLPSObj.autECLFieldList.Refresh(1)
</pre>

初始化后，就可以用 `autECLPSObj` 中的方法进行各种操作，常用的有 `SendText(str,row,col)`, `SendKey("[enter]")`, `GetText(row,col,len)`。还有更多的方法跟属性见下图。

Methods:

![][autECLPSMethods]

Properties:

![][Properties]

---

## 实施

根据 customer id 得到 CALL 信息基本步骤可包括：

1. 根据客户 ID(customerId) 查询所有 CALL 信息命令 `ci,customerId (如 ci,818255) -> SendKeys("[enter]")`. 可先查询到有多少页，方法： `SendKeys("[pf2]")` 翻到最后一页，读取红色框内的数据，并统计总共有多少 `_`, 命令 `GetTextRect(Long StartRow, Long StartCol, Long EndRow, Long EndCol)`, 具体到此处为 `GetTextRect(5,2,18,2)`, 若得到k个 `_`, 则对应到数据量序号的位置为 `(5+k-1,4)`, 读取3位即为序列号 `N=GetText(5+k-1,4,3)`
![][fig1]


2. 回到第1页 `SendKeys(“[pf3]”)`, 依次进入CALL, 在当前界面可以得到 
 `CallNum (3,59,7), MT (6,4,4), SN (6,9,9), Model (7,10,3). fix/repair日期 (16,43,10)`. PS: 这个界面并不是第1页,而是一个概述页面, 要进入第1页可发送enter指令, `SendKeys("[enter]")`, 想回到这个概述页面,可以发送指令 `"[pf6]"`.
![][fig2]


3. 进入 CALL 详情的第1页, 可读取 CALL 的创建时间, `GetText(17,51,8)`
![][fig3]


[fig1]: http://lxiongh.qiniudn.com/blog/2014-8-15/fig1.png-mark
[fig2]: http://lxiongh.qiniudn.com/blog/2014-8-15/fig2.png-mark
[fig3]: http://lxiongh.qiniudn.com/blog/2014-8-15/fig3.png-mark
[autECLPSMethods]: http://lxiongh.qiniudn.com/blog/2014-8-15/autECLPSMethods.png-mark
[Properties]: http://lxiongh.qiniudn.com/blog/2014-8-15/Properties.png-mark


<script src="https://google-code-prettify.googlecode.com/svn/loader/run_prettify.js"></script>

---

[Call Information Loader by CustomerId v0.1beta.xlsm](http://lxiongh.qiniudn.com/blog/2014-8-15/Call_Information_Loader_by_CustomerId_v0.2beta.xlsm)
