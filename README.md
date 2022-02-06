# ubuntu18.04LTS安装ROS

## 一、环境简介

ubuntu18.04LTS 、ROS Melodic Morenia&#x20;

参考：[古月居ROS入门21讲](https://www.bilibili.com/video/BV1zt411G7Vn/?p=5) [官方安装教程](http://wiki.ros.org/melodic/Installation/Ubuntu)

## 二、 安装

在安装之前，一定保证可从互联网下载中选中以下四个选项。因为后面要添加ros的源 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200601191100505.png?x-oss-process=image/watermark,type\_ZmFuZ3poZW5naGVpdGk,shadow\_10,text\_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MTcyMTU2,size\_16,color\_FFFFFF,t\_70)

### 1. 添加ros软件源

```bash
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main " > /etc/apt/sources.list.d/ros-latest.list'
```

### 2. 添加秘钥

```bash
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
```

### 3. 安装ROS

```bash
sudo apt update
sudo apt install ros-melodic-desktop-full
```

这里第二步可能会出现错误，再更新一次执行就好了

#### 【2020-12-03】

第二步安装时可能会有缺少依赖 使用`sudo aptitude install ros-melodic-desktop-full` 可能第一个方案并不好用，可以换个方案

### 4. 初始化rosdep

#### 【2021-07-28】最实用方法 离线update

1. 首先在 https://github.com/ros/rosdistro 中下载ZIP文件，并解压。比如我这里把它放在`~/Downloads/rosdistro`中
2. 修改文件 `sudo vim ~/Downloads/rosdistro/rosdep/sources.list.d/20-default.list`

```

# 将文中内容修改为一下内容，注意路径

# os-specific listings first
yaml file:///home/dream/Downloads/rosdistro/rosdep/osx-homebrew.yaml osx

# generic
yaml file:///home/dream/Downloads/rosdistro/rosdep/base.yaml
yaml file:///home/dream/Downloads/rosdistro/rosdep/python.yaml
yaml file:///home/dream/Downloads/rosdistro/rosdep/ruby.yaml
gbpdistro file:///home/dream/Downloads/rosdistro/releases/fuerte.yaml fuerte

# newer distributions (Groovy, Hydro, ...) must not be listed anymore, they are being fetched from the rosdistro index.yaml instead
```

1. 修改默认url地址

`sudo vim /usr/lib/python2.7/dist-packages/rosdep2/sources_list.py`

将第72行左右的内容修改为以下内容

```

# default file to download with 'init' command in order to bootstrap
# rosdep
DEFAULT_SOURCES_LIST_URL = 'file:///home/dream/Downloads/rosdistro/rosdep/sources.list.d/20-default.list'
# DEFAULT_SOURCES_LIST_URL = 'https://ghproxy.com/https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/sources.list.d/20-default.list'

# seconds to wait before aborting download of rosdep data
DOWNLOAD_TIMEOUT = 15.0
```

1. 修改代码

```
sudo vim /usr/lib/python2.7/dist-packages/rosdep2/rep3.py

# 第39行  注意按自己的路径
REP3_TARGETS_URL = 'file:///home/dream/Downloads/rosdistro/releases/targets.yaml'

--------------------------------------------------------------------------------
sudo vim /usr/lib/python2.7/dist-packages/rosdistro/__init__.py

# 第68行 注意路径
DEFAULT_INDEX_URL = 'file:///home/dream/Downloads/rosdistro/index-v4.yaml'
```

```bash
sudo apt-get install python-rosdep
```

```
sudo rosdep init 

rosdep update
```

如果需要更新，只需更新下载的文件即可

<details>

<summary>内容为以前步骤，除2021-05-30更新方法，均不够稳定，可以不用参考。</summary>

*   \`\`\`bash sudo rosdep init \`\`\` 这里出了问题

    ````
            一开始有找不到rosdep的错误

            ```bash
            sudo apt-get install python-rosdep
            ```
            
            安装完成后，又出现了新错误，如下
            ### ERROR: cannot download default sources list from:
            https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/sources.list.d/20-default.list
            Website may be down.
            
            解决这个问题，找了一些办法


            ```bash
            sudo apt-get install python-wstool ros-melodic-ros 
            ```
            并不成功
            
            ```bash
            sudo -E rosdep init
            ```
            还是不成功
            
            ```bash
            #打开host文件
            sudo gedit /etc/hosts
            #在文件末尾添加
            151.101.84.133 raw.githubusercontent.com
            ```
            终于成功了,参考连接[解决办法](https://community.bwbot.org/topic/811/rosdep-init-%E6%88%96%E8%80%85rosdep-update-%E8%BF%9E%E6%8E%A5%E9%94%99%E8%AF%AF%E7%9A%84%E8%A7%A3%E5%86%B3%E5%8A%9E%E6%B3%95)
            ### 可用ip发生变化2020-12-03
            
            ```bash
            #当前可用ip 2020-06-05
            151.101.76.133  raw.githubusercontent.com
            140.82.113.4        github.com
            185.199.111.153     assets-cdn.github.com
            199.232.69.194      github.global.ssl.fastly.net
            #可以先ping一下地址测试
            ```

            ### 解决init失败方法（2021.05.30）
            
            ```bash
            sudo vim /etc/ros/rosdep/sources.list.d/20-default.list
            
            #复制以下内容，以下内容来自
            https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/sources.list.d/20-default.list
            
            # os-specific listings first
            yaml https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/osx-homebrew.yaml osx
            
            # generic
            yaml https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/base.yaml
            yaml https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/python.yaml
            yaml https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/ruby.yaml
            gbpdistro https://raw.githubusercontent.com/ros/rosdistro/master/releases/fuerte.yaml fuerte
            
            # newer distributions (Groovy, Hydro, ...) must not be listed anymore, they are being fetched from the rosdistro index.yaml instead
            ```


            接下来继续安装过程
            
            ```bash
            rosdep update
            ```
            ### 【2020-12-03】
            这里如果出错可能是网络问题，可以试一下手机热点
            
            ### 【2021-05-30】
            可以先测试一下网络延迟，然后将等待时间更改到对应延时，即可解决time out 问题
            
            ```bash
            ping raw.githubusercontent.com
            
            cd /usr/lib/python2.7/dist-packages/rosdep2
            sudo vim source_list.py
            sudo vim gbpdistro_support.py
            sudo vim rep3.py
            
            #将文件中的DOWNLOAD_TIMEOUT改为相应值超出一部分
            
            ```

            ### 【2021-05-30】
            上一方法主要针对网络较好时，这个是使用https://ghproxy.com的代理加速，目前来看这个方法是最高效的
            
            ```bash
            cd /usr/lib/python2.7/dist-packages/
            
            sudo vim rosdep2/source_list.py
            #在download_rosdep_data方法中添加  310行
            url="https://ghproxy.com/"+url
            
            #在以下文件中的网址前添加“https://ghproxy.com/”
            /usr/lib/python2.7/dist-packages/rosdep2/gbpdistro_support.py 36行
            /usr/lib/python2.7/dist-packages/rosdep2/sources_list.py 72行
            /usr/lib/python2.7/dist-packages/rosdep2/rep3.py	39行
            /usr/lib/python2.7/dist-packages/rosdistro/__init__.py  68行
            /usr/lib/python2.7/dist-packages/rosdistro/manifest_provider/github.py 68行 119行
            ```


        </li>   
    ````

</details>

***

### 5. 设置环境变量

```bash
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

可以输入ros，按tab键检查是否成功 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200601193246731.png?x-oss-process=image/watermark,type\_ZmFuZ3poZW5naGVpdGk,shadow\_10,text\_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MTcyMTU2,size\_16,color\_FFFFFF,t\_70)

### 6. 安装rosinstall

```bash
sudo apt install python-rosinstall python-rosinstall-generator python-wstool build-essential
```

## 三、 测试

接下来我们就跑一下小乌龟试试 以下三个命令要在三个终端中执行

```bash
roscore
rosrun turtlesim turtlesim_node
rosrun turtlesim turtle_teleop_key
```

可以看到小乌龟已经跑起来了，安装结束 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200601193831268.png?x-oss-process=image/watermark,type\_ZmFuZ3poZW5naGVpdGk,shadow\_10,text\_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MTcyMTU2,size\_16,color\_FFFFFF,t\_70)
