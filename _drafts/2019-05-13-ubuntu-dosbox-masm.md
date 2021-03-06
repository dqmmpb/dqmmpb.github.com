---
layout: post
title:  "Ubuntu Dosbox MASM"
date:   2019-05-13 17:20:00 +0800
categories: jekyll update
---

## Ubuntu下汇编语言环境

### 安装dosbox & masm

参考 [Run MASM programs on ubuntu.](https://ksaikiranr.wordpress.com/2016/05/01/run-masm-programs-on-ubuntu/)

安装`dosbox`

ps: 虽然dosemu也是模拟dos环境的，但按照https://blog.csdn.net/yilovexing/article/details/54971811 安装的masm无法正常启动，直接报错，所以还是选择dosbox

```
sudo apt-get install dosbox
```

下载MSAM

下载地址：https://sourceforge.net/projects/masm611/

移动到Software目录

```
mv masm611.rar ~/Software/
```

使用unrar解压

```
unrar x masm611.rar
```

创建masm611安装目录

```
mkdir masm
```

运行 dosbox

```
dosbox
```

在dosbox中挂载目录(masm安装目录，masm611安装文件)

```
mount C: ~/Software/masm
mount Y: ~/Software/masm611
Y:
cd DISK1
SETUP.EXE
```

选择`Install the Macro Assembler using defaults`，选择安装到C盘，一路默认Next即可

安装完成后，修改dosbox的配置文件

```
vim .dosbox/dosbox-0.74.conf
```

在尾部添加如下内容，保存退出，重启dosbox
```
mount C: ~/Software/masm
PATH=%PATH%;C:/MASM611/BIN;C:/MASM611/BINR;
C:
```


### 安装vim for dos

下载地址(https://www.vim.org/download.php#pc) vim73_46d32.zip  (https://www.dosbox.com/wiki/Software:Vim)

解压到C:盘路径VIM73，解压CWSDPMI，运行VIM前执行CWSDPMI.EXE

ps: vim for dos并不好用，推荐还是在ubuntu下使用vim编辑asm文件，在通过Ctrl+F4快捷键刷新dosbox目录（同步mount的目录）

```
cd VIM73
CWSDPMI.EXE
VIM.EXE
```

修改dosbox的配置文件

```
vim .dosbox/dosbox-0.74.conf
```

在尾部添加如下内容(VIM运行前必须先运行CWSDPMI.EXE，通过dosbox配置文件执行)，保存退出，重启dosbox
```
mount C: ~/Software/masm
PATH=%PATH%;C:/MASM611/BIN;C:/MASM611/BINR;C:/VIM73;C:/VIM73/CSDPMI4B/BIN
C:
CWSDPMI.EXE
```

### 编写asm程序

#### 源码

```
mkdir TEST
VIM TEST/hello.asm
```

```asm
DATA SEGMENT
        HELLO DB "Hello World!$"
DATA ENDS

CODE SEGMENT
        ASSUME CS:CODE,DS:DATA
START:
        MOV AX,DATA
        MOV DS,AX
        LEA DX,HELLO
        MOV AH,9H  ; Output a string
        INT 21H
        MOV AL,0H  ; 0 for normal exit, non-zero for error
        MOV AH,4CH ; Exit
        INT 21H
CODE ENDS
END START

```

#### 编译&运行

```
MASM hello.asm
LINK hello.obj
hello.exe
```
ps:

错误代码：网上代码`MOV AX, 4CH`用于Exit，但结果却是exe程序无法返回dos，一度认为是ubuntu下安装的dosbox或masm有问题导致的。

正确代码：`MOV AH, 4CH`，参看（https://www.csc.depauw.edu/~bhoward/asmtut/asmtut12.html ）或者`MOV AX,4C00H`,建议单独设置`AH`和`AL`.如果不关心`AL`的值,`MOV AL,0H`可省略

### 安装debugx

#### 安装

安装debugx进行调试（https://thestarman.pcministry.com/asm/debug/debug.htm ） ，下载（https://sites.google.com/site/pcdosretro/enhdebug ） 最后链接下载

解压到C:盘目录DEBUGX， 修改dosbox的配置文件，添加环境变量

```
vim .dosbox/dosbox-0.74.conf
```
添加
```
mount C: ~/Software/masm
PATH=%PATH%;C:/MASM611/BIN;C:/MASM611/BINR;C:/VIM73;C:/VIM73/CSDPMI4B/BIN;C:/DEBUGX
C:
CWSDPMI.EXE
```

#### 调试

执行`debug`或`debug.com`

```
debug HELLO.EXE
-u
```

### debugx相关命令

常用命令
```
u 对机器代码反汇编显示
r 观看和修改寄存器的值。
d 显示内存区域的内容。 如：`d 0000` 其中0000为内存地址起始位置
g 执行汇编指令。 如：`g 0003` 其中0003为指令地址（执行到该指令前一条指令，0003对应的指令不执行）
t 单步跟踪
p 单步跟踪，与T命令不同的是：P命令不会跟踪进入子程序或软中断。
q 退出DEBUG，回到DOS状态。 
```
更多命令参看（https://sites.google.com/site/pcdosretro/enhdebug ）

### 其他参考资料

https://thestarman.pcministry.com/asm/debug/DOSstub.htm

https://bingyishow.top/Technical-article/54.html


#### dosbox常用快捷键

```
Alt+Enter       //切换全屏
Alt+Pause       //暂停模拟
Ctrl+F1         //改变键盘映射
Ctrl+Alt+F5     //开始/停止录制视频
Ctrl+F4         //交换挂载的磁盘映像，也就是更新磁盘文件
Ctrl+F5         //截图
Ctrl+F6         //开始/停止录制声音
Ctrl+F7         //减少跳帧
Ctrl+F8         //增加跳帧
Ctrl+F9         //关闭DOSBOX
Ctrl+F10        //捕捉/释放鼠标
Ctrl+F11        //模拟减速
Ctrl+F12        //加速模拟
Alt+F12         //不锁定速度
```

#### 使用FreeDos编辑器

下载：https://archive.org/details/freedos_edit_09a_

解压到C:盘目录EDIT， 修改dosbox的配置文件，添加环境变量

```
vim .dosbox/dosbox-0.74.conf
```
添加
```
mount C: ~/Software/masm
PATH=%PATH%;C:/MASM611/BIN;C:/MASM611/BINR;C:/VIM73;C:/VIM73/CSDPMI4B/BIN;C:/DEBUGX;C:/EDIT
C:
CWSDPMI.EXE
```