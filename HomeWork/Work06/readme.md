#  BGP. Основы

#  Цель:
настроить BGP между автономными системами и организовать доступность между офисами Москва и С.-Петербург.

###  Задание:
  1. Настроите eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас.
  2. Настроите eBGP между провайдерами Киторн и Ламас.
  3. Настроите eBGP между Ламас и Триада.
  4. Настроите eBGP между офисом С.-Петербург и провайдером Триада.
  5. Организуете IP доступность между пограничным роутерами офисами Москва и С.-Петербург.

![Схема лабораторной работы](СхемаДЗ.png)

####  1. Настроите eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас.
Настройка на R14
```
router bgp 1001
R14(config)#bgp router-id 192.168.0.3
R14(config)#network 20.20.0.129
R14(config)#network 192.168.0.3
R14(config)#neighbor 20.20.0.129 remote-as 101
```
Настройка на R15
```
R15(config)#router bgp 1001
R15(config-router)#bgp router-id 192.168.0.2
R15(config-router)#network 192.168.0.2
R15(config-router)#network 20.20.0.137
R15(config-router)#Neighbor 20.20.0.137 remote-as 301
```
Настройка на R22
```
R22(config)#router bgp 101
R22(config-router)#bgp router-id 192.168.0.129
R22(config-router)#network 20.20.0.130
R22(config-router)#network 192.168.0.129
R22(config-router)#network 20.20.0.134
R22(config-router)#network 20.20.0.197
R22(config-router)#neighbor 20.20.0.130 remote-as 1001
R22(config-router)#neighbor 20.20.0.130 remote-as 1001
*Nov 18 15:56:19.825: %BGP-5-ADJCHANGE: neighbor 20.20.0.130 Up
R22(config-router)#neighbor 20.20.0.134 remote-as 301
R22(config-router)#neighbor 20.20.0.197 remote-as 520
```
Настройка на R21
```
R21(config)#router bgp 301
R21(config-router)#bgp router-id 192.168.0.137
R21(config-router)#network 20.20.0.133
R21(config-router)#network 20.20.0.138
R21(config-router)#network 20.20.0.209
R21(config-router)#network 192.168.0.137
R21(config-router)#neighbor 20.20.0.133 remote-as 101
R21(config-router)#
*Nov 18 16:02:25.089: %BGP-5-ADJCHANGE: neighbor 20.20.0.133 Up
R21(config-router)#neighbor 20.20.0.138 remote-as 1001
R21(config-router)#
*Nov 18 16:02:55.973: %BGP-5-ADJCHANGE: neighbor 20.20.0.138 Up
R21(config-router)#neighbor 20.20.0.209 remote-as 520
```
