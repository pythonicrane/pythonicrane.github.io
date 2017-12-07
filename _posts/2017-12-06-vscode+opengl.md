---
layout: post
title: "VSCode+Clang+OpenGL(GLUT)"
date: 2017-12-06 21:38:06
tag: 其他
---
<h2>一、配置VSCode</h2>
参考：https://www.zhihu.com/question/30315894
下载并安装VSCode,Clang,MinGW64，配置好环境变量.</br>

<h2>二、编译和调试C/C++</h2>
新版VSC需要在目录下进行，所以新建一个文件夹，再在文件夹中添加“.vscode”文件夹，在该文件夹下面编写四个文件,"c_cpp_properties.json"(c/c++属性)，"launch.json"(调试脚本)，"settings.json"(本项目下的局域设置)，"tasks.json"(编译脚本)。新建项目的时候，只需要将“.vscode”放进项目工作目录下就行了，而且不需要再修改，可以实现DEVC++那样的多个main函数文件。如下：
1. 在c_cpp_properties.json中设置头文件库和链接库路径
```c++
//c_cpp_properties.json
{
    "configurations": [
        {
            "name": "Win32",
            "intelliSenseMode": "clang-x64",
            "includePath": [
                "${workspaceFolder}",//可以将头文件放在工作目录下
                "C:/llvm/lib/gcc/x86_64-w64-mingw32/7.2.0/include/c++",
                "C:/llvm/lib/gcc/x86_64-w64-mingw32/7.2.0/include/c++/x86_64-w64-mingw32",
                "C:/llvm/lib/gcc/x86_64-w64-mingw32/7.2.0/include/c++/backward",
                "C:/llvm/lib/gcc/x86_64-w64-mingw32/7.2.0/include",
                "C:/llvm/include",
                "C:/llvm/x86_64-w64-mingw32/include",
                "C:/llvm/lib/gcc/x86_64-w64-mingw32/7.2.0/include-fixed"
            ],
            "defines": [
                "_DEBUG",
                "UNICODE",
                "__GNUC__=7",
                "__cdecl=__attribute__((__cdecl__))"
            ],
            "browse": {
                "path": [
                    "${workspaceFolder}",//可以将链接库放在工作目录下
                    "C:/llvm/lib/gcc/x86_64-w64-mingw32/7.2.0/include/c++",
                    "C:/llvm/lib/gcc/x86_64-w64-mingw32/7.2.0/include/c++/x86_64-w64-mingw32",
                    "C:/llvm/lib/gcc/x86_64-w64-mingw32/7.2.0/include/c++/backward",
                    "C:/llvm/lib/gcc/x86_64-w64-mingw32/7.2.0/include",
                    "C:/llvm/include",
                    "C:/llvm/x86_64-w64-mingw32/include",
                    "C:/llvm/lib/gcc/x86_64-w64-mingw32/7.2.0/include-fixed"
                ],
                "limitSymbolsToIncludedHeaders": true,
                "databaseFilename": ""
            }
        }
    ],
    "version": 3
}
```
2. launch.json调试脚本
```c++
{
    // 使用 IntelliSense 了解相关属性。
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations":
    [
        {
            "name": "(gdb) Launch", // 配置名称，将会在启动配置的下拉菜单中显示
            "type": "cppdbg", // 配置类型，这里只能为cppdbg
            "request": "launch", // 请求配置类型，可以为launch（启动）或attach（附加）
            "program": "${fileDirname}/${fileBasenameNoExtension}.exe", // 将要进行调试的程序的路径
            "args": [], // 程序调试时传递给程序的命令行参数，一般设为空即可
            "stopAtEntry": false, // 设为true时程序将暂停在程序入口处，我一般设置为true
            "cwd": "${workspaceFolder}", // 调试程序时的工作目录
            "environment": [], // （环境变量？）
            "externalConsole": true, // 调试时是否显示控制台窗口，一般设置为true显示控制台
            "internalConsoleOptions": "neverOpen", // 如果不设为neverOpen，调试时会跳到“调试控制台”选项卡，你应该不需要对gdb手动输命令吧？
            "MIMode": "gdb", // 指定连接的调试器，可以为gdb或lldb。但目前lldb在windows下没有预编译好的版本。
            "miDebuggerPath": "gdb.exe", // 调试器路径。
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": false
                }
            ],
            "preLaunchTask": "Compile" // 调试会话开始前执行的任务，一般为编译程序。与tasks.json的label相对应
        }
    ]
}
```

3. settings.json,本项目下的局域设置,会隐藏全局设置，但只在本项目成立,文件最下面有全局环境配置，可以自行修改。
```c++
{
    //安装的插件列表：Bracket Pair Colorizer，C/C++ for Visual Studio Code,C/C++ Clang Command Adapter,C/C++ Snippets,Code Runner,GBKtoUTF8,Include Autocomplete,OneDark-Pro,Python
    "files.defaultLanguage": "cpp", // ctrl+N新建文件后默认的语言

    "code-runner.runInTerminal": true, // 设置成false会在“输出”中输出，无法交互
    "code-runner.executorMap": {
        "c": "cd $dir && clang $fileName -o $fileNameWithoutExt.exe -Wall -g -Og -static-libgcc -fcolor-diagnostics --target=x86_64-w64-mingw -std=c11 && $dir$fileNameWithoutExt",
        "cpp": "cd $dir && clang++ $fileName -o $fileNameWithoutExt.exe -Wall -g -Og -static-libgcc -fcolor-diagnostics --target=x86_64-w64-mingw -std=c++17 && $dir$fileNameWithoutExt"
    }, // 设置code runner的命令行
    "code-runner.saveFileBeforeRun": true, // run code前保存
    "code-runner.preserveFocus": true, // 若为false，run code后光标会聚焦到终端上。如果需要频繁输入数据可设为false
    "code-runner.clearPreviousOutput": false, // 每次run code前清空属于code runner的终端消息

    "C_Cpp.clang_format_sortIncludes": true, // 格式化时调整include的顺序（按字母排序）
    "C_Cpp.intelliSenseEngine": "Default", // 可以为Default或Tag Parser，后者较老，功能较简单。具体差别参考cpptools插件文档
    "C_Cpp.errorSquiggles": "Disabled", // 因为有clang的lint，所以关掉
    "editor.formatOnType": true, // 输入时就进行格式化，默认触发字符较少，分号可以触发
    "editor.snippetSuggestions": "top", // snippets代码优先显示补全

    "clang.cflags": [ // 控制c语言静态检测的参数
        "--target=x86_64-w64-mingw",
        "-std=c11",
        "-Wall"
    ],
    "clang.cxxflags": [ // 控制c++静态检测时的参数
        "--target=x86_64-w64-mingw",
        "-std=c++11",
        "-Wall"
    ],
    "clang.completion.enable":false // 效果稍好，但太卡，故关掉
}

/*全局设置
{
    "editor.fontFamily": "Consolas, 微软雅黑", // 控制编辑器字体
    "editor.fontSize": 18,
    "workbench.colorTheme": "One Dark Pro", // 主题
    "files.trimTrailingWhitespace": true, // 保存时，删除每一行末尾的空格
    "workbench.colorCustomizations": {
            "activityBar.foreground": "#33ff66" // 自定义颜色
        },
    "git.enabled": true, // 如果你不用git，我建议你关闭它
    "editor.minimap.enabled": false, // 我个人不用minimap，就是右边那个东西
    "editor.dragAndDrop": false, // 选中文字后，可以拖动它们调整位置。我是不需要
    "files.autoGuessEncoding": true // 启用后，会在打开文件时尝试猜测字符集编码

}
*/
```

4. "tasks.json"，编译文件，类似于CMAKE。
```c++
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
            {
                "label": "Compile", // 任务名称，与launch.json的preLaunchTask相对应
                "command": "clang++", // 要使用的编译器
                "args": [
                    "${file}",
                    "-o", // 指定输出文件名，不加该参数则默认输出a.exe
                    "${fileDirname}/${fileBasenameNoExtension}.exe",
                    "-g", // 生成和调试有关的信息
                    "-Wall", // 开启额外警告
                    "-static-libgcc", // 静态链接
                    // "-lglut32",//使用glut
                    // "-lglu32",
                    // "-lopengl32",
                    "-fcolor-diagnostics",
                    "--target=x86_64-w64-mingw", // 默认target为msvc，不加这一条就会找不到头文件
                    "-std=c++11" // C语言最新标准为c11，或根据自己的需要进行修改
                ], // 编译命令参数
                "type": "shell",
                "group": {
                    "kind": "build",
                    "isDefault": true // 设为false可做到一个tasks.json配置多个编译指令，需要自己修改本文件，我这里不多提
                },
                "presentation": {
                    "echo": true,
                    "reveal": "always", // 在“终端”中显示编译信息的策略，可以为always，silent，never。具体参见VSC的文档
                    "focus": false, // 设为true后可以使执行task时焦点聚集在终端，但对编译c和c++来说，设为true没有意义
                    "panel": "shared" // 不同的文件的编译信息共享一个终端面板
                }
                //"problemMatcher":"$gcc" // 如果你不使用clang，去掉前面的注释符，并在上一条之后加个逗号。照着我的教程做的不需要改（也可以把这行删去)
            }
        ]
}
```

VSCode本身只类似于sublime，需要安装插件才能实现各种语言的IDE，C/C++安装的插件有：Bracket Pair Colorizer，C/C++ for Visual Studio Code,C/C++ Clang Command Adapter,C/C++ Snippets,Code Runner,GBKtoUTF8,Include Autocomplete,OneDark-Pro。其功能参考帮助文档。
至此就可以完成DEVC++的大部分功能了，而且可扩展性更强。

 <h2>三、使用OpenGL库GLUT</h2>
 1. 获取glut，官方的glut是32位的，在WIN1064位系统下存在skipping incompatible编译问题，也就是兼容性问题，参考http://web.eecs.umich.edu/~sugih/courses/eecs487/glut-howto/和https://developer.nvidia.com/cg-toolkit-download，从Nvidia的CGKit中获取64位版本，即glut.h,glut32.lib,glut32.dll文件，都需要64位版本的，可以放在clang的LLVM相应路径中，也可以直接放在工作控件文件夹中，在task.json中添加参数，即"args": [   ]中，"-lglut32","-lglu32"，"-lopengl32"，注意每个参数都要逗号隔开。