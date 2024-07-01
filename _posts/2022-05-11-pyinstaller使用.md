---
layout: post
catalog: true
tags:
  - Python
---


**安装pyinstaller 库** 

```
pip install pyinstaller
```

**打包命令**

```
pyinstaller options ~/myproject/source/myscript.py
```

**缩短命令**

 in GNU/Linux:

```
pyinstaller --noconfirm --log-level=WARN \
    --onefile --nowindow \
    --add-data="README:." \
    --add-data="image1.png:img" \
    --add-binary="libfoo.so:lib" \
    --hidden-import=secret1 \
    --hidden-import=secret2 \
    --upx-dir=/usr/local/share/ \
    myscript.spec
```

in Windows

```
pyinstaller --noconfirm --log-level=WARN ^
    --onefile --nowindow ^
    --add-data="README;." ^
    --add-data="image1.png;img" ^
    --add-binary="libfoo.so;lib" ^
    --hidden-import=secret1 ^
    --hidden-import=secret2 ^
    --icon=..\MLNMFLCN.ICO ^
    myscript.spec
```

# 1. 常规选项

```
-h , --help	show this help message and exit

-v , --version	Show program version info and exit.

--distpath DIR	Where to put the bundled app (default: ./dist)

--workpath WORKPATH
 	Where to put all the temporary work files, .log, .pyz and etc. (default: ./build)
 	
-y , --noconfirm
 	Replace output directory (default: SPECPATH/dist/SPECNAME) without asking for confirmation
 	
--upx-dir UPX_DIR
 	Path to UPX utility (default: search the execution path)
 	
-a , --ascii	Do not include unicode encoding support (default: included if available)

--clean	Clean PyInstaller cache and remove temporary files before building.

--log-level LEVEL
 	Amount of detail in build-time console messages. LEVEL may be one of TRACE, DEBUG, INFO, WARN, ERROR, CRITICAL (default: INFO).
```

# 2. 要生成什么

```
-D , --onedir	Create a one-folder bundle containing an executable (default)

-F , --onefile	Create a one-file bundled executable.

--specpath DIR	Folder to store the generated spec file (default: current directory)

-n NAME , --name NAME
 	Name to assign to the bundled app and spec file (default: first script’s basename)
```

# 3. 捆绑选项

引用了哪些外部文件，他们在哪里

```
--add-data <SRC;DEST or SRC:DEST>
 	Additional non-binary files or folders to be added to the executable. The path separator is platform specific, os.pathsep (which is ; 在 Windows and : on most unix systems) is used. This option can be used multiple times.
 	
--add-binary <SRC;DEST or SRC:DEST>
 	Additional binary files to be added to the executable. 见 --add-data option for more details. This option can be used multiple times.
 	
-p DIR , --paths DIR
 	A path to search for imports (like using PYTHONPATH). Multiple paths are allowed, separated by ‘:’, or use this option multiple times
 	
--hidden-import MODULENAME , --hiddenimport MODULENAME
 	Name an import not visible in the code of the script(s). This option can be used multiple times.
 	
--additional-hooks-dir HOOKSPATH
 	An additional path to search for hooks. This option can be used multiple times.
 	
--runtime-hook RUNTIME_HOOKS
 	Path to a custom runtime hook file. A runtime hook is code that is bundled with the executable and is executed before any other code or module to set up special features of the runtime environment. This option can be used multiple times.
 	
--exclude-module EXCLUDES
 	Optional module or package (the Python name, not the path name) that will be ignored (as though it was not found). This option can be used multiple times.
 	
--key KEY	The key used to encrypt Python bytecode.
```

# 4. 怎么生成

```
-d <all,imports,bootloader,noarchive> , --debug <all,imports,bootloader,noarchive>
 	
Provide assistance with debugging a frozen application. This argument may be provided multiple times to select several of the following options.

all: All three of the following options.
imports: specify the -v option to the underlying Python interpreter, causing it to print a message each time a module is initialized, showing the place (filename or built-in module) from which it is loaded. See https://docs.python.org/3/using/cmdline.html#id4 .
bootloader: tell the bootloader to issue progress messages while initializing and starting the bundled app. Used to diagnose problems with missing imports.
noarchive: instead of storing all frozen Python source files as an archive inside the resulting executable, store them as files in the resulting output directory.

-s , --strip	Apply a symbol-table strip to the executable and shared libs (not recommended for Windows)

--noupx	Do not use UPX even if it is available (works differently between Windows and *nix)

--upx-exclude FILE
 	Prevent a binary from being compressed when using upx. This is typically used if upx corrupts certain binaries during compression. FILE is the filename of the binary without path. This option can be used multiple times.
```

# 5. win和mac的特殊选项

```
-c , --console , --nowindowed
 	Open a console window for standard i/o (default). On Windows this option will have no effect if the first script is a ‘.pyw’ file.
 	
-w , --windowed , --noconsole
 	Windows and Mac OS X: do not provide a console window for standard i/o. On Mac OS X this also triggers building an OS X .app bundle. On Windows this option will be set if the first script is a ‘.pyw’ file. This option is ignored in *NIX systems.
 	
-i <FILE.ico or FILE.exe,ID or FILE.icns> , --icon <FILE.ico or FILE.exe,ID or FILE.icns>
 	FILE.ico: apply that icon to a Windows executable. FILE.exe,ID, extract the icon with ID from an exe. FILE.icns: apply the icon to the .app bundle on Mac OS X
```

# 6. win的特殊选项

```
--version-file FILE
 	add a version resource from FILE to the exe
 	
-m <FILE or XML> , --manifest <FILE or XML>
 	add manifest FILE or XML to the exe
 	
-r RESOURCE , --resource RESOURCE
 	Add or update a resource to a Windows executable. The RESOURCE is one to four items, FILE[,TYPE[,NAME[,LANGUAGE]]]. FILE can be a data file or an exe/dll. For data files, at least TYPE and NAME must be specified. LANGUAGE defaults to 0 or may be specified as wildcard * to update all resources of the given TYPE and NAME. For exe/dll files, all resources from FILE will be added/updated to the final executable if TYPE, NAME and LANGUAGE are omitted or specified as wildcard *.This option can be used multiple times.
 	
--uac-admin	Using this option creates a Manifest which will request elevation upon application restart.

--uac-uiaccess	Using this option allows an elevated application to work with Remote Desktop.
```

# 7. 将资源文件一起打包进exe

基本原理：Pyinstaller 可以将资源文件一起bundle到exe中，当exe在运行时，会生成一个临时文件夹，程序可通过`sys._MEIPASS`访问临时文件夹中的资源

**建立资源文件夹**

把用到的外部资源png、txt等其他文件放到一个文件夹下

**修改主程序py**

修改主程序中读取资源数据路径部分的代码

```python
import sys
import os

#生成资源文件目录访问路径
def resource_path(relative_path):
    if getattr(sys, 'frozen', False): #是否Bundle Resource
        base_path = sys._MEIPASS
    else:
        base_path = os.path.abspath(".")
    return os.path.join(base_path, relative_path)

#访问res文件夹下数据.txt的内容
filename = resource_path(os.path.join("res","数据.txt"))
```

**打包exe**

打包py程序

**修改spec**

```
修改前datas=[]
修改后datas=[('res','res')]
意思是
将主程序当前目录下的res目录（及其目录中的文件）加入目标exe中，在运行时放在零时文件的根目录下，名称为res

datas[]列表里面是元组，左边是你要添加的filename（相对路径即可），右边是拷贝到项目中之后的文件夹名字。

比如：(‘res/bg.jpg’, ‘res’) 中的 ‘res/bg.jpg’ 表示工程根目录下res文件夹下有bg.jpg图片，拷贝到项目之后的res文件夹。
(‘exam.db’, ‘.’) 中的’exam.db’是工程根目录下的文件，’.'表示根目录，即拷贝到项目中的根目录下。
```

**再次打包exe**

打包spec文件