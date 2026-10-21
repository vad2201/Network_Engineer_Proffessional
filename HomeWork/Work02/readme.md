#  PBR

#  Цель:
настроить политику маршрутизации в офисе Чокурдах и распределить трафик между двумя линками.

###  Задание:

  1. Настроите политику маршрутизации для сетей офиса.
  2. Распределите трафик между двумя линками с провайдером.
  3. Настроите отслеживание линка через технологию IP SLA.(только для IPv4)
  4. Настройте для офиса Лабытнанги маршрут по-умолчанию.

## Схема лабораторной работы:
![Схема лабораторной работы](схема.png)

###  Таблица IP адресации

| Устройство  | Интерфейс | Адрес              | Шлюз                | 
|-----|------|--------------------------|------------------------|
| Триада | |                |           |      
|R25 |e0/1 | 20.20.0.229      |           | 
|    |e0/3 | 20.20.0.225      |           | 
|R26 |e0/1 | 20.20.0.233      |           | 
| Чокурдах | |                |           |      
|R28 |e0/0 | 20.20.0.234      | 20.20.0.233  | 
|    |e0/1 | 20.20.0.226      | 20.20.0.225 | 
| Лабытнанги | |                |           |      
|R27 |e0/0 | 20.20.0.230      | 20.20.0.229  | 

###  Настройка IP SLA
Доступность R26
```
R28(config)#ip sla 1
R28(config-ip-sla)#icmp-echo 20.20.0.233 source-interface e0/0
R28(config-ip-sla-echo)#timeout 5000
R28(config-ip-sla-echo)#frequency 5
R28(config-ip-sla-echo)#ip sla schedule 1 life forever start-time now
```
Создаем трек, который будет отслеживать SLA 1
```
R28(config)#track 1 ip sla 1 reachability
```
Доступность R25
```
R28(config)#ip sla 2
R28(config-ip-sla)#icmp-echo 20.20.0.225 source-interface e0/1
R28(config-ip-sla-echo)#timeout 5000
R28(config-ip-sla-echo)#frequency 5
R28(config-ip-sla-echo)#ip sla schedule 2 life forever start-time now
```
Создаем трек, который будет отслеживать SLA 2
```
R28(config)#track 2 ip sla 2 reachability
```
Создаем маршруты по умолчанию
```
R28(config)#ip route 0.0.0.0 0.0.0.0 20.20.0.233 track 1
R28(config)#ip route 0.0.0.0 0.0.0.0 20.20.0.225 10 track 2
```

Перед отключением  интерфейса, проверяем настройки на R28
```
R28#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 20.20.0.233 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 20.20.0.233
      20.0.0.0/8 is variably subnetted, 4 subnets, 2 masks
C        20.20.0.224/30 is directly connected, Ethernet0/1
L        20.20.0.226/32 is directly connected, Ethernet0/1
C        20.20.0.232/30 is directly connected, Ethernet0/0
L        20.20.0.234/32 is directly connected, Ethernet0/0
      192.168.0.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.0.64/27 is directly connected, Loopback0
L        192.168.0.65/32 is directly connected, Loopback0
      192.168.5.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.5.0/24 is directly connected, Ethernet0/2.50
L        192.168.5.1/32 is directly connected, Ethernet0/2.50
      192.168.6.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.6.0/24 is directly connected, Ethernet0/2.60
L        192.168.6.1/32 is directly connected, Ethernet0/2.60
      192.168.100.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.100.0/24 is directly connected, Ethernet0/2.100
L        192.168.100.65/32 is directly connected, Ethernet0/2.100
R28#show track
Track 1
  IP SLA 1 reachability
  Reachability is Up
    1 change, last change 00:32:13
  Latest operation return code: OK
  Latest RTT (millisecs) 1
  Tracked by:
    Static IP Routing 0
Track 2
  IP SLA 2 reachability
  Reachability is Up
    1 change, last change 00:28:50
  Latest operation return code: OK
  Latest RTT (millisecs) 1
  Tracked by:
```
Отключаем интерфейс на R26 и смотрим состояние маршрутов по умолчанию на R28
```
R25#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R26(config)#interface Ethernet0/1
R26(config-if)#shutdown

R28#show track
*Oct 20 14:23:53.495: %TRACK-6-STATE: 2 ip sla 2 reachability Down -> Up
R28#show track
Track 1
  IP SLA 1 reachability
  Reachability is Down
    2 changes, last change 00:02:52
  Latest operation return code: Timeout
  Tracked by:
    Static IP Routing 0
Track 2
  IP SLA 2 reachability
  Reachability is Up
    3 changes, last change 00:00:02
  Latest operation return code: OK
  Latest RTT (millisecs) 1
  Tracked by:
    Static IP Routing 0
R28#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 20.20.0.225 to network 0.0.0.0

S*    0.0.0.0/0 [10/0] via 20.20.0.225
      20.0.0.0/8 is variably subnetted, 4 subnets, 2 masks
C        20.20.0.224/30 is directly connected, Ethernet0/1
L        20.20.0.226/32 is directly connected, Ethernet0/1
C        20.20.0.232/30 is directly connected, Ethernet0/0
L        20.20.0.234/32 is directly connected, Ethernet0/0
      192.168.0.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.0.64/27 is directly connected, Loopback0
L        192.168.0.65/32 is directly connected, Loopback0
      192.168.5.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.5.0/24 is directly connected, Ethernet0/2.50
L        192.168.5.1/32 is directly connected, Ethernet0/2.50
      192.168.6.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.6.0/24 is directly connected, Ethernet0/2.60
L        192.168.6.1/32 is directly connected, Ethernet0/2.60
      192.168.100.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.100.0/24 is directly connected, Ethernet0/2.100
L        192.168.100.65/32 is directly connected, Ethernet0/2.100
```
Трек 1 (SLA 1), отвечающий за маршрут по умолчанию на R26 отключился, и механизм SLA включил вторичный маршрут на 20.20.0.225

###  PBR
На маршрутизаторе R28 создадим стандартный списки ACL для идентификации трафика VLAN 50 и VLAN 60
```
R28(config)#ip access-list standard VLAN50
R28(config-std-nacl)#permit 192.168.5.0 0.0.0.255
R28(config-std-nacl)#ip access-list standard VLAN60
R28(config-std-nacl)#permit 192.168.6.0 0.0.0.255
```
Cоздаем карту маршрутов с именем R28, соответствующую спискам доступа, и устанавливаем интерфейсы.
```
R28(config-route-map)#route-map R28 permit 10
R28(config-route-map)#match ip address VLAN 50
R28(config-route-map)#set ip next-hop 20.20.0.233
R28(config-route-map)#route-map R28 permit 20
R28(config-route-map)#match ip address VLAN 60
R28(config-route-map)#set ip next-hop 20.20.0.225
```
Применяем карту маршрутов 
```
R28(config-if)#interface Ethernet0/2.50
R28(config-subif)#ip policy route-map R28
R28(config-subif)#interface Ethernet0/2.60
R28(config-subif)#ip policy route-map R28
```
Для того чтобы пинговать интерфейсы R25 и R26 с компьютеров, использующих разные маршруты по умолчанию необходимо настроить маршрутизацию между R25 и R26
```
R25(config)#ip route 20.20.0.232 255.255.255.252 20.20.0.222
R26(config)#ip route 20.20.0.224 255.255.255.252 20.20.0.221
```

Проводим проверку прохождения сигнала

VC30
```
VPCS> trace 20.20.0.225
trace to 20.20.0.225, 8 hops max, press Ctrl+C to stop
 1   192.168.5.1   1.949 ms  1.304 ms  1.125 ms
 2   20.20.0.233   0.992 ms  0.554 ms  0.538 ms
 3   *20.20.0.221   1.334 ms (ICMP type:3, code:3, Destination port unreachable)  *

VPCS> ping 20.20.0.225

84 bytes from 20.20.0.225 icmp_seq=1 ttl=254 time=1.722 ms
84 bytes from 20.20.0.225 icmp_seq=2 ttl=254 time=1.642 ms
84 bytes from 20.20.0.225 icmp_seq=3 ttl=254 time=1.760 ms
84 bytes from 20.20.0.225 icmp_seq=4 ttl=254 time=1.500 ms
84 bytes from 20.20.0.225 icmp_seq=5 ttl=254 time=0.981 ms
```

Весь трафик VC30 идет 

VC31

```
VPCS> trace 20.20.0.233
trace to 20.20.0.233, 8 hops max, press Ctrl+C to stop
 1   192.168.6.1   0.724 ms  0.824 ms  1.960 ms
 2   20.20.0.225   0.644 ms  0.649 ms  0.507 ms
 3   *20.20.0.222   1.490 ms (ICMP type:3, code:3, Destination port unreachable)  *

VPCS> ping 20.20.0.233

84 bytes from 20.20.0.233 icmp_seq=1 ttl=254 time=1.613 ms
84 bytes from 20.20.0.233 icmp_seq=2 ttl=254 time=1.519 ms
84 bytes from 20.20.0.233 icmp_seq=3 ttl=254 time=0.628 ms
84 bytes from 20.20.0.233 icmp_seq=4 ttl=254 time=1.196 ms
84 bytes from 20.20.0.233 icmp_seq=5 ttl=254 time=2.037 ms
```
Весь трафик VC31 идет через R25


###  Настроим маршрут по умолчанию в Лабытнанги
```
R27(config)#ip route 0.0.0.0 0.0.0.0 20.20.0.229
```
