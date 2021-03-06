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

#### Просмотр текущего времени (Крайне полезно при работе с логами)
```
show calendar
```
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
#### Запланировать перезагрузку
##### Перезагрузить коммутатор В конкретное время (23:59 в примере)
```
config
reload at 23 59
```
##### Перезагрузить коммутатор через определенное время (В примере через 10 часов)
```
config
reload in hour(minute) 10
```
#### Отменить запланированую перезагрузку
```
config
reload cancel
```
#### Сохранить Конфигурацию
```
copy running-config startup-config
```

### Интерфейсы (1/N - номер порта)
#### Положить порт
```
interface ethernet 1/N
shutdown
```
#### Поднять порт
```
interface ethernet 1/N
no shutdown
```
#### Добавить описание порта
```
config
interface ethernet 1/N
description 'ОПИСАНИЕ ТУТ'
```
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

#### настроить port monitor (N - порт на который будет зеркалироваться трафик, Z - порт абонента трафик которого будем зеркалировать)
```
interface ethernet 1/N port monitor
ethernet 1/Z both
```

#### Тест кабеля (ES3528M)
```
test cable-diagnostics tdr int et 1/N

show cable-diagnostics tdr int et 1/N
```

#### Тест кабеля (ESC3510-28T и новее)
```
test cable-diagnostics int et 1/N

show cable-diagnostics int et 1/N
```


 Возможные результаты:
- ok(0) - кабель целый
- short(*) - короткое замыкание
- open(*) - возможн обрыв или повреждении изоляции
- no cable(*) - обрыв
   где * - приблизительное расстояние до места повреждения.



### MAC-адреса
#### Показать mac-адреса
```
show mac-address-table
```
#### Показать mac-адреса на порту
```
show mac-address-table interface ethernet 1/N
```
##### Аналогично можно просмотреть во влане
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
### MULTICAST
#### Включить Мультикаст на порту
```
interface ethernet 1/N
ip igmp max-groups 10
ip igmp max-groups action replace
mvr type receiver
```
#### Выключить Мультикаст на порту
```
interface ethernet 1/N
no ip igmp max-groups
ip igmp max-groups action deny
no mvr type
```
#### Просмотр Мультикаст потоков на портах
```
show mac-address-table multicast
```
