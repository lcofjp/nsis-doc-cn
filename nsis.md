## 2.3 脚本结构

NSIS脚本包含安装包属性(Attributes),页面(Pages)和片段(Sections)/函数(Functions)。也可以使用编译指令进行编译器操作。其中OutFile指令是必须要有的，它告诉NSIS安装包应该输出到哪里，同时脚本文件必须包含至少一个Section。

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