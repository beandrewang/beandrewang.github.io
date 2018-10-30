---
layout: post
title: ffmpeg 中文手册
date: 2017-1-22 15:30:00
tags: Multimedia
postPatterns: 'circuitBoard'
---

[TOC]/

# 1 总览

```
ffmpeg [global_options] {[input_file_options] -i input_url} ... {[output_file_options] output_url} ...
```

# 2 说明

`ffmpeg` 是一个快速的音视频转码器，并且可是从实时的音视频源中抓取数据。他可以通过一个高质量的多相滤波器转换任意音频的采样频率，缩放任意视频的尺寸。

`ffmpeg` 可以从任意数量的输入文件中读取数据，输出到任意数量的文件中，这里的文件包括通常意义的文件，管道，网络流，输入设备等，输入文件可以通过`-i` 选项指定，输出文件通过一个纯文本的字符串指定。在命令行上发现的所有的非选项内容都被认为是输出文件名。

原则上，所有的输入或输出文件都可以包含任意数量的不同类型的流，包括视频流，音频流，字幕流，附件流和数据流。数量和类型受容器的限制。输入文件中的某一个流保存到输出文件中的哪一个流，这件事情可以由`ffmpeg` 自动完成或由 `-map` 选项来指定 （见流选择章节）。

如果某个选项是针对哪一个输入文件的，需要使用从0开始的索引号指定，第一个文件的索引号为0，第二个文件的索引号为1，以此类推。同样的，文件中的流也是用索引号来表示的。例如 `2:3` 代表第3个文件中的第4个流，详见流选择章节。

通用规则是，所有选项作用于其后边的第一个文件。因此，顺序是非常重要的，你可以在命令行中重复指定相同的选项，只是指定的文件不同。那些全局的选项需要在命令行中优先指定。

千万不要输入文件和输出文件交叉出现在命令行中，先统一指定所有的输入文件，然后再指定所有的输入文件。同时也不要把指定给不同文件的选项弄混了。

* 设置输出文件的视频码率为64kbit/s: 
```
ffmpeg -i input.avi -b:v 64k -bufsize 64k output.avi
```

* 指定输出文件的帧率为24：
```
ffmpeg -i input.avi -r 24 output.avi
```

* 强制指定输入文件的帧率为1，输出文件的帧率为24
```
ffmpeg -r 1 -i input.m2v -r 24 output.avi
```

# 3 详细说明

`ffmpeg` 中的转码流程可以用下图来描述:

![](https://github.com/beandrewang/beandrewang.github.io/blob/master/images/ffmpeg_manual/transcode_flow.png?raw=true)

`ffmpeg` 调用 libavformat 库(包含demuxer) 读取输入文件，从中提取出编码的数据包。如果有多个输入文件，`ffmpeg` 会根据最低的时间戳信息保持文件间的输入流同步。

编码的数据包会交给解码器，除非使用了流拷贝，详情查看后边章节。解码器输出无压缩的数据帧，然后再交给滤波器做处理（见下一节）。经过滤波器后，这些数据帧会再传给编码器，输出压缩的数据包，最后把这些数据交给muxer, 把这些数据保存成文件。

## 3.1 滤波

在编码之前，`ffmpeg` 会在libavfilter库中使用滤波器处理解码后的原始音视频数据。各种不同的滤波器组成一个滤波器组。`ffmpeg` 可以区分简单和复杂两种滤波器组。

### 3.1.1 简单滤波器组

简单滤波器组由单输入单输出滤波器构成，并且滤波器都是同种类型。他们可以用下图表达。

![](https://github.com/beandrewang/beandrewang.github.io/blob/master/images/ffmpeg_manual/simple_filter_graph.png?raw=true)

简单滤波器组可以通过`-per-stream` `-filter` 选项进行配置，视频滤波器选项简化为 `-vf`, 音频滤波器选项简化为`-af`. 一个简单的视频滤波器组可以表达成以下形式：

![](https://github.com/beandrewang/beandrewang.github.io/blob/master/images/ffmpeg_manual/simple_video_filter_graph.png?raw=true)

注意，有些滤波器会改变帧的属性，但是并不会改变其内容。例如，上边例子中的 `fps` 滤波器改变了帧率 ，但是每一帧的内容是没有被改变的。再者，`setpts` 滤波器，只是改变了时间戳信息，而帧内容没有改变。

### 3.1.2 复杂滤波器组

复杂滤波器组是那些不能简单的用线性处理描述的滤波器。举个例子，当这个滤波器组由多个输入和输出，或者输出的流类型跟输入不同。这样的滤波器组可以用下图描述：

![](https://github.com/beandrewang/beandrewang.github.io/blob/master/images/ffmpeg_manual/complex_filter_graph.png?raw=true)

复杂滤波器组通过 `-filter_complex` 选项进行配置。注意，这个选项是全局的，也就是说不能单独为单个流添加复杂滤波器组。

`-lavfi` 选项跟 `-filter_complex` 选项等效。

常见的复杂滤波器组是 `overlay`, 有两个视频输入一个视频输出，其中一个视频会改在另一个视频的上边。与其对应的是 `amix` 滤波器。

## 3.2 流拷贝

流拷贝是通过给 `-codec` 选项添加 `copy` 参数的一种模式。可以让 ffmpeg 省掉了先解码再编码的步骤，只有demuxer和muxer. 经常应用于改变视频文件类型，或修改容器的元数据。可以用下图描述:

![](https://github.com/beandrewang/beandrewang.github.io/blob/master/images/ffmpeg_manual/stream_copy.png?raw=true)

因为没有编解码，流拷贝运行的非常快并且可以做到无损。但是，有些时候是不能用流拷贝的，例如你想应用滤波器组。

# 4 流选择

默认情况下，ffmpeg 只从输入文件中选择一个视频流，一个音频流，一个字幕流，然后把这些流保存到输出文件中。ffmpeg通过下边的标准选择最优的视频，音频和字幕：

* 视频，选择最高分辨率的流
* 音频，选择有最多升到的流
* 字幕，选择第一个字幕流
* 如果多个同类型的流具有相同的参数，那么选取第一个。

我们可以通过 `-vn/-an/-sn/-dn` 选项禁用这些默认标准。如果你想全部手动选择，可以通过`-map` 选项进行配置。

# 5 选项

除非特别标明，所有的数字选项都采用字符串表示，数字后边可能需要加国际单位制的前缀，像"K", "M"或"G"等。

如果"i"被追加到国际单位制前缀后的话，组成的前缀表示二进制倍数，基于1024的幂而不是1000的幂。如果追加的是"B"的话，表示这个数值的8倍。因此，允许我们使用像"KB", "MiB", "G"和"B"作为后缀。

不带参数的选项表示是布尔选项，并且设置该选项为真。如果要设置此选项为假，那么只需要在该选项前边加上前缀"no"。举例来说，"nofoo"会设置"foo"选项为假。

## 5.1 流指示符

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

## 5.2 通用选项

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

## 5.3 AVOptions

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

## 5.4 主要选项

**`-f fmt (input/output)`**

指定输入文件和输出文件的格式。通常情况下输入文件的格式是自动检测出的，输出文件的格式是通过文件后缀猜出来的。所以，在大多数情况下，不需要使用这个选项。

**`-i url (input)`**

输入文件的路径。

**`-y (global)`**

不提示，直接覆盖输出文件。

**`-n (global)`**

不要覆盖输出文件，如果输出文件已存在直接退出。

**`-stream_loop number (input)`**

设置输入文件的循环次数。0 表示不循环，-1代表无限循环。

**`-c[:stream_specifier] codec (input/output,per-stream)`**

**`-codec[:stream_specifier] codec (input/output,per-stream)`**

给输出流选择编码器，给输入流选择解码器。codec 是编码器或解码器的名字，或者是 copy，表示不会对输入流进行重新编码。

例如：

```
ffmpeg -i INPUT -map 0 -c:v libx264 -c:a copy OUTPUT
```

表示对所有的视频流使用libx264编码，拷贝所有的音频流。

对每个流，总是应用最后的`-c` 选项，例如，

```
ffmpeg -i INPUT -map 0 -c copy -c:v:1 libx264 -c:a:137 libvorbis OUTPUT
```

除了第2个视频流使用libx264编码和第138个音频流使用libvorbis编码之外，拷贝所有剩余流。

**`-t duration (input/output)`**

作为输入文件的参数时，表示从输入文件中读取 duration指定时间的数据。

作为输出文件的参数是，表示当时间超过duration指定的时间时，停止写文件。

duration 必须符合时间持续标准，详见[(ffmpeg-utils)the Time duration section in the ffmpeg-utils(1) manual](http://www.ffmpeg.org/ffmpeg-utils.html#time-duration-syntax).

`-t` 和 `-to` 互相排斥，不过 `-t` 具有更高的优先级。

**`-to position (output)`**

在某个位置时停止写文件。位置必须符号时间持续标准 ，详见[(ffmpeg-utils)the Time duration section in the ffmpeg-utils(1) manual](http://www.ffmpeg.org/ffmpeg-utils.html#time-duration-syntax).

`-t` 和 `-to` 互相排斥，不过 `-t` 具有更高的优先级

**`-fs limit_size (output)`**

限制文件大小，以字节计。一旦达到了限定大小，停止写入数据。

**`-ss position (input/output)`**

作为输入文件参数时，跳转到输入文件的position位置，注意在大多数的文件格式中，是很难精确的跳转的，ffmpeg只会跳转到指定位置的附近。在转码时，默认开启 `-accurate_seek` 参数。在跳转点和position之间的数据，会在解码后丢弃。当流拷贝或使用 `-noaccurate_seek` 时，会保留这些数据。

当作为输出文件参数时，解码后丢弃输入，直到时间戳跟position匹配为止。

position 必须符号时间持续标准，详见[(ffmpeg-utils)the Time duration section in the ffmpeg-utils(1) manual](http://www.ffmpeg.org/ffmpeg-utils.html#time-duration-syntax).

**`sseof position (input/output)`**

跟 `-ss` 选项很像，只是会跳转到从文件结尾处开始计数的某一个为止，这个数是个负数，0表示文件尾。

**`-itsoffset offset (input)`**

设置输入文件的时间偏移量。

偏移量必须符合时间持续标准，详见[(ffmpeg-utils)the Time duration section in the ffmpeg-utils(1) manual](http://www.ffmpeg.org/ffmpeg-utils.html#time-duration-syntax).

这个偏移量会被加到输入文件的时间戳中。如果offset是正数，代表相应的流会延时offset的时间。

**`-timestamp date (output)`**

设置容器中的录制时间戳。

date必须符合日期标准，详见 [(ffmpeg-utils)the Date section in the ffmpeg-utils(1) manual](http://www.ffmpeg.org/ffmpeg-utils.html#date-syntax).

**`-metadata[:metadata_specifier] key=value (output,per-metadata)`**

设置元数据。

可以给流，章节和节目设置专门的元数据 metadata_specifier. 详见`-map_metadata`. 

这个选项可以覆盖 `-map_metadata` 设置的元数据。可以是设置一个空值来删除元数据。

例如，设置输出文件的标题：

```
ffmpeg -i in.avi -metadata title="my title" out.flv
```

设置第一个音频流的语言：

```
ffmpeg -i INPUT -metadata:s:a:0 language=eng OUTPUT
```

**`-disposition[:stream_specifier] value (output,per-stream)`**

设置流的属性。

这个选项会覆盖从输入文件中拷贝的流属性。也可以通过设置0值来删除某个属性。

可以使用如下的属性，

`default`
`dub`
`original`
`comment`
`lyrics`
`karaoke`
`forced`
`hearing_imparied`
`visual_imparied`
`clean_effects`
`captions`
`descriptions`
`metadata`

例如， 设置第二个音频流为默认流：

```
ffmpeg -i in.mkv -disposition:a:1 default out.mkv
```

设置第二个字幕流为默认流，并且删除第一个字幕流的默认属性:

```
ffmpeg -i INPUT -disposition:s:0 0 -disposition:s:1 default OUTPUT
```

**`-program [title=title:][program_num=program_num:]st=stream[:st=stream...] (output)`**

使用指定的标题和节目号创建节目。并且给节目添加指定的流。

**`target type (output)`**

指定目标文件类型，如 `vcd`, `svcd`, `dvd`, `dv`, `dv50`. 文件类型通常添加像`pal-`, `ntsc-` 或 `film-` 的前缀以满足相应的标准。然后所有的像码率，编解码器，内存大小等参数会被自动设置。例如

```
ffmpeg -i myfile.avi -target vcd /tmp/vcd.mpg
```

尽管如此，只要不跟相应的标准冲突，你也可以指定其他的选项，例如:

```
ffmpeg -i myfile.avi -target vcd -bf 2 /tmp/vcd.mpg
```

未完待续。。。



> Written with [StackEdit](https://stackedit.io/).
