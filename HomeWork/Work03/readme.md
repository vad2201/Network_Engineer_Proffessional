#  OSPF

#  Цель:
настроить OSPF в офисе Москва, разделить сеть на зоны и настроить фильтрацию между зонами.

###  Задание:

  1. Маршрутизаторы R14-R15 находятся в зоне 0 - backbone.
  2. Маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по умолчанию.
  3. Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию.
  4. Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101.
    
####  Определяем на R14, R15 зону area 0

```
R14(config)#router ospf 1
R14(config-router)#router-id 14.14.14.14
R14(config)#interface loopback 0
R14(config-if)#ip ospf 1 area 0
R14(config-if)#interface e0/2
R14(config-if)#ip ospf 1 area 0

R15(config)#router ospf 1
R15(config-router)#router-id 15.15.15.15
R15(config-router)#interface loopback 0
R15(config-if)#ip ospf 1 area 0
R15(config-if)#interface e0/2
R15(config-if)#ip ospf 1 area 0
```

Так как R19  получает только маршруты по умолчанию, значит зона 101 - total stub
```
R19(config)#router ospf 1
R19(config-router)#router-id 19.19.19.19
R19(config-router)#area 101 stub no-summary
R19(config-if)#interface loopback 0
R19(config-if)#ip ospf 1 area 101
R19(config-router)#interface e0/0
R19(config-if)#ip ospf 1 area 101
```

Так как маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101, значит зона 102 - normal.
```
R20(config)#router ospf 1
R20(config-router)#router-id 20.20.20.20
R19(config-router)#interface loopback 0
R19(config-if)#ip ospf 1 area 102
R20(config-if)#interface e0/0
R20(config-if)#ip ospf 1 area 102
```
Так как маршрутизаторы R12-R13 находятся в зоне 10 и дополнительно к маршрутам должны получать маршрут по умолчанию, значит зона 10 - stub.
```
R12(config)#router ospf 1
R12(config-router)#router-id 12.12.12.12
R12(config-router)#area 10 stub
R12(config)#interface loopback 0
R12(config-if)#ip ospf 1 area 10
R12(config)#interface range e0/2-3
R12(config-if-range)#ip ospf 1 area 10
R12(config)#interface Ethernet0/0.10
R12(config-if)#ip ospf 1 area 10
R12(config)#interface Ethernet0/0.20
R12(config-if)#ip ospf 1 area 10
R12(config-if)#interface Ethernet0/1.100
R12(config-if)#ip ospf 1 area 10

R13(config)#router ospf 1
R13(config-router)#router-id 13.13.13.13
R13(config-router)#area 10 stub
R13(config)#interface loopback 0
R13(config-if)#ip ospf 1 area 10
R13(config-if)#interface range e0/2-3
R13(config-if-range)#ip ospf 1 area 10
R12(config)#interface Ethernet0/0.10
R12(config-if)#ip ospf 1 area 10
R12(config)#interface Ethernet0/0.20
R12(config-if)#ip ospf 1 area 10
R12(config-if)#interface Ethernet0/1.100
R12(config-if)#ip ospf 1 area 10
```

Так как интерфейс e0/3 маршрутизатора R14 находится в зоне 101, а интерфейсы e0/0 и e0/1 в зоне 10
```
R14(config-router)#area 101 stub no-summary
R14(config-router)#area 10 stub
R14(config-router)#interface range e0/0-1
R14(config-if-range)#ip ospf 1 area 10
R14(config)#interface e0/3
R14(config-if)#ip ospf 1 area 101
```
Так как интерфейс e0/3 маршрутизатора R15 находится в зоне 102, а интерфейсы e0/0 и e0/1 в зоне 10
```
R15(config)#router ospf 1
R15(config-router)#area 10 stub
R15(config-router)#interface range e0/0-1
R15(config-if-range)#ip ospf 1 area 10
R15(config)#interface e0/3
R15(config-if)#ip ospf 1 area 102
```

####  Настраиваем фильтрацию в зоне 102

Создаем prefix-list.
Так как фильтроваться трафик должен в зоне 102, фильтрацию проводим на маршрутизаторе R15
```
R15(config)#ip prefix-list 101_in seq 10 deny 20.20.0.4/30
R15(config)#ip prefix-list 101_in seq 20 deny 192.168.0.12/32
R15(config)#ip prefix-list 101_in seq 30 permit 0.0.0.0/0 le 32
```

Проверяем таблицу маршрутизации и доступ с R19 на R20 до применения prefix-list
```
R20#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 20.20.0.26 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 20.20.0.26
      20.0.0.0/8 is variably subnetted, 7 subnets, 3 masks
O IA     20.20.0.8/30 [110/30] via 20.20.0.26, 00:00:40, Ethernet0/0
O IA     20.20.0.12/30 [110/20] via 20.20.0.26, 00:00:40, Ethernet0/0
O IA     20.20.0.16/30 [110/30] via 20.20.0.26, 00:00:40, Ethernet0/0
O IA     20.20.0.20/30 [110/20] via 20.20.0.26, 00:00:40, Ethernet0/0
C        20.20.0.24/30 is directly connected, Ethernet0/0
L        20.20.0.25/32 is directly connected, Ethernet0/0
O IA     20.20.0.136/29 [110/20] via 20.20.0.26, 00:00:40, Ethernet0/0
      192.168.0.0/24 is variably subnetted, 5 subnets, 2 masks
C        192.168.0.0/27 is directly connected, Loopback0
O IA     192.168.0.2/32 [110/11] via 20.20.0.26, 00:00:40, Ethernet0/0
O IA     192.168.0.4/32 [110/21] via 20.20.0.26, 00:00:40, Ethernet0/0
O IA     192.168.0.5/32 [110/21] via 20.20.0.26, 00:00:40, Ethernet0/0
L        192.168.0.6/32 is directly connected, Loopback0
O IA  192.168.1.0/24 [110/30] via 20.20.0.26, 00:00:40, Ethernet0/0
O IA  192.168.2.0/24 [110/30] via 20.20.0.26, 00:00:40, Ethernet0/0
      192.168.100.0/24 is variably subnetted, 2 subnets, 2 masks
O IA     192.168.100.0/24 [110/30] via 20.20.0.26, 00:00:40, Ethernet0/0
O IA     192.168.100.0/27 [110/30] via 20.20.0.26, 00:00:40, Ethernet0/0
```
Маршруты из подсети 20.20.0.4 не появляются на маршрутизаторе R20 даже без использования фильтрации.
Путем многочисленных экспериментов с настройками маршрутизаторов были сделаны выводы, что маршрут до 0.4 
не появляется на R20, так  как подсеть 0.4 подключена к R14 напрямую, а между R14 и R15 нет синхонизации зоны 0.
Пришлось добавлять интерфейсы на маршрутизаторы R14 и R15 и связывать их напрямую в зоне 0.




Применяем фильтр на R15
```
R15(config)#router ospf 1
R15(config-router)#area 102 filter-list prefix 101_in in
```
