---
layout: post
title:  Install wxPython in OSX
category : others
tagline: 
tags : [wxPython,wx,MAC]
---

## No Module named wx Problem

### Install using brew tool
<code>
brew install wxpython
</code>

### test for wxpython
<code>
<p>import wx</p>
<p>ouput error: "No Module name wx"</p>
</code>

### fix this problem
选择相应的版本即可
<code>
<p>import wxversion</p>
<p>wxversion.select('3.0')</p>
<p>import wx</p>
</code>

More Information, Please ref. to [link](http://0x55aa.sinaapp.com/算法-编程/121.html)
