---
layout: post
title:  linux声卡驱动
category : linux
tagline:  
tags : [audio, driver]
---
{% include JB/setup %}


安装声卡驱动
-----------------

    apt-get install alsa-utils

---------------------

重启后出现错误
------------------

    Setting up ALSA...amixer: Mixer hw:0 load error: Invalid argument
    amixer: Mixer hw:0 load error: Invalid argument
    amixer: Mixer hw:0 load error: Invalid argument
    amixer: Mixer hw:0 load error: Invalid argument
    amixer: Mixer hw:0 load error: Invalid argument
    amixer: Mixer hw:0 load error: Invalid argument

----------------------

解决方法
------------------

[Click for some detail (the second answer)](https://answers.launchpad.net/ubuntu/+source/alsa-driver/+question/159105)

    This is a known issue with the ALC887 sound codec.
    See the following bugreport for details
    https://bugs.launchpad.net/ubuntu/+source/alsa-driver/+bug/669092
    Please run the following command in a Terminal:
    gksudo gedit /etc/modprobe.d/alsa-base.conf
    REMOVE the following single line using the gedit editor , IF it was previously added:
    options snd-hda-intel model=auto
    ADD the following single line using the gedit editor:
    options snd-hda-intel model=generic
    SAVE the change to /etc/modprobe.d/alsa-base.conf
    Then REBOOT and retest sound using headphones and speakers.
    After rebooting, make sure all channels are UNMUTED and volume set to 100% in alsamixer and gnome-alsamixer

