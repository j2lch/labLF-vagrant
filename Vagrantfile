# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'getoptlong'

nodes = [
  { :hostname => 'master', :ip => '192.168.56.50', :box => 'hashicorp/bionic64', :ram => 2048, :cpu => 2 },
  { :hostname => 'node1', :ip => '192.168.56.51', :box => 'hashicorp/bionic64', :ram => 1024, :cpu => 2 }
]

opts = GetoptLong.new(
  ['--private-network', GetoptLong::OPTIONAL_ARGUMENT ]
)

privateNetwork = ''

opts.each do |opt, arg|
  case opt
    when '--private-network'
      privateNetwork=arg
  end
end

ssh_pub_key = File.readlines("./labLF-ssh-key.pub").first.strip

Vagrant.configure("2") do |config|
  nodes.each do |node|
    config.vm.define node[:hostname] do |nodeconfig|
      nodeconfig.vm.box = node[:box]
      nodeconfig.vm.hostname = node[:hostname]
      nodeconfig.vm.network  "private_network", ip: node[:ip]

      nodeconfig.vm.provider "virtualbox" do |vb|
        vb.memory = node[:ram]
        vb.cpus = node[:cpu]
      end
    end

    config.vm.provision 'shell', privileged: true, inline: <<-SHELL
      sudo useradd -m -s /bin/bash student
      sudo usermod -aG sudo student
    SHELL


    config.vm.provision 'shell', privileged: true, inline: <<-SHELL
      sudo su - student
      mkdir /home/student/.ssh
      touch /home/student/.ssh/authorized_keys
      echo #{ssh_pub_key} >> /home/student/.ssh/authorized_keys
    SHELL
  
  end
end 

