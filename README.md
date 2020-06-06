# Сбор и анализ логов

## Настройка сервера log 
([typescript](https://github.com/awesomenmi/logs/blob/master/log_script)):

1. Разрешим запуск сервера для соединений UDP на порте 514. Изменим файл конфига _/etc/rsyslog.conf_ - расскоментируем строчки _$ModLoad imudp_ и _$UDPServerRun 514_
2. Настроим SElinux `semanage port -m -t syslogd_port_t -p udp 514` 
3. Перезапустим службу `systemctl restart rsyslog`
4. Настроим прослушивание auditd порта 60. Изменим файл конфиг _/etc/audit/auditd.conf_ - раскоментируем строчку _##tcp_listen_port = 60_ и перезапустим службу

## Настройка клиента web


