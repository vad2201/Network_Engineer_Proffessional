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
