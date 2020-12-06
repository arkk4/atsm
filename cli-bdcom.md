# Список команд
## BDCOM

### Управление ОЛТом
#### Информация по ОЛТу
```
show version
```
#### Загрузка процессора ОЛТа
```
show cpu
```
#### Логирование
```
show logging
```
#### Просмотр текущего времени на ОЛТе (Крайне полезно при работе с логами)
```
show sntp
```

###########################################################
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
