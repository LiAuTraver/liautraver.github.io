---
title: "Using MSVC in Visual Studio Code"
description: "When GCC does not work..."
date: 2024-02-17T13:17:25+08:00
image: vscode.png
math: 
license: 
hidden: false
comments: true
draft: false
categories: 
  - Experience
tags: 
  - C++
  - VS Code
  - MSVC
---

> ## **<span style = "color: red;">*The content was outdated and would be updated in the near future.*</span>**

When I first ventured into the realm of C++, DEV C++ served as my initial Integrated Development Environment (IDE). It boasted simplicity and minimalism, making it particularly welcoming for beginners. However, at the time of its release, DEV's GCC was stuck at C++11. The outdated user interface further dampened its appeal. There's no point using a decade-old compiler and it's outdated user interface. Nor does it have intellisense or any other modern IDE features. So I moved to Visual Studio, only to find it too complex and bloated for my taste.Visual Studio Code is the perfect middle ground for me. It's lightweight, has intellisense, and supports the latest C++ standard. After struggling with the editor for a whole day, the GCC `launch.json` and `tasks.json` still failed to work in the integrated terminal. The external terminal launched was *surprisingly* slow. I had to shift my focus to MSVC.

The official MSVC documentation in VS Code proved to be not only sparse but also somewhat difficult to implement, demanding the opening of VS Code in Visual Studio Development Command Prompt/Powershell. Thanks to [Futrime's blog](https://blog.futrime.com/en/p/compiling-c/c-programs-in-vscode-with-msvc/), I finally figured it out. Definitely check [him](https://blog.futrime.com/en) out! However, there is room for improvement in both the `tasks.json` and the launch speed within the integrated terminal.

In his blog, Futrime introduced the `window` option in `tasks.json` to launch tasks in the integrated terminal using `Developer Command Prompt for VS`. However, it is unnecessary to execute every task in the VS prompt, as it proves slow and time-consuming. Adding the `VSDevCmd.bat` or `DevShell.dll` module to environmental variables is also discouraged due to potential system slowdowns. Instead, a more efficient approach involves pasting the full path of these files into the `args` option in `tasks.json`. Here's an excerpt from my `tasks.json`:

``` json
{
			"type": "shell",
			"label": "cl - C++",
			"command": "powershell.exe",
			"args": [
				"-Command",
				"Import-Module 'M:\\Coding\\.vscode\\Microsoft.VisualStudio.DevShell.dll'",
				";Enter-VsDevShell 0eeed396 -SkipAutomaticLocation",
				";cl.exe /TP /Zi /EHsc /std:c++latest /Zc:__cplusplus /Fo${fileDirname}\\output\\ /Fd${fileDirname}\\output\\ /Od /MP /W4 /Fe:${fileDirname}\\output\\${fileBasenameNoExtension}.exe /I${workspaceFolder}\\include\\ ${file}",
			],
			"problemMatcher": [
				"$msCompile"
			],
			"group": {
				"kind": "build",
				"isDefault": true
			},
			"presentation": {
				"reveal": "never",
				"panel": "shared",
				"focus": true,
				"close": false
			},
		},
```

Don't scream. I understand this example may seem intricate, so let me break it down step by step.

`command` is set to powershell.exe.
`args` represents the arguments that the command will take.
To obtain the launch command for your VSDevShell, open Windows Terminal, press <kbd> CTRL </kbd> + <kbd> , </kbd> to access settings, navigate to the VS Dev Powershell profile, and review your VSDevShell's launch command. The specific command may vary between different VS versions. For me, it was:

``` powershell
powershell.exe -NoExit -Command "&{Import-Module """C:\Program Files\Microsoft Visual Studio\2022\Community\Common7\Tools\Microsoft.VisualStudio.DevShell.dll"""; Enter-VsDevShell 0eeed396 -SkipAutomaticLocation -DevCmdArguments """-arch=x64 -host_arch=x64"""}"
```

It might seem tempting to directly include this command in the args, but the challenge lies in the fact that args is not a simple string; it's an array of strings. The most vexing part is that the integrated PowerShell launched by VS Code task does not recognize `&` as a command, and spaces in the path pose additional complications. The reason is unknown for me. Thus, I copied the dll module to a valid path and included it in the args. The cl.exe and its flags were also added to the args. However, the task would run endlessly, as VS Code's task does not terminate unless the command does. Removing `-NoExit` ensures the task terminates, and there's no need for `;exit` at the end of the command.

Also, due to peculiarities in PowerShell, `;` is not a valid separator if we put it separately or at the end of the command. Nor do the cl args in which has space. We have to put the whole command into a single string and separate the commands with `;`.
`0eeed396` is the hash of the VS 2022 Community Edition x64 Developer Powershell.
`-SkipAutomaticLocation` is to prevent the shell from changing the current directory to the workspace folder.

My cl args targets the latest C++ standard, generates full debugging information, and outputs the object file, pdb file and the executable file to the `output` folder in the same directory as the source file.

Then creates a `launcher profile` in the `launch.json` for debugging. Notice the `type` must be `cppvsdbg` rather than `cppdbg`. All done! Now you can press <kbd> F5 </kbd> to build and debug your C++ program in VS Code with MSVC without any hassle.

> Photo by [Visual Studio Code Team](https://code.visualstudio.com/brand) on [Microsoft](https://www.microsoft.com/en-us)