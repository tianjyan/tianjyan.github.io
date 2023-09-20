---
layout: post
title: VSC配置Electron调试环境
date: 2016-06-27 21:15:58
tags: [Visual Studio Code]
categories: [配置]
---
## Windows：
configurations中添加：

    {
        "name": "Windows Launch",
        "type": "node",
        "request": "launch",
        "program": "${workspaceRoot}/main.js",
        "stopOnEntry": false,
        "args": [],
        "cwd": "${workspaceRoot}",
        "preLaunchTask": null,
        "runtimeExecutable": "C:\\Users\\tianjie.yang\\AppData\\Roaming\\npm\\node_modules\\electron-prebuilt\\dist\\electron.exe",
        "runtimeArgs": [
            "--nolazy"
        ],
        "env": {
            "NODE_ENV": "development"
        },
        "externalConsole": false,
        "sourceMaps": false,
        "outDir": null
    }

## Mac：
configurations中添加：

    {
        "name": "Mac Launch",
        "type": "node",
        "request": "launch",
        "program": "${workspaceRoot}/main.js",
        "stopOnEntry": false,
        "args": [],
        "cwd": "${workspaceRoot}",
        "preLaunchTask": null,
        "runtimeExecutable": "/usr/local/bin/Electron",
        "runtimeArgs": [
            "--nolazy"
        ],
        "env": {
            "NODE_ENV": "development"
        },
        "externalConsole": false,
        "sourceMaps": false,
        "outDir": null
    }
