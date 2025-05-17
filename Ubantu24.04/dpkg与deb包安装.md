# deb包安装

## 总则
千万别用系统自带的应用商店，难用得要死，中文还有乱码，tm离谱的是其他app中文都是正常显示的  
我的建议：  
1. 直接去软件官网下载安装包  
2. 用终端安装  
记住：Ubuntu基于Debian，所以要下载.deb格式的安装包  

## 本人情况
设备：树莓派5  
架构：arm64  

## dpkg常用命令
### 安装
先用cd进入安装包的目录，因为我的包是从浏览器下的，所以位置是/home/Downloads  
然后
```sh
sudo dpkg -i 你的安装包名字.deb
```
### 卸载软件（保留配置文件）
sudo dpkg -r 包名

#### 彻底卸载（连配置文件一起删）
sudo dpkg -P 包名

#### 安装包名与包名？
如果你仔细看我写的这两行代码，你会发现安装部分写的是安装包名，卸载部分写的是包名。  
这并不是我写错了，二是因为这两者完全不是同一个东西！
##### 举个例子
假设我要安装vscode，他的安装包名字是 `code_1.90.0-1234_amd64.deb`：
```sh
我安装时输入的指令：  
sudo dpkg -i code_1.90.0-1234_amd64.deb
卸载时：  
sudo dpkg -r code   
```
##### 怎么看包名？
```sh
dpkg -I 你的安装名字.deb | grep "Package:"
```
按下回车后，终端会显示`Package: code`，这个`code`就是包名

## 常用软件下载直达
- Vscode：[官网下载](https://code.visualstudio.com/download)  
- QQ：[Linux版下载](https://im.qq.com/linuxqq/index.shtml)  

> 注：安装完如果提示依赖问题，可以试试 `sudo apt --fix-broken install`