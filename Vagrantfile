# -*- mode: ruby -*-
# vi: set ft=ruby :
# Using yaml to load external configuration files
require 'yaml'
Vagrant.configure("2") do |config|
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  commands = YAML.load_file('commands.yaml')
  commands.each do |command|
    config.vm.provision "file", source: "./id_rsa.pub", destination: "~/.ssh/id_rsa.pub"
    config.vm.provision "file", source: "./id_rsa", destination: "~/.ssh/id_rsa" # copy private key
    config.vm.provision :shell, inline: command
  end
  servers = YAML.load_file('servers.yaml')
  servers.each do |servers| 
    config.vm.define servers["name"] do |srv|
      srv.vm.box = servers["box"] 
      srv.vm.hostname = servers["name"]
      srv.vm.network "private_network", ip: servers["ip"], :adapater=>2
      srv.vm.network :forwarded_port, guest: 22, host: servers["port"]
      srv.vm.provision :shell, inline: "sed -i'' '/^127.0.0.1\\t#{srv.vm.hostname}\\t#{srv.vm.hostname}$/d' /etc/hosts"
      srv.vm.provider :virtualbox do |vb|
        vb.name = servers["name"]
        vb.cpus = servers["cpus"]
        vb.memory = servers["ram"]
        vb.customize ["modifyvm", :id, "--cpuexecutioncap", "33"]
      end
    end
  end
end