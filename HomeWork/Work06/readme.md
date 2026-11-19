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

####  Настройки eBGP в Москве
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
####  Настройка в Киторне на R22
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
####  Настройка в Ламасе на R21
```
R21(config)#router bgp 301
R21(config-router)#bgp router-id 192.168.0.137
R21(config-router)#network 20.20.0.133
R21(config-router)#network 20.20.0.138
R21(config-router)#network 20.20.0.209
R21(config-router)#network 192.168.0.137
R21(config-router)#neighbor 20.20.0.133 remote-as 101
*Nov 18 16:02:25.089: %BGP-5-ADJCHANGE: neighbor 20.20.0.133 Up
R21(config-router)#neighbor 20.20.0.138 remote-as 1001
*Nov 18 16:02:55.973: %BGP-5-ADJCHANGE: neighbor 20.20.0.138 Up
R21(config-router)#neighbor 20.20.0.209 remote-as 520
```
####  Настройка BGP в Санкт-Петербурге на R18
```
R18(config)#router bgp 2042
R18(config-router)#bgp router-id 192.168.0.30
R18(config-router)#network 20.20.0.213
R18(config-router)#network 20.20.0.237
R18(config-router)#network 192.168.0.39
R18(config-router)#neighbor 20.20.0.213 remote-as 520
R18(config-router)#neighbor 20.20.0.237 remote-as 520
```
####  Настройка BGP в Триаде
Настройка на R23
```
R23(config)#router bgp 520
R23(config-router)#bgp router-id 192.168.0.97
R23(config-router)#network 192.168.0.97
R23(config-router)#network 20.20.0.198
R23(config-router)#neighbor 20.20.0.198 remote-as 101
*Nov 19 08:42:29.064: %BGP-5-ADJCHANGE: neighbor 20.20.0.198 Up
```
Настройка на R24
```
R24(config)#router bgp 520
R24(config-router)#bgp router-id 192.168.0.99
R24(config-router)#network 192.168.0.99
R24(config-router)#network 20.20.0.210
R24(config-router)#network 20.20.0.214
R24(config-router)#neighbor 20.20.0.210 remote-as 301
*Nov 19 08:49:07.898: %BGP-5-ADJCHANGE: neighbor 20.20.0.210 Up
R24(config-router)#neighbor 20.20.0.214 remote-as 2042
*Nov 19 08:49:35.690: %BGP-5-ADJCHANGE: neighbor 20.20.0.214 Up
```
Настройка на R26
```
R26(config)#router bgp 520
R26(config-router)#bgp router-id 192.168.0.100
R26(config-router)#network 192.168.0.100
R26(config-router)#network 20.20.0.238
R26(config-router)#neighbor 20.20.0.238 remote-as 2042
*Nov 19 08:53:48.476: %BGP-5-ADJCHANGE: neighbor 20.20.0.238 Up
```
