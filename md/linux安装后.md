# 搜狗输入法

**第一步**   
打开ubuntu自带的商店，搜索*fcitx*,安装搜索出来的3个软件  
**第二步**  
下载[suogou输入法linux版本](https://pinyin.sogou.com/linux/?r=pinyin)   
**第三步**    
->打开系统设置*Region&Language*     
->*Manage Installed Languages* ，跳出弹框，忽略即可  
->*Keyboard input method system*,选择fcitx  
-> 打开软件*fcitx configutrayion*,添加*souguo pinyin*放第一个就好了

# JDK

 
[JDK下载官网](https://www.oracle.com/technetwork/java/javase/downloads/index.html)  
解压：  
```linux
sudo tar -zxvf jdk-8u191-linux-x64.tar.gz -C /usr/local
```
编辑环境变量
```linux
 vim ~/.bashrc
 ```
 输入以下内容（注意jdk文件夹的名字）
 ```linux
 export JAVA_HOME=/usr/local/jdk1.8.0_181
 export PATH=${JAVA_HOME}/bin:$PATH
```
使配置生效
```linux
source ~/.bashrc 
```
# 安装VM虚拟机
下载14.x的版本  
[VM下载官网](https://my.vmware.com/cn/web/vmware/info/slug/desktop_end_user_computing/vmware_workstation_pro/14_0)  
下载完后添加权限
```linux
sudo chmod +x VMware-Workstation-Full-14.1.2-8497320.x86_64.bundle
```
安装
```linux
sudo ./VMware-Workstation-Full-14.1.2-8497320.x86_64.bundle
```
激活码
*VF19H-8YY5L-48DQY-JEWNG-YPKF6*  
*FF31K-AHZD1-H8ETZ-8WWEZ-WUUVA*  
*CV7T2-6WY5Q-48EWP-ZXY7X-QGUWD*  

如果没有安装*gcc*,还不能打开，通过以下命令安装*gcc*
```linux
sudo apt-get  install  build-essential
```



# Idea

[idea下载官网](https://www.jetbrains.com/idea/download/#section=linux)

解压  
```linux
sudo tar -zxvf ideaIC-2018.3.1-no-jdk.tar.gz -C /usr/local/
```
通过命令打开
```linux
/usr/local/idea-IC-183.4588.61/bin/idea.sh
```
配置,打开后，不要跳过配置，根据自动配置，可以在lunix系统上创建启动器等，省得自己配了！！！  
如果出现:
```linux
 Failed to load module "canberra-gtk-module"
```
那么就执行命令
```linux
sudo apt-get install libcanberra-gtk-module
```
如果无法以图标的方式打开，则
```linux
sudo vim /etc/environment 
# 加入这句话
JAVA_HOME="/usr/local/jdk1.8.0_191"
```
注销用户重新登录即可，原因与linux不同的环境变量有关。

#  其他软件

**Google 浏览器**：[谷歌浏览器官网](https://www.google.cn/chrome/index.html)  
**WPS**：[wps官网](http://www.wps.cn/product/wpslinux#)  
**vscode**： [vscode下载官网](https://code.visualstudio.com/)  


# 美化 

**第一步，安装美化工具**
```linux
sudo apt install gnome-tweak-tool
```
**第二步，去下载插件或扩展**  
[扩展下载地址](https://extensions.gnome.org/ )  
注意：不要用谷歌浏览器，因为新版的谷歌开始不支持插件了。  
打开之后，先安装一个浏览器插件。否则会有相应的警告，这样是无法安装扩展的。  
**第三步，安装常用扩展**  
*User THemes*：用来设置主题的  
*Hide to Bar*：用来隐藏lunix上边栏
*Dock to bash*：用来设置启动器的

**美化资源下载网站**    
[wallhaven,壁纸下载](https://alpha.wallhaven.cc/)  
[各种主题下载](https://www.opendesktop.org/s/Gnome)

# 更换锁屏登录壁纸

输入以下命令
```linux
sudo vim /usr/share/gnome-shell/theme/ubuntu.css
```
搜索关键字*lockDialogGroup*,修改如下示例
```linux
 background: #2c001e url(file:///home/famel/Pictures/wallhaven-32182.jpg);
```

# 安装xmind8
[xmind官网](https://www.xmind.cn/download/xmind8/)  
下载软件后，解压后进根目录, 输入
```linux
sudo ./setup.sh --fix-missing
```
添加快捷方式  
先添加图标
```linux
sudo vim ~/.local/share/applications/xmind.desktop
```
输入以下内容
```
[Desktop Entry]
Type=Application
Path=<path to xmind unzip dir>/XMind_amd64
Exec=<path to xmind unzip dir>/XMind_amd64/XMind
Name=XMind
Comment=Create mind maps
GenericName=Planning Tool
Icon=<path to xmind unzip dir>/XMind_amd64/configuration/org.eclipse.osgi/983/0/.cp/icons/xmind.256.png
Categories=Office;
```
*< path to xmind unzip dir >*对应xmind目录
