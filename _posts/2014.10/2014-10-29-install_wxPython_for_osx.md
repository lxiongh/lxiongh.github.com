---
layout: post
title:  Install wxPython in OSX
category : tips
tagline: 
tags : [wxPython,wx,MAC]
---

## No Module named wx Problem

### Install using brew tool

    brew install wxpython

### test for wxpython

    import wx
    ouput error: "No Module name wx"

### fix this problem

选择相应的版本即可

    import wxversion
    wxversion.select('3.0')
    import wx

More Information, Please ref. to [link](http://0x55aa.sinaapp.com/算法-编程/121.html)
