# SSH
## 总教程
【ubuntu安装ssh,设置开机自启动 -  CSDN App】https://blog.csdn.net/mankeywang/article/details/133708714?sharetype=blogdetail&shareId=133708714&sharerefer=APP&sharesource=2301_79422411&sharefrom=link

## 安装net-tools和openssh-server
```
sudo apt-get install net-tools
sudo apt-get install openssh-server
```
## 开机自启动ssh（一般已经设置）
```
sudo systemctl enable ssh
```
## 查看是否开启ssh服务
```
sudo systemctl status vsftpd
```
如果看到```active```那说明ssh-server已经启动
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
用户名填ubuntu系统的用户名（就是系统开机登录界面显示的那个），密码填这个用户的密码
## 断开连接
```putty-sh
exit
```