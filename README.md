# BD-Upload
> Automatic Blu-ray Disk Upload Toolkit  
> A Script used for scanning BDinfo and take screenshots for BDISO or BDMV on Linux  

转发蓝光原盘（不支持 UHD）时可以使用的一个脚本  
支持对 BDISO 或 BDMV 进行如下操作：扫描 BDinfo、截图、生成缩略图、重新制作种子  

### Installation

Dedicated Server or VPS  
```
wget -qO /usr/local/bin/bluray https://github.com/Aniverse/bdupload/raw/master/bluray
chmod +x bluray
```

Shared Seedbox with SSH access  
```
cd ; git clone https://github.com/Aniverse/bluray
echo "PATH=~/bluray:$PATH" > ~/.bashrc ; PATH=~/bluray:$PATH
```

### Guide

![检查是否缺少软件](https://github.com/Aniverse/filesss/raw/master/Images/2018.02.22.bluray.01.png)

一开始脚本会检查是否缺少脚本需要用到的软件；如有缺少，你可以选择
1. 用 root 权限安装所需软件  
2. 无需 root 权限，使用脚本内置的软件库来继续运行  
3. 退出  

![询问选项](https://github.com/Aniverse/filesss/raw/master/Images/2018.02.22.bluray.02.png)

注意：路径里即使带空格也不需要双引号  

目前可以实现以下功能：  

- **判断是 BDISO 还是 BDMV**  
输入一个**完整的路径**，若该路径是文件夹且内含名为 BDMV 的文件夹的话则认为该资源是 BDMV  
文件扩展名是 ISO、且挂载后目录里有 BDMV 文件夹存在的，则认为是 BDISO  
如果输入的文件名中出现了 HEVC、2160p、UHD 之类的字眼，则认为是 UHD Blu-ray，会提示不支持，要求重新输入  

- **自动挂载镜像**  
本操作需要用 root 权限执行 mount 命令，如无 root 权限则无法使用  
如果是 BDISO，会挂载成 BDMV，并问你是否需要对这个挂载生成的文件夹重命名  
（有时候 BDISO 的标题就是 DISC1 之类的，重命名下可能更好）  
考虑到操作完成后可能还需要做种，因此脚本不做解除挂载的操作，如有需要，请使用 `jiegua` 脚本  

- **截图**  
自动寻找 BD 里体积最大的 m2ts 截 10 张 png 图，可以自定义截图分辨率  
由于某些 BD 的实际显示分辨率和原始分辨率不一样，因此脚本对分辨率做了计算，默认使用 DAR 的分辨率  

- **扫描 BDinfo**  
BDinfo 采用 mono + bdinfocli 来实现。由于 bdinfocli 年久失修，对于 TrueHD Atmos 和 DTS:X 音轨无法较好地支持  
默认是自动扫描第一个最长的 mpls；也可以手动选择扫描哪一个 mpls  
BDinfo 会输出三个报告，一个是原版的，一个是 Main Summary，一个是 Quick Summary  
一般而言发种写个 Quick Summary 就差不多了  

- **生成缩略图**  
这个功能默认不启用；其实一般也不太用得上  
注意：这个功能有时候运行会卡住不动，暂无解决办法 ...  

- **制作种子**  
针对 BDISO，默认选择重新制作种子；针对 BDMV，默认选择不重新制作种子  
制作种子时可以选择是否写上特定的 Tracker 地址  
有一些站点比如 HD-Torrents 就必须写上站点的 Tracker  
提供自定义 Tracker 地址的选项  
提供 BT Trackers 的选项  

- **制作种子时是否过滤非必要文件**  
如检测到存在形如 !UHD、ANY!、FAB!、disc.inf 之类的文件，会询问是否在制作种子时过滤掉这些文件  
对于 BDISO，会把挂载出来的 BDMV 和 CERTIFICATE 复制到一个临时目录上制作种子，因此速度较慢；这个文件夹在运行完以后会保留（包含完整的 BD 内容）  
对于 BDMV，会把多余的文件移动到其他目录，再对原始文件夹制作种子，操作完毕后再把文件移动回来；这个临时文件夹在运行完以后会被删除  

- **使用 rclone 同步文件**  
需要你自己设置好 rclone，且在脚本里设置好 rclone remote path 才能使用（不然这个选项不会出现）  

![正常运行](https://github.com/Aniverse/filesss/raw/master/Images/2018.02.22.bluray.03.png)

脚本运行中 ...  

![输出结果](https://github.com/Aniverse/filesss/raw/master/Images/2018.02.22.bluray.04.png)

如果选择扫描 BDinfo，则全部任务完成后会在 SSH 上输出 BDinfo Quick Summary，直接从 SSH 上复制即可  

![h5ai](https://github.com/Aniverse/filesss/raw/master/Images/2018.02.22.bluray.05.png)

安装了 `h5ai` 的话可以在网页上预览、下载生成的截图、BDinfo、种子  



### Known Issues

- **有时候输入正确的路径反应却不对**  
暂无解决办法，我还不知道这是由于什么原因导致的  

### To Do List



### Under Consideration

- **判断操作是否成功**  
目前操作中哪一步翻车了也不会有翻车了的提醒  

- **自动上传到 ptpimg**  
调用 ptpimg_uploader 来完成，脚本跑完后会输出 ptpimg 的链接。运行之前你需要自己配置好 ptpimg_uploader  

  -------------------
### Screenshots

![FH-输出结果](https://github.com/Aniverse/filesss/raw/master/Images/OLD/2018.02.16.bdupload.04.png)
![FH-h5ai](https://github.com/Aniverse/filesss/raw/master/Images/OLD/2018.02.16.bdupload.05.png)

在 FeralHosting 盒子上运行的结果  

  -------------------
### Some references

https://xxxxxx.org/forums/viewtopic?topicid=61434  

