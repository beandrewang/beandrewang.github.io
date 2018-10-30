---
layout: post
title: FFplay 中文手册
date: 2017-1-20 12:48:00
tags: Multimedia
postPatterns: 'circuitBoard'
---

[TOC]

# 1.  提要

`ffplay [options] [input_url]`

# 2. 说明

FFplay是一个基于FFmpeg和SDL库的轻量级多媒体播放器，经常被用于测试各种FFmpeg API. 

# 3. 选项

除非特别标明，所有的数字选项都采用字符串表示，数字后边可能需要加国际单位制的前缀，像"K", "M"或"G"等。

如果"i"被追加到国际单位制前缀后的话，组成的前缀表示二进制倍数，基于1024的幂而不是1000的幂。如果追加的是"B"的话，表示这个数值的8倍。因此，允许我们使用像"KB", "MiB", "G"和"B"作为后缀。

不带参数的选项表示是布尔选项，并且设置该选项为真。如果要设置此选项为假，那么只需要在该选项前边加上前缀"no"。举例来说，"nofoo"会设置"foo"选项为假。

## 3.1 流指示符

有些选项要被应用到每一个流上，例如bitrate和codec。流指示符被用来精确的表明某个选项参数应用于哪一个流。

流指示符是一个字符串，通常被追加在选项后边，并用冒号分割。例如 `-codec:a:1 ac3` 包含了流指示符 `a:1`，这表示编码器 `ac3` 被用于第二个音频流。

流指示符也可以用于多个流，这样选项参数就可以应用于这些流。例如 `-b:a 128k` 被应用于所有的音频流。

如果没有流指示符，代表该选项参数被应用于所有的流，例如 `codec copy`，`-codec: copy` 表示直接拷贝流而不进行重新编码。

常用的流指示符有：

**`stream_index`**

> 匹配该选项指定的流。例如 `-threads:1 4`， 表示设置第一个流的线程号为4. 

**`stream_type[:stream_index]`**

`stream_type` 有以下几种类型，

> 'v' 表示所有的视频流
> 'V' 表示不带缩略图的视频流
> 'a' 表示音频
> 's' 表示字幕
> 'd' 表示数据
> 't' 表示附件

如果给出 `stream_index` 那么表示`stream_index` 代表的流是这种类型，否则，代表所有的流都是这种类型。

**`p:program_id[:stream_index]`**

如果给出 `stream_index`, 则其代表的流在`program_id` 表达的进程中操作，否则，所有的流都在此进程中。

**#stream_id或i:stream_id**

匹配`stream_id` 表示的流(例如 MPEG-TS 容器中的PID)

**m:key[:value]**

匹配元数据标签 `key` 的值为指定值得流。如果没有给出`value`， 匹配所有具有该元数据标签的流。

**u**

匹配具有可用配置的流，codec必须指定必要的参数，像视频的长宽，音频的采样频率等。

注意，在 `FFmpeg` 中，用元数据匹配只作用于指定的输入文件。

## 3.2 通用选项

以下选项在所有的 `ff*` 工具中通用，

**`-L`**

> 显示许可证

**-h, -?, -help, --help [arg]**

显示帮助。可选参数可以只打印指定项的帮助信息。如果没有指定可选参数，只显示基本的工具选项。

可选参数 `arg` ，请参考如下详细描述

| arg | 描述|
| ----| -----------|
| long | 打印高级选项帮助信息 |
| full | 打印完整的选项列表，包含encoder，decoder，muxer, demuxer, filter的共享的和私有的选项信息 |
| decoder=decoder_name | 打印指定解码器的详细信息，可以使用`-decoders` 选项获取所有支持的解码器列表 |
| encoder=encoder_name | 打印指定编码器的详细信息，可以使用`-encoders` 选项获取所有支持的编码器列表 |
| demuxer=demuxer_name | 打印指定demuxer的详细信息，可以使用`-formats` 选项获取所有支持的demuxer和muxer列表 |
| muxer=muxer_name | 打印指定muxer的详细信息，可以使用`-formats` 获取所有支持的demuxer和muxer列表 |
| filter=filter_name | 打印指定的filter的详细信息，可以使用`-filters` 获取所有支持的filter列表 |

**`-version`**

显示版本号

**`-formats`**

显示可用的容器(包含设备)

**`-devices`**

显示可用的设备

**`-codecs`**

显示`libavcodec` 支持的所有编解码器, 注意此文档中的术语`codec` 代表媒体流格式。

**`-decoders`**

显示可用的解码器

**`-encoders`**

显示可用的编码器

**`-bsfs`**

显示可用的码流滤波器

**`-protocols`**

显示可用的协议栈

**`-filters`**

显示可用的`libavfilter` 滤波器。

**`-pix_fmts`**

显示可用的像素格式

**`sample_fmts`**

显示可用的采样格式

**`-layout`**

显示频道名和标准的频道布局

**`-color`**

显示识别的颜色名称

**`sources device[, opt1=val1[,opts=val2]...]`**

显示自动检测到的输入设备。有些输入设备的名称可能以来操作系统，这样就没法自动检测到。返回列表可能是不完整的。
```
ffmpeg -sources pulse,server=192.168.0.4
```

**`-sinks device[,opt1=val1[,opt2=val2]...]`**

显示自动侦测到的输出设备。有些输出设备的名称可能依赖操作系统，这样就没法自动检测到。返回列表可能是不完整的。
```
ffmpeg -sinks pulse,server=192.168.0.4
```

**`-loglevel [repeat+]loglevel | -v [repeat+]loglevel`**

设置库的日志等级。添加`repeat+` 表明重复的日志输出不会覆盖第一行，并且后边的重复日志会被忽略。`repeat` 也可以单独使用。如果`repeat` 被单独使用，也没有指定日志优先级，那么将使用默认的日志等级。如果指定了多个日志等级参数，使用`repeat` 将不会改变日志等级。`loglevel` 是一个字符串或一个数字，可以是一下的形式，

| 日志等级 | 描述 |
|--------| -----|
| `quiet, -8` | 什么都不输入 |
| `panic, 0` | 只显示那些可能导致程序崩溃的致命错误，例如所有的断言错误 |
| `fatal, 8` | 只显示导致程序崩溃的信息 |
| `error, 16` | 显示所有的错误信息 |
| `warning, 24` | 显示所有的警告和错误信息 |
| `info, 32` | 试试所有提示类信息，包含警告信息，错误信息|
| `verbose, 40` | 跟 `info` 相同 |
| `debug, 48` | 显示所有信息，包含调试信息 |
| `trace, 56` | |

默认情况下，程序的logs会不给输出到`stderr`. 如果中断支持颜色，警告和错误会用颜色高亮显示出来。可以使用`AV_LOG_FORCE_NOCOLOR` 或 `NO_COLOR` 关掉颜色，也可以使用 `AV_LOG_FORCE_COLOR` 打开颜色。`NO_COLOR` 已经过时。

**`-report`**

把所有的命令行和终端打印信息输出到当前目录的文件`program-YYYYMMDD-HHMMSS.log` . 这个文件有助于分析bug, 默认使用 `-loglevel-verbose`.

设置任意值到环境变量`FFREPORT` 也可以达到同样的效果。也可以追加level, 举例如下
```
FFREPORT=file=ffreport.log:level=32 ffmpeg -i input output
```

**`hide_banner`**

禁止打印横幅。

所有的FFmpeg工具通常都会显示版权信息，编译选项和库版本号。增加这个选项可以让程序不打印这些信息。

**`-cpuflags flags (global)`**

允许设置和清空CPU标志，这个选项只是用于测试。如果你不了解这些标志位的意义，建议你不要使用。

```
ffmpeg -cpuflags -sse+mmx ...
ffmpeg -cpuflags mmx ...
ffmpeg -cpuflags 0 ...
```

可用的标志位如下：

| 平台 | 标志位 |
|-----| -----|
| x86 | `mmx`, `mmxext`, `sse`, `sse2`, `sse2slow`, `sse3`, `sse3slow`, `ssse3`, `atom`, `sse4.1`, `sse4.2`, `avx`, `avx2`, `xop`, `fma3`, `fma4`, `3dnow`, `3dnowext`, `bmi1`, `bmi2`, `cmov` |
| ARM | `armv5te`, `armv6`, `armv6t2`, `vfp`, `vfpv3`, `neon`, `setend`|
| AArch64 | `armv8`, `vfp`, `neon` |
| PowerPC | `altivec` |
| Specific Processors | `pentium2`, `pentium3`, `pentium4`, `k6`, `k62`, `athlon`, `athlonxp`, `k8`|

**`-opencl_bench`**

这个选项用于对支持`OpenCL` 的设备进行基准测试并且打印出结果。使用此选项前请确保你的FFmpeg编译的时候添加了选项`-enable-opencl`. 

如果FFmpeg配置了`-enable-opencl`， `OpenCL` 的全局上下文选项可以通过`-opencl_options` 进行配置。请参考 `ffmpeg-utils` 手册中的 `OpenCL Options` 部分。还可以指定平台和设备来运行OpenCL代码。默认情况下，FFmpeg会运行在第一个平台的第一个设备上。而OpenCL 的全局上下文选项允许用户选择OpenCL设备，大多数用户会选择系统中最快的OpenCL设备。

这个选项有助于选择系统中最快的OpenCL设备。内置的基准程序会在所有的OpenCL设备上运行并且对设备的性能做出评价。运行结束后会输出一个按照性能高低的顺序排列的列表，性能越高，排名越靠前。然后用户可以用性能最好的设备运行FFmpeg, 以获取最高的硬件加速性能。

通常用下面的步骤来应用最快的OpenCL设备，

```
ffmpeg -opencl_bench
```

记录下平台ID(pidx)和设备ID(didx)，然后执行下边的指令，

```
ffmpeg -opencl_options platform_idx=pidx:device_idx=didx ...
```

**`-opencl_options options (global)`**

设置OpenCL环境变量，只有FFmpeg编译时添加了`--enable-opencl` 才能使用该选项。

选项必须是一个符合 `key=value` 的序列，中间用":" 分割开。详见 `ffmpeg-utils` 手册中的 `OpenCL Options` 部分。

## 3.3 AVOptions

这些选项由`libavformat`, `libavdevice`, `libavcodec` 库提供。可以使用`-help` 选项来查看AVOptions支持列表。其可以分为两类，

**通用**

这些选项可以用用于所有的容器，编解码器或设备。`AVFormatContext` 中定义了关于容器和设备的通用选项，`AVCodecContext` 中定义了关于编解码器的通用选项。

**私有**

这些选项是针对特定容器，设备或编解码器的。他们被定义在相应的容器/设备/编解码器中。

例如，要将一个MP3文件的文件头由默认的ID3V2.4改成ID3V2.3, 可以使用`id3v2_version` 这个MP3 容器的私有选项：	

```
ffmpeg -i input.flac -id3v2_version 3 out.mp3
```

所有的编解码器AVOptions 都是针对特定流的，因此需要使用流指示符来指定这个选项应用于哪一个流。

> **注意**， AVOptions不能使用布尔选项，即没有`nooption` 这样的语法，应该使用 `-option 0/ -option 1`.

## 3.4 主要选项

**`-x width`**

强制显示宽度

**`-y height`**

强制显示长度

**`-s size`**

设置帧的尺寸(W*H), 像 raw YUV这样的没有容器头并且没有帧尺寸信息的视频需要制定这个参数。但是这个选项已经过时了，建议采用私有选项中的`-video_size`.

**`fs`**

启动全屏模式

**`an`**

禁用音频

**vn**

禁用视频

**`sn`**

禁用字幕

**`ss pos`**

跳转到 pos 位置。注意在大多数格式中是不可能精确的跳转的，因此 ffplay 只是跳转到离pos最近的位置。pos 必须符合时间长度标准。

**`-t duration`**

播放duration秒的音视频。duration必须符合时间长度标准。

**`-bytes`**

跳转到多少字节处。

**`-nodisp`**

禁用图形显示。

**`-volume`**

设置开始音量。`0` 表示静音，`100` 表示没有音量加和音量减。负数会用`0` 替换，大于100的书会用100替换。

**`-f fmt`**

强制格式。

**`-window_title tile`**

设置窗口标题（默认是输入文件名）。

**`-loop number`**

循环播放次数，0表示无限循环。

**`-showmode mode`**

设置使用的显示模式。mode可以为，

| 模式 | 描述 |
|---|----|
| `0, video`| 显示视频 |
| `1, waves`| 显示声波 |
| `2, rdft` | 显示声音频带，使用离散傅里叶逆变换|

默认值是 `video`, 如果视频不存在或不能播放，那么会自动选择 `rdft` .

你可以通过按`w` 键循环切换这些模式。

**`-vf filtergraph`**

创建一个滤波器组对视频进行滤波。滤波器只有一个视频输入和视频输出。在滤波器图中，输入靠标签 `in` 指定，输出靠标签 `out` 指定，详情参考ffmpeg-filters手册中的滤波器图语法。

你可以多次指定这个参数，并且可以配合显示模式使用，`w` 键循环切换。

**`-af filtergraph`**

音频的滤波器组，可以使用 `-filters` 查看支持的所有滤波器(包括源和汇)。

**`-i input url`**

读 input_url

## 3.5 高级选项

**`-pix_fmt format`**

设置像素格式。这个选项已经过期，建议使用 `-pixel_format`.

**`-stats`**

打印多个播放信息统计表，尤其是显示流持续时间，编解码器参数，流的当前位置和音视频同步的漂移。默认情况这个选项是打开的，可以使用`nostats` 选项关闭。

**`genpts`**

生成pts. 

**`sync type`**

设置主时钟为音频(`type=audio`), 视频(`type=video`)或外部(`type=ext`). 默认是音频。主时钟用于音视频同步，大多数多媒体播放器使用音频作为主时钟，但是在一些特殊的情况下(串流或高清广播)，很有必要修改主时钟。这个选项通常用于调试。

**`-ast audio_stream_specifier`**

根据流指示符选择想要的音频流。

**`-vst video_stream_specifier`**

根据流指示符选择想要的视频流。

**`-sst subtitle_stream_specifier`**

根据流指示符选择想要的字幕。

**`-autoexit`**

当视频播放完成后自动退出。

**`-exitonkeydown`**

按任意键退出

**`-exitonmousedown`**

按下鼠标键退出。

**`-codec:media_specifier codec_name`**

强制指定 media_specifier 代表的流的解码器，media_specifier可以为`a` (音频), `v` (视频)，和`s` (字幕)。

**`-acodec codec_name`**

强制指定音频解码器。

**`-vcodec codec_name`**

强制指定视频解码器。

**`-scodec codec_name`**

强制指定字幕解码器

**`-autorotate`**

根据文件元数据自动旋转视频。默认是开启的，可以用`-noautorotate` 关闭。

**`-framedrop`**

当视频跟音频不同步时丢弃视频帧。如果主时钟是视频，这个选项是默认关闭的。使用这个选项可以丢弃所有的跟主时钟不同步的帧, 可以使用`-noframedrop` 关闭。

**`infbuf`**

不限制输入内存大小，尽可能多的从输入文件中读取数据。如果输入是实时流时，默认开启。可以使用 `-noinfbuf` 关闭。

## 3.6 播放时的控制命令

| 命令 | 功能 |
| ---- | ---- |
| q, ESC | 退出 |
| f | 全屏 |
| p, SPC | 暂停 |
| m | 静音 |
| 9， 0 | 减小,增大音量 |
| /, * | 减小,增大音量 |
| a | 在当前节目中循环播放所有音频频道 |
| v | 循环视频频道 |
| t | 在当前节目中循环播放所有字幕频道 |
| c | 循环节目 |
| w | 循环视频滤波器或显示模式 |
| s | 跳到下一帧, 如果视频没有暂停，先暂停，然后调到下一帧 |
| left/right | 向后/向前跳10秒 |
| down/up | 向后/向前跳1分钟 |
| page down/page up | 跳到前/后章，如果没有章，向后/向前跳10分钟 |
| right mouse click | 跳到宽度小数对应的文件百分比位置 |
| left mouse double-click | 全屏 |

> Written with [StackEdit](https://stackedit.io/).
