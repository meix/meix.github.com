---
layout : life
title: QT在release版本产生pdb文件
category : Ruby On Rails
duoshuo: true
date : 2014-10-30
---

******

##环境说明

|QtCreator|QtLibrary|编译器|
|:-------:|:-------:|:---:|
|Qt Creator 2.7.0|4.8.4-msvc|msvc9.0(VS2008)|

<!-- more -->

##背景说明

>
项目中需要对发布版本追踪崩溃问题，我们在项目中利用Qtbreakpoint来产生崩溃文件```*.dump```文件，但是若是想要对应的定位崩溃点还需要对应的 ```*.pdb``` 文件，但是在```Qt```的Release版本下默认是不产生```*.pdb```文件

##如何配置

* 方案1 修改编译环境，对所有项目的release都产生```*.pdb```文件,步骤如下：
 * 1. 找到编译器对应的配置文件，如我的系统安装的是VS2008则我需要找**Qt的安装目录/4.8.4/\mkspecs\win32-msvc2008\qmake.conf**
 * 2. 增加如下两行配置项到文件并保存

 ```sh
        QMAKE_CFLAGS_RELEASE    = -O2 -MD -Zi
        QMAKE_LFLAGS_RELEASE    = /INCREMENTAL:NO /DEBUG
 ```
 
 * 保存后重启Qtcreator或者直接重新新编译

* 方案2 修改程序的工程文件 ```*.pro``` ，增加如下：
 *
  
 ```sh
        QMAKE_LFLAGS_RELEASE    += /MAP
        QMAKE_CFLAGS_RELEASE    += /Zi
        QMAKE_LFLAGS_RELEASE    += /debug /opt:ref
 ```    