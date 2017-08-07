# Mac 配置FFmpeg环境

[FFmpeg官网](http://ffmpeg.org/)


### 一、安装homebrew

`"homebrew"是Mac平台的一个包管理工具，提供了许多Mac下没有的Linux工具等，而且安装过程很简单。`

有关homebrew的使用
[转载](http://www.cnblogs.com/TankXiao/p/3247113.html)

1. 打开终端输入命令行：

		brew
		
	如果终端输出结果是：
	
	![brew输出信息](https://github.com/wenchao8023/FFmpegGet/blob/master/imgs/brew安装信息.png)

2. 如果不是上面的结果就表示需要安装homebrew，命令行：

		ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
		
3. 卸载homebrew：

		brew cleanup
		
### 二、安装FFmpeg

1. 利用上面的 homebrew 安装 FFmpeg：

		brew install ffmpeg --with-ffplay
		
	除了安装选项 --with-ffplay 外，还可以在安装的时候安装更多的选项：
		
		–with-fdk-aac  (Enable the Fraunhofer FDK AAC library)
		–with-ffplay  (Enable FFplay media player)
		–with-freetype  (Build with freetype support)
		–with-frei0r  (Build with frei0r support)
		–with-libass  (Enable ASS/SSA subtitle format)
		–with-libcaca  (Build with libcaca support)
		–with-libvo-aacenc  (Enable VisualOn AAC encoder)
		–with-libvorbis  (Build with libvorbis support)
		–with-libvpx  (Build with libvpx support)
		–with-opencore-amr  (Build with opencore-amr support)
		–with-openjpeg  (Enable JPEG 2000 image format)
		–with-openssl  (Enable SSL support)
		–with-opus  (Build with opus support)
		–with-rtmpdump  (Enable RTMP protocol)
		–with-schroedinger  (Enable Dirac video format)
		–with-speex  (Build with speex support)
		–with-theora  (Build with theora support)
		–with-tools  (Enable additional FFmpeg tools)
		–without-faac  (Build without faac support)
		–without-lame  (Disable MP3 encoder)
		–without-x264  (Disable H.264 encoder)
		–without-xvid  (Disable Xvid MPEG-4 video encoder)
		–devel  (install development version 2.1.1)
		–HEAD  (install HEAD version)
		
2. 当命令结束之后，查看安装的FFmpeg信息，输入命令：

		brew info ffmpeg
		
	输出结果如下：
	
	![FFmpeg信息](https://github.com/wenchao8023/FFmpegGet/blob/master/imgs/FFmpeg信息.png)
	
	图中有很多库，有红叉的表示没有安装上，绿叉表示安装上了
	
3. 更新 FFmpeg，输入命令行：
		
		brew update
		
	或者是：
	
		brew update ffmpeg
		
4. 如果在`1`中没有选到的库（这里指的是`brew info ffmpeg`中可选的库，并不包括ffplay），可以单独安装，输入命令行：

		brew install [FORMULA...]
		
	安装 yasm 库，输入命令：
		
		brew install yasm
