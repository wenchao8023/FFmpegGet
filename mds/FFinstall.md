# Mac 配置FFmpeg环境

[FFmpeg官网](http://ffmpeg.org/)


### 一、安装homebrew

`"homebrew"是Mac平台的一个包管理工具，提供了许多Mac下没有的Linux工具等，而且安装过程很简单。`

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

		brew install ffmpeg
		
2. 当命令结束之后，查看安装的FFmpeg信息，输入命令：

		brew info ffmpeg
		
	输出结果如下：
	
	![FFmpeg信息](https://github.com/wenchao8023/FFmpegGet/blob/master/imgs/FFmpeg信息.png)
	
	图中有很多库，有红叉的表示没有安装上，绿叉表示安装上了
	
3. 更新 FFmpeg，输入命令行：
		
		brew update
		
	或者是：
	
		brew update ffmpeg
		
4. 安装某个特定的库，输入命令行：

		brew install [FORMULA...]
		
	安装 yasm 库，输入命令：
		
		brew install yasm
