# -*- mode: ruby -*-
# vi: set ft=ruby :

home_folder = Dir.home()
ssh_key_pub = File.readlines("./insecure-key.pub").first.strip

Vagrant.configure("2") do |config|
  config.vm.define "rm" do |rm|
    rm.vm.box = "envimation/ubuntu-xenial-docker"
    rm.vm.box_version = "1.0.0-1502068394"
    rm.vm.box_check_update = false
    rm.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 1
    end
    rm.vm.hostname = "router-machine"
    rm.vm.network "public_network", ip: "192.168.137.127", :netmask => "255.255.255.0"#, :bridge => '<physical host interface>'
    rm.vm.synced_folder "#{home_folder}", "/vagrant_data", create: true, owner: "root", group: "root"
    rm.vm.provision "file", source: "conf/", destination: "~/conf"
    rm.vm.provision "shell", inline: <<-SHELL
      echo "Router machine based on ubuntu-xenial-docker vagrant box" > /etc/motd
      echo #{ssh_key_pub} >> ~/.ssh/authorized_keys
      apt-get update -q && apt-get install dnsmasq
      cp ~/conf/hosts-local /etc/hosts-local
      mv /etc/dnsmasq.conf /etc/dnsmasq.conf.bak
      cp ~/conf/dnsmasq.conf /etc/dnsmasq.conf
      service dnsmasq restart
      source ~/conf/update-iptables.sh
    SHELL
  end
end
