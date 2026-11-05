#  EIGRP

#  Цель:
настроить EIGRP named-mode для IPv4 и IPv6 в офисе С.-Петербург.

###  Задание:
  1. В офисе С.-Петербург настроить EIGRP.
  2. R32 получает только маршрут по умолчанию.
  3. R16-17 анонсируют только суммарные префиксы.
  4. Использовать EIGRP named-mode для настройки сети.
  5. Настройка осуществляется одновременно для IPv4 и IPv6.
  
## Схема лабораторной работы:
![Схема лабораторной работы](СхемаДЗ.png)

####  R16
```
R16(config)#router eigrp 1
R16(config-router)#router-id 16.16.16.16
R16(config-router)#network 20.20.0.40 0.0.0.3
R16(config-router)#network 20.20.0.36 0.0.0.3
R16(config-router)#network 192.168.3.0 0.0.0.255
R16(config-router)#network 192.168.4.0 0.0.0.255
R16(config-router)#network 192.168.0.32 0.0.0.31
R16(config-router)#network 192.168.100.0 0.0.0.255
R16(config-router)#auto-summary
R16(config-router)#passive-interface default
R16(config-router)#no passive-interface Loopback0
R16(config-router)#no passive-interface Ethernet0/0.30
R16(config-router)#no passive-interface Ethernet0/0.40
R16(config-router)#no passive-interface Ethernet0/2.100
R16(config-router)#no passive-interface Ethernet0/1
R16(config-router)#no passive-interface Ethernet0/3
```

####  R17
```
R17(config)#router eigrp 1
R17(config-router)##router-id 17.17.17.17
R17(config-router)#network 20.20.0.32 0.0.0.3
R17(config-router)#network 192.168.3.0 0.0.0.255
R17(config-router)#network 192.168.4.0 0.0.0.255
R17(config-router)#network 192.168.0.0 0.0.0.31
R17(config-router)#network 192.168.100.0 0.0.0.255
R17(config-router)#auto-summary
R17(config-router)#passive-interface default
R17(config-router)#no passive-interface Loopback0
R17(config-router)#no passive-interface Ethernet0/0.30
R17(config-router)#no passive-interface Ethernet0/0.40
R17(config-router)#no passive-interface Ethernet0/2.100
R17(config-router)#no passive-interface Ethernet0/1
```

####  R18
```
R18(config)#router eigrp 1
R18(config-router)#router-id 18.18.18.18
R18(config-router)#network 20.20.0.36 0.0.0.3
R18(config-router)#network 20.20.0.32 0.0.0.3
R18(config-router)#network 20.20.0.212 0.0.0.3
R18(config-router)#network 20.20.0.236 0.0.0.3
R18(config-router)#network 192.168.0.0 0.0.0.31
R18(config-router)#no auto-summary
```

####  R32
```
R32(config)#router eigrp 1
R32(config-router)#router-id 32.32.32.32
R32(config-router)#network 0.0.0.0 0.0.0.0
R32(config-router)#no auto-summary
R32(config-router)#passive-interface default
R32(config-router)#no passive-interface Loopback0
R32(config-router)#no passive-interface Ethernet0/0
```

