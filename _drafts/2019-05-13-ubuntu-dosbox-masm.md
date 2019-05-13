---
layout: post
title:  "Ubuntu Dosbox MASM"
date:   2019-05-13 17:20:00 +0800
categories: jekyll update
---

## Ubuntu下汇编语言环境

参考 [Run MASM programs on ubuntu.](https://ksaikiranr.wordpress.com/2016/05/01/run-masm-programs-on-ubuntu/)

安装`dosbox`

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
vim dosbox-0.74.conf
```

在尾部添加如下内容，保存退出，重启dosbox
```
mount C: ~/Software/masm
PATH=%PATH%;C:/MASM611/BIN;C:/MASM611/BINR;
C:
```

安装vim for dos

下载地址(https://www.vim.org/download.php#pc) vim73_46d32.zip  (https://www.dosbox.com/wiki/Software:Vim)
解压到C:盘路径VIM73，解压CWSDPMI，运行VIM前执行CWSDPMI.EXE

```
cd VIM73
CWSDPMI.EXE
VIM.EXE
```

修改dosbox的配置文件

```
vim dosbox-0.74.conf
```

在尾部添加如下内容(VIM运行前必须先运行CWSDPMI.EXE，通过dosbox配置文件执行)，保存退出，重启dosbox
```
mount C: ~/Software/masm
PATH=%PATH%;C:/MASM611/BIN;C:/MASM611/BINR;C:/VIM73;C:/VIM73/CSDPMI4B/BIN
C:
CWSDPMI.EXE
```

编写asm程序
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
	MOV AH,09H
	INT 21H
	MOV AX,4CH
	INT 21H
CODE ENDS
END START
```

编译&运行

```
MASM hello.asm
LINK hello.obj
hello.exe
```
