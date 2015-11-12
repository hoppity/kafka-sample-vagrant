# -*- mode: ruby -*-
# vi: set ft=ruby :
require 'yaml'

cpus   = ENV.fetch("num_cpus", 2)
memory = ENV.fetch("mem_size", 2048)
box    = "williamyeh/ubuntu-trusty64-docker"

builds = {
  'kafka' => {
    compose: "machines/kafka/docker-compose.yml",
    ip: "192.168.33.10",
    forwarded_ports: [
      { guest: 2181, host: 2181 },
      { guest: 9092, host: 9092 },
      { guest: 8081, host: 8081 },
      { guest: 8082, host: 8082 },
      { guest: 9000, host: 9000 }
    ]
  },
  'redis' => {
    compose: "machines/redis/docker-compose.yml",
    ip: "192.168.33.20",
    forwarded_ports: [
      { guest: 6379, host: 6379 }
    ]
  }
}

Vagrant.configure(2) do |config|
  config.vm.box = box

  builds.each_pair do |name, opts|
    config.vm.define name.to_sym do |guest|

      guest.vm.provider :virtualbox do |vb|
        vb.cpus   = cpus
        vb.memory = memory
      end

      guest.vm.provider :vmware_fusion do |vf|
        vf.gui = false
        vf.vmx['numvcpus'] = cpus
        vf.vmx['memory'] = memory
      end

      guest.vm.network "private_network", ip: opts[:ip]
      
      forwarded_ports = opts[:forwarded_ports]

      opts[:forwarded_ports].each do |ports|
        guest.vm.network "forwarded_port", guest: ports[:guest], host: ports[:host]
      end

      guest.vm.provision "shell", inline: "sudo apt-get update"
      guest.vm.provision :docker
      guest.vm.provision :docker_compose, yml: "/vagrant/#{opts[:compose]}", run: "always"

    end
  end
end
