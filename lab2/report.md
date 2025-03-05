University: ITMO University

Faculty: PIn

Course: IP-telephony

Year: 2024/2025

Group: K34202

Author: Shalyapina Maria Vasilievna

Lab: Lab2

Date of create: 04.03.2025

Date of finished: 04.03.2025

# Лабораторная работа №2 "Конфигурация voip в среде Сisco packet tracer"

## Описание
Для выполнения данной лабораторной работы собирается схема соединения. Необходимо проверить, правильно ли подключены все узлы устройств. Предварительно удалить все преды- дущие конфигурационные файлы на маршрутизаторах Cisco 2811, на коммутаторе Cisco catalyst 3560.

## Цель работы
Изучить построение сети IP-телефонии с помощью маршрутизатора Cisco 2811, коммутатора Cisco catalyst 3560 и IP телефонов Cisco 7960.

## Ход выполнения работы

### Часть 1

1. В Cisco Packet Tracer собираем схему соединения, согласно заданию

![1](./assets/1.jpg)

2. На маршрутизаторе отключаем DNS lookup и задаем пароли для защиты доступа к консоли локально и удаленно

```
CMERouter(config)#no ip domain-lookup
CMERouter(config)#enable secret cisco
CMERouter(config)#line console 0
CMERouter(config-line)#password cisco
CMERouter(config-line)#login
CMERouter(config-line)#exit
CMERouter(config)#line vty 0 4
CMERouter(config-line)#password cisco
CMERouter(config-line)#login
CMERouter(config-line)#exit
```

3. На роутере присвоим ip-адрес интерфейсу Fa0/0

![2](./assets/2.jpg)

4. Настроим DHCP и передачу голоса аналогично прошлой лабораторной работе, присвоим номера телефонам
```
CMERouter(config)#ip dhcp pool VoIP
CMERouter(dhcp-config)#network 10.0.0.0 255.255.255.0
CMERouter(dhcp-config)#def
CMERouter(dhcp-config)#default-router 10.0.0.1
CMERouter(dhcp-config)#option 150 ip 10.0.0.1
CMERouter(dhcp-config)#exit
CMERouter(config)#telephony-service 
CMERouter(config-telephony)#max-ephones 5
CMERouter(config-telephony)#max-dn 5
CMERouter(config-telephony)#ip source-address 10.0.0.1 port 2000
CMERouter(config-telephony)#auto assign 1 to 5
CMERouter(config-telephony)#exit
```

5. На коммутаторе указываем голосовой vlan
```
Switch(config)#int range Fa0/1-4
Switch(config-if-range)#switchport voice vlan 1
```

6. Подключаем телефоны и проверяем связность

![3](./assets/3.jpg)
   
### Часть 2

1. Собираем схему согласно заданию

![4](./assets/4.jpg)
   
2. На коммутаторе настроим vlan 10 и 20 для передачи данных и голоса соответственно
```
Switch(config)#vlan 10
Switch(config-vlan)#name data
Switch(config-vlan)#exit
Switch(config)#vlan 20
Switch(config-vlan)#name voice
Switch(config-vlan)#exit
Switch(config)#int range fa0/2-4
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport voice vlan 20
Switch(config-if-range)#switchport access vlan 10
Switch(config-if-range)#exit
Switch(config)#int fa0/1
Switch(config-if)#switchport mode trunk
Switch(config-if)#exit
```
3. Дальше настроим роутер. Добавим DHCP пулы:
```
Router(config)#ip dhcp pool VoIP
Router(dhcp-config)#network 10.20.0.0 255.255.255.0
Router(dhcp-config)#default-router 10.20.0.1
Router(dhcp-config)#option 150 ip 10.20.0.1
Router(dhcp-config)#exit
Router(config)#ip dhcp excluded-address 10.20.0.1
Router(config)#ip dhcp excluded-address 10.10.0.1
Router(config)#ip dhcp pool Data
Router(dhcp-config)#network 10.10.0.0 255.255.255.0
Router(dhcp-config)#default-router 10.10.0.1
Router(dhcp-config)#exit
```

Пропишем саб-интерфейсы:
```
Router(config)#int fa0/0.10
Router(config-subif)#encapsulation Dot1Q 10
Router(config-subif)#ip address 10.10.0.1 255.255.255.0
Router(config-subif)#exit
Router(config)#int fa0/0.20
Router(config-subif)#encapsulation Dot1Q 20
Router(config-subif)#ip address 10.20.0.1 255.255.255.0
Router(config-subif)#exit
```

И аналогично предыдущему заданию поднимаем и настраиваем телефонный сервис

4. Теперь нужно подключить телефоны, включить на ПК протокол DHCP и можно проверять

![5](./assets/5.jpg)

![6](./assets/6.jpg)

![7](./assets/7.jpg)


## Выводы
В ходе выполнения лабораторной работы были настроены две схемы подключения с IP-телефонией
