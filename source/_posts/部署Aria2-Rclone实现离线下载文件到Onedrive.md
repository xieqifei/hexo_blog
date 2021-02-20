---
title: 部署Aria2+Rclone实现离线下载文件到Onedrive
top: false
cover: false
toc: true
mathjax: false
date: 2020-06-25 09:55:43
password:
summary: 在服务器上安装Aria2和Rclone，实现一条指令将远程文件上床到Onedrive。需要配合Ui界面使用。
tags:
 - Aria2
 - Rclone
categories: Linux
---



# 一：前言

Aria2是一款多线程下载的应用，可以充分利用网络带宽，实现满速下载。Rclone是个网盘管理工具，可以绑定Onedrive实现将网盘挂载到本地。利用aria2和Rclone，可以在服务器上通过指令，直接将远程文件下载到本地，并自动保存到Onedrive。

# 二：安装Aria2

推荐使用一键脚本：

```powershell
wget -N git.io/aria2.sh && chmod +x aria2.sh && ./aria2.sh
```

此脚本会自动下载关联Rclone的脚本，即aria2文件下载结束自动文件上传到Onedrive。

# 三：安装rclone

```shell
curl https://rclone.org/install.sh | sudo bash
```

安装完毕，配置rclone

```shell
rclone config
```

配置方式参考《[Rclone在win上挂在Onedrive](https://sci.ci/2020/06/22/rclone-zai-win-shang-gua-zai-onedrive/)》，但是不完全相同。

如果你的系统有UI，那么可以和在win上一样配置rclone。如果是纯指令界面，则按下面操作配置。

你需要现在windows上部署rclone，以获取onedrive授权的token。token在`C:\Users\你的用户名\.config\rclone`中

打开`rclone.conf`将token从`{"access_token":`开始到`}`结束的内容复制下来。

在`rclone config`出现

```shell
Remote config                                                
Use auto config? 
 * Say Y if not sure 
 * Say N if you are working on a remote or headless machine
y) Yes (default) 
n) No                                                        
y/n>
```

时输入 `n`，不自动配置。

在接下来出现的

```
result>
```

中，输入你拷贝的token。

剩下的和windows配置相同。

然后修改aria2配置，以配置将下载文件自动上传到onedrive

# 四：配置自动上传脚本

[Aria2 一键安装管理脚本 增强版](https://p3terx.com/go/aHR0cHM6Ly9naXRodWIuY29tL1AzVEVSWC9hcmlhMi5zaA==) 整合了 [Aria2 完美配置](https://p3terx.com/go/aHR0cHM6Ly9naXRodWIuY29tL1AzVEVSWC9hcmlhMl9wZXJmZWN0X2NvbmZpZw==) ，安装后会附带一些列增强功能脚本，Rclone 自动上传脚本就是其中之一。由于默认不启用，所以需要手动启用。

- 输入`vim /root/.aria2c/autoupload.sh`打开自动上传脚本进行编辑，脚本中有中文注释，按照自己的实际情况进行修改，一般只需要修改下面2个部分。

```none
# Rclone 配置时填写的网盘名(name)
DRIVE_NAME='Onedrive'
# 网盘目录。即上传目标路径，留空为网盘根目录，末尾不要有斜杠。
DRIVE_PATH='/DRIVEX/Download'
```

- 输入`vim /root/.aria2c/aria2.conf`打开 Aria2 配置文件进行修改。或使用[Aria2 一键安装管理脚本 增强版](https://p3terx.com/go/aHR0cHM6Ly9naXRodWIuY29tL1AzVEVSWC9hcmlhMi5zaA==)中的手动修改选项打开配置文件进行修改。找到“下载完成后执行的命令”，把`delete.aria2.sh`替换为`autoupload.sh`。

```none
# 下载完成后执行的命令
on-download-complete=/root/.aria2c/autoupload.sh
```

- 重启 Aria2

```none
service aria2 restart
```

# 五：参考资料

《[Aria2 + Rclone 实现 OneDrive、Google Drive 等网盘离线下载](https://p3terx.com/archives/offline-download-of-onedrive-gdrive.html)》