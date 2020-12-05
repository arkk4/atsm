# Список команд
## EDGE-CORE

### Управление Коммутатором
#### Информация по коммутатору
```
show system
```

#### Загрузка процессора коммутатора (ES3528M и новее)
```
show process cpu
```
#### Диспетчер задач свича (ESC3510-28T и новее)
```
show process cpu task
```
#### Логирование
-
```
show log ram
```
Логи из ОЗУ (инфа храится до ребута)

-
```
show log flash
```
Логи из ПЗУ (Работают выборочно, полезная инфа бывает редко)

#### Текущая конфигурация коммутатора
```
show running-config
```
#### Текущая конфигурация порта (ES3528M и новее)
```
show running-config interface ethernet 1/N
```
#### Перезагрузить коммутатор
```
reload

y
 ```


### Интерфейсы (1/N - номер порта)
#### Показать статистику по всем интерфейсам
```
show interfaces brief
```
#### Показать статистику по определенному интерфейсу
```
show interface status ethernet 1/N
```
#### Показать статистику счетчиков по определенному интерфейсу
```
show interface counters ethernet 1/N
```
#### Сбросить статистику счетчиков по определенному интерфейсу
```
clear counters ethernet 1/N
```
#### Замер оптического сигнала
###### Работает на свичах ES3528M и новее, на ECS4120-28F замер возможен только с комбо портов
```
show interface transceiver ethernet 1/N
```

Если вывыд содержит просто
```
Ehternet 1/N
```
SFP не поддерживает замер

## MAC-адреса
#### Показать mac-адреса
```
show mac-address-table
```
#### Показать mac-адреса на порту
```
show mac-address-table interface ethernet 1/N
```
###### Аналогично можно просмотреть во влане
```
show mac-address-table vlan NNN  
```

#### Увеличить предел mac-адресов (Y - максимально количество mac-ов)
```
interface ethernet 1/N
port security max-mac-count Y
```
#### Очистить 1 mac-адрес
```
no mac-address-table static XX-XX-XX-XX-XX-XX vlan NNN
```
#### Очистить все mac-адреса с порта
```
interface ethernet 1/N
port security max-mac-count 0
port security max-mac-count 10
```
## Порты
Порт UP (поднять программно порт)
config
interface ethernet X/X
no shutdown
exit
Порт DOWN (Выключить программно порт)
config
interface ethernet 1/X
shutdown
exit
Перебить порт с uplink на абонентский, на 28 портовом коммутаторе (как правило для портов 25,26,27,28)
configure
interface ethernet 1/###_номер_порта
switchport multicast packet-rate 64
switchport broadcast packet-rate 64
switchport mode access
no ip dhcp snooping trust
ip arp inspection trust
ip arp inspection limit rate 1
no switchport acceptable-frame-types
switchport allowed vlan add ###_нужный_vlan
switchport native vlan ###_нужный_vlan
switchport allowed vlan remove (XXX,XXX,XXX,... Вланы которые сейчас есть на порту)
port security max-mac-count 10
no port security action
ip igmp max-groups 10
ip igmp max-groups action replace
mvr type receiver
end
copy running-config startup-config
startup1.cfg
Перебить порт с uplink на абонентский, на 10 и 26 портовом коммутаторе (как правило для портов 9,10 или 26,26)
configure
interface ethernet 1/###_номер_порта
switchport broadcast
no ip dhcp snooping trust
no switchport acceptable-frame-types
switchport allowed vlan add ###_нужный_vlan
switchport native vlan ###_нужный_vlan
switchport allowed vlan remove (XXX,XXX,XXX,... Вланы которые сейчас есть на порту)
port security max-mac-count 10
no port security action
ip igmp max-groups 10
ip igmp max-groups action replace
mvr type receiver
end
copy running-config startup-config
startup1.cfg
Изменить абонентский VLAN на поту
configure
interface ethernet 1/X
switchport allowed vlan add ХХХ - Нужный VLAN
switchport native vlan ХХХ - Нужный VLAN
switchport allowed vlan remove ХХХ - Текущий VLAN
end
copy running-config startup-config
startup1.cfg
Перебить порт под управляемый коммутатор
на 26ти портовых коммутаторах
no port security max-mac-count
no port security action
no ip igmp filter
no ip igmp max-groups
ip igmp max-groups action deny
no switchport broadcast
switchport acceptable-frame-types tagged
switchport mode trunk
switchport allowed vlan add **вланы прописаны на апоинке** tagged
switchport native vlan 300
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
pppoe intermediate-agent trust
mvr type source
на 28ми портовых коммутаторах
no port security max-mac-count
no port security action
no ip igmp filter
no ip igmp max-groups
ip igmp max-groups action deny
no switchport broadcast
switchport allowed vlan add 1 untagged
switchport acceptable-frame-types tagged
switchport mode trunk
switchport allowed vlan add **вланы прописанные на аплинке** tagged
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
pppoe intermediate-agent trust
mvr type source
—-
Диагностика повреждения кабеля на **28 портовых** коммутаторах
test cable-diagnostics tdr int et 1/X  (**тест**)
show cable-diagnostics tdr int et 1/X  (**просмотр результатов**)

 Возможные результаты:
- ok(0) - кабель целый
- short(*) - короткое замыкание
- open(*) - возможн обрыв или повреждении изоляции
- no cable(*) - обрыв
   где * - приблизительное расстояние до места повреждения.

создать mvr vlan
conf
vlan data
vlan **нужный влан name iptv**нужный влан m e s a
ex
interface eth 1/28 - порт ап линка
switch allowed vlan add **нужный влан tag
ex
mvr vlan **нужный влан
настроить port monitor
con in eth 1/1 port monitor ethernet 1/3 both
в 1 порт включаем ноут и удаляем native vlan, порт 3 это абонента

Dlink
Команды для Dlink

Логин: tech
Пароль: tech
show fdb - показывает всю таблицу маков на d'lnk
show fdb port <port> - показывает всю таблицу маков на порту
show fdb mac_address <macaddr> - ищет конкретный мак в таблице
show fdb vlan <vlan_name> - показывает все маки в конкретном влане
show vlan - показывает все вланы и порты на которые они спущены
show ports - показывает линки на всех портах
show ports <port> - показывает линк на конкретном порту
show switch - инфа по коммутатору
show error ports <portlist> - показывает кол-во ошибок на порту

OLT
ИНСТРУКЦИЯ по PON

Logi: admin
Password: admin
Для ввода команд необходимо ввести enable
show mac address-table dynamic - посмотреть всю mac таблицу на OLT
В GTClient в форме «Биллинг Абоненты» В примечаниях указан MAC ONU абонента например (fcfa.f796.3f44) смотрим на каком интерфейсе светится ONU? Если ONU нет, то либо она отключена либо нет оптического сигнала(ONU светится красным)
show mac address-table dynamic | include fcfa.f796.3f44
Получаем следущий вывод:
Vlan	Mac Address   	Type   	Ports
----	-----------   	----   	-----
1   	fcfa.f796.3f44	DYNAMIC	epon0/1:9

Смотрим какие MAC адреса есть на ONU у конкретного абонента
show mac address-table interface epon 0/1:9
Получаем следущий вывод:
Vlan	Mac Address   	Type   	Ports
----	-----------   	----   	-----
1   	fcfa.f796.3f44	DYNAMIC	epon0/1:9
1569	2cab.250b.738b	DYNAMIC	epon0/1:9

show epon active-onu - выводит таблицу всех активных  ОНУ подключённых к коммутатору.
show mac address-table H.H.H** - поиск конкретного мака
show ip dhcp-relay snooping binding all**- позволяет просматривать ip абонта
clear mac address-table dynamic address H.H.H - позволяет удалить конкретный мас адрес epon reboot onu interface ePON 0/1:1 - удаленная перезагрузка ONU
	show epon optical-transceiver-diagnosis interface epon0/3:22** - возвращает уровень сигнала который поступает на ОНУ

чтобы узнать в какой порт подключен абонент нужно ввести show running-config interface epon0/1 - (последняя цифра означает номер epon и может варьироваться от 1 до 4) там будут маки зарегистрированных ONU и можно узнать к какому порту она подключена


Конфигурация коммутаторов
EdgeCore_28 ES3528M
130_taganrogskoj_divizii-106-2-0#sh running-config
building running-config, please wait…
!<stackingDB>00</stackingDB>
!<stackingMac>01_70-72-cf-24-15-ee_03</stackingMac>
!
!
!
sntp server 10.1.222.254 0.0.0.0 0.0.0.0
!
sntp client
sntp poll 10800
clock timezone Kiev hour 3 minute 0 after-utc
!
!
prompt 130_taganrogskoj_divizii-106-2
snmp-server location 130_taganrogskoj_divizii-106-2
!
!
!
!
ip igmp filter
no dot1q-tunnel system-tunnel-control
!
!
!
!
!
!
snmp-server community public ro
snmp-server community private rw
!
!
username admin access-level 15
username admin password 7 21232f297a57a5a743894a0e4a801fc3
username guest access-level 0
username guest password 7 084e0343a0486ff05530df6c705c8bb4
enable password level 15 7 1b3231655cebb7a1f783eddf27d254ca
!
!
!
!
!
!
!
Таблица вланов этого коммутатора
vlan database
 vlan 1 name DefaultVlan media ethernet state active
 vlan 212 name vinet212 media ethernet state active
 vlan 300 name vmng300 media ethernet state active
 vlan 370 name vmng370 media ethernet state active
 vlan 400 name viptv400 media ethernet state active
 vlan 470 name iptv470 media ethernet state active
 vlan 3522 name v3522 media ethernet state active
!
!
!
!
!
!
spanning-tree mst configuration
!

Блок IPTV
mvr
mvr group 239.0.0.1 239.0.0.255
mvr group 239.0.1.1 239.0.1.15
mvr group 239.1.0.1 239.1.0.255
mvr vlan 470
-
ip igmp profile 1
permit
range 239.0.0.1 239.0.0.255
range 239.1.0.1 239.1.0.255
-
ip igmp profile 65535
deny
range 224.0.1.0 239.255.255.255

Блок DHCP
ip dhcp snooping
ip dhcp snooping vlan 212
ip dhcp snooping vlan 3522
ip dhcp snooping information option
ip dhcp snooping information option remote-id ip-address encode hex
!
no lldp
!
!
!
!
ip arp inspection
ip arp inspection vlan 212,3522
ip arp inspection validate src-mac
!
!
!
!
!
interface vlan 1
interface vlan 212
interface vlan 300

IP коммутатора
interface vlan 370 - **менеджмент vlan этой ветки**
ip address 10.1.222.35 255.255.255.0

interface vlan 400
interface vlan 470
interface vlan 3522
!
!
!

Настройки порта Grizli
interface ethernet 1/1
 port security max-mac-count 10
 port security action trap
ip arp inspection limit rate 1
switchport allowed vlan add 3522 untagged
 switchport native vlan 3522
 switchport allowed vlan remove 1
 switchport mode access
 spanning-tree spanning-disabled
pppoe intermediate-agent port-enable

Настройки порта Trinity
interface ethernet 1/2
 port security max-mac-count 10
 port security action trap
 ip arp inspection trust
 ip arp inspection limit rate 1
 switchport allowed vlan add 212 untagged
 switchport native vlan 212
 switchport allowed vlan remove 1
 spanning-tree spanning-disabled
 pppoe intermediate-agent port-enable
 ip igmp filter 1
ip igmp max-groups 10
 ip igmp max-groups action replace
mvr type receiver

Настройки аплинков
interface ethernet 1/25	\\
 no switchport broadcast	\\
 ip dhcp snooping trust	\\
 ip arp inspection trust	\\
 switchport allowed vlan add 1 untagged	\\
 switchport acceptable-frame-types tagged	\\
 switchport allowed vlan add 1,212,300,370,400,470,3522 tagged	\\
 switchport mode trunk	\\
 spanning-tree spanning-disabled	\\
 pppoe intermediate-agent port-enable	\\
 pppoe intermediate-agent trust	\\
 mvr type source	\\
!
interface ethernet 1/26	\\
 no switchport broadcast	\\
 ip dhcp snooping trust	\\
 ip arp inspection trust	\\
 switchport allowed vlan add 1 untagged	\\
 switchport acceptable-frame-types tagged	\\
 switchport allowed vlan add 1,212,300,370,400,470,3522 tagged	\\
 switchport mode trunk	\\
 spanning-tree spanning-disabled	\\
 pppoe intermediate-agent port-enable	\\
 pppoe intermediate-agent trust	\\
 mvr type source	\\
!
interface ethernet 1/27	\\
 no switchport broadcast	\\
 ip dhcp snooping trust	\\
 ip arp inspection trust	\\
 switchport allowed vlan add 1 untagged	\\
 switchport acceptable-frame-types tagged	\\
 switchport allowed vlan add 1,212,300,370,400,470,3522 tagged	\\
 switchport mode trunk	\\
 spanning-tree spanning-disabled	\\
 pppoe intermediate-agent port-enable	\\
 pppoe intermediate-agent trust	\\
 mvr type source	\\
!
interface ethernet 1/28	\\
 no switchport broadcast	\\
 ip dhcp snooping trust	\\
 ip arp inspection trust	\\
 switchport allowed vlan add 1 untagged	\\
 switchport acceptable-frame-types tagged	\\
 switchport allowed vlan add 1,212,300,370,400,470,3522 tagged	\\
 switchport mode trunk	\\
 spanning-tree spanning-disabled	\\
 pppoe intermediate-agent port-enable	\\
 pppoe intermediate-agent trust	\\
 mvr type source	\\

!
!
!
management http-client 10.1.0.1 10.1.0.254
management http-client 10.1.222.253 10.1.222.253
management http-client 10.1.222.254 10.1.222.254
management snmp-client 10.1.0.1 10.1.0.254
management snmp-client 10.1.222.253 10.1.222.253
management snmp-client 10.1.222.254 10.1.222.254
management telnet-client 10.1.0.1 10.1.0.254
management telnet-client 10.1.222.253 10.1.222.253
management telnet-client 10.1.222.254 10.1.222.254
!
!
!
ip default-gateway 10.1.222.254
!
!
!
!
!
no spanning-tree
!
!
!
!
!
ip ssh server
!
!
!
!
!
line console
silent-time 0
!
!
line VTY
!
!
!
end
!

EdgeCore_26 ES3526XA
Kuprina-33-4-0#sh running-config
building running-config, please wait…
!<stackingDB>00</stackingDB>
!<stackingMac>01_00-12-cf-f8-44-60_00</stackingMac>
!
!
!
sntp server 10.1.102.254 0.0.0.0 0.0.0.0
!
sntp client
sntp poll 10800
clock timezone Kiev hour 3 minute 0 after-utc
prompt Kuprina-33-4
snmp-server location Kuprina-33-4
!
!
!
no cluster
!
ip igmp filter
broadcast bit-rate 8 level 8
multicast bit-rate 8 level 8
unicast bit-rate 8 level 8
!
no dot1q-tunnel system-tunnel-control
!
!
snmp-server community public ro
snmp-server community private rw
!
!
username admin access-level 15
username admin password 7 21232f297a57a5a743894a0e4a801fc3
username guest access-level 0
username guest password 7 084e0343a0486ff05530df6c705c8bb4
enable password level 15 7 1b3231655cebb7a1f783eddf27d254ca
!
!
no logging trap
!
!
!
!
!
vlan database
vlan 1 name DefaultVlan media ethernet state active
vlan 102 name vinet102 media ethernet state active
vlan 300 name vmng300 media ethernet state active
vlan 302 name vmng302 media ethernet state active
vlan 350 name vdsp350 media ethernet state active
vlan 400 name viptv400 media ethernet state active
vlan 3002 name cln3002 media ethernet state active
vlan 4093 media ethernet state active
!
!
!
spanning-tree MST configuration
!
!
!
mvr
mvr group 239.0.0.1 220
mvr group 239.1.0.1 35
mvr vlan 400
!
ip igmp profile 1
permit
range 239.0.0.1 239.0.0.255
range 239.1.0.1 239.1.0.255
!
ip igmp profile 65535
range 224.0.1.0 239.255.255.255
!
!
ip dhcp snooping
ip dhcp snooping vlan 102
ip dhcp snooping vlan 3002
ip dhcp snooping information option
ip dhcp snooping information option remote-id ip-address encode hex
!
no lldp
!
!
pppoe intermediate-agent
!
interface VLAN 1
!
interface VLAN 102
!
interface VLAN 300
!
interface VLAN 302
IP address 10.1.102.40 255.255.255.0
!
interface VLAN 350
!
interface VLAN 400
!
interface VLAN 3002
!
interface VLAN 4093
!
!
!
interface ethernet 1/1
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/2
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/3
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/4
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/5
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/6
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/7
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/8
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/9
port security max-mac-count 10
port security action trap
switchport allowed vlan add 3002 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 3002
switchport allowed vlan remove 1
spanning-tree spanning-disabled
!
interface ethernet 1/10
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/11
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/12
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/13
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/14
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/15
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/16
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/17
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/18
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/19
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/20
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/21
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/22
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/23
port security max-mac-count 10
port security action trap
switchport allowed vlan add 102 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 102
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/24
port security max-mac-count 3
port security action trap
switchport allowed vlan add 3002 untagged
switchport allowed vlan add 4093 tagged
switchport native vlan 3002
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
!
interface ethernet 1/25
no switchport broadcast
ip dhcp snooping trust
switchport acceptable-frame-types tagged
switchport mode trunk
switchport allowed vlan add 102,300,302,350,400,3002,4093 tagged
switchport native vlan 300
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
pppoe intermediate-agent trust
mvr type source
!
interface ethernet 1/26
no switchport broadcast
ip dhcp snooping trust
switchport acceptable-frame-types tagged
switchport mode trunk
switchport allowed vlan add 102,300,302,350,400,3002,4093 tagged
switchport native vlan 300
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
pppoe intermediate-agent trust
mvr type source
!
!
!
management http-client 10.1.0.1 10.1.0.254
management http-client 10.1.102.254 10.1.102.254
management snmp-client 10.1.0.1 10.1.0.254
management snmp-client 10.1.102.253 10.1.102.253
management snmp-client 10.1.102.254 10.1.102.254
management telnet-client 10.1.0.1 10.1.0.254
management telnet-client 10.1.102.253 10.1.102.253
management telnet-client 10.1.102.254 10.1.102.254
!
!
ip default-gateway 10.1.102.254
!
no spanning-tree
!
!
!
!
!
ip ssh server
!
!
!
!
line console
silent-time 0
!
!
line VTY
!
!
!
end
!
EdgeCore_10 ES3510
Vladimirskaja-36-2-0#sh running-config
building running-config, please wait…
!<stackingDB>00</stackingDB>
!<stackingMac>01_00-12-cf-a2-a6-d0_01</stackingMac>
!
!
sntp server 0.0.0.0 0.0.0.0 0.0.0.0
!
prompt Vladimirskaja-36-2
!
!
!
no cluster
!
ip igmp filter
clock timezone-predefined GMT-Greenwich-Mean-Time-Dublin,Edinburgh,Lisbon,London
broadcast bit-rate 8 level 8
multicast bit-rate 8 level 8
unicast bit-rate 8 level 8
!
!
!
snmp-server community public ro
snmp-server community private rw
!
!
username admin access-level 15
username admin password 7 21232f297a57a5a743894a0e4a801fc3
username guest access-level 0
username guest password 7 084e0343a0486ff05530df6c705c8bb4
enable password level 15 7 1b3231655cebb7a1f783eddf27d254ca
!
!
no logging trap
!
!
!
!
!
vlan database
vlan 1 name DefaultVlan media ethernet state active
vlan 376 name mgm376 media ethernet state active
vlan 400 name viptv400 media ethernet state active
vlan 476 name viptv476 media ethernet state active
vlan 1374 name vinet1374 media ethernet state active
vlan 3536 name vinet3536 media ethernet state active
!
!
!
spanning-tree MST configuration
!
!
!
mvr
mvr group 239.0.0.1 255
mvr vlan 476
!
ip igmp profile 1
permit
range 239.0.0.1 239.0.0.255
range 239.1.0.1 239.1.0.35
!
ip igmp profile 65535
range 224.0.1.0 239.255.255.255
!
!
ip dhcp snooping
ip dhcp snooping vlan 1374
ip dhcp snooping vlan 3536
ip dhcp snooping information option
ip dhcp snooping information option remote-id ip-address encode hex
!
no lldp
!
!
!
pppoe intermediate-agent
!
interface VLAN 1
!
interface VLAN 376
IP address 10.1.236.136 255.255.255.0
!
interface VLAN 400
!
interface VLAN 476
!
interface VLAN 1374
!
interface VLAN 3536
!
!
!
interface ethernet 1/1
description kolco
shutdown
port security max-mac-count 10
port security action trap
switchport allowed vlan add 3536 untagged
switchport native vlan 3536
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
!
interface ethernet 1/2
port security max-mac-count 10
port security action trap
switchport allowed vlan add 1374 untagged
switchport native vlan 1374
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/3
port security max-mac-count 10
port security action trap
switchport allowed vlan add 1374 untagged
switchport native vlan 1374
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/4
port security max-mac-count 8
port security action trap
switchport allowed vlan add 3536 untagged
switchport native vlan 3536
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/5
port security max-mac-count 10
port security action trap
switchport allowed vlan add 1374 untagged
switchport native vlan 1374
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/6
port security max-mac-count 10
port security action trap
switchport allowed vlan add 1374 untagged
switchport native vlan 1374
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/7
port security max-mac-count 10
port security action trap
switchport allowed vlan add 1374 untagged
switchport native vlan 1374
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/8
port security max-mac-count 10
port security action trap
switchport allowed vlan add 1374 untagged
switchport native vlan 1374
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 5
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/9
port security max-mac-count 9
port security action trap
switchport allowed vlan add 1374 untagged
switchport native vlan 1374
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
ip igmp filter 1
ip igmp max-groups 10
ip igmp max-groups action replace
mvr type receiver
!
interface ethernet 1/10
no switchport broadcast
ip dhcp snooping trust
switchport acceptable-frame-types tagged
switchport mode trunk
switchport allowed vlan add 376,400,476,1374,3536 tagged
switchport native vlan 376
switchport allowed vlan remove 1
spanning-tree spanning-disabled
pppoe intermediate-agent port-enable
pppoe intermediate-agent trust
mvr type source
!
!
!
management http-client 10.1.0.1 10.1.0.254
management http-client 10.1.236.254 10.1.236.254
management snmp-client 10.1.0.1 10.1.0.254
management snmp-client 10.1.236.253 10.1.236.253
management snmp-client 10.1.236.254 10.1.236.254
management telnet-client 10.1.0.1 10.1.0.254
management telnet-client 10.1.236.253 10.1.236.253
management telnet-client 10.1.236.254 10.1.236.254
!
!
!
!
!
ip default-gateway 10.1.236.254
!
!
!
!
no spanning-tree
!
!
!
!
!
ip ssh server
!
!
!
!
line console
silent-time 0
!
!
line VTY
!
!
!
end


Актуальные прошивки коммутаторов
28 портовый Edge-Core ES3528M Operation Code Version: 1.4.12.3
26 портовый Edge-Core ES3526XA Operation Code Version: 1.1.0.40
10 портовый Edge-Core ES3510 Operation Code Version: 1.1.0.40
