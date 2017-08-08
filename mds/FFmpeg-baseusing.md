# FFmpeg基本用法

[参考链接](http://www.360doc.com/content/17/0804/14/46078576_676615270.shtml)
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

`ffplay -i testsrc -vf transpose=1`

如testsrc视频水平翻转(左右翻转)

`ffplay -i testsrc -vf hflip`

> 音频过滤器 -af

实现慢速播放，声音速度是原始速度的50%

`ffplay p629100.mp3 -af atempo=0.5`

> 如何实现顺时针旋转90度并水平翻转？ 

**过滤器链（Filterchain）**

基本语法

`Filterchain = 逗号分隔的一组filter`

`语法：“filter1,filter2,filter3,…filterN-2,filterN-1,filterN”`

顺时针旋转90度并水平翻转

`ffplay -i testsrc -vf transpose=1,hflip`

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
`fplay -i testsrc -vf split[a][b];[a]pad=2*iw[1];[b]hflip[2];[1][2]overlay=w`


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

这三种方式都是设置29.97fps的帧率

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
		
		ffmpeg -i input.avi -vf scale=400:400/a output.mp4
		ffmpeg -i input.avi -vf scale=400:-1 output.mp4
		
	相反地，高度固定300，宽度成比例：
	
		ffmpeg -i input.avi -vf scale=-1:300 output.mp4
		ffmpeg -i input.avi -vf scale=300*a:300 output.mp4
	
	
## 第五部分 裁剪/填充视频

* 裁剪视频crop filter

* 从输入文件中选取你想要的矩形区域到输出文件中,常见用来去视频黑边。

		crop:ow[:oh[:x[:y:[:keep_aspect]]]]
		
	四个参数含义：宽度、高度、x的位置、y的位置
		
* 裁剪输入视频的左三分之一，中间三分之一，右三分之一

		ffmpeg -i input -vf crop=iw/3:ih :0:0 output 
		ffmpeg -i input -vf crop=iw/3:ih :iw/3:0 output 
		ffmpeg -i input -vf crop=iw/3:ih :iw/3*2:0 output
		
* 裁剪帧的中心

	当我们想裁剪区域在帧的中间时，裁剪filter可以跳过输入x和y值，他们的默认值是

	Xdefault  = ( input width - output width)/2 

	Ydefault  = ( input height - output height)/2
	
		ffmpeg -i input_file -v crop=Xdefault:Ydefault output_file
	
	裁剪中间一半区域：
	
		ffmpeg -i input.avi -vf crop=iw/2:ih/2 output.avi
		
* 比较裁剪后的视频和源视频

		ffplay -i jidu.mp4 -vf split[a][b];[a]drawbox=x=(iw-300)/2:(ih-300)/2:w=300:h=300:c=yellow[A];[A]pad=2*iw[C];[b]crop=300:300:(iw-300)/2:(ih-300)/2[B];[C][B]overlay=w*2.4:40
		
* 自动检测裁剪区域

	cropdetect  filter 自动检测黑边区域
	
		ffplay jidu.mp4 -vf cropdetect
		
* 然后用检测到的值来裁剪视频

		ffplay jidu.mp4 –vf crop=672:272:0:54
		
* 填充(pad)

	在视频帧上增加一快额外额区域，经常用在播放的时候显示不同的横纵比
	
		pad=width[:height:[:x[:y:[:color]]]]
		
	五个参数含义：填充宽、填充高、填充x坐标、填充y坐标、填充颜色
		
		ffmpeg -i input_file -vf pad=860:660:30:30:pink outputFile
		
	* 填充图片

		创建一个30个像素的粉色宽度来包围一个SVGA尺寸的图片：
		
			ffmpeg -i photo.jpg -vf pad=860:660:30:30:pink framed_photo.jpg
			
	* 填充视频

			ffmpeg -i test.mp4 -vf pad=860:660:30:30:pink framed_video.mp4

* `4:3 -> 16:9`

	一些设备只能播放 `16:9` 的横纵比，`4:3`的横纵比必须在水平方向的两边填充成`16:9`，
	
	高度被保持，宽度等于高度乘以16/9，x（输入文件水平位移）值由表达式(output_width - input_width)/2来计算。
	
	* 4：3到16:9的通用命令是：

			ffmpeg -i input -vf pad=ih*16/9:ih :(ow-iw)/2:0:color output
		
			ffplay -i testsrc -vf pad=ih*16/9:ih:(ow-iw)/2:0:pink
			
	* 16:9到4:3

		为了用4:3的横纵比来显示16:9的横纵比，填充输入文件的垂直两边，宽度保持不变，高度是宽度的3/4，y值（输入文件的垂直偏移量）是由一个表达式（output_height-input_height）/2计算出来的。	
		16:9到4:3的通用命令：

			ffmpeg -i input -vf pad=iw :iw*3/4:0:(oh-ih)/2:color output
			
			ffplay -i testsrc=size=320x180 -vf pad=iw:iw*3/4:0:(oh-ih)/2:pink
			
			
## 第六部分 翻转和旋转

### 翻转

* 水平翻转

	语法：`-vf hflip`
	
		ffplay  -i testsrc -vf hflip
			
* 垂直翻转

	语法：`-vf vflip`
	
		ffplay -i testsrc -vf vflip
		
### 旋转

语法：transpose = 0 | 1 | 2 | 3

参数 | 含义
:--: | :--: 
1 | 顺时针旋转90°
3 | 顺时针旋转90°，然后水平翻转
2 | 逆时针旋转90°
0 | 逆时针旋转90°，然后垂直翻转


## 第七部分	模糊和锐化

### 模糊

语法：`boxblur=luma_r:luma_p[:chroma_r:chram_p[:alpha_r:alpha_p]]`

	ffplay -i testscr -vf boxblur=1:10:4:10

参数 : luma_r和alpha_r半径取值范围是0~min(w,h)/2, chroma_r半径的取值范围是0~min(cw/ch)/2

### 锐化

语法：`-vf unsharp=l_msize_x:l_msize_y:l_amount:c_msize_x:c_msize_y:c_amount`

所有的参数是可选的，默认值是5:5:1.0:5:5:0.0

参数具体含义

参数 | 含义 | 取值范围 | 默认值
:--: | :--: | :--: | :--:
l\_msize_x | 水平亮度矩阵 | 3-13 | 5
l\_msize_y | 垂直亮度矩阵 | 3-13 | 5
l\_amount  | 亮度强度		| -2.0-5.0，负数为模糊效果 | 1.0
c\_msize_x | 水平色彩矩阵 | 3-13 | 5
c\_msize_y | 垂直色彩矩阵 | 3-13 | 5
c\_amount  | 色彩强度		| -2.0-5.0，负数为模糊效果 | 1.0

**【例子】**

使用默认值，亮度矩阵为5x5和亮度值为1.0

	ffmpeg -i input -vf unsharp output.mp4
	
高斯模糊效果(比较强的模糊)：

	ffplay -i testsrc -vf unsharp=13:13:-2
	
## 第八部分 覆盖（画中画）

语法： `overlay[=x[:y]`

参数都可选，默认值为0

**【例子】**

* Logo在左上角

		fmpeg -i pair.mp4 -i logo.png -filter_complex overlay pair1.mp4	
	
* Logo在右上角

		ffmpeg -i pair.mp4 -i logo.png -filter_complex overlay=W-w  pair2.mp4
		
* Logo在左下角

		ffmpeg -i pair.mp4 -i logo.png -filter_complex overlay=0:H-h  pair2.mp4
		
* Logo在右下角

		ffmpeg -i pair.mp4 -i logo.png -filter_complex overlay=W-w:H-h  pair2.mp4
		
* 删除logo

	语法：`-vf delogo=x:y:w:h[:t[:show]]`
	
	x:y 离左上角的坐标

	w:h  logo的宽和高
	
	t: 矩形边缘的厚度默认值4
	
	show：若设置为1有一个绿色的矩形，默认值0.
	
		ffplay -i jidu.mp4 -vf delogo=50:51:60:60:100:0