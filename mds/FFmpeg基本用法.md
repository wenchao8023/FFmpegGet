# FFmpeg基本用法

## 第一部分 基础

**术语**

术语 | 解释
:-- | :--
容器(Container) | 一种文件格式，比如flv，mkv等。包含下面5种流以及文件头信息。
流(Stream) | 一种视频数据信息的传输方式，5种流：音频，视频，字幕，附件，数据。
帧(Frame) | 代表一幅静止的图像，分为I帧，P帧，B帧。
编解码器(Codec) | 是对视频进行压缩或者解压缩，CODEC =COde （编码） +DECode（解码）
复用(mux) | 把不同的流按照某种容器的规则放入容器，这种行为叫做复用（mux）
解复用(demux) | 把不同的流从某种容器中解析出来，这种行为叫做解复用(demux)

**简介**

FFmpeg的名称来自MPEG视频编码标准，前面的“FF”代表“Fast Forward”，FFmpeg是一套可以用来记录、转换数字音频、视频，并能将其转化为流的开源计算机程序。可以轻易地实现多种视频格式之间的相互转换。FFmpeg的用户有Google，Facebook，Youtube，优酷，爱奇艺，土豆等。

组成库 | 解释
:-- | :--
1.libavformat | 用于各种音视频封装格式的生成和解析，包括获取解码所需信息以生成解码上下文结构和读取音视频帧等功能，包含demuxers和muxer库
2.libavcodec | 用于各种类型声音/图像编解码
3.libavutil | 包含一些公共的工具函数
4.libswscale | 用于视频场景比例缩放、色彩映射转换
5.libpostproc | 用于后期效果处理
6.ffmpeg | 是一个命令行工具，用来对视频文件转换格式，也支持对电视卡实时编码
7.ffsever | 是一个HTTP多媒体实时广播流服务器，支持时光平移
8.ffplay | 是一个简单的播放器，使用ffmpeg 库解析和解码，通过SDL显示

**FFmpeg处理流程**

> 过滤器(Filter)

在多媒体处理中，filter的意思是被编码到输出文件之前用来修改输入文件内容的一个软件工具。如：视频翻转，旋转，缩放等。

`语法：[input_link_label1][input_link_label2]… filter_name=parameters [output_link_label1][output_link_label2]…`

过滤器图link label ：是标记过滤器的输入或输出的名称

> 视频过滤器 -vf

如testsrc视频按顺时针方向旋转90度

`ffplay -f lavfi -i testsrc -vf transpose=1`

如testsrc视频水平翻转(左右翻转)

`ffplay -f lavfi -i testsrc -vf hflip`

> 音频过滤器 -af

实现慢速播放，声音速度是原始速度的50%

`ffplay p629100.mp3 -af atempo=0.5`

> 如何实现顺时针旋转90度并水平翻转？ 

**过滤器链（Filterchain）**

基本语法

`Filterchain = 逗号分隔的一组filter`

`语法：“filter1,filter2,filter3,…filterN-2,filterN-1,filterN”`

顺时针旋转90度并水平翻转

`ffplay -f lavfi -i testsrc -vf transpose=1,hflip`

基本步骤

1. 源视频宽度扩大两倍。`ffmpeg -i jidu.mp4 -t 10 -vf pad=2*iw output.mp4`
2. 源视频水平翻转。`ffmpeg -i jidu.mp4 -t 10 -vf hflip output2.mp4`
3. 水平翻转视频覆盖output.mp4。`ffmpeg -i output.mp4 -i output2.mp4 -filter_complex overlay=w compare.mp4`

以上步骤可以用带有链接标记的过滤器图(Filtergraph)只需一条命令。

**过滤器图（Filtergraph）**

基本语法

```
Filtergraph = 分号分隔的一组filterchain
“filterchain1;filterchain2;…filterchainN-1;filterchainN”
```

Filtergraph的分类

1. 简单(simple) 一对一
2. 复杂(complex）多对一， 多对多

用ffplay直接观看结果：
`fplay -f lavfi -i testsrc -vf split[a][b];[a]pad=2*iw[1];[b]hflip[2];[1][2]overlay=w`


* F1: split过滤器创建两个输入文件的拷贝并标记为[a],[b]
* F2: [a]作为pad过滤器的输入，pad过滤器产生2倍宽度并输出到[1].
* F3: [b]作为hflip过滤器的输入，vflip过滤器水平翻转视频并输出到[2].
* F4: 用overlay过滤器把 [2]覆盖到[1]的旁边.


**选择媒体流**
一些多媒体容器比如AVI，mkv，mp4等，可以包含不同种类的多个流，如何从容器中抽取各种流呢？
语法
-map file_number:stream_type[:stream_number]

特别流符号的说明

1. -map 0 选择第一个文件的所有流
2. -map i:v 从文件序号i(index)中获取所有视频流， -map i:a 获取所有音频流，-map i:s 获取所有字幕流等等。
3. 特殊参数-an,-vn,-sn分别排除所有的音频，视频，字幕流。

注意:文件序号和流序号从0开始计数。

## 第二部分 帮助和查看帮助

* FFmpeg工具有一个巨大的控制台帮助。下表描述了可用的一些选项，斜体字表示要被替换的项，ffplay和ffprobe也有一些类似的选项。

**帮助**

* 可用的bit流 ：ffmpeg –bsfs
* 可用的编解码器：ffmpeg –codecs
* 可用的解码器：ffmpeg –decoders
* 可用的编码器：ffmpeg –encoders
* 可用的过滤器：ffmpeg –filters
* 可用的视频格式：ffmpeg –formats
* 可用的声道布局：ffmpeg –layouts
* 可用的license：ffmpeg –L
* 可用的像素格式：ffmpeg –pix_fmts
* 可用的协议：ffmpeg -protocals

## 第三部分 码率、帧率和文件大小

码率和帧率是视频文件的最重要的基本特征，对于他们的特有设置会决定视频质量。如果我们知道码率和时长那么可以很容易计算出输出文件的大小。

* 帧率：帧率也叫帧频率，帧率是视频文件中每一秒的帧数，肉眼想看到连续移动图像至少需要15帧。
* 码率：比特率(也叫码率，数据率)是一个确定整体视频/音频质量的参数，秒为单位处理的字节数，码率和视频质量成正比，在视频文件中中比特率用bps来表达。

**帧率**

1. 用 -r 参数设置帧率。`ffmpeg –i input –r fps output`
2. 用fps filter设置帧率。`ffmpeg -i clip.mpg -vf fps=fps=25 clip.webm`

**码率**

这三种方式都是设置29.97fps的码率

```
ffmpeg -i input.avi -r 29.97 output.mpg
ffmpeg -i input.avi -r 30000/1001 output.mpg
ffmpeg -i input.avi -r netsc output.mpg
```

**码率、文件大小**

设置码率 –b 参数

`ffmpeg -i film.avi -b 1.5M film.mp4`

音频：-b:a     视频： - b:v

设置视频码率为1500kbps

ffmpeg -i input.avi -b:v 1500k output.mp4

**控制输出文件大小**

-fs (file size首字母缩写) 

ffmpeg -i input.avi -fs 1024K output.mp4

计算输出文件大小

(视频码率+音频码率) * 时长 /8 = 文件大小K

## 第四部分 调整视频分辨率

* 用-s参数设置视频分辨率，参数值wxh，w宽度单位是像素，h高度单位是像素

	`ffmpeg -i input_file -s 320x240 output_file`

* 预定义的视频尺寸

	下面两条命令有相同效果

		ffmpeg -i input.avi -s 640x480 output.avi
		ffmpeg -i input.avi -s vga output.avi
	
* Scale filter调整分辨率

	Scale filter的优点是可以使用一些额外的参数
	
		Scale=width:height[:interl={1|-1}]
	
	下面两条命令有相同效果
	
		ffmpeg -i input.mpg -s 320x240 output.mp4 
		ffmpeg -i input.mpg -vf scale=320:240 output.mp4
	
* 对输入视频成比例缩放

	改变为源视频一半大小

		ffmpeg -i input.mpg -vf scale=iw/2:ih/2 output.mp4
		
	改变为原视频的90%大小
		
		ffmpeg -i input.mpg -vf scale=iw*0.9:ih*0.9 output.mp4
		
	在未知视频的分辨率时，保证调整的分辨率与源视频有相同的横纵比
	
	宽度固定400，高度成比例：
		
		ffmpeg -i input.avi -vf scale=400:400/a
		ffmpeg -i input.avi -vf scale=400:-1
		
	相反地，高度固定300，宽度成比例：
	
		ffmpeg -i input.avi -vf scale=-1:300
		ffmpeg -i input.avi -vf scale=300*a:300
	
	
## 第五部分 裁剪/填充视频

* 裁剪视频crop filter

* 从输入文件中选取你想要的矩形区域到输出文件中,常见用来去视频黑边。

		crop:ow[:oh[:x[:y:[:keep_aspect]]]]
		
* 裁剪输入视频的左三分之一，中间三分之一，右三分之一

		ffmpeg -i input -vf crop=iw/3:ih :0:0 output 
		ffmpeg -i input -vf crop=iw/3:ih :iw/3:0 output 
		ffmpeg -i input -vf crop=iw/3:ih :iw/3*2:0 output
		
* 裁剪帧的中心

	当我们想裁剪区域在帧的中间时，裁剪filter可以跳过输入x和y值，他们的默认值是

	Xdefault  = ( input width - output width)/2 

	Ydefault  = ( input height - output height)/2
	
		ffmpeg -i input_file -v crop=w:h output_file
	
	裁剪中间一半区域：
	
		ffmpeg -i input.avi -vf crop=iw/2:ih/2 output.avi