## Docker 笔记

### 1 在Win10上准备centos7

> `采用vagrant+virtual box`

**安装环境是环境是**

1. `win10 64位`
2. `VirtualBox-6.0.12-133076-Win`
3. `vagrant_2.2.6_x86_64`
4. `centos7`
5. `XShell6`

#### 1.1 下载安装vagrant

```
01 访问Vagrant官网[https://www.vagrantup.com/]

02 下载对应的版本安装

03 命令行输入vagrant，测试是否安装成功
```

#### 1.2 下载安装virtual box

````
01 VirtualBox官网[https://www.virtualbox.org/]

02 下载对应的操作系统版本安装
````

#### 1.3 安装centos7

```
01 创建centos7文件夹，并进入其中[目录全路径不要有中文字符]

02 在此目录下打开cmd，运行vagrant init centos/7
   此时会在当前目录下生成Vagrantfile，同时指定使用的镜像为centos/7。
   也可以在官网手动下载centos/7的镜像文件，名称是virtualbox.box文件

03 将virtualbox.box文件添加到vagrant管理的镜像中
	(1)下载好的的virtualbox.box文件
    (2)保存到磁盘的某个目录，比如D:\vagrant\ios\docker\virtualbox.box
    (3)添加镜像并起名叫centos/7：
       vagrant box add centos/7 D:\vagrant\ios\docker\virtualbox.box
    (4)vagrant box list  查看本地的box[这时候可以看到centos/7]

04 centos/7镜像有了，根据Vagrantfile文件启动创建虚拟机
   来到centos7文件夹，在此目录打开cmd窗口，执行vagrant up, 
   结束后，打开virtual box查看centos7是否创建成功。
	
06 vagrant常用命令
	(1)vagrant ssh    
    	进入刚才创建的centos7中
    (2)vagrant status
    	查看centos7的状态
    (3)vagrant halt
    	停止/关闭centos7
    (4)vagrant destroy
    	删除centos7
    (5)vagrant status
    	查看当前vagrant创建的虚拟机
    (6)Vagrantfile中也可以写脚本命令，使得centos7更加丰富
    	但是要注意，修改了Vagrantfile，要想使正常运行的centos7生效，必须使用vagrant reload
```

