---
title: "Using Module in VS Code With MSVC"
description: 
date: 2024-02-09T18:29:58+08:00
image: 
hidden: false
comments: true
categories:
    - Experiences
tags:
    - C++
    - MSVC
    - VS Code
draft: true
---

// temporary take notes here

cl /c /ZI /JMC /W3 /WX- /diagnostics:column /sdl /FS /Od /D _DEBUG /D _CONSOLE /D _UNICODE /D UNICODE /Gm- /EHsc /RTC1 /MDd /GS /fp:precise /Zc:wchar_t /Zc:forScope /Zc:inline /std:c++latest /permissive- /ifcOutput "C:\Users\LiAu\source\repos\ConsoleApplication1\x64\Debug\std.ixx.ifc" /Fo"x64\Debug\std.ixx.obj" /Fd"x64\Debug\vc143.pdb" /sourceDependencies "C:\Users\LiAu\source\repos\ConsoleApplication1\x64\Debug\std.ixx.ifc.d.json" /external:W3 /Gd /interface /FC /errorReport:prompt  /TP "..\..\..\..\..\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.38.33130\modules\std.ixx"

cl.exe /c /reference std=C:\Users\LiAu\source\repos\ConsoleApplication1\x64\Debug\std.ixx.ifc /ZI /JMC /W3 /WX- /diagnostics:column /sdl /Od /D _DEBUG /D _CONSOLE /D _UNICODE /D UNICODE /Gm- /EHsc /RTC1 /MDd /GS /fp:precise /Zc:wchar_t /Zc:forScope /Zc:inline /std:c++latest /permissive- /Fo"x64\Debug\\" /Fd"x64\Debug\vc143.pdb" /external:W3 /Gd /TP /FC /errorReport:prompt ConsoleApplication1.cpp

link.exe /OUT:"C:\Users\LiAu\source\repos\ConsoleApplication1\x64\Debug\ConsoleApplication1.exe" /INCREMENTAL /ILK:"x64\Debug\ConsoleApplication1.ilk" kernel32.lib user32.lib gdi32.lib winspool.lib comdlg32.lib advapi32.lib shell32.lib ole32.lib oleaut32.lib uuid.lib odbc32.lib odbccp32.lib /MANIFEST /MANIFESTUAC:"level='asInvoker' uiAccess='false'" /manifest:embed /DEBUG /PDB:"C:\Users\LiAu\source\repos\ConsoleApplication1\x64\Debug\ConsoleApplication1.pdb" /SUBSYSTEM:CONSOLE /TLBID:1 /DYNAMICBASE /NXCOMPAT /IMPLIB:"C:\Users\LiAu\source\repos\ConsoleApplication1\x64\Debug\ConsoleApplication1.lib" /MACHINE:X64 x64\Debug\ConsoleApplication1.obj

		{
			"label": "cl & ms incremental linker - build with module",
			"type": "shell",
			"command": "powershell.exe",
			"args": [
				"-Command",
				"&{Import-Module 'M:\\Coding\\.vscode\\Microsoft.VisualStudio.DevShell.dll'; Enter-VsDevShell 0eeed396 -SkipAutomaticLocation -DevCmdArguments '-arch=x64'",
				"; cl.exe /c /reference ${fileDirname}\\..\\module\\*.ifc /ZI /JMC /W4 /WX- /diagnostics:column /sdl /Od /D _DEBUG /D _CONSOLE /D _UNICODE /D UNICODE /Gm- /EHsc /RTC1 /MDd /GS /fp:precise /Zc:wchar_t /Zc:forScope /Zc:inline /std:c++latest /permissive-  /Fo${fileDirname}\\output\\ /Fd${fileDirname}\\output\\ /external:W4 /Gd /TP /FC ${file}",
				"; link.exe /INCREMENTAL /OUT:${fileDirname}\\output\\${fileBasenameNoExtension}.exe /ILK:${fileDirname}\\output\\${fileBasenameNoExtension}.ilk kernel32.lib user32.lib gdi32.lib winspool.lib comdlg32.lib advapi32.lib shell32.lib ole32.lib oleaut32.lib uuid.lib odbc32.lib odbccp32.lib /DEBUG /PDB:${fileDirname}\\output\\${fileBasenameNoExtension}.pdb /SUBSYSTEM:CONSOLE /TLBID:1 /DYNAMICBASE /NXCOMPAT /IMPLIB:${fileDirname}\\output\\${fileBasenameNoExtension}.lib ${fileDirname}\\output\\${fileBasenameNoExtension}.obj ${fileDirname}\\..\\module\\*.obj}",
			],
			"problemMatcher": [
				"$msCompile"
			],
			"group": {
				"kind": "build",
				"isDefault": true
			},
			"presentation": {
				"reveal": "silent",
				"panel": "shared",
				"focus": true,
				"close": false
			},
		},
		{
			"label": "cl - build module interface unit",
			"type": "shell",
			"command": "powershell.exe",
			"args": [
				"-Command",
				"&{Import-Module 'M:\\Coding\\.vscode\\Microsoft.VisualStudio.DevShell.dll'; Enter-VsDevShell 0eeed396 -SkipAutomaticLocation -DevCmdArguments '-arch=x64'; cl.exe /c /ZI /JMC /W4 /WX- /diagnostics:column /sdl /FS /Od /D _DEBUG /D _CONSOLE /D _UNICODE /D UNICODE /Gm- /EHsc /RTC1 /MDd /GS /fp:precise /Zc:wchar_t /Zc:forScope /Zc:inline /std:c++latest /permissive- /ifcOutput ${fileDirname}\\${fileBasenameNoExtension}.ifc /Fo${fileDirname}\\ /Fd${fileDirname}\\ /sourceDependencies ${fileDirname}\\${fileBasenameNoExtension}.ifc.d.json /external:W4 /Gd /interface /FC /TP ${file}}"
			],
			"problemMatcher": [
				"$msCompile"
			],
			"group": {
				"kind": "build",
				"isDefault": false
			},
			"presentation": {
				"reveal": "always",
				"panel": "shared",
				"focus": true,
				"close": false
			},
		},