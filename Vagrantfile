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
  
    NAME = "inspec-jenkins-box"
  
    unless Vagrant.has_plugin?("vagrant-reload")
      puts 'Installing vagrant-reload Plugin...'
      system('vagrant plugin install vagrant-reload')
    end
  
    unless Vagrant.has_plugin?("vagrant-proxyconf")
      puts 'Installing vagrant-proxyconf Plugin...'
      system('vagrant plugin install vagrant-proxyconf')
    end
  
    # Every Vagrant development environment requires a box. You can search for
    # boxes at https://vagrantcloud.com/search.
    config.vm.box = "inspec-jenkins"
    config.vm.box_url = "https://yum.oracle.com/boxes/oraclelinux/latest/ol7-latest.box"
  
    # Disable automatic box update checking. If you disable this, then
    # boxes will only be checked for updates when the user runs
    # `vagrant box outdated`. This is not recommended.
    config.vm.box_check_update = false
  
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
    # config.vm.network "public_network"
  
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
       # Display the VirtualBox GUI when booting the machine
       #vb.gui = true
  
       # Customize the amount of memory on the VM:
       vb.memory = "1024"
       vb.name = NAME
    end
  
    #if Vagrant.has_plugin?("vagrant-proxyconf")
    #   config.proxy.http = "http://server:port/"
    #   config.proxy.https = "https://server:port/"
    #   config.proxy.no_proxy = "localhost,127.0.0.1"
    #end
  
    # VM Hostname
    config.vm.hostname = NAME
  
    # Port forwarding
    config.vm.network "forwarded_port", guest: 8080, host: 8080
    config.vm.network "forwarded_port", guest: 50000, host: 50000
  
    # View the documentation for the provider you are using for more
    # information on available options.
    
    config.vm.provision "shell", inline: "echo 'INSTALLER: OS Installation complete, Oracle Linux 7 ready to use!'"
    config.vm.provision "shell", inline: "echo 'INSTALLER: Installing ChefDK...'"
    config.vm.provision "shell", inline: "curl -O https://packages.chef.io/files/stable/chefdk/2.5.3/el/7/chefdk-2.5.3-1.el7.x86_64.rpm"
    config.vm.provision "shell", inline: "yum localinstall -y chefdk-2.5.3-1.el7.x86_64.rpm"
    config.vm.provision "shell", inline: "echo 'INSTALLER: Installing Jenkins...'"
    config.vm.provision "shell", inline: "curl -O https://pkg.jenkins.io/redhat-stable/jenkins.repo"
    config.vm.provision "shell", inline: "mv jenkins.repo /etc/yum.repos.d/"
    config.vm.provision "shell", inline: "rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key"
    config.vm.provision "shell", inline: "yum install -y java jenkins git vim docker"
    config.vm.provision "shell", inline: "systemctl enable docker; systemctl start docker"
    config.vm.provision "shell", inline: "systemctl enable jenkins; systemctl start jenkins"

  end