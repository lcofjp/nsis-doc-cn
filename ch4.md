# 第四章：脚本参考

## 4.1 脚本文件格式

NSIS脚本文件(.nsi)仅仅是一个包含脚本代码的文本文件。

**命令**

命令行的格式是：'命令 [参数]'

```NSIS
File "myfile"
```

**注释**

以分号(;)或者井号(#)开头的行是注释。注释也可以放在命令的后面。也可以使用C风格的注释来注释一行或者多行内容。

```NSIS
; Comment
# Comment

# Comment \
    Another comment line (see `Long commands` section below)

/*
Comment
Comment
*/

Name /* comment */ mysetup

File "myfile" ; Comment
```

如果想使用;或#开头的参数，则需要把它们放入引号之中。

**插件**

调用插件的格式：'plugin::command [parameters]'。更多信息参见[插件DLL](http://nsis.sourceforge.net/Docs/Chapter4.html#plugindlls)。

```NSIS
nsExec::Exec "myfile"
```

**数值**

对于作为数值的参数，可以使用十进制、十六进制(0x开头的数字)或者八进制(0开头的数字)。

颜色应该被设置为十六进制RGB格式，类似于HTML但是不包含'#'。

```NSIS
IntCmp 1 0x1 lbl_equal

SetCtlColors $HWND CCCCCC
```

**字符串**

表达包含空格的字符串，需要使用引号：

```NSIS
MessageBox MB_OK "Hi there!"
```


