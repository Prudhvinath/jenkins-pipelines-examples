# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"

  # Default Jenkins port is forwarded to the Host
  config.vm.network "forwarded_port", guest: 8080, host: 8080

  # Using a static IP
  config.vm.network "private_network", ip: "192.168.0.20"

  # Mounted directories
  config.vm.synced_folder ".", "/opt/provisioning"

  # Resources configuration
  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = "4096"
    vb.cpus = 2
    vb.name = "jenkins-vagrant"
  end

  # Install needed tools
  config.vm.provision :docker
  config.vm.provision :docker_compose
  # Allow jenkins user to run docker
  config.vm.provision "shell", inline: <<-SHELL
    usermod -a -G staff,docker vagrant
  SHELL

end