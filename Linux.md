# Linux

## apt 与 apt-get 的区别

[Linux 中 apt 与 apt-get 命令的区别与解释](https://www.sysgeek.cn/apt-vs-apt-get/)

## 初学者教程-Ubuntu

[面向初学者的 Linux 命令行](https://ubuntu.com/tutorials/command-line-for-beginners#1-overview)

## WSL2 下 Ubuntu22.04 的问题

使用 sudo apt install 时出现警告信息

Failed to retrieve available kernel versions.

Failed to check for processor microcode upgrades.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.

[问题解释](https://askubuntu.com/questions/1404129/ubuntu-22-04-lts-on-wsl-failed-to-retrieve-available-kernel-versions-failed)

[一个搬运的国内版本](https://fogsbane.dev/zh-CN/q/e72a1770ada8af11920fbc03bd1b633f53c99d5ae73737008be2f41358470b92)

简要就是说 WSL2 是在容器内运行 Ubuntu22.04，无法识别内核和处理器
