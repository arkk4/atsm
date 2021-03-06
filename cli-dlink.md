# Список команд
## Dlink

### Управление Коммутатором
#### Информация по коммутатору
```
show switch
```
#### Загрузка процессора коммутатора
```
show utilization cpu
```
#### Загрузка ОЗУ коммутатора
```
show utilization dram
```
#### Логирование
```
show log
```
#### Просмотр текущего времени на свиче (Крайне полезно при работе с логами)
```
show time
```
#### Текущая конфигурация коммутатора
```
show config effective
```
#### Сохранить Конфигурацию
```
save
```
### Интерфейсы
#### Положить порт
```
config ports N state disable
```
#### Поднять порт
```
config ports N state enable
```
#### Добавить описание порта
```
config ports N description 'ОПИСАНИЕ ТУТ'
```
#### Показать статистику по всем интерфейсам
```
show ports description
```
#### Показать загрузку порта
```
show utilization ports N
```
#### Показать статистику по определенному интерфейсу
```
show ports N details
```
#### Показать ошибки на интерфейсе
```
show error ports N
```
#### Очистить ошибки на интерфейсе
```
clear counters ports N
```
#### Замер оптического сигнала
```
show ddm ports N status
```
##### Если вывод по всем столбикам прочерки - SFP не поддерживает Замер

#### Просмотр всех вланов на свиче
```
show vlan
```

### MAC адреса
#### Показать таблицу маков
```
show fdb
```
#### Показать таблицу маков на определенном порту
```
show fdb port N
```
#### Показать таблицу маков в определенном влане (XYZ - имя влана, NNN - числовой тэг)
```
show fdb vlan XYZ
```
```
show fdb vlanid NNN
```
#### Поиск определенного мака
```
show fdb mac_address XXXXXXXXXXXX
```
