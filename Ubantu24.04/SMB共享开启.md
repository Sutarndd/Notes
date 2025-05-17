SMB（Server Message Block）是一种**网络文件共享协议**，主要用于在计算机之间共享文件、打印机等资源。它是 Windows 网络的默认共享协议，但也支持 Linux（如 Samba）、macOS 和 iPadOS。

---

### **SMB 的核心特点**
1. **跨平台共享**  
   - Windows、Linux（通过 Samba）、macOS、Android/iPadOS 都支持。
2. **高性能**  
   - 比 FTP 更高效，适合大文件传输和局域网共享。
3. **安全性**  
   - 支持用户认证（用户名/密码）和加密（SMB3.0+ 默认加密）。
4. **功能丰富**  
   - 不仅共享文件，还能共享打印机、串行端口等。

---

### **SMB vs. FTP 对比**
| 特性                | SMB                          | FTP                         |
|---------------------|-----------------------------|----------------------------|
| **协议设计**         | 专为局域网优化，高性能        | 较老，延迟较高              |
| **加密支持**         | SMB3.0+ 默认加密             | 需 FTPS/SFTP 才安全         |
| **跨平台兼容性**     | Windows/Linux/macOS/iPadOS   | 通用，但 iPadOS 原生不支持  |
| **适用场景**         | 内网文件共享、打印机共享      | 互联网文件传输              |

---


 
### 配置 SMB 
#### **1. 安装 Samba**
```bash
sudo apt update
sudo apt install samba
```

#### **2. 配置共享目录**
编辑 Samba 配置文件：
```bash
sudo vim /etc/samba/smb.conf
```
##### 局部共享（只共享某个/一些文件）
在文件末尾添加（示例共享 `/home/pi/share`）：
```ini
[pi_share]
   path = /home/pi/share
   browseable = yes
   read only = no
   guest ok = no
   valid users = pi
```
##### 全局共享 （共享整个目录）
复制下面代码到文件时后面的注释要全部删除，不然会报错！  
```ini
[ubuntu_root]
   path = /
   browseable = yes
   read only = no  # 允许写入（危险！建议设为 yes）
   guest ok = no   # 禁止匿名访问
   valid users = your_username  # 允许访问的用户
   force user = root  # 以 root 身份访问（慎用！）
   force group = root
   create mask = 0777
   directory mask = 0777
```
#### **3. 设置 Samba 用户密码**
```bash
sudo smbpasswd -a pi  # 会提示输入密码
```

#### **4. 重启 Samba 服务**
```bash
sudo systemctl restart smbd
```
