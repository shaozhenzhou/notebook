# Windows10家庭版 开启关闭Hyper-V
* 重要说明
```
Windows 10家庭版只能装Docker Toolbox，而且不能开Hyper-v，
因为会和Oracle VM VirtualBox冲突，Docker Toolbox是依赖于VirualBox
```
## Windows10家庭版安装Hyper-V
- 将下面的内容复制到编辑器或者记事本当中：
```
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del hyper-v.txt
Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
```
- 将以上内容保存为Hyper-V.cmd
- 在系统桌面上，找到并右键点击 Hyper-V.cmd 文件图标，在右键菜单中点击：以管理员身份运行（A）
- Windows命令处理，我们等待处理完成以
- 在最末处输入：Y，电脑自动重启，进行配置更新
- 配置更新重启完成以后，去控制面板->所有控制面板项->程序和功能，点击启用或关闭Windows功能
- 已经有了Hyper-v功能，完成
---
## Windows10家庭版彻底删除Hyper-V
### 复制一个关闭Hyper-V特性的windows启动项
- 首先以管理员的身份运行“CMD”
- 在命令提示符中输入命令“bcdedit /copy {current} /d “Windows10 no Hyper-V”
- 输入命令“bcdedit /set {XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX} hypervisorlaunchtype OFF”，然后重启电脑
- 重启电脑后在两个启动项中选择 no Hyper-V 的即可启用无Hyper-V特性的环境
- 注意：将第2步运行后的命令出现在{}里的序列号替换第3步{}里的“XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX”
### 删除Hyper-V启动项，或者多余的启动项
- CMD中运行bcdedit，找到相对应的启动项ID，并记录
- 运行bcdedit /delete {XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX}，然后重启电脑






