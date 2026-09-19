---
title: Golang Delve DAP Attach 远程调试
date: 2025-04-23T10:57:33+08:00
draft: false
categories:
  - go
tags:
  - go
---
# 准备
1. 远程服务器安装Delve调试器
```shell
go install github.com/go-delve/delve/cmd/dlv@latest 
```
2. 本地开发环境及其源码，进行编译后推送到远程服务器
   编译时请注意不要启用优化或者去除调试信息，使用以下命令构建
   
   ```shell
   go build -gcflags "all=-N -l" -o binaryname
	```
3. 服务器启动程序运行，然后使用命令行启动附加当前服务进程并转发调试信息
   ```shell
   dlv attach $ServicePID --headless --listen=:$DebugServerPORT --api-version=2 --log --log-output=dap,debugger --only-same-user=false
	```
# 调试
1. 在支持DAP协议的IDE或编辑器上连接到当前调试进程，以vscode为例。
   - 点击导航栏的运行按钮，点击添加配置
   - 选择connect to server选项
   - 输入远程服务器的地址和端口号，会自动生成好配置文件。
   - 对于有防火墙的设备注意放行端口，对于公网服务器请注意采取其他安全措施，如使用ssh转发端口到本地进行安全的调试服务，或者使用防火墙访问限制等功能。
2. 调试过程与常见调试过程相同，对于有多模块（多main.go）请注意是否当前源码路径与远程二进制文件相同，否则可能导致意外。