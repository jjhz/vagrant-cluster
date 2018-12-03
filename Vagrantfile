# -*- mode: ruby -*-
# vi: set ft=ruby :
# Using yaml to load external configuration files
require 'yaml'
Vagrant.configure("2") do |config|
  # Using the hostmanager vagrant plugin to update the host files
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  # Loading in the list of commands that should be run when the VM is provisioned.
  commands = YAML.load_file('commands.yaml')
  commands.each do |command|
    config.vm.provision :shell, inline: command
  end
  # Loading in the VM configuration information
  servers = YAML.load_file('servers.yaml')
  servers.each do |servers| 
    config.vm.define servers["name"] do |srv|
      srv.vm.box = servers["box"] # Speciy the name of the Vagrant box file to use
      srv.vm.hostname = servers["name"] # Set the hostname of the VM
      srv.vm.network "private_network", ip: servers["ip"], :adapater=>2 # Add a second adapater with a specified IP
      srv.vm.network :forwarded_port, guest: 22, host: servers["port"] # Add a port forwarding rule
      srv.vm.provision :shell, inline: "sed -i'' '/^127.0.0.1\\t#{srv.vm.hostname}\\t#{srv.vm.hostname}$/d' /etc/hosts"
 
      srv.vm.provider :virtualbox do |vb|
        vb.name = servers["name"] # Name of the VM in VirtualBox
        vb.cpus = servers["cpus"] # How many CPUs to allocate to the VM
        vb.memory = servers["ram"] # How much memory to allocate to the VM
        vb.customize ["modifyvm", :id, "--cpuexecutioncap", "33"]  # Limit to VM to 33% of available CPU
      end
    end
  end
end