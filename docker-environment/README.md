## Docker 笔记

### 1 在Win10上准备centos7

> `采用vagrant+virtual box`

**安装环境是环境是**

- `win10 64位`
- `VirtualBox-6.0.12-133076-Win`
- `vagrant_2.2.6_x86_64`
- `centos7`
- `XShell6`

#### 1.1 下载安装vagrant

- 访问Vagrant官网[https://www.vagrantup.com/]
- 下载对应的版本安装
- 命令行输入vagrant，测试是否安装成功

#### 1.2 下载安装virtual box

- VirtualBox官网[https://www.virtualbox.org/]
- 下载对应的操作系统版本安装

#### 1.3 安装centos7

- 创建centos7文件夹，并进入其中[目录全路径不要有中文字符]
- 在此目录下打开cmd，运行vagrant init centos/7
     此时会在当前目录下生成Vagrantfile，同时指定使用的镜像为centos/7。
     也可以在官网手动下载centos/7的镜像文件，名称是virtualbox.box文件
- 将virtualbox.box文件添加到vagrant管理的镜像中
  1. 下载好的的virtualbox.box文件
  2. 保存到磁盘的某个目录，比如D:\vagrant\ios\docker\virtualbox.box
  3. 添加镜像并起名叫centos/7：
     vagrant box add centos/7 D:\vagrant\ios\docker\virtualbox.box
  4. vagrant box list  查看本地的box[这时候可以看到centos/7]

-  开cmd窗口,执行vagrant up,根据Vagrantfile文件启动创建虚拟机。
- vagrant常用命令
  - vagrant ssh         【进入刚才创建的centos7中】
  - vagrant status     【查看centos7的状态】
  - vagrant halt          【停止/关闭centos7】
  - vagrant destroy   【删除centos7】
  - vagrant status      【查看当前vagrant创建的虚拟机】
  - Vagrantfile中也可以写脚本命令，使得centos7更加丰富
     但是要注意，修改了Vagrantfile，要想使正常运行的centos7生效，必须使用vagrant reload

#### 1.4 Xshell连接centos7

按下列步骤操作：
- vagrant ssh，进入到虚拟机中
- sudo -i
- vi /etc/ssh/sshd_config，并修改PasswordAuthentication yes后输入[:wq]关闭vi。
- passwd修改密码，比如root
- systemctl restart sshd
- Xshell里使用账号root/root进行登录

#### 1.5 Vagrantfile通用写法

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "centos/7"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
    config.vm.provider "virtualbox" do |vb|
        vb.memory = "4000"
        vb.name= "centos7-01"
        vb.cpus= 2
    end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
```

#### 1.6 box的打包分发

- 退出虚拟机[vagrant halt]
- 通过打包命令[vagrant package --output docker-centos7.box]，等到docker-centos7.box
- 将docker-centos7.box添加到其他的vagrant环境中
  	vagrant box add docker-centos7 docker-centos7.box
- 新建文件夹【docker-centos7】里，执行下列命令:
  	vagrant init docker-centos7根据Vagrantfile启动虚拟机[vagrant up]

### 2 安装docker

> docker官网：https://docs.docker.com/install/linux/docker-ce/centos/

- 根据上面的操作，用xshell连接创建号的centos7虚拟机


- 卸载之前的docker
  	sudo yum remove docker \
                    docker-client \
                    docker-client-latest \
                    docker-common \
                    docker-latest \
                    docker-latest-logrotate \
                    docker-logrotate \
                    docker-engine
                    
- 安装必要的依赖
  	sudo yum install -y yum-utils \
      device-mapper-persistent-data \
      lvm2
      
- 设置docker仓库
  	sudo yum-config-manager \
        --add-repo \
        https://download.docker.com/linux/centos/docker-ce.repo
- 安装docker
  	sudo yum install -y docker-ce docker-ce-cli containerd.io
- 启动docker
  	sudo systemctl start docker
- 测试docker安装是否成功
  	sudo docker run hello-world

>  [访问这个地址,添加阿里云镜像加速器:https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors]