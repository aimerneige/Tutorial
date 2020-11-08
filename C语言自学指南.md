# C语言自学指南

## 开发环境配置

### 使用Visual Studio 2019
1. 在官网下载Visual Studio 2019
2. 双击运行安装程序，在安装完Visual Studio Installer后选择安装"C/C++桌面开发"
3. 使用时新建解决方案选择C++控制台应用即可
### 使用Visual Studio Code
1. 在官网下载Visual Studio Code
2. 安装Visual Studio Code
3. 安装插件Code Runner、CppTools等
4. 在官网下载LLVM(Pre-Built Binaries:Windows(64-bit))
5. 安装LLVM，并将其bin文件夹添加到环境变量中
6. 在SourceForge上下载MinGW-w64(Toolchains targetting Win64/Personal Builds/mingw-builds/[版本]/threads-posix/seh/)
7. 解压缩MinGW-w64，并将其bin文件夹添加到环境变量中
8. 打开PowerShell或者cmd，输入 `gcc -v` 检查环境是否正确安装，如果输出
   ``` 
   gcc : 无法将“gcc”项识别为 cmdlet、函数、脚本文件或可运行程序的名称。请检查名称的拼写，如果包括路径，请确保路径正确，然后再试一次。
    所在位置 行:1 字符: 1
    + gcc -v
    + ~~~
        + CategoryInfo          : ObjectNotFound: (gcc:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
   ```
   或者
   ```
   'gca' 不是内部或外部命令，也不是可运行的程序或批处理文件。
   ```
   则说明环境没有配置成功，需要检查一下LLVM的安装和环境变量的设置
9. 新建一个文件夹作为代码存储的文件夹，这里命名为`C-Project`
10. 进入文件夹，右键，点击"通过code打开"，或者打开VSCode后点击"文件"->"打开文件夹"。方便起见可以在打开后将其保存为工作区
11. 新建一个文件，命名为`testCode.c`，写入如下代码
    ```
    #include <stdio.h>

    int main(int argc, char const *argv[])
    {
        printf("%s", "Hello World");
        return 0;
    }
    ```
12. 点击右上角三角运行，如果和 上面测试环境 步骤一样报错，但是测试环境时没有报错，那么重启VSCode即可。应该会在下面"输出栏"有如下输出
    ```
    [Running] cd "d:\test\" && gcc test.c -o test && "d:\test\"test
    Hello World
    [Done] exited with code=0 in 0.669 seconds
    ```
13. 但是编辑器内include部分有波浪线，没有成功的找到头文件，需要添加配置文件，新建一个文件夹`.vscode`，并在里面新建文件`launch.json`、`settings.json`、`tasks.json`
14. 在`launch.json`里面写入如下内容
    ```
    {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "(GCC) Launch C",
                "type": "cppdbg",
                "request": "launch",
                "program": "${workspaceFolder}/build/${fileBasenameNoExtension}.exe",
                "args": [],
                "stopAtEntry": false,
                "cwd": "${workspaceFolder}",
                "environment": [],
                "externalConsole": true,
                "MIMode": "gdb",
                // 这里填你 MinGW-w64 安装目录下的 gdb 路径
                "miDebuggerPath": "C:\\VSCode+C\\mingw64\\bin\\gdb.exe",
                "setupCommands": [
                    {
                        "description": "Enable pretty-printing for gdb",
                        "text": "-enable-pretty-printing",
                        "ignoreFailures": true
                    }
                ],
                // 这里要与你在 tasks.json 中配置的 label 一致
                "preLaunchTask": "compileC",
            }
        ]
    }
    ```
    在`settings.json`里面写入
    ```
    {
        // 在终端中运行编译命令，否则我们无法与程序通过标准输入交互
        "code-runner.runInTerminal": true,
        // 如果你全局设置中的默认终端是 WSL 之类的，那么可以在工作区设置中改回 PowerShell
        //"terminal.integrated.shell.windows": "C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        // 运行代码之前清除之前的输出
        "code-runner.clearPreviousOutput": true,
        // 开启这个后在运行编译命令之前会自动 cd 至文件所在目录
        "code-runner.fileDirectoryAsCwd": true,
        // 因为上面那个选项会自动 cd，所以我删除了默认编译命令中的 cd 语句
        // 同时我将编译结果的输出目录修改为了同目录下的 build 文件夹
        // 不然源码文件和编译结果混杂在一个目录中非常杂乱（尤其是刷题时）
        // 这里只保留了 C 和 C++ 的编译命令，有需要其他语言的请自行添加
        "code-runner.executorMap": {
            "c": "cls && gcc $fileName -fexec-charset=GBK -o build/$fileNameWithoutExt && .\\build\\$fileNameWithoutExt",
            "cpp": "cls && g++ $fileName -fexec-charset=GBK -o build/$fileNameWithoutExt && .\\build\\$fileNameWithoutExt",
        },
        // 运行代码后切换焦点至终端，方便直接输入测试数据
        "code-runner.preserveFocus": false,
        // 在运行代码之前保存文件
        "code-runner.saveFileBeforeRun": true,

        // 提高自定义片段的优先级
        "editor.snippetSuggestions": "top",
        // 控制是否根据文档中的文字计算自动完成列表。
        "editor.wordBasedSuggestions": false,
        // tab键补全
        "editor.tabCompletion": "on",
        // 词语优先级
        "editor.suggest.localityBonus": true,
        // 这里更改为根据之前的补全进行排序
        "editor.suggestSelection": "recentlyUsedByPrefix"
    }
    ```
    在`task.json`里面写入
    ```
        {
        // See https://go.microsoft.com/fwlink/?LinkId=733558
        // for the documentation about the tasks.json format
        "version": "2.0.0",
        "tasks": [
            {
                "label": "compileC",
                "type": "shell",
                "command": "gcc",
                "args": [
                    "-g",
                    "\"${file}\"",
                    "-fexec-charset=GBK",
                    "-o",
                    "\"${workspaceFolder}\\build\\${fileBasenameNoExtension}\""
                ],
                "presentation": {
                    "reveal": "always",
                    "panel": "shared",
                    "focus": false,
                    "echo": true
                },
                "group": {
                    "kind": "build",
                    "isDefault": true
                },
                "problemMatcher": {
                    "owner": "cpp",
                    "fileLocation": "absolute",
                    "pattern": {
                        "regexp": "^(.*):(\\d+):(\\d+):\\s+(error|warning|note):\\s+(.*)$",
                        "file": 1,
                        "line": 2,
                        "column": 3,
                        "severity": 4,
                        "message": 5
                    }
                }
            }
        ]
    }
    ```
15. 新建文件夹`build`
16. 将文件夹.vscode和build从**工作区**删除，**注意不要删除文件**，防止误触
### 使用CLion

## 书籍推荐

1. 《C Primer Plus》(中国工信出版集团、人民邮电出版社)
**珍爱生命，原理谭浩强**

## 视频教程推荐

1. 中国大学MOOC-浙江大学-翁恺教授-C语言程序设计