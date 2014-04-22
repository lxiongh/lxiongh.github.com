---
layout: post
title:  在网页中插入优酷视频
category : others
tagline: 
tags : [video, others]
---

有的时候，我们需要在网页里插入一些视频，来更好的渲染主题。下面的一段代码展示了该如何在网页里插入一段优酷视频。

{% highlight html %}
<embed src=http://static.youku.com/v1.0.0149/v/swf/loader.swf?VideoIDS=XMjQ5OTk1MTQ4&winType=adshow&isAutoPlay=true 
quality="high" width="580" height="435" align="middle" allowScriptAccess="never" allowNetworking="internal" 
allowfullscreen="true" autostart="0" type="application/x-shockwave-flash"></embed>
{% endhighlight %}

参数说明
---

**调整是否自动播放:** `isAutoPlay=false`（不自动） `isAutoPlay=true`（自动播放）

**调整大小:** `width="580" height="435"`

**常见几种比例:** `384×256、580×435、610×460`

**替换视频地址:** VideoIDS=是URL链接中视频的ID（注意大小写）

视频示例《把我留下》

<embed src=http://static.youku.com/v1.0.0149/v/swf/loader.swf?VideoIDS=XMjQ5OTk1MTQ4&winType=adshow&isAutoPlay=true 
quality="high" width="580" height="435" align="middle" allowScriptAccess="never" allowNetworking="internal" 
allowfullscreen="true" autostart="0" type="application/x-shockwave-flash"></embed>
