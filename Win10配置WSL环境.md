# 	Win10配置WSL环境保姆级教程

## 下载和安装Windows Subsystem Linux







## WSL初始配置

Ubuntu的WSL性能比虚拟机的完整版Ubuntu要好，系统开销也更小，但是其功能并不完整，在使用前需要进行一些配置和安装。

**配置源地址**：

命令行中输入：

```shell
sudo vim /etc/apt/sources.list
```

按键盘```i```键，进入vim插入模式，并插入一下代码：

```shell
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

然后按```esc```键进入命令模式，输入```:wq! ```保存退出。

**更新工具**

sources.list文件保存之后，依次输入：

```shell
sudo apt-get update
```

```shell
sudo apt-get upgrade
```

输入命令只有，耐心等待安装完成。



## WSL环境配置

**安装Nginx**



**安装JDK**



**安装MySQL**



**安装Docker**



**安装Python**