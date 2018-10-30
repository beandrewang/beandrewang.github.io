---
layout: post
title: Remove the residuals after uninstalling vs2015
tags: tools
date: 2016-11-26 13:46:00
postPatterns: 'circuitBoard'
---

As we all know, there are lots of residuls even we uninstall vs2015. It is so awful. If we sometimes uninstall any component belongs to it over the contrl panel, it would be not uninstallable any more. 

Is there any way to fix this issue, fortunately, Microsoft seemed realise this issue and released a tool: [VisualStudioUninstaller](https://github.com/Microsoft/VisualStudioUninstaller/releases)

Unzip it and run **Setup.ForcedUninstall.exe**, it will check and go through your regs and clear all the residual values belong to vs2015. After running this, your system will be very clean for vs2015, you could reinstall it now. 

