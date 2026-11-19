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
R14(config-router)#network 20.20.0.0 mask 255.255.255.224
R14(config-router)#network 20.20.0.4 mask 255.255.255.252
R14(config-router)#network 20.20.0.8 mask 255.255.255.252
R14(config-router)#network 20.20.0.16 mask 255.255.255.252
R14(config-router)#network 20.20.0.240 mask 255.255.255.252
R14(config)#neighbor 20.20.0.129 remote-as 101
```
Настройка на R15
```
R15(config)#router bgp 1001
R15(config-router)#bgp router-id 192.168.0.2
R15(config-router)#network 20.20.0.0 mask 255.255.255.224
R15(config-router)#network 20.20.0.12 mask 255.255.255.252
R15(config-router)#network 20.20.0.20 mask 255.255.255.252
R15(config-router)#network 20.20.0.24 mask 255.255.255.252
R15(config-router)#network 20.20.0.240 mask 255.255.255.252
R15(config-router)#Neighbor 20.20.0.137 remote-as 301
```
####  Настройка в Киторне на R22
```
R22(config)#router bgp 101
R22(config-router)#bgp router-id 192.168.0.129
R22(config-router)#network 20.20.0.128 mask 255.255.255.248
R22(config-router)#network 20.20.0.128 mask 255.255.255.252
R22(config-router)#network 20.20.0.132 mask 255.255.255.252
R22(config-router)#neighbor 20.20.0.130 remote-as 1001
*Nov 18 15:56:19.825: %BGP-5-ADJCHANGE: neighbor 20.20.0.130 Up
R22(config-router)#neighbor 20.20.0.134 remote-as 301
R22(config-router)#neighbor 20.20.0.197 remote-as 520
```
####  Настройка в Ламасе на R21
```
R21(config)#router bgp 301
R21(config-router)#bgp router-id 192.168.0.137
R21(config-router)#network 20.20.0.136 mask 255.255.255.248
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
R18(config-router)#network 20.20.0.32 mask 255.255.255.224
R18(config-router)#network 20.20.0.32 mask 255.255.255.252
R18(config-router)#network 20.20.0.36 mask 255.255.255.252
R18(config-router)#network 20.20.0.40 mask 255.255.255.252
R18(config-router)#neighbor 20.20.0.213 remote-as 520
R18(config-router)#neighbor 20.20.0.237 remote-as 520
```
####  Настройка BGP в Триаде
Настройка на R23
```
R23(config)#router bgp 520
R23(config-router)#bgp router-id 192.168.0.97
R23(config-router)#network 20.20.0.192 mask 255.255.255.192
R23(config-router)#network 20.20.0.196 mask 255.255.255.252
R23(config-router)#network 20.20.0.200 mask 255.255.255.252
R23(config-router)#network 20.20.0.204 mask 255.255.255.252
R23(config-router)#neighbor 20.20.0.198 remote-as 101
*Nov 19 08:42:29.064: %BGP-5-ADJCHANGE: neighbor 20.20.0.198 Up
```
Настройка на R24
```
R24(config)#router bgp 520
R24(config-router)#bgp router-id 192.168.0.99
R24(config-router)#network 20.20.0.192 mask 255.255.255.192
R24(config-router)#network 20.20.0.200 mask 255.255.255.252
R24(config-router)#network 20.20.0.208 mask 255.255.255.252
R24(config-router)#network 20.20.0.212 mask 255.255.255.252
R24(config-router)#network 20.20.0.216 mask 255.255.255.252
R24(config-router)#neighbor 20.20.0.210 remote-as 301
*Nov 19 08:49:07.898: %BGP-5-ADJCHANGE: neighbor 20.20.0.210 Up
R24(config-router)#neighbor 20.20.0.214 remote-as 2042
*Nov 19 08:49:35.690: %BGP-5-ADJCHANGE: neighbor 20.20.0.214 Up
```
Настройка на R26
```
R26(config)#router bgp 520
R26(config-router)#bgp router-id 192.168.0.100
R26(config-router)#network 20.20.0.192 mask 255.255.255.192
R26(config-router)#network 20.20.0.216 mask 255.255.255.252
R26(config-router)#network 20.20.0.220 mask 255.255.255.252
R26(config-router)#network 20.20.0.232 mask 255.255.255.252
R26(config-router)#network 20.20.0.236 mask 255.255.255.252
R26(config-router)#neighbor 20.20.0.238 remote-as 2042
*Nov 19 08:53:48.476: %BGP-5-ADJCHANGE: neighbor 20.20.0.238 Up
```
####  Проверка eBGP в Москве
Настройка на R14
```
R14#show ip bgp
BGP table version is 22, local router ID is 192.168.0.3
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  20.20.0.4/30     0.0.0.0                  0         32768 i
 *>  20.20.0.8/30     0.0.0.0                  0         32768 i
 *>  20.20.0.16/30    0.0.0.0                  0         32768 i
 *>  20.20.0.32/30    20.20.0.129                            0 101 301 520 2042 i
 *>  20.20.0.36/30    20.20.0.129                            0 101 301 520 2042 i
 r>  20.20.0.128/30   20.20.0.129              0             0 101 i
 *>  20.20.0.132/30   20.20.0.129              0             0 101 i
 *>  20.20.0.136/29   20.20.0.129                            0 101 301 i
 *>  20.20.0.196/30   20.20.0.129                            0 101 520 i
 *>  20.20.0.200/30   20.20.0.129                            0 101 520 i
 *>  20.20.0.204/30   20.20.0.129                            0 101 520 i
 *>  20.20.0.208/30   20.20.0.129                            0 101 301 520 i
     Network          Next Hop            Metric LocPrf Weight Path
 *>  20.20.0.212/30   20.20.0.129                            0 101 301 520 i
 *>  20.20.0.216/30   20.20.0.129                            0 101 301 520 i
 *>  20.20.0.240/30   0.0.0.0                  0         32768 i
```
Настройка на R15
```
R15#show ip bgp
BGP table version is 17, local router ID is 192.168.0.2
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  20.20.0.12/30    0.0.0.0                  0         32768 i
 *>  20.20.0.20/30    0.0.0.0                  0         32768 i
 *>  20.20.0.24/30    0.0.0.0                  0         32768 i
 *>  20.20.0.32/30    20.20.0.137                            0 301 520 2042 i
 *>  20.20.0.36/30    20.20.0.137                            0 301 520 2042 i
 *>  20.20.0.128/30   20.20.0.137                            0 301 101 i
 *>  20.20.0.132/30   20.20.0.137                            0 301 101 i
 r>  20.20.0.136/29   20.20.0.137              0             0 301 i
 *>  20.20.0.196/30   20.20.0.137                            0 301 101 520 i
 *>  20.20.0.200/30   20.20.0.137                            0 301 520 i
 *>  20.20.0.204/30   20.20.0.137                            0 301 101 520 i
 *>  20.20.0.208/30   20.20.0.137                            0 301 520 i
 *>  20.20.0.212/30   20.20.0.137                            0 301 520 i
 *>  20.20.0.216/30   20.20.0.137                            0 301 520 i
     Network          Next Hop            Metric LocPrf Weight Path
 *>  20.20.0.240/30   0.0.0.0                  0         32768 i
```
####  Проверка eBGP в Киторне на R22
```
R22#show ip bgp
BGP table version is 25, local router ID is 192.168.0.129
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  20.20.0.4/30     20.20.0.130              0             0 1001 i
 *>  20.20.0.8/30     20.20.0.130              0             0 1001 i
 *>  20.20.0.12/30    20.20.0.134                            0 301 1001 i
 *>  20.20.0.16/30    20.20.0.130              0             0 1001 i
 *>  20.20.0.20/30    20.20.0.134                            0 301 1001 i
 *>  20.20.0.24/30    20.20.0.134                            0 301 1001 i
 *>  20.20.0.32/30    20.20.0.134                            0 301 520 2042 i
 *>  20.20.0.36/30    20.20.0.134                            0 301 520 2042 i
 *>  20.20.0.128/30   0.0.0.0                  0         32768 i
 *>  20.20.0.132/30   0.0.0.0                  0         32768 i
 *>  20.20.0.136/29   20.20.0.134              0             0 301 i
 r>  20.20.0.196/30   20.20.0.197              0             0 520 i
 *   20.20.0.200/30   20.20.0.134                            0 301 520 i
 *>                   20.20.0.197              0             0 520 i
     Network          Next Hop            Metric LocPrf Weight Path
 *>  20.20.0.204/30   20.20.0.197              0             0 520 i
 *>  20.20.0.208/30   20.20.0.134                            0 301 520 i
 *>  20.20.0.212/30   20.20.0.134                            0 301 520 i
 *>  20.20.0.216/30   20.20.0.134                            0 301 520 i
 *   20.20.0.240/30   20.20.0.134                            0 301 1001 i
 *>                   20.20.0.130              0             0 1001 i
```
####  Проверка eBGP в Ламасе на R21
```
R21#show ip bgp
BGP table version is 27, local router ID is 192.168.0.137
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  20.20.0.4/30     20.20.0.133                            0 101 1001 i
 *>  20.20.0.8/30     20.20.0.133                            0 101 1001 i
 *>  20.20.0.12/30    20.20.0.138              0             0 1001 i
 *>  20.20.0.16/30    20.20.0.133                            0 101 1001 i
 *>  20.20.0.20/30    20.20.0.138              0             0 1001 i
 *>  20.20.0.24/30    20.20.0.138              0             0 1001 i
 *>  20.20.0.32/30    20.20.0.209                            0 520 2042 i
 *>  20.20.0.36/30    20.20.0.209                            0 520 2042 i
 *>  20.20.0.128/30   20.20.0.133              0             0 101 i
 r>  20.20.0.132/30   20.20.0.133              0             0 101 i
 *>  20.20.0.136/29   0.0.0.0                  0         32768 i
 *>  20.20.0.196/30   20.20.0.133                            0 101 520 i
 *>  20.20.0.200/30   20.20.0.209              0             0 520 i
 *                    20.20.0.133                            0 101 520 i
     Network          Next Hop            Metric LocPrf Weight Path
 *>  20.20.0.204/30   20.20.0.133                            0 101 520 i
 r>  20.20.0.208/30   20.20.0.209              0             0 520 i
 *>  20.20.0.212/30   20.20.0.209              0             0 520 i
 *>  20.20.0.216/30   20.20.0.209              0             0 520 i
 *>  20.20.0.240/30   20.20.0.138              0             0 1001 i
 *                    20.20.0.133                            0 101 1001 i
```
####  Проверка eBGP в Санкт-Петербурге на R18
```
R18#show ip bgp
BGP table version is 27, local router ID is 192.168.0.30
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  20.20.0.4/30     20.20.0.213                            0 520 301 101 1001 i
 *>  20.20.0.8/30     20.20.0.213                            0 520 301 101 1001 i
 *>  20.20.0.12/30    20.20.0.213                            0 520 301 1001 i
 *>  20.20.0.16/30    20.20.0.213                            0 520 301 101 1001 i
 *>  20.20.0.20/30    20.20.0.213                            0 520 301 1001 i
 *>  20.20.0.24/30    20.20.0.213                            0 520 301 1001 i
 *>  20.20.0.32/30    0.0.0.0                  0         32768 i
 *>  20.20.0.36/30    0.0.0.0                  0         32768 i
 *>  20.20.0.128/30   20.20.0.213                            0 520 301 101 i
 *>  20.20.0.132/30   20.20.0.213                            0 520 301 101 i
 *>  20.20.0.136/29   20.20.0.213                            0 520 301 i
     Network          Next Hop            Metric LocPrf Weight Path
 *>  20.20.0.200/30   20.20.0.213              0             0 520 i
 *>  20.20.0.208/30   20.20.0.213              0             0 520 i
 r>  20.20.0.212/30   20.20.0.213              0             0 520 i
 *   20.20.0.216/30   20.20.0.237              0             0 520 i
 *>                   20.20.0.213              0             0 520 i
 *>  20.20.0.220/30   20.20.0.237              0             0 520 i
 *>  20.20.0.232/30   20.20.0.237              0             0 520 i
 r>  20.20.0.236/30   20.20.0.237              0             0 520 i
 *>  20.20.0.240/30   20.20.0.213                            0 520 301 1001 i
```
####  Проверка eBGP в Триаде
Проверка на R23
```
R23#show ip bgp
BGP table version is 20, local router ID is 192.168.0.97
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  20.20.0.4/30     20.20.0.198                            0 101 1001 i
 *>  20.20.0.8/30     20.20.0.198                            0 101 1001 i
 *>  20.20.0.12/30    20.20.0.198                            0 101 301 1001 i
 *>  20.20.0.16/30    20.20.0.198                            0 101 1001 i
 *>  20.20.0.20/30    20.20.0.198                            0 101 301 1001 i
 *>  20.20.0.24/30    20.20.0.198                            0 101 301 1001 i
 *>  20.20.0.128/30   20.20.0.198              0             0 101 i
 *>  20.20.0.132/30   20.20.0.198              0             0 101 i
 *>  20.20.0.136/29   20.20.0.198                            0 101 301 i
 *>  20.20.0.196/30   0.0.0.0                  0         32768 i
 *>  20.20.0.200/30   0.0.0.0                  0         32768 i
 *>  20.20.0.204/30   0.0.0.0                  0         32768 i
 *>  20.20.0.240/30   20.20.0.198                            0 101 1001 i
```
Проверка на R24
```
R24#show ip bgp
BGP table version is 24, local router ID is 192.168.0.99
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  20.20.0.4/30     20.20.0.210                            0 301 101 1001 i
 *>  20.20.0.8/30     20.20.0.210                            0 301 101 1001 i
 *>  20.20.0.12/30    20.20.0.210                            0 301 1001 i
 *>  20.20.0.16/30    20.20.0.210                            0 301 101 1001 i
 *>  20.20.0.20/30    20.20.0.210                            0 301 1001 i
 *>  20.20.0.24/30    20.20.0.210                            0 301 1001 i
 *>  20.20.0.32/30    20.20.0.214              0             0 2042 i
 *>  20.20.0.36/30    20.20.0.214              0             0 2042 i
 *>  20.20.0.128/30   20.20.0.210                            0 301 101 i
 *>  20.20.0.132/30   20.20.0.210                            0 301 101 i
 *>  20.20.0.136/29   20.20.0.210              0             0 301 i
 *>  20.20.0.200/30   0.0.0.0                  0         32768 i
 *>  20.20.0.208/30   0.0.0.0                  0         32768 i
 *>  20.20.0.212/30   0.0.0.0                  0         32768 i
     Network          Next Hop            Metric LocPrf Weight Path
 *>  20.20.0.216/30   0.0.0.0                  0         32768 i
 *>  20.20.0.240/30   20.20.0.210                            0 301 1001 i
```
Проверка на R26
```
R26#show ip bgp
BGP table version is 7, local router ID is 192.168.0.100
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  20.20.0.32/30    20.20.0.238              0             0 2042 i
 *>  20.20.0.36/30    20.20.0.238              0             0 2042 i
 *>  20.20.0.216/30   0.0.0.0                  0         32768 i
 *>  20.20.0.220/30   0.0.0.0                  0         32768 i
 *>  20.20.0.232/30   0.0.0.0                  0         32768 i
 *>  20.20.0.236/30   0.0.0.0                  0         32768 i
```
####  Проверка IP доступности между пограничным роутерами офисами Москва и С.-Петербург.IP связи
```
R14#ping 20.20.0.214
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 20.20.0.214, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
R15#ping 20.20.0.214
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 20.20.0.214, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms

```
