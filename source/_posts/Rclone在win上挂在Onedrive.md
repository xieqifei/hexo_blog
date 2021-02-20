---
title: Rclone在win上挂在Onedrive
top: false
cover: false
toc: true
mathjax: false
date: 2020-06-22 20:07:26
password:
summary: Windows自带的Onedrive同步上传速度非常慢，与Oneindex配合使用效果并不理想。为了提高同步速度，实现文件及时上传、下载和删除。使用Github上的开源项目Rclone，将Onedrive挂载到Windows系统上。
tags: Rclone
categories: Windows

---

## 一：Rclone下载

首先下载适用于 Windows 的 rclone 👇

官网下载：https://rclone.org/downloads/
GitHub下载：https://github.com/ncw/rclone

在[rclone官网](https://rclone.org/downloads/)中，Windows 平台下选择下载 AMD64 - 64 Bit

![](https://i.loli.net/2020/06/22/flecoyBhTO1kUdQ.png)

或者在[github](https://github.com/rclone/rclone/releases)下载。

![](https://i.loli.net/2020/06/22/2dAOCENv1Sy8Dwq.png)

下载后解压到一个英文路径中。

![](https://i.loli.net/2020/06/22/EfHUwhdZGsBmR7c.png)

另外在Windows平台使用rclone还需要另一个依赖工具`winfsp`，下载地址：http://www.secfs.net/winfsp/download/ ，下载后一路安装即可。

![](https://i.loli.net/2020/06/22/lgdOxXbMBzLwIAV.png)

## 二： 配置环境变量

1. 在电脑桌面右键点击“此电脑”的“属性”选项
2. 选择“高级系统设置”选项
3. 在系统变量中找到path，新建添加刚才解压后的路径

![](https://i.loli.net/2020/06/22/nW4auBq5tSxMm6e.png)

检查环境变量是否配置成功。

按`win`+`X`，然后按`A` 打开 `powershell` ，当然也可以去打开 `cmd` ，输入`rclone --version`，如果出现下面的输出则安装成功，否则检查上面步骤的环境变量是否配置正确。

## 三：配置Rclone

在终端中依次输入以下命令行，请根据我下的步骤进操作。

下载
```powershell
PS C:\WINDOWS\system32> rclone config                // 第一步在终端输入 rclone config
2020/06/22 19:36:22 NOTICE: Config file "C:\\Users\\12412\\.config\\rclone\\rclone.conf" not found - using defaults
No remotes found - make a new one
n) New remote
s) Set configuration password
q) Quit config
n/s/q> n                                  //第二步输入n创建新的配置， 
name> Onedrive_local                      //第三步 输入一个英文名称 ，中间也不要有空格
Type of storage to configure.
Enter a string value. Press Enter for the default ("").
Choose a number from below, or type in your own value
 1 / 1Fichier
   \ "fichier"
 2 / Alias for an existing remote
   \ "alias"
 3 / Amazon Drive
   \ "amazon cloud drive"
 4 / Amazon S3 Compliant Storage Provider (AWS, Alibaba, Ceph, Digital Ocean, Dreamhost, IBM COS, Minio, etc)
   \ "s3"
 5 / Backblaze B2
   \ "b2"
 6 / Box
   \ "box"
 7 / Cache a remote
   \ "cache"
 8 / Citrix Sharefile
   \ "sharefile"
 9 / Dropbox
   \ "dropbox"
10 / Encrypt/Decrypt a remote
   \ "crypt"
11 / FTP Connection
   \ "ftp"
12 / Google Cloud Storage (this is not Google Drive)
   \ "google cloud storage"
13 / Google Drive
   \ "drive"
14 / Google Photos
   \ "google photos"
15 / Hubic
   \ "hubic"
16 / In memory object storage system.
   \ "memory"
17 / Jottacloud
   \ "jottacloud"
18 / Koofr
   \ "koofr"
19 / Local Disk
   \ "local"
20 / Mail.ru Cloud
   \ "mailru"
21 / Mega
   \ "mega"
22 / Microsoft Azure Blob Storage
   \ "azureblob"
23 / Microsoft OneDrive
   \ "onedrive"
24 / OpenDrive
   \ "opendrive"
25 / OpenStack Swift (Rackspace Cloud Files, Memset Memstore, OVH)
   \ "swift"
26 / Pcloud
   \ "pcloud"
27 / Put.io
   \ "putio"
28 / QingCloud Object Storage
   \ "qingstor"
29 / SSH/SFTP Connection
   \ "sftp"
30 / Sugarsync
   \ "sugarsync"
31 / Tardigrade Decentralized Cloud Storage
   \ "tardigrade"
32 / Transparently chunk/split large files
   \ "chunker"
33 / Union merges the contents of several upstream fs
   \ "union"
34 / Webdav
   \ "webdav"
35 / Yandex Disk
   \ "yandex"
36 / http Connection
   \ "http"
37 / premiumize.me
   \ "premiumizeme"
38 / seafile
   \ "seafile"
Storage> 23                 //第四步 输入要配置的网盘类型 因为我们要配置Microsoft OneDrive 因此输入23
** See help for onedrive backend at: https://rclone.org/onedrive/ **

Microsoft App Client Id
Leave blank normally.
Enter a string value. Press Enter for the default ("").
client_id>                //第五步 直接回车
Microsoft App Client Secret
Leave blank normally.
Enter a string value. Press Enter for the default ("").
client_secret>            //第六步 直接回车 
Edit advanced config? (y/n)
y) Yes
n) No (default)
y/n> n                   //第七步 输入n 不进行高级配置 
Remote config
Use auto config?
 * Say Y if not sure
 * Say N if you are working on a remote or headless machine
y) Yes (default)
n) No
y/n> y                  //第八步 输入y 使用自动配置授权
If your browser doesn't open automatically go to the following link: http://127.0.0.1:53682/auth?state=DWKiZ1cLZoWl9buxJbPXgw
Log in and authorize rclone for access
Waiting for code...
Got code
Choose a number from below, or type in an existing value
 1 / OneDrive Personal or Business
   \ "onedrive"
 2 / Root Sharepoint site
   \ "sharepoint"
 3 / Type in driveID
   \ "driveid"
 4 / Type in SiteID
   \ "siteid"
 5 / Search a Sharepoint site
   \ "search"
Your choice> 1              //第九步 输入1 因为现在我配置的是 OneDrive Personal or Business 类型的网盘
Found 1 drives, please select the one you want to use:
0:  (personal) id=58719e3298535c9
Chose drive to use:> 0     //第十步 输入0
Found drive 'root' of type 'personal', URL: https://onedrive.live.com/?cid=058719e3298535c9
Is that okay?
y) Yes (default)
n) No                    //第十一步 直接回车
y/n>
--------------------
[Onedrive_local]
type = onedrive
token = {"access_token":"BAAUO9chh8cJscQLmU+LSWpbnr0vmwwAAZ6th5X8qdv3jqko0KCKeFVHdJigXdmpkGf3aO/W9HQahNAmDTxEVF2TnPtPNCw7M/me6FRz4mD9+x/jfJIb8Qewi8NpYMfYXdmPyUUVcPp1G+bwKOIFfJlNa6Can21gaGhTKgdEaz27GwetIvwjy7Qo/eDh/jarwoHEKljM8fN3UF1xz/RS0Ir+DC6CrvNIP3MUpbBF31/qUWJekWn3XqdT1j6m9bBfJ9Xk8heaY1/MT+1SA6fs6IPV5mO5m6dtjfsr1wO3xLGuaEutBPlNT9t219R55MZibwt746mw+VRTMCLsMrFZqVGK6IHzXyG3t+5NOrdP9yelb0AVQDZgAACJVvxd1cmt0LOAKEWlmYeQi3Eo6NKs9l5Abb4AsL2V02tbQt3AheMGLOxpOfyWOUUNaqvFAdSWRGQU8JLgFOA0YWy+UtYHSBG8SIy40jFI5czeqFoCzXguNijP2eiE+JdJREz9fO0q947/QK0K4MxVBqQxmlD1RDujCtnIDsT5r1ICG2AN40wSWqGvanKyctRNPdEm0BjmTrmXh0qTinmJ+BBVe70AU3ZigEzfMULxP+i+vwI2rA7VbiAuXnDUE75t49RGBiCMS+p00x0smGI685pmbPYQoOcMuVLHVdIxSDCO+YLSgpaHaBrruIvFbhto0OczwvYKn8XXEIp0CM6XEy55UYTuLkJFRDWX9VB/mSHarvexYqtEi4EYK2nABm4KrngexH0IdyhdwDKx7gNnLcMP9YYXG0cybppINxx67jFxjQM8/TWQPAz4cADt/FfPPVvuVABOZdhKmpuScj6qOhWsCfh6KH385UiM2Z+Rn2t9hZaXt3ZRmu+W5XAx9+E1SnlewdTiH5/VwB8QhCuRvS89hK/51BeMz4cJ5R9dLEqjGDxZ0HHay52DDhQYta/yG0VN2pkWTg0+T7rVdXbgC607y47sWmqP540tigNZPc0AgrWK3TXFOAJAEAoV9Xg+QUhJKYQNnrtFWI9nKbSFVHEeg8vvl5efZviF2szVI4wProgIHpUydqfnMlR3vRZlei/rmGUC9IICjdNTKPWSGmilJdbdxHMUc/MDyfYhxJA69cwI=","token_type":"Bearer","refresh_token":"MCeVIl4QGaJb!yGdTF6FLD!8N7hR4D8Z1bV4mpta08v64kR!MLcolxhea*NOv24M5V!uqHzBvi5dSW9wVHXGqALSe9kML5HKxV12XougeYafX2dcdyxvZ*cYEa6D0l6hIzovUwFpVjv3lqiP66jpzY4KxYMtZqQHf5PN!VGJf52xDKcsIUseoKD98z7olU3E2kyaZqJW8j6o5dBTV!Lmo5DVc31VpGrfeUnwhOcZkO7xN7IUEy8Vs9YXKQ0C4cin4qpyDSQ3pLpwy09ZpVE2bKOt4XQP7JLWQa6iGQ*r8zgewRo7ULYoEpRXYsqSBdwX6pJEQnRzCDcJimHxXBEV59rE*Ykwc9SijTkBN4haK6br9y2YVQ2UKegfQiErCHY33kzmSMAPJUwiooUNc6GytUz5O6ZxsBMghhb6B6NiSBCs!U5xRon1y*zzOsh9EArHk4y2rihu7BkkC13bJqxeMvhw$","expiry":"2020-06-22T20:38:09.8687266+08:00"}
drive_id = 58719e3298825c9
drive_type = personal
--------------------
y) Yes this is OK (default)
e) Edit this remote
d) Delete this remote
y/e/d>                   //第十二步 回车
Current remotes:

Name                 Type
====                 ====
Onedrive_local       onedrive          //显示你的设置则成功！

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> q           //第十三步 输入q结束配置
```

## 四：挂载Onedrive到本地

1. ~~自行下载安装git~~

~~此时请使用 `git bash` 的终端执行以下命令，因为我使用`cmd` 和 `powershell` 都出现关闭终端后挂载程序退出、本地挂载的OneDrive退出的现象。 如果你的windows没有安装git ， 请[自行安装](https://www.baidu.com/s?wd=windows 安装git&ie=utf-8)。若你使用 `cmd` 的话 ，`cmd` 是不能退出的，要保持 `cmd` 不退出本地硬盘才一直挂载着。~~

2. ~~桌面新建txt文本文件。编辑内容为：~~

```powershell
start "" "%ProgramFiles%\Git\git-bash.exe" -c "rclone mount Onedrive_local:/  Q: --cache-dir C:\Users\97532\Rclone --vfs-cache-mode writes &"
```

~~`Onedrive_local` 替换为你自己前面设置的名称 。~~

~~`Q:` 替换为你想要挂载后硬盘的盘符名称即可，记得不要和本地的C盘、D盘等重复。~~

~~`C:\Users\97532\Rclone` 为本地缓存目录，可自行设置 。~~

~~上述代码，使用git bash程序执行rclone挂载磁盘的指令。cmd无法识别`&`，也就是退出程序继续挂载指令。~~

3. ~~重命名txt文件为`rclone_startup.bat`~~
4. ~~双击运行~~

~~![image-20200622210455993](C:\Users\97532\AppData\Roaming\Typora\typora-user-images\image-20200622210455993.png)~~

~~出现：`The service rclone has been started` 或者磁盘列表出现Onedrive，则说明挂载成功。~~

5. ~~设置开机启动~~

~~由于git程序在重启后会停止，需要将`rclone_startup.bat`移动到开机启动文件里，每次开机让磁盘自动挂载。~~

~~将`rclone_startup.bat`放在`C:\Users\用户名\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup` 中~~

~~![](https://i.loli.net/2020/06/22/Cp7mKkB4tAZ3i9w.png)237.png)~~

~~现在试试重启电脑。。重启后需要等待十几秒，才会运行git bash程序挂载Onedrive。~~

~~git bash运行后会有一闪而过的黑色窗口。此时磁盘应该挂载成功了~~。

> 删掉上面的方法，是由于步骤太过冗杂，需要安装git bash，而且开机启动时存在一闪而过的黑色窗口，运行后的挂载盘也存在问题。

------

挂载操作在退出cmd后就自动结束了，所以我们需要让它后台运行。
rclone虽然提供了`--daemon`参数来实行后台运行，但是该参数并不适合于windows环境中。会有如下提示：

```
λ rclone mount GDrive:/ x: --cache-dir F:\Temp --vfs-cache-mode writes --daemon
2018/05/01 09:54:19 background mode not supported on windows platform
```

所以，我们需要另外想个办法让rclone能够后端运行以及开机自动挂载。

1. **新建一个文本文件。**

填入以下内容，**请注意修改倒数第二行的`WS.Run`中相关命令为你上步成功执行的命令**，然后将该文件名改为`rclone.vbs` （后缀名为`.vbs`即可）

```vbscript
Option Explicit
Dim WMIService, Process, Processes, Flag, WS
Set WMIService = GetObject("winmgmts:{impersonationlevel=impersonate}!\\.\root\cimv2")
Set Processes = WMIService.ExecQuery("select * from win32_process")
Flag = true
for each Process in Processes
    if strcomp(Process.name, "rclone.exe") = 0 then
        Flag = false
        exit for
    end if
next
Set WMIService = nothing
if Flag then
    Set WS = Wscript.CreateObject("Wscript.Shell")
    WS.Run "rclone mount Onedrive_local:/  Q: --cache-dir C:\Users\97532\Rclone --vfs-cache-mode writes", 0
end if
```

> `Onedrive_local` 替换为你自己前面设置的名称 。
>
> `Q:` 替换为你想要挂载后硬盘的盘符名称即可，记得不要和本地的C盘、D盘等重复。
>
> `C:\Users\97532\Rclone` 为本地缓存目录，可自行设置 。
>
> 关于`vfs-cache-mode`项设置，还是建议看下官方的说明根据自己的需求和网络情况来进行选择 https://rclone.org/commands/rclone_mount/#file-caching 。这里只做简单说明：
>
> - `off`： 本地不做任何缓存，所有文件直接从云端获取并写入。**建议网速特别好时（复制粘贴大文件时建议至少100M管以上速度）使用。**
> - `minimal`：（和off类似，但是已经打开的文件会被缓存到本地。**个人推荐，小文件基本够用，但是如果你的网络情况（梯子）不是特别好的话，用writes也行**
> - `writes`：（如果文件属性为`只读`则只从云端获取，不然先缓存在本地进行读写操作，随后被同步。**个人推荐使用，但是在直接从本地复制文件到GDrive时还是看网络情况**
> - `full`：（所有的读写操作都会缓存到磁盘中。然后才会同步。**不是很推荐。会导致所有文件均被缓存到本地。直到达到你缓存总额（--cache-total-chunk-size，默认大小10G）。但是你网速特别差时也可以使用。**

如果你需要中断这个挂载操作，请直接在任务管理器中kill掉`rclone.exe`进程即可。

2. **开机自启**

将这个文件`rclone.vbs`复制（或者剪贴）到开机项中`C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp`（Windows 10）即可实现开机自动挂载~

## 五：参考资料

《[Windows平台下使用 Rclone 挂载 OneDrive 为本地硬盘](https://www.lifeee.top/posts/14654.html)》

《[CMD打开Git Bash](https://blog.csdn.net/lilongsy/article/details/89916797)》

《[Windows-如何从DOS命令行启动Git Bash？](http://www.itkeyword.com/doc/9018080391155886x507/how-to-launch-git-bash-from-dos-command-line)》

《[使用rclone在Windows下挂载Google个人/团队云盘](https://blog.rhilip.info/archives/874/)》