# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "log" do |log|
    log.vm.box = "centos/7"
    log.vm.box_check_update = false

    log.vm.hostname = "log"
    log.vm.network "private_network", ip: "192.168.100.11"
  end

  config.vm.define "web" do |web|
    web.vm.box = "centos/7"
    web.vm.box_check_update = false

    web.vm.hostname = "web"
    web.vm.network "private_network", ip: "192.168.100.10"
  end
end