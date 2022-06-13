# 用户指南

## 开始使用
根据收到的登录信息：
```
ssh ubuntu@<ip> -p <port>
    password: <password>
    IP: <ip>
    port: <port>
    mapped username: <username>
```

使用命令 `ssh ubuntu@<ip> -p <port>` 登录服务器（校外需要 NTU VPN）.

输入收到的初始密码，首次登录后会提示进行修改密码. 请使用强密码.

现在可以作为 `ubuntu` 用户开始使用了（一般 "mapped username: `<username>`" 这行无关紧要）.

## 默认设置
已经配置好的环境包括：
* 环境内的全部权限，可以任意使用而不影响其他用户（甚至可以自行安装 Docker）
* Ubuntu 20.04
* CUDA (11.6.2) and cuDNN (8.4.1.50)
* Miniconda (4.12.0) and Python 3.9
* 供服务器所有用户使用的共享目录 `/Data` （只读）和 `/Resource` （可读写）
* 主机中的映射用户 `<username>` （默认不启用）
