---
layout: post
title: Windows 10 安装 Docker 错误汇总
tags: tool docker
date: 2018-11-09 10:13:00
image: /img/docker_getstarted.png
---

在 Windows 10 安装 Docker 时，会出现如下的错误信息：

1. 即便 BIOS 开启了虚拟化使能， 但是有时仍会报这个错误

![](/img/docker_error1.png)

**解决方案**

* 查看 Windows 启动加载项

```
C:\Windows\system32>bcdedit

Windows 启动管理器
--------------------
标识符                  {bootmgr}
device                  partition=C:
description             Windows Boot Manager
locale                  zh-CN
inherit                 {globalsettings}
default                 {current}
resumeobject            {3bd4c9c5-b8d8-11e8-b844-a338eff780b8}
displayorder            {current}
toolsdisplayorder       {memdiag}
timeout                 30

Windows 启动加载器
-------------------
标识符                  {current}
device                  partition=C:
path                    \Windows\system32\winload.exe
description             Windows 10
locale                  zh-CN
inherit                 {bootloadersettings}
recoverysequence        {3bd4c9c9-b8d8-11e8-b844-a338eff780b8}
recoveryenabled         Yes
allowedinmemorysettings 0x15000075
osdevice                partition=C:
systemroot              \Windows
resumeobject            {3bd4c9c5-b8d8-11e8-b844-a338eff780b8}
nx                      OptIn
bootmenupolicy          Standard
hypervisorlaunchtype    Off
```

会看到 `hypervisorlaunchtype    Off`.

* 使能 `hypervisorlaunchtype`, 在命令行输入 `bcdedit /set hypervisorlaunchtype auto`

```
C:\Windows\system32>bcdedit /set hypervisorlaunchtype auto
操作成功完成。
```

* 重启电脑

