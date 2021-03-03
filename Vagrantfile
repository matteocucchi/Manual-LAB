
# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV['VAGRANT_NO_PARALLEL'] = 'yes'

Vagrant.configure(2) do |config|

  GlusterCount = 2

  # Gluster Nodes
  (1..GlusterCount).each do |i|
    config.vm.define "gluster#{i}" do |gluster|
      gluster.vm.box = "centos/7"
      gluster.vm.hostname = "gluster#{i}.example.com"
      gluster.vm.network "private_network", ip: "172.16.16.1#{i}"
      gluster.vm.provider "virtualbox" do |v|
        v.name = "gluster#{i}"
        v.memory = 2048
        v.cpus = 1
      end
    end
  end

  # Client Node
  config.vm.define "client" do |client|
    client.vm.box = "centos/7"
    client.vm.hostname = "client.example.com"
    client.vm.network "private_network", ip: "172.16.16.20"
    client.vm.provider "virtualbox" do |v|
      v.name = "client"
      v.memory = 2048
      v.cpus = 1
    end
  end

  # Load Balancer Node
  config.vm.define "loadbalancer" do |loadbalancer|
    loadbalancer.vm.box = "centos/7"
    loadbalancer.vm.hostname = "loadbalancer.example.com"
    loadbalancer.vm.network "private_network", ip: "172.16.16.100"
    loadbalancer.vm.provider "virtualbox" do |v|
      v.name = "loadbalancer"
      v.memory = 1024
      v.cpus = 1
    end
  end

  MasterCount = 2

  # Kubernetes Master Nodes
  (1..MasterCount).each do |i|
    config.vm.define "kmaster#{i}" do |kmaster|
      kmaster.vm.box = "centos/7"
      kmaster.vm.hostname = "kmaster#{i}.example.com"
      kmaster.vm.network "private_network", ip: "172.16.16.10#{i}"
      kmaster.vm.provider "virtualbox" do |v|
        v.name = "kmaster#{i}"
        v.memory = 2048
        v.cpus = 2
      end
    end
  end

  NodeCount = 2

  # Kubernetes Worker Nodes
  (1..NodeCount).each do |i|
    config.vm.define "kworker#{i}" do |workernode|
      workernode.vm.box = "centos/7"
      workernode.vm.hostname = "kworker#{i}.example.com"
      workernode.vm.network "private_network", ip: "172.16.16.20#{i}"
      workernode.vm.provider "virtualbox" do |v|
        v.name = "kworker#{i}"
        v.memory = 1024
        v.cpus = 1
      end
    end
  end

end