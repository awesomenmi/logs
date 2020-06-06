# Сбор и анализ логов

## Настройка сервера log 
###### ([typescript](https://github.com/awesomenmi/logs/blob/master/log_script))

1. Разрешим запуск сервера для соединений UDP на порте 514. Изменим файл конфига _/etc/rsyslog.conf_ - расскоментируем строчки _$ModLoad imudp_ и _$UDPServerRun 514_
2. Настроим SElinux `semanage port -m -t syslogd_port_t -p udp 514` 
3. Перезапустим службу `systemctl restart rsyslog`
4. Настроим прослушивание auditd порта 60. Изменим файл конфиг _/etc/audit/auditd.conf_ - раскоментируем строчку _##tcp_listen_port = 60_ и перезапустим службу

## Настройка клиента web

### Настройка rsyslog и nginx 

1. Установим nginx `yum install nginx`
2. Настроим rsyslog на отправку критичных логов на удаленный сервер _192.168.100.11_. Отредактируем _/etc/rsyslog.conf_ файл, добавив строчку _*.crit 192.168.100.11:54_ и перезапустим службу `systemctl restart rsyslog`
3. Настроим nginx на отправку access-логов на удаленный сервер _192.168.100.11_. Отредактируем файл _/etc/nginx/nginx.conf_ заменив строчку _access_log  /var/log/nginx/access.log  main;_ на _access_log syslog:server=192.168.100.11,facility=local7,tag=nginx,severity=info main;_
4. Настроим nginx на хранение критичных логов локально, приписав _crit_ к строчке _error_log /var/log/nginx/error.log;_ в конфиге _/etc/rsyslog.conf_  - _error_log /var/log/nginx/error.log crit;_
5. Настроим nginx на отправку erorr_log на удаленный сервер. Отредактируем файл _/etc/nginx/nginx.conf_ вставис строчку _error_log syslog:server=192.168.100.11,facility=local6,tag=nginx_error;_ после ранее добавленной _error_log /var/log/nginx/error.log crit;_
6. Перезапустим nginx `systemctl restart nginx`

### Настройка auditd и audisp-plugin

###### ([typescript](https://github.com/awesomenmi/logs/blob/master/web_script))

1. Добавим правило аудита для мониторинга изменений конфига nginx. Создадим файл _/etc/audit/rules.d/nginx_conf.rules_ c содержимым `-w /etc/nginx -p wa -k nginx_conf`
2. Установим audisp-plugin `yum install audisp-plugin`
3. Настроим IP-адрес удаленного сервера, добавив его в файл конфига _/etc/audisp/audisp-remote.conf_, параметр remote_server=192.168.100.11
4. Включим удаленную отправку логов отредактировав параметр _active = yes_ в файле _/etc/audisp/plugins.d/au-remote.conf_
5. Отключим локальное хранение auditd логов отредактировав параметр _write_logs = no_ 
6. Перезапуcтим службу `write_logs = no`

