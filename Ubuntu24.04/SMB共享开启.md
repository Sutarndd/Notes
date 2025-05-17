 # SMB文件协议的开启
 
 ## 为什么有这篇文章？
 给树莓派开启ftp后打算用iPad自带的文件app连接树莓派传文件，结果一直失败，捣鼓了半天才发现自带的文件app不支持ftp协议，支持SMB，想着既然ftp都弄了不如全面一点，把SMB也搞了，所以有了这篇教程  
 ## 什么是SMB？
 SMB（Server Message Block）是一种**网络文件共享协议**，主要用于在计算机之间共享文件、打印机等资源。它是 Windows 网络的默认共享协议，但也支持 Linux（如 Samba）、macOS 和 iPadOS。
除了跨平台，他还有以下的优点
-高性能
比 FTP 更高效，适合大文件传输和局域网共享。
-安全性
支持用户认证（用户名/密码）和加密（SMB3.0+ 默认加密）。
-功能丰富
不仅共享文件，还能共享打印机、串行端口等。
总之，他比ftp更好用就对了
## 配置 SMB 
### **1. 安装 Samba**
```bash
sudo apt update
sudo apt install samba
```

### **2. 配置共享目录**
编辑 Samba 配置文件：
```bash
sudo vim /etc/samba/smb.conf
```
下面给出**smb.conf 文件的翻译**
### **1. 配置文件结构**
`smb.conf` 由 **全局设置（`[global]`）** 和 **共享定义（如 `[share_name]`）** 两部分组成：
```ini
[global]
    # 全局参数（影响所有共享）
    workgroup = WORKGROUP
    server string = Samba Server

[shared_folder]
    # 共享目录的独立参数
    path = /path/to/share
    browseable = yes
    read only = no
```

---

### **2. 全局参数（`[global]`）**
| **参数**               | **说明**                                                                 | **示例值**                     |
|------------------------|-------------------------------------------------------------------------|-------------------------------|
| `workgroup`           | 定义 Samba 所在的工作组（需与 Windows 网络一致）                          | `WORKGROUP`（默认）           |
| `server string`       | 服务器描述信息                                                           | `My Samba Server`             |
| `security`            | 认证模式：<br> `user`（用户名密码）<br> `share`（匿名共享，不安全）       | `user`                        |
| `map to guest`        | 处理无效用户的方式：<br> `bad user`（拒绝访问）<br> `Bad Password`（降级为访客） | `bad user`                    |
| `interfaces`          | 指定监听的网络接口                                                       | `interfaces = 192.168.1.0/24` |
| `log file`            | 日志文件路径                                                             | `/var/log/samba/log.%m`       |
| `max log size`        | 单个日志文件大小（KB）                                                   | `50000`（50MB）               |

---

### **3. 共享定义参数（`[share_name]`）**
| **参数**          | **说明**                                                                 | **示例值**               |
|-------------------|-------------------------------------------------------------------------|-------------------------|
| `path`           | 共享目录的物理路径（必须存在且有权限）                                    | `/home/shared`          |
| `browseable`     | 是否在网络上可见（`yes`/`no`）                                           | `yes`                   |
| `read only`      | 是否只读（`yes`/`no`）                                                  | `no`（允许写入）        |
| `guest ok`       | 是否允许匿名访问（`yes`/`no`）                                           | `no`（需认证）          |
| `valid users`    | 允许访问的用户（逗号分隔）                                                | `user1, user2`          |
| `create mask`    | 新建文件的默认权限（八进制）                                              | `0644`                  |
| `directory mask` | 新建目录的默认权限（八进制）                                              | `0755`                  |
| `force user`     | 强制所有操作以指定用户身份执行（慎用）                                    | `nobody`                |

---

### **4. 安全相关参数**
| **参数**               | **说明**                                                                 |
|------------------------|-------------------------------------------------------------------------|
| `encrypt passwords`    | 启用密码加密（默认 `yes`，必须开启）                                     |
| `hosts allow`          | 允许访问的 IP（逗号分隔）                                                |
| `hosts deny`           | 拒绝访问的 IP                                                            |
| `writable`            | 同 `read only = no`，控制写入权限                                        |

---

### 具体例子
#### 局部共享（只共享某个/一些文件）
在文件末尾添加（示例共享 `/home/pi/share`）：
```ini
[名字]
   path = /home/pi/share
   browseable = yes
   read only = no
   guest ok = no
   valid users = pi
```
#### 全局共享 （共享整个目录）
```ini
[名字]
   path = /
   browseable = yes
   read only = no
   guest ok = no
#下面用户名改成自己的
   valid users = your_username
   force user = root  
   force group = root
   create mask = 0777
   directory mask = 0777
```
### **3. 设置 Samba 用户密码**
```bash
sudo smbpasswd -a 系统的用户名  # 会提示输入密码
```
解释一下
- **`smbpasswd`**：Samba 专用的密码管理工具。
- **`-a`**：表示添加（Add）一个新用户到 Samba 的密码数据库。
- **`your_username`**：替换为你的 **Ubuntu 系统用户名**（比如我的系统用户名是pi）
实操的时候对这行命令产生了疑问，问了DeepSeek，这是他给的答案
### **执行后会发生什么？**
1. 系统会提示你输入并确认一个密码（**此密码可以与系统登录密码不同**）。
2. 密码会被加密并保存到 Samba 的私有数据库（`/etc/samba/smbpasswd` 或 `/var/lib/samba/private/passdb.tdb`）。
3. 此后，必须用 **这个 Samba 密码** 访问共享文件夹（而不是系统密码）。
### **一些问题**
### 如果我不走这一步会怎么样？
- 你无法通过 SMB 访问共享，即使你输入的系统密码是对的。  
- 客户端会提示 **“无效的用户名或密码”**。

### 我要怎么修改或者删除这个密码？
- **修改密码**：  
  ```bash
  sudo smbpasswd your_username
  ```
- **删除用户**：  
  ```bash
  sudo smbpasswd -x your_username
  ```

### 用户必须提前存在吗？
- 对，`your_username` 必须是 **Ubuntu 上已存在的系统用户**（可通过 `id your_username` 检查）。  
- 若用户不存在，需先创建：  
  ```bash
  sudo adduser your_username  # 创建系统用户
  sudo smbpasswd -a your_username  # 再添加到 Samba
  ```

---

### **4. 重启 Samba 服务**
```bash
sudo systemctl restart smbd
```
### **5.连接**
连接时url地址填写:`smb://SMB服务器的IP地址`  
选择 「注册用户」，输入：
名称：树莓派的 SMB 系统的用户名（如 pi）。
密码：对应密码。