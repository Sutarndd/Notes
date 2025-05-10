# SSH
## 总教程
【ubuntu安装ssh,设置开机自启动 -  CSDN App】https://blog.csdn.net/mankeywang/article/details/133708714?sharetype=blogdetail&shareId=133708714&sharerefer=APP&sharesource=2301_79422411&sharefrom=link

## 安装net-tools和openssh-server
```
sudo apt-get install net-tools
sudo apt-get install openssh-server
```
## 开机自启动ssh（一般都是默认启动）
```
sudo systemctl enable ssh
```
## 查看是否开启ssh服务
```
dpkg -l | grep ssh	
```
如果看到```sshd```那说明ssh-server已经启动
如果没有则可以这样启动：
```
sudo service ssh start
```
## 配置相关：
ssh-server配置文件位于```/etc/ssh/sshd_config```，在这里可以定义SSH的服务端口，默认端口是22，可以自己定义成其他端口号.

## 使用SSH远程登录
格式为：
```
ssh 用户名@ip地址
```
比如chris @192.168.123.121
chris是地址为192.168.123.121机器上的一名用户，需要输入密码
## 断开连接
```
exit
```