# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "log" do |log|
    log.vm.box = "centos/7"
    log.vm.box_check_update = false

    log.vm.hostname = "log"
    log.vm.network "private_network", ip: "192.168.100.11"

    log.vm.provision "shell", inline: <<-'SHELL'
      user=root
      eval "sed -i '/$ModLoad imudp/s/^#\+//' /etc/rsyslog.conf"
      eval "sed -i '/$UDPServerRun 514/s/^#\+//' /etc/rsyslog.conf"
      setenforce 0
      systemctl restart rsyslog
      eval "sed -i '/tcp_listen_port = 60/s/^##\+//' /etc/audit/auditd.conf"
      service auditd restart
      systemctl daemon-reload
    SHELL
  end

  config.vm.define "web" do |web|
    web.vm.box = "centos/7"
    web.vm.box_check_update = false

    web.vm.hostname = "web"
    web.vm.network "private_network", ip: "192.168.100.10"

    web.vm.provision "shell", inline: <<-SHELL
      user=root
      yum install epel-release -y
      yum install nginx -y
      setenforce 0
      echo "*.crit @192.168.100.11:514" >> /etc/rsyslog.conf
      systemctl restart rsyslog
      eval "sed -i 's#\    access_log  /var/log/nginx/access.log  main;#\    access_log  syslog:server=192.168.100.$
      eval "sed -i 's#error_log /var/log/nginx/error.log;#error_log /var/log/nginx/error.log crit;#g' /etc/nginx/ng$
      eval "sed -i '/^error_log/a error_log syslog:server=192.168.100.11,facility=local6,tag=nginx_error;' /etc/ngi$
      systemctl restart nginx
      echo "-w /etc/nginx -p wa -k nginx_conf" >> /etc/audit/rules.d/nginx_conf.rules
      yum -y install audispd-plugins
      eval "sed -i 's/remote_server = /remote_server = 192.168.100.11/g' /etc/audisp/audisp-remote.conf"
      eval "sed -i 's/active = no/active = yes/g' /etc/audisp/plugins.d/au-remote.conf"
      eval "sed -i 's/write_logs = yes/write_logs = no/g' /etc/audit/auditd.conf"
      service auditd restart
     systemctl daemon-reload
    SHELL
  end
end
