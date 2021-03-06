# vagrant 安装虚拟机

## 1 下载并安装

下载 vagrant

https://www.vagrantup.com/

下载box

https://app.vagrantup.com/boxes/search

下载centos7 
https://cloud.centos.org/centos/7/vagrant/x86_64/images/CentOS-7-x86_64-Vagrant-2004_01.VirtualBox.box

vagrant 添加box

vagrant box add centos/7 ../CentOS-7-x86_64-Vagrant-2004_01.VirtualBox.box


## 2 编写Vagrantfile

```shell
boxes = [
    {
        :name => "m1",
        :eth1 => "192.168.1.11",
        :mem => "3000",
        :cpu => "2"
    },
    {
        :name => "w1",
        :eth1 => "192.168.1.12",
        :mem => "3000",
        :cpu => "2"
    },
    {
        :name => "w2",
        :eth1 => "192.168.1.13",
        :mem => "3000",
        :cpu => "2"
    }
]

Vagrant.configure(2) do |config|

  config.vm.box = "centos/7"
  
   boxes.each do |opts|
      config.vm.define opts[:name] do |config|
        config.vm.hostname = opts[:name]

        config.vm.provider "virtualbox" do |v|
          v.customize ["modifyvm", :id, "--memory", opts[:mem]]
		      v.customize ["modifyvm", :id, "--cpus", opts[:cpu]]
		      v.customize ["modifyvm", :id, "--name", opts[:name]]
        end

        config.vm.network :public_network, ip: opts[:eth1]
      end
  end

end
```

## 3 执行vagrant 命令

```shell
vagrant up
```

## 4 修改s sh 登陆

```shell
vagrant ssh m1
sudo -i
vim /etc/ssh/sshd_config
修改：
PasswordAuthentication yes
systemctl restart sshd

修改密码：
passwd

ssh 登陆时：
可能会报错 WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
解决方案：在客户端输入：ssh-keygen -R 192.168.1.11(远程ip)
```

## 5 单机的Vagrantfile

```shell
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

   config.vm.network "public_network" , ip: "192.168.1.105"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
   config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
     vb.memory = "3000"
     vb.name = "1-centos7"
     vb.cpus = "2"
   end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end

```

