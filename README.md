# unifi-usg-snmp: Патч UniFi USG для отдачи тепературы по SNMP


# Установка

1. Копируем скрипты <i>cpuTemp</i> <i>phyTemp</i> на USG
```
scp cpuTemp phyTemp USER@USG:/tmp/
```
2. Заходим на USG по SSH повышаем права
```
ssh USER@$USG
sudo -s
```
3. Перемещаем скрипты /usr/local/bin
```
mv /tmp/cpuTemp /usr/local/bin
mv /tmp/phyTemp /usr/local/bin
chown root:root /usr/local/bin/cpuTemp
chown root:root /usr/local/bin/phyTemp
chmod 755 /usr/local/bin/cpuTemp
chmod 755 /usr/local/bin/phyTemp
```
4. Даем повышенные права пользователю snmp на запуск ubnt-hal для получения температуры
```
echo "snmp     ALL = NOPASSWD: /usr/sbin/ubnt-hal" >> /etc/sudoers
```
5. Настраиваем SNMPD для получения температуры

```
echo "pass .1.3.6.1.4.1.4413.1.1.43.1.8.1.5.1.0 /usr/local/bin/cpuTemp" >> /etc/snmp/snmpd.conf
echo "pass .1.3.6.1.4.1.4413.1.1.43.1.8.1.5.1.1 /usr/local/bin/phyTemp" >> /etc/snmp/snmpd.conf
```

6. Перезапускаем SNMPD
```
/etc/init.d/snmpd restart
```

# Использование

Не забудьте настроить snmp на USG
```
configure
edit service snmp community ***
set network x.x.x.x/x
set network x.x.x.x/x
set authorization ro
exit
commit
save
```
Подсетей можно указывать несколько
Комьюнити лучше указывать отличное от public

Настройте свою систему мониторинга на данные OID
```
OID .1.3.6.1.4.1.4413.1.1.43.1.8.1.5.1.0 CPU температура
OID .1.3.6.1.4.1.4413.1.1.43.1.8.1.5.1.1 PHY температура
```