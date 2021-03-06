# 第二章：基础教程

## 2.1 介绍

我们下载或者购买的大部分软件包都带有安装程序。安装程序用来完成文件的拷贝或者更新、写注册表键值、写入配置信息、创建快捷方式等等。所有这些都是自动完成的，而用户需要做的仅仅是提供一些信息，剩余的全部由安装程序完成。用户通过安装向导，做出适当的选择，然后就是等待安装完成了。等安装程序完成之后，用户要做的就是简单的启动程序。用户不需要关心任何事情，因为所有的安装步骤都有安装程序来完成。

NSIS就是一个为开发者生成这样安装程序的工具。NSIS可以创建简单的仅仅拷贝文件的安装包，也可以创建包含处理高级任务的安装包，比如写注册表键、设置环境变量、从网络下载最新的文件以及自定义配置文件等等。NSIS非常便利，它的脚本语言简单易学。

NSIS将所有的文件和安装脚本编译成为一个可执行文件，使您的应用程序便于发布。NSIS仅对数据增加了大约34KB的NSIS代码（对于默认配置）。NSIS虽然占用的开销极小，但由于它强大的脚本语言以及对外部插件的支持，其提供了大量的配置选项。

## 2.2 脚本文件

创建NSIS安装包第一件要做的事就是编写一个NSIS脚本。NSIS脚本就是一个具有特定语法的常规文本文件。你可以用任何一个文本编辑器来编辑脚本。通常建议使用带有行号显示的文本编辑器，因为NSIS使用行号来指出错误的出处以及对潜在的错误给予警告。同时也建议使用支持语法高亮功能的编辑器。你可以在[NSIS Wiki](http://nsis.sf.net/)下载NSIS专用的带语法高亮的的编辑器。

在NSIS脚本中，每一行都被当做命令处理。如果你的命令放在一行太长的话，可以使用反斜线'\'作为一行的结束，编译器将把下一行作为此行的内容进行解析。比如：

```NSIS
Messagebox MB_OK|MB_ICONINFORMATION \
"This is a sample that shows how to use line breaks for larger commands in NSIS scripts"
```

如果你想在字符串中使用双引号，要么使用$\"对双引号进行转移，要么使用一种不同的引号作为字符串的开始，比如单引号(')或反引号(`)。

关于脚本格式的细节，请参阅[脚本文件格式](http://nsis.sourceforge.net/Docs/Chapter4.html#fileformat)。

脚本文件的默认扩展名为.nsi。头文件的扩展名是.nsh。通过把代码分成多个部分并放入不同的头文件来帮助你整理代码，可以把函数和宏放在头文件中，然后在不同的安装包中引用这些文件。这会使得更新代码变得容易，也使代码更易读。使用!include命令来包含头文件，在NSIS目录下的Include目录中的头文件可以直接通过他们的名字来包含，比如：

```NSIS
!include Sections.nsh
```

## 2.3 脚本结构

NSIS脚本包含安装包属性(Attributes),页面(Pages)和段(Sections)/函数(Functions)。也可以使用编译指令进行编译器操作。其中OutFile指令是必须要有的，它告诉NSIS安装包应该输出到哪里，同时脚本文件必须包含至少一个Section。

### 2.3.1 安装包属性

安装包属性决定安装包的行为和外观。通过这些属性可以改变在安装时展示的文本、安装类型等。其中大部分命令只能在运行时设置而不能改变。

其他的基本指令包括Name和InstallDir。

更多的安装包属性信息，请参见[Installer Attributes](http://nsis.sourceforge.net/Docs/Chapter4.html#instattribs)

### 2.3.2 页面

非静默安装会有一组向导界面用于配置安装程序。你可以通过Page命令设置展示哪些界面（或用PageEx实现高级设定）。典型的设置界面可能包含：

> - Page license
> - Page components
> - Page directory
> - Page instfiles
> - UninstPage uninstConfirm
> - UninstPage instfiles

对于安装程序，这组典型界面将会显示一个许可协议界面、组件安装选择界面、安装目录选择、安装进度界面。对于卸载程序，它将显示一个确认页面、卸载进度界面。

### 2.3.3 段（Sections）

用户在安装程序中选择安装多个部件是很常见的，比如在NSIS发布的安装程序中你可以选择安装额外的工具、插件、示例等等。每一个组件都拥有它自己的一段代码。如果用户选择安装这个组件，那么组件对应的代码就会被执行。在脚本中，这段代码在段（Section)中定义。每一个section对应一个组件页面中的组件。段的名字就是组件的名字，组件被选择后段中的代码就会被执行。安装程序可以只有一个段，但是如果需要让用户来选择安装某些组件，就需要用多个段来完成。

卸载程序也可以有多个段，卸载程序的段名需要加前缀'un.'。例如：

```NSIS
Section "Installer Section"
SectionEnd

Section "un.Uninstaller Section"
SectionEnd
```

sections中的指令与安装程序属性指令有很大的不同，它们在用户的计算机运行安装程序时运行。这些指令可用于提取文件、读写注册表、INI文件和常规文件，创建目录、创建快捷方式等等。可以参考[Instructions](http://nsis.sourceforge.net/Docs/Chapter4.html#instr)获取更多信息。

最基本的指令，1.SetOutPath：设置程序的安装路径，2.File：提取文件，例：

```NSIS
Section "My Program"
    SetOutPath $INSTDIR
    File "My Program.exe"
    File "Readme.txt"
SectionEnd
```

关于sections的更多信息请参见[Sections](http://nsis.sourceforge.net/Docs/Chapter4.html#sections)。

### 2.3.4 函数(Functions)

函数(Functions)类似于段(sections)，其中包含脚本代码。段与函数的区别是它们被调用的方式。有两种类型的函数：用户函数和回调函数。

用户函数由用户在段或者其他函数中通过Call指令来调用。用户函数只由在用户主动调用它们的时候才会执行。用户函数执行完毕后，安装程序会继续执行Call指令后面的指令，除非在用户函数里终止安装（aborted the installation）。安装程序需要在多处执行同一段代码时，使用用户函数最为合适，把共用的代码放到函数里面不仅节省了复制代码的时间，同时也更易于维护。

回调函数在发生某些预定义事件时，由安装程序调用，比如安装程序开始执行时。回调函数是可选的。比如在安装程序开始执行时弹出一个欢迎界面，可以定义一个叫做.onInit的函数。NSIS编译器会通过函数名识别出这是一个回调函数并在安装程序开始时调用它。

```NSIS
Function .onInit
    MessageBox MB_YESNO "This will install My Program. Do you wish to continue?" IDYES gogogo
        Abort
    gogogo:
FunctionEnd
```

Abort在回调函数中具有特殊意义。不同的回调函数中Abort具有不同的意义，更多信息参见[回调函数](http://nsis.sourceforge.net/Docs/Chapter4.html#callbacks)。上面这个例子中，Abort通知安装程序停止初始化安装程序并立即退出。

更多关于函数的信息请参见[Functions](http://nsis.sourceforge.net/Docs/Chapter4.html#functions)。

### 2.3.5 使用脚本

#### 2.3.5.1 逻辑代码结构

代码的条件执行或循环执行可以使用StrCmp、IntCmp、IfErrors、Goto等指令来控制。然而，还可以用更便利的方式来完成。LogicLib提供了一些非常简单的宏用来简化构建复杂的逻辑结构。其语法类似于其他编程语言，对于新手和高手而言都更易于使用，语法说明位于[LogicLib.nsh](http://nsis.sourceforge.net/Include/LogicLib.nsh)中。


例如，不使用LogicLib，检查一个变量的值的实现如下：

```NSIS
StrCmp $0 'some value' 0 +3
  MessageBox MB_OK '$$0 is some value'
  Goto done
StrCmp $0 'some other value' 0 +3
  MessageBox MB_OK '$$0 is some other value'
  Goto done
# else
  MessageBox MB_OK '$$0 is "$0"'
done:
```

对于实现相同功能，如果使用了LogicLib，代码将会易于阅读和理解，如下：

```NSIS
${If} $0 == 'some value'
  MessageBox MB_OK '$$0 is some value'
${ElseIf} $0 == 'some other value'
  MessageBox MB_OK '$$0 is some other value'
${Else}
  MessageBox MB_OK '$$0 is "$0"'
${EndIf}
```

使用switch也可以实现相同的功能：

```NSIS
${Switch} $0
  ${Case} 'some value'
    MessageBox MB_OK '$$0 is some value'
    ${Break}
  ${Case} 'some other value'
    MessageBox MB_OK '$$0 is some other value'
    ${Break}
  ${Default}
    MessageBox MB_OK '$$0 is "$0"'
    ${Break}
${EndSwitch}
```

多个条件判断也是支持的，下面的代码将在$0和$1都为空的时候，给用户提示一条信息：

```NSIS
${If} $0 == ''
${AndIf} $1 == ''
  MessageBox MB_OK|MB_ICONSTOP 'both are empty!'
${EndIf}
```

LogicLib去除了对标签(label)和相对跳转的依赖，防止产生标签命名冲突，也省去了每次更改脚本时要调整相对跳转偏移的麻烦。

LogicLib支持常规的while、do、for循环使循环更容易实现，下面的示例使用LogicLib实现计数到5的功能：

```NSIS
StrCpy $R1 0
${While} $R1 < 5
  IntOp $R1 $R1 + 1
  DetailPrint $R1
${EndWhile}
```

```NSIS
${For} $R1 1 5
  DetailPrint $R1
${Next}
```

```NSIS
StrCpy $R1 0
${Do}
  IntOp $R1 $R1 + 1
  DetailPrint $R1
${LoopUntil} $R1 >= 5
```

使用LogicLib需要在脚本的头部添加下面一行语句：

```NSIS
!include LogicLib.nsh
```

更多的示例可以参考[LogicLib.nsi](http://nsis.sourceforge.net/Examples/LogicLib.nsi)。

#### 2.3.5.2 变量(Variables)

可以使用[Var](http://nsis.sourceforge.net/Docs/Chapter4.html#var)命令来声明自己的变量。变量是全局的，可以在所有的段(Section)和函数(Function)中使用。

声明和使用用户变量：

```NSIS
Var BLA ;声明变量

Section bla
  StrCpy $BLA "123" ;现在你可以使用变量$BLA了
SectionEnd
```

除此之外，还有一个栈空间，用来作为临时存储。命令[Push](http://nsis.sourceforge.net/Docs/Chapter4.html#Push)和[Pop](http://nsis.sourceforge.net/Docs/Chapter4.html#Pop)用来访问栈空间，Push向栈中增加一个值，Pop从栈中移除一个值并赋给变量。

对于所有代码，有20个[寄存器变量](http://nsis.sourceforge.net/Docs/Chapter4.html#varother)($0-$9,$R0-$R9)可以用。这些静态的变量不需要声明也不会产生命名冲突。如果你想在多处代码中共用这些变量，需要在执行其他代码前把变量保存到栈中并在其他代码执行之后再从栈中恢复原来的值。

在调用函数之后，这些变量还是包含以前的值。注意有多个变量时的操作顺序（后入先出）：

```NSIS
Function bla
  Push $R0      ;保存变量
  Push $R1

    ...code... ;调用其他函数或执行某些代码

  Pop $R1       ;恢复变量
  Pop $R0
FunctionEnd
```

#### 2.3.5.3 调试脚本 (Debugging Scrips)

当使用NSIS所做的工作越多，脚本就会越复杂，这会导致潜在错误的增加，尤其是处理的变量很多的时候。有一些方法可以帮助我们调试代码，我们可以使用MessageBoxs或者DetailPrint来显示变量的内容。借助DumpState插件，我们可以得到所有变量的概览。安装程序的所有行为默认会在Log窗口中打印输出。可以通过在Log窗口上右击选择“Copy Detail To Clipboard”来访问日志。还有一种方式是直接把日志写入到文件里，请参考[这里](http://nsis.sourceforge.net/Docs/AppendixD.html#dumplogtofile)。

### 2.3.6 脚本执行 (Script Execution)

当一个用户运行程序安装器或者卸载器的时候，页面会按着在脚本中定义的顺序进行显示。当到达instfiles页面时，被选中的组件所对应的段将按着在脚本中定义的顺序执行。如果没有组件页面显示，则会执行所有的段，此时假定所有组件都被选中且没被脚本禁止。

除了段中的代码，还有回调函数中的代码，如果定义了回调函数，则他们有可能在段代码之前执行。比如，.onInit回调函数会在任何脚本中其他的代码之前执行。还有[页面回调函数](http://nsis.sourceforge.net/Docs/Chapter4.html#pagecallbacks_explain)，他们会在页面显示过程中某个时间点被执行。

### 2.3.7 编译器命令 (Compiler Commands)

编译器命令将会在计算机执行编译时执行，可以用于条件编译、头文件包含、执行应用程序、改变工作目录等等。最常用的功能是defines，Defines是编译时期的常量。你可以定义产品的版本号然后在脚本中使用，比如：

```NSIS
!define VERSION "1.0.3"
Name "My Program ${VERSION}"
OutFile "My Program Installer - ${VERSION}.exe"
```

更多关于defines的信息请参见[条件编译](http://nsis.sourceforge.net/Docs/Chapter5.html#compdefines)。

另一个常用的功能是宏(macros)。宏用于在编译期间根据定义以及定义的值来插入代码，这样做的目的是对于仅有一些微小变动的代码可以被写成一段通用代码并多次重用，比如：

```NSIS
!macro MyFunc UN
Function ${UN}MyFunc
  Call ${UN}DoRegStuff
  ReadRegStr $0 HKLM Software\MyProgram key
  DetailPrint $0
FunctionEnd
!macroend

!insertmacro MyFunc ""
!insertmacro MyFunc "un."
```

上面定义的宏避免了为安装器和卸载器分别编写同样的代码。两个!insertmacros语句分别插入了两个函数，一个是安装器调用的MyFunc函数，一个是卸载器调用的un.MyFunc函数，这两个函数执行了相同的内容。

更多信息请参见[编译期命令](http://nsis.sourceforge.net/Docs/Chapter5.html#comptime)。

## 2.4 编译器 （Compiler)

创建安装包的过程中，在编写完脚本之后，就要对其进行编译了。MakeNSIS.exe就是NSIS的编译器，它将读取并解析你的脚本，最终为你生成一个安装程序。

对于编译只需在.nsi文件上点击右键，然后选择"Compile NSIS Script"，此时NSIS的编译器接口程序MakeNSISW将会调用MakeNSIS去编译这个脚本文件。MakeNSISW会接收MakeNSIS的输出，并在一个窗口中展示出来，使你可以进行查阅、拷贝、测试安装包以及更多其他操作。也可以在命令行中调用makensis.exe进行操作。

编译器会对脚本进行检查并给出警告或者错误提示。当有错误发生时（比如需要2个参数但只给出了1个参数），编译器将会终止编译并显示一个包含错误行号的简短错误信息。对于非严重性的错误，编译器将给出一条警告（比如在一个脚本里有两个DirText命令）。如果脚本没有错误，编译器将会生成一个用于发布程序的安装包。

NSIS支持不同的压缩方式，具体可以参考[这里](http://nsis.sourceforge.net/Docs/Chapter4.html#asetcompressor)。ZLIB是默认的压缩方式，其具有速度快占用内存小的特点。LZMA是用于创建网络发布的小体积安装包的首选。BZIP2的压缩率通常介于ZLIB和LZMA之间，在需要低内存占用和快速脚本编译时很有帮助。

在Linux、BSD或者Mac OS X servers系统中编译Windows安装包也是可行的。具体请参考[构建NSIS](http://nsis.sourceforge.net/Docs/AppendixG.html#build)。

## 2.5 现代用户界面 （Modern UI)

NSIS的一个受欢迎的用户界面是Modern User Interface。它具有最近版本windows的类似向导的界面。现代用户界面不仅仅是一个定制的资源文件，它还具有很多新的界面元素。它提供了用于描述当前步骤的白色页头、在组件页中的描述区域、欢迎页、可供用户选择运行程序或者重启系统的完成页等等。

更多信息请参见[现代用户界面自述文件](http://nsis.sourceforge.net/Docs/Modern%20UI%202/Readme.html)和[现代用户界面示例](http://nsis.sourceforge.net/Examples/Modern%20UI)。

## 2.6 插件

NSIS支持从脚本中调用插件，插件是由C、C++、Delphi或其他编程语言生成的DLL文件，可以为NSIS提供更强大的代码库。

可以通过下面这样的语句来进行插件的调用：

```NSIS
DLLName::FunctionName "parameter number 1" "parameter number 2" "parameter number 3"
```

插件中的每一个函数都有其各自的需求。比如函数参数，有的不需要参数，有的根据需要可能具有多个参数。例如：

```NSIS
nsExec::ExecToLog '"${NSISDIR}\makensis.exe" /CMDHELP'
Pop $0 ; Process exit code or "error"
InstallOptions::dialog "$PLUGINSDIR\test.ini"
Pop $0 ; success/back/cancel/error
NSISdl::download http://download.nullsoft.com/winamp/client/winamp291_lite.exe $R0
Pop $0 ; "success" or a error code
```

NSIS已知的插件将会被列在编译器输出的顶部（verbose level 4)。 NSIS会在NSIS目录下的[插件文件夹](http://nsis.sourceforge.net/Plugins/)下搜索插件并列出所有的可用函数。可以使用!addplugindir指示NSIS去搜索其他的目录。

NSIS发行版已经包含了很多插件。[InstallOptions](http://nsis.sourceforge.net/Docs/InstallOptions/Readme.html)是一个流行的插件，可以组合NSIS的页面命令来定制页面（参见[页面](http://nsis.sourceforge.net/Docs/Chapter4.html#pages)）。 [开始菜单插件](http://nsis.sourceforge.net/Docs/StartMenu/Readme.txt)提供了一个供用户选择开始菜单文件夹的页面。NSIS提供了很多用于不同功能的插件，可以在[文档目录](http://nsis.sourceforge.net/Docs/)下面查找帮助文件以及示例程序。同时也可以在线查找更多的插件：[NSIS Wiki](http://nsis.sf.net/)。

也可以创建自己的插件，NSIS提供了C/C++和Delphi语言可以直接使用的头文件，参阅[插件示例](http://nsis.sourceforge.net/Examples/Plugin/)学习如果编写插件。自带插件的源代码可以在源代码包中找到。

## 2.7 更多内容

这个教程只是描述了NSIS的最基本功能，想了解NSIS的更多内容，需要花点时间来阅读手册的其余内容。
