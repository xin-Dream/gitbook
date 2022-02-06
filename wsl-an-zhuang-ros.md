---
description: 在子系统中安装ROS有几处需要注意的地方，其他与正常安装无异，请在阅读完本文后，参考ubuntu18.04LTS安装ROS进行安装。
---

# WSL安装ROS

## 参考

[WSL (Ubuntu 18.04 LTS) 安装ROS，可运行Rviz和Gazebo](https://blog.csdn.net/Undefinedefity/article/details/106187430) [ubuntu18.04LTS安装ROS](https://blog.csdn.net/qq\_45172156/article/details/106479607)

## 设置wsl使用Windows的clash代理

在安装时**sudo rosdep init**和**rosdep update**时经常会响应超时或连接失败，在子系统中就可以方便多了。

1. 在clash中查看端口号
2. 在子系统中添加代理

```bash
sudo vim ~/.bashrc

export http_proxy=127.0.0.1:7890
export https_proxy=127.0.0.1:7890
#此处7890对应上面端口号
```

添加代理后rosdep update 一次通过&#x20;

## 设置可用窗口

安装完ROS后，使用小海龟测试时在打开窗口时会有错误。请按以下步骤安装Xlaunch，以下步骤可以打开rviz，否则还要改很多。 ~~目前还没设置好gazebo，后续会设置好会更新。~~ 内容已修改，gazebo可正常启动

1. [下载Xserver](https://sourceforge.net/projects/vcxsrv/)
2. 在wsl中修改配置，修改后务必重启终端

```bash
sudo vim ~/.bashrc

#在末尾添加以下内容
# export DISPLAY=:0.0
# export LIBGL_ALWAYS_INDIRECT=
# export LIBGL_ALWASY_SOFTWARE=1

#修改为以下内容，rviz与gazebo都能正常显示
export DISPLAY=localhost:0

#wq保存退出后
source ~/.bashrc
```

1. 打开XLaunch&#x20;
2. 启动rviz与小乌龟&#x20;

注. 如果之前安装过Xming，可能会有冲突，卸载掉其中一个就好了
