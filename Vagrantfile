# -*- mode: ruby -*-

# vi: set ft=ruby :

 

hosts = [

  { :hostname => "n1-master",  :ip => "192.168.15.10", :cpu => "2", :ram => "4096" },

  { :hostname => "n2-worker1",  :ip => "192.168.15.11", :cpu => "2", :ram => "2048" },

  { :hostname => "n3-worker2",  :ip => "192.168.15.12", :cpu => "2", :ram => "2048" },

  { :hostname => "n4-bastion",  :ip => "192.168.15.13", :cpu => "2", :ram => "2048" },

]

 

Vagrant.configure("2") do |config|

  # always use Vagrants insecure key

  config.ssh.insert_key = false

  # forward ssh agent to easily ssh into the different machines

  config.ssh.forward_agent = true

  check_guest_additions = true

  functional_vboxsf = false

  config.vm.box = "bento/centos-7.9"

  hosts.each do |host|

    config.vm.hostname = host[:hostname]

    config.vm.define host[:hostname] do |machine|

      machine.vm.network "private_network", ip: host[:ip]

      machine.vm.provider "virtualbox" do |v|

        v.name = host[:hostname]

        v.cpus = host[:cpu]

        v.memory = host[:ram]

      end

    end

  end

end