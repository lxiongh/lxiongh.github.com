---
layout: post
title:  WingIDE破解及fcitx 设置
category : python
tagline: 
tags : [WingIDE, python, crack]
---

# WingIDE 破解

[转至 http://bbs.pediy.com/showthread.php?t=148699](http://bbs.pediy.com/showthread.php?t=148699)

我们要修改的两个文件是wingctl.pyo和abstract.pyo（文件位于process文件夹中）。   
![alt](https://raw.github.com/lxiongh/lxiongh.github.com/master/_posts/image/wingide_crack/file.png)   
这两个文件都是编译之后的二进制文件，是没有办法直接看到python代码的，需要首先将二进制代码解析为字节码。直接使用linux下的unpyclib进行处理吧。从这里下载[unpyclib](https://pypi.python.org/pypi/unpyclib/0.8.1)进行安装，如果上面的地址失效了大家就自己搜索吧。安装之后所有的py脚本会安装到如下的目录中：   
![alt](https://raw.github.com/lxiongh/lxiongh.github.com/master/_posts/image/wingide_crack/unpyc.png)  
打开终端执行如下命令cd /usr/lib/UnPyc/unpyclib/切换到unpyclib目录下，执行sudo python application.py -d abstract.pyo > /home/obaby/桌面/abstract.txt 解析abstract.pyo文件并输出到桌面的abstract.txt文件中。  
![alt](https://raw.github.com/lxiongh/lxiongh.github.com/master/_posts/image/wingide_crack/jiexi.png)  
现在就可以用文本编辑器来查看解析后的字节码指令了。直接使用文本编辑器打开abstract.txt搜索Invalid license activation会定位到如下所示的代码位置：  
![alt](https://raw.github.com/lxiongh/lxiongh.github.com/master/_posts/image/wingide_crack/1.png)  
具体代码如下：

    code:
    0000523E STR: 't\x00\x00d\x01\x00\x83\x01\x00|\x01\x00d\x02\x00&lt;x6\x00t\x01\x00D].\x00}\x03\x00\x01\x00i\x02\x00...' (2F 03 00 00 74 00 00 64 01 00 83 01...)

    ……省略部分代码……
 
    000001BC 64 - LOAD_CONST 'Invalid license activation'
    000001BF 83 - CALL_FUNCTION
    000001C2 66 - BUILD_TUPLE
    000001C5 53 - RETURN_VALUE
    000001C6 6E - JUMP_FORWARD -&gt; 000001CA
    000001C9 01 - POP_TOP
    000001CA 7C - LOAD_FAST 'self'
    000001CD 69 - LOAD_ATTR '_GetTermDaysLeft'
    000001D0 7C - LOAD_FAST 'lic'
    000001D3 83 - CALL_FUNCTION
    000001D6 7D - STORE_FAST 'daysleft'
    000001D9 7C - LOAD_FAST 'daysleft'
    000001DC 64 - LOAD_CONST -1
    000001DF 6A - COMPARE_OP "=="
    000001E2 6F - JUMP_IF_FALSE -&gt; 000001F9

后面的字节码指令也不难理解，直接按照字面意思理解即可。那么要想程序试用期永不过期，只需要只需要将 000001E2 6F – JUMP_IF_FALSE -> 000001F9 一行跳转到过期的代码nop掉即可。
知道了如何修改，那么剩下的就比较简单了。通过计算偏移来定位二进制代码的位置。函数的起始地址为0000523E，相对偏移为000001E2，那么实际的二进制指令便宜也就是0000523E + 000001E2 =5420.用十六进制编辑器打开abstract.pyo。跳转到这个地址，代码如下：  
![alt](https://raw.github.com/lxiongh/lxiongh.github.com/master/_posts/image/wingide_crack/2.png)  
通过计算可以知道指令的长度为3.那么要修改为nop就需要将6F 14 00全部用09填充掉。填充之后的效果如下图：  
![alt](https://raw.github.com/lxiongh/lxiongh.github.com/master/_posts/image/wingide_crack/3.png)  
保存之后为了确保修改无误可以重新解析一下文件看下修改之后的代码是否已经nop掉了。重新解析之后的代码如下图所示：  
![alt](https://raw.github.com/lxiongh/lxiongh.github.com/master/_posts/image/wingide_crack/4.png)  
到这里这个文件的修改就完成了，直接重新压缩回zip文件即可，但是单纯修改这个东西还是会弹出剩余天数的提示。
那么现在就来修改第二个文件wingctl.pyo。还是用上面的方法处理之后的代码如下所示：  

    00002880 STR: '\x88\x00\x00i\x00\x00o\x08\x00\x01d\x01\x00Sn\x01\x00\x01\x88\x00\x00i\x01\x00\x83\x00\x00}\x01\x00|\x01\x00d\x02...' (BE 01 00 00 88 00 00 69 00 00 6F 08...)
    00000000 88 - LOAD_DEREF 'self'
    00000003 69 - LOAD_ATTR '_fExpiringLicenseCheck'
    00000006 6F - JUMP_IF_FALSE -&gt; 00000011
    00000009 01 - POP_TOP
    0000000A 64 - LOAD_CONST None
    0000000D 53 - RETURN_VALUE
    0000000E 6E - JUMP_FORWARD -&gt; 00000012
    00000011 01 - POP_TOP
    00000012 88 - LOAD_DEREF 'self'
    00000015 69 - LOAD_ATTR '_GetTermDaysLeft'

原来的这个函数太长了，这里只显示前面一点点，其实这个就是弹出授权只剩九天的那个提示窗口。 如果去掉那些恼人的提示信息，只需要让程序在 0000000D 53 – RETURN_VALUE一行直接返回即可。那么唯一需要做的就是把上面的 00000006 6F – JUMP_IF_FALSE -> 00000011一行代码nop掉。而所有的代码应该都是三个字节的，定位到二进制文件中的对应的位置如下所示：     
![alt](https://raw.github.com/lxiongh/lxiongh.github.com/master/_posts/image/wingide_crack/wingctl.png)  

修改完成之后同样压缩回zip文件中。其实到现在这个东西的爆破已经非常简单了，只要过程分析清楚了，想怎么爆破都可以了。通过上面的方法修改之后就可以显示正常的授权信息。  
![alt](https://raw.github.com/lxiongh/lxiongh.github.com/master/_posts/image/wingide_crack/WingIDE.png)  
通过上面的方法修改之后的二进制python文件同样可以用于linux系统下，但是从windows操作系统下压缩的放到linux系统下会导致wing无法正常启动，所以最好的办法是在linux系统下将文件重新压缩回压缩包中。授权效果如下：  
![alt](https://raw.github.com/lxiongh/lxiongh.github.com/master/_posts/image/wingide_crack/linux.png)  
并且检查更新是没有问题的：  
![alt](https://raw.github.com/lxiongh/lxiongh.github.com/master/_posts/image/wingide_crack/update.png) 


----------------------------

# WingIDE fcitx输入法设置

[转至 http://blog.csdn.net/liangpz521/article/details/8067237](http://blog.csdn.net/liangpz521/article/details/8067237)  

默认情况下wingIDE下面是不能输入中文的，若想输入中文注释则有障碍了，可以按如下几步进行设置：  
1）打开菜单FILE －》FILE SETS －》USER INTERFACE  
2) Keyboard模块下有个 Custom Key Bindings 点insert 添加 ctrl+backspace   在commmand中输入 im-swtich -s fcitx 确定保存  
3）USER INTERFACE面板中，选中Use System Gtk  
完成后，即可随意输入中文注释啦  

默认情况下，Debug IO 显示中文时会乱码， 修改方法为：  
打开菜单FILE——》FILE SETS——》Debugger——》I/O   ，Debug I/O Encoding 设置为utf-8即可