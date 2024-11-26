# Lab-2

## OSPF в UNDELAY сети

(Механошин Алексей и Подлеснов Александр)

---

Схема подключения осталась прежняя, как в LAB-1:

![Топология-и-маршрутизатор](images/ospf-laba.png)

Как и план адрессного пространства.

<details>
Общая сеть для всех ЦОД-ов (для трех): ```10.100.0.0/14```

- (Диапазон хостов 10.100.0.1 - 10.103.255.254 )

1) Сеть 10.100.0.0/16 Оставим в резерве.
2) Для первого ЦОД-а суммарное: ```10.101.0.0/16``` ( 10.101.0.1 - 10.101.255.254)
3) Для второго ЦОД-а суммарное: ```10.102.0.0/16``` (10.102.0.1 - 10.102.255.254)

Таким образом план нумерации будет следуюший

IP = 10.10**D**.**S**xy.**M**zz

Где:

- D = номер ЦОД-а
- S = номер leaf/spine (**1** - leaf, **2**- spine)
- Mzz - значения по порядку

В ```x``` третьего октета кодируем номер Leaf или Spine

- с **1** по **5**
- 0 - для Border Leaf

В ```y``` третьего октета кодируем:

- 1 - Loopback 1 для UNDERLAY
- 2 - Loopback 2 для OVERLAY
- 3 - резерв, напрмер дял vPC keep-alive
- 4 - p2p линк
- 5 - сервисы

Loopack-s:

- ```10.101.111.1/32``` - ЦОД-1, Leaf-1,  Loopack - 1
- ```10.101.112.1/32``` - ЦОД-1, Leaf-1,  Loopack - 2
- ```10.101.121.1/32``` - ЦОД-1, Leaf-2,  Loopack - 1
- ```10.101.122.1/32``` - ЦОД-1, Leaf-2,  Loopack - 2
- ```10.101.131.1/32``` - ЦОД-1, Leaf-3,  Loopack - 1
- ```10.101.132.1/32``` - ЦОД-1, Leaf-3,  Loopack - 2
- ```10.101.141.1/32``` - ЦОД-1, Leaf-4,  Loopack - 1
- ```10.101.142.1/32``` - ЦОД-1, Leaf-4,  Loopack - 2
- ```10.101.211.1/32``` - ЦОД-1, Spine-1, Loopack - 1
- ```10.101.212.1/32``` - ЦОД-1, Spine-1, Loopack - 2
- ```10.101.221.1/32``` - ЦОД-1, Spine-2, Loopack - 1
- ```10.101.222.1/32``` - ЦОД-1, Spine-2, Loopack - 2

Border Leaf Loopacks:

- ```10.101.11.1``` - ЦОД-1, BRD-Leaf-1 Loopack-1
- ```10.101.12.1``` - ЦОД-1, BRD-Leaf-1 Loopack-2
- ```10.101.21.1``` - ЦОД-1, BRD-Leaf-2 Loopack-1
- ```10.101.22.1``` - ЦОД-1, BRD-Leaf-2 Loopack-2

Примеры сетей для vPC:

- ```10.101.113.0/30``` - vPC ЦОД-1, Leaf-1 to Leaf-2 (10.101.113.1 - 10.101.113.2)
- ```10.101.133.0/30``` - vPC ЦОД-1, Leaf-3 to Leaf-4 (10.101.133.1 - 10.101.133.2)

Cети P2P пиров, как и нумерация в октете идёт со стороны Spine:

- ```10.101.214.0/30``` - сеть в ЦОД-1, Spine-1 до Leaf-1 (10.101.214.1  - 10.101.214.2)
- ```10.101.214.4/30``` - сеть в ЦОД-1, Spine-1 до Leaf-2 (10.101.214.5  - 10.101.214.6)
- ```10.101.214.8/30``` - сеть в ЦОД-1, Spine-1 до Leaf-3 (10.101.214.9  - 10.101.214.10)
- ```10.101.214.12/30```- сеть в ЦОД-1, Spine-1 до Leaf-4 (10.101.214.13 - 10.101.214.14)
- ```10.101.224.0/30``` - сеть в ЦОД-1, Spine-2 до Leaf-1 (10.101.224.1  - 10.101.224.2)
- ```10.101.224.4/30``` - сеть в ЦОД-1, Spine-2 до Leaf-2 (10.101.224.5  - 10.101.224.6)
- ```10.101.224.8/30``` - сеть в ЦОД-1, Spine-2 до Leaf-3 (10.101.224.9  - 10.101.224.10)
- ```10.101.224.12/30```- сеть в ЦОД-1, Spine-2 до Leaf-4 (10.101.224.13 - 10.101.224.14)

Или в обратную сторону:

- ```10.101.214.0/30``` - сеть в ЦОД-1, Leaf-1 до Spine-1
- ```10.101.224.0/30``` - сеть в ЦОД-1, Leaf-1 до Spine-2
- ```10.101.214.4/30``` - сеть в ЦОД-1, Leaf-2 до Spine-1
- ```10.101.224.4/30``` - сеть в ЦОД-1, Leaf-2 до Spine-2
- ```10.101.214.8/30``` - сеть в ЦОД-1, Leaf-3 до Spine-1
- ```10.101.224.8/30``` - сеть в ЦОД-1, Leaf-3 до Spine-2
- ```10.101.214.12/30```- сеть в ЦОД-1, Leaf-4 до Spine-1
- ```10.101.224.12/30```- сеть в ЦОД-1, Leaf-4 до Spine-2

---
Сети BRD Leaf-Spine:

- ```10.101.14.0/30```  - сеть в ЦОД-1, Spine-1 до BRD-Leaf-1 (10.101.14.1 - 10.101.14.2)
- ```10.101.14.4/30```  - сеть в ЦОД-1, Spine-1 до BRD-Leaf-2 (10.101.14.5 - 10.101.14.6)
- ```10.101.24.0/30```  - сеть в ЦОД-1, Spine-2 до BRD-Leaf-1 (10.101.24.1 - 10.101.24.2)
- ```10.101.24.4/30```  - сеть в ЦОД-1, Spine-2 до BRD-Leaf-2 (10.101.24.5 - 10.101.24.6)

IP установлены следующим образом

Leaf-R1# sh ip int br

```text
Interface            IP Address
Lo1                  10.101.111.1
Lo2                  10.101.112.1
Eth1/1               10.101.214.2
Eth1/2               10.101.224.2
Eth1/15              10.101.113.1
```

Leaf-R2# sh ip int br

```text
Interface            IP Address
Lo1                  10.101.121.1
Lo2                  10.101.122.1
Eth1/1               10.101.214.6
Eth1/2               10.101.224.6
Eth1/15              10.101.113.2
```

Leaf-R3# sh ip int br

```text
Interface            IP Address
Lo1                  10.101.131.1
Lo2                  10.101.132.1
Eth1/1               10.101.214.10
Eth1/2               10.101.224.10
Eth1/15              10.101.133.1
```

Leaf-R4# sh ip int br

```text
Interface            IP Address
Lo1                  10.101.141.1
Lo2                  10.101.142.1
Eth1/1               10.101.214.14
Eth1/2               10.101.224.14
Eth1/15              10.101.133.2
```

Spine-R1# sh ip int br

```text
Interface            IP Address
Lo1                  10.101.211.1
Lo2                  10.101.212.1
Eth1/1               10.101.214.1
Eth1/2               10.101.214.5
Eth1/3               10.101.214.9
Eth1/4               10.101.214.13
Eth1/6               10.101.14.1
Eth1/7               10.101.14.5
```

Spine-R2# sh ip int br

```text
Interface            IP Address
Lo1                  10.101.221.1
Lo2                  10.101.222.1
Eth1/1               10.101.224.1
Eth1/2               10.101.224.5
Eth1/3               10.101.224.9
Eth1/4               10.101.224.13
Eth1/6               10.101.24.1
Eth1/7               10.101.24.5
```

BRF-Leaf-R1# sh ip int br

```text
Interface            IP Address
Lo1                  10.101.11.1
Lo2                  10.101.12.1
Eth1/1               10.101.14.2
Eth1/2               10.101.24.2
```

BRD-Leaf-R2# sh ip int br

```text
Interface            IP Address
Lo1                  10.101.21.1
Lo2                  10.101.22.1
Eth1/1               10.101.14.6
Eth1/2               10.101.24.6
```

<summary>
План адрессного пространства
<summary>
</details>


Прежде чем настраивать OSPF его необходимо его включить на всех устройствах в ЦОДе, - на примере Leaf-1, ибо настройки одинаковы дляя всех:

```
Leaf-R1# conf t
Enter configuration commands, one per line. End with CNTL/Z.
Leaf-R1(config)# feature ospf

```

Далее запускаем сам процес, прописываем на нем router-id совпадающий с IP Loopack1 и сразу прописываем что интерфейсы, кроме лупбэков, будут по умолчанию в пассивном режиме:

```
Leaf-R1(config)# router ospf UNDERLAY
Leaf-R1(config-router)# router-id 10.101.111.1
Leaf-R1(config-router)# passive-interface default
Leaf-R1(config-router)# exit
Leaf-R1(config)#
```

Сразу включим его на Loopack1:

```
Leaf-R1(config)# interface loopback1
Leaf-R1(config-if)# ip router ospf UNDERLAY area 0.0.0.0
Leaf-R1(config-if)# exit
Leaf-R1(config)# 
```

Далее на всех необходимых интерфейсах включаем ospf, выключив их из пассивности и указав область (area) и тип точка-точка:

```
Leaf-R1(config)# interface Ethernet1/1
Leaf-R1(config-if)# ip ospf network point-to-point
Leaf-R1(config-if)# ip router ospf UNDERLAY area 0.0.0.0
Leaf-R1(config-if)# no ip ospf passive-interface
Leaf-R1(config-if)# exit
Leaf-R1(config)# 
```

Настраиваем маршрутизатор, например мы хотим получать дефолтный маршрут с него

```
ToWAN#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
ToWAN(config)#interface Loopback1
ToWAN(config-if)#description Router-ID
ToWAN(config-if)#ip address 172.24.200.1 255.255.255.255
ToWAN(config-if)#exit
ToWAN(config)#
```

Запускаем OSPF, дефолт - интерфейсы в пассивном режиме.

```
ToWAN(config)#router ospf 10
ToWAN(config-router)#router-id 172.24.200.1
ToWAN(config-router)#passive-interface default
ToWAN(config-router)#no passive-interface Ethernet0/0
ToWAN(config-router)#no passive-interface Ethernet0/1
ToWAN(config-router)#exit
ToWAN(config)#
```

И настраиваем интерфейсы до Border Leafs

```
ToWAN(config)#interface Ethernet0/0
ToWAN(config-if)#ip address 172.24.1.2 255.255.255.252
ToWAN(config-if)#ip ospf network point-to-point
ToWAN(config-if)#ip ospf 10 area 0
ToWAN(config-if)#exit
ToWAN(config)#interface Ethernet0/1
ToWAN(config-if)#ip address 172.24.2.2 255.255.255.252
ToWAN(config-if)#ip ospf network point-to-point
ToWAN(config-if)#ip ospf 10 area 0
ToWAN(config-if)#exit
```

Добавим Loopack1 в ospf

```
ToWAN(config)#interface Loopback1
ToWAN(config-if)#ip ospf 10 area 0
ToWAN(config-if)#end
```

Проверяем соседство

```
ToWAN#sh ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
10.101.21.1       0   FULL/  -        00:00:36    172.24.2.1      Ethernet0/1
10.101.11.1       0   FULL/  -        00:00:36    172.24.1.1      Ethernet0/0
ToWAN#
```

Для разнообразия добавим на маршрутизатор дефолтный маршрут и расспостраним его

```
ToWAN#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
ToWAN(config)#ip route 0.0.0.0 0.0.0.0 Null0
ToWAN(config)#router ospf 10
ToWAN(config-router)#default-information originate
ToWAN(config-router)#exit
ToWAN(config)#exit
ToWAN#
```

Проверим соседство на Spine-1

```
Spine-R1# sh ip ospf neighbors 
 OSPF Process ID UNDERLAY VRF default
 Total number of neighbors: 6
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.101.111.1      1 FULL/ -          3d08h    10.101.214.2    Eth1/1 
 10.101.121.1      1 FULL/ -          3d08h    10.101.214.6    Eth1/2 
 10.101.131.1      1 FULL/ -          3d08h    10.101.214.10   Eth1/3 
 10.101.141.1      1 FULL/ -          3d08h    10.101.214.14   Eth1/4 
 10.101.11.1       1 FULL/ -          3d05h    10.101.14.2     Eth1/6 
 10.101.21.1       1 FULL/ -          3d05h    10.101.14.6     Eth1/7 
Spine-R1# 
```

Проверим соседство на Spine-1

```
Spine-R2# sh ip ospf neighbors
 OSPF Process ID UNDERLAY VRF default
 Total number of neighbors: 6
 Neighbor ID     Pri State            Up Time  Address         Interface
 10.101.111.1      1 FULL/ -          3d07h    10.101.224.2    Eth1/1 
 10.101.121.1      1 FULL/ -          3d07h    10.101.224.6    Eth1/2 
 10.101.131.1      1 FULL/ -          3d07h    10.101.224.10   Eth1/3 
 10.101.141.1      1 FULL/ -          3d07h    10.101.224.14   Eth1/4 
 10.101.11.1       1 FULL/ -          3d05h    10.101.24.2     Eth1/6 
 10.101.21.1       1 FULL/ -          3d05h    10.101.24.6     Eth1/7 
Spine-R2# 
```

Проверим базу ospf на Leaf-1

```
Leaf-R1# sh ip ospf database 
        OSPF Router with ID (10.101.111.1) (Process ID UNDERLAY VRF default)

                Router Link States (Area 0.0.0.0)

Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.101.11.1     10.101.11.1     42         0x800000e4 0x53aa   7   
10.101.21.1     10.101.21.1     35         0x800000ce 0x8360   7   
10.101.111.1    10.101.111.1    586        0x800000c5 0xed65   5   
10.101.121.1    10.101.121.1    593        0x800000c4 0x9b8a   5   
10.101.131.1    10.101.131.1    594        0x800000c2 0x09f0   5   
10.101.141.1    10.101.141.1    594        0x800000c3 0x705a   5   
10.101.211.1    10.101.211.1    1035       0x800000ad 0x949c   13  
10.101.221.1    10.101.221.1    1033       0x800000d6 0xc9a7   13  
172.24.200.1    172.24.200.1    36         0x800000a1 0x3730   5   

                Type-5 AS External Link States 

Link ID         ADV Router      Age        Seq#       Checksum Tag
0.0.0.0         172.24.200.1    43         0x80000004 0x8196    10

Leaf-R1# 

```

И маршруты которые нам добавил OSPF

```
Leaf-R1# sh ip route ospf-UNDERLAY 
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

0.0.0.0/0, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [110/1], 00:01:27, ospf-UNDERLAY, type-2, tag 10
    *via 10.101.224.1, Eth1/2, [110/1], 00:01:27, ospf-UNDERLAY, type-2, tag 10
10.101.11.1/32, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [110/81], 3d06h, ospf-UNDERLAY, intra
    *via 10.101.224.1, Eth1/2, [110/81], 3d06h, ospf-UNDERLAY, intra
10.101.14.0/30, ubest/mbest: 1/0
    *via 10.101.214.1, Eth1/1, [110/80], 3d08h, ospf-UNDERLAY, intra
10.101.14.4/30, ubest/mbest: 1/0
    *via 10.101.214.1, Eth1/1, [110/80], 3d08h, ospf-UNDERLAY, intra
10.101.21.1/32, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [110/81], 3d05h, ospf-UNDERLAY, intra
    *via 10.101.224.1, Eth1/2, [110/81], 3d05h, ospf-UNDERLAY, intra
10.101.24.0/30, ubest/mbest: 1/0
    *via 10.101.224.1, Eth1/2, [110/80], 3d07h, ospf-UNDERLAY, intra
10.101.24.4/30, ubest/mbest: 1/0
    *via 10.101.224.1, Eth1/2, [110/80], 3d07h, ospf-UNDERLAY, intra
10.101.121.1/32, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [110/81], 3d08h, ospf-UNDERLAY, intra
    *via 10.101.224.1, Eth1/2, [110/81], 3d07h, ospf-UNDERLAY, intra
10.101.131.1/32, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [110/81], 3d08h, ospf-UNDERLAY, intra
    *via 10.101.224.1, Eth1/2, [110/81], 3d07h, ospf-UNDERLAY, intra
10.101.141.1/32, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [110/81], 3d08h, ospf-UNDERLAY, intra
    *via 10.101.224.1, Eth1/2, [110/81], 3d07h, ospf-UNDERLAY, intra
10.101.211.1/32, ubest/mbest: 1/0
    *via 10.101.214.1, Eth1/1, [110/41], 3d08h, ospf-UNDERLAY, intra
10.101.214.4/30, ubest/mbest: 1/0
    *via 10.101.214.1, Eth1/1, [110/80], 3d08h, ospf-UNDERLAY, intra
10.101.214.8/30, ubest/mbest: 1/0
    *via 10.101.214.1, Eth1/1, [110/80], 3d08h, ospf-UNDERLAY, intra
10.101.214.12/30, ubest/mbest: 1/0
    *via 10.101.214.1, Eth1/1, [110/80], 3d08h, ospf-UNDERLAY, intra
10.101.221.1/32, ubest/mbest: 1/0
    *via 10.101.224.1, Eth1/2, [110/41], 3d07h, ospf-UNDERLAY, intra
10.101.224.4/30, ubest/mbest: 1/0
    *via 10.101.224.1, Eth1/2, [110/80], 3d07h, ospf-UNDERLAY, intra
10.101.224.8/30, ubest/mbest: 1/0
    *via 10.101.224.1, Eth1/2, [110/80], 3d07h, ospf-UNDERLAY, intra
10.101.224.12/30, ubest/mbest: 1/0
    *via 10.101.224.1, Eth1/2, [110/80], 3d07h, ospf-UNDERLAY, intra
172.24.1.0/30, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [110/120], 3d06h, ospf-UNDERLAY, intra
    *via 10.101.224.1, Eth1/2, [110/120], 3d06h, ospf-UNDERLAY, intra
172.24.2.0/30, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [110/120], 3d05h, ospf-UNDERLAY, intra
    *via 10.101.224.1, Eth1/2, [110/120], 3d05h, ospf-UNDERLAY, intra
172.24.200.1/32, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [110/121], 00:01:27, ospf-UNDERLAY, intra
    *via 10.101.224.1, Eth1/2, [110/121], 00:01:27, ospf-UNDERLAY, intra

Leaf-R1# 
```

Попробуем пропинговать Loopack на маршрутизаторе от IP локального Loopback

```
Leaf-R1# 
Leaf-R1# ping 172.24.200.1 source 10.101.111.1
PING 172.24.200.1 (172.24.200.1) from 10.101.111.1: 56 data bytes
64 bytes from 172.24.200.1: icmp_seq=0 ttl=252 time=22.898 ms
64 bytes from 172.24.200.1: icmp_seq=1 ttl=252 time=10.377 ms
64 bytes from 172.24.200.1: icmp_seq=2 ttl=252 time=13.001 ms
64 bytes from 172.24.200.1: icmp_seq=3 ttl=252 time=17.403 ms
64 bytes from 172.24.200.1: icmp_seq=4 ttl=252 time=13.901 ms

--- 172.24.200.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 10.377/15.515/22.898 ms
Leaf-R1# 
```

Так-же проверим что остальные Loopack в ЦОД-е у нас пингуются

```
"Leaf-2"
Leaf-R1# ping 10.101.121.1 source 10.101.111.1 count 1 
PING 10.101.121.1 (10.101.121.1) from 10.101.111.1: 56 data bytes
64 bytes from 10.101.121.1: icmp_seq=0 ttl=253 time=12.674 ms

"Leaf-3"
Leaf-R1# ping 10.101.131.1 source 10.101.111.1 count 1
PING 10.101.131.1 (10.101.131.1) from 10.101.111.1: 56 data bytes
64 bytes from 10.101.131.1: icmp_seq=0 ttl=253 time=14.483 ms

"Leaf-4"
Leaf-R1# ping 10.101.141.1 source 10.101.111.1 count 1
PING 10.101.141.1 (10.101.141.1) from 10.101.111.1: 56 data bytes
64 bytes from 10.101.141.1: icmp_seq=0 ttl=253 time=17.684 ms

"Spine-1"
Leaf-R1# ping 10.101.211.1 source 10.101.111.1 count 1
PING 10.101.211.1 (10.101.211.1) from 10.101.111.1: 56 data bytes
64 bytes from 10.101.211.1: icmp_seq=0 ttl=254 time=9.346 ms

"Spine-2"
Leaf-R1# ping 10.101.221.1 source 10.101.111.1 count 1
PING 10.101.221.1 (10.101.221.1) from 10.101.111.1: 56 data bytes
64 bytes from 10.101.221.1: icmp_seq=0 ttl=254 time=11.951 ms

"BRF-Leaf-R1"
Leaf-R1# ping 10.101.11.1 source 10.101.111.1 count 1
PING 10.101.11.1 (10.101.11.1) from 10.101.111.1: 56 data bytes
64 bytes from 10.101.11.1: icmp_seq=0 ttl=253 time=16.77 ms

"BRF-Leaf-R2"
Leaf-R1# ping 10.101.21.1 source 10.101.111.1 count 1
PING 10.101.21.1 (10.101.21.1) from 10.101.111.1: 56 data bytes
64 bytes from 10.101.21.1: icmp_seq=0 ttl=253 time=14.384 ms
Leaf-R1# 
```
Все Loopack-и доступны, свзяность установлена.

#### Настройки маршрутизаторов ####

<details>
<summary>
Leaf-1
<summary>
</details>

<details>
<summary>
Leaf-2
<summary>
</details>

<details>
<summary>
Leaf-3
<summary>
</details>

<details>
<summary>
Leaf-4
<summary>
</details>

<details>
<summary>
Spine--1
<summary>
</details>

<details>
<summary>
Spine-2
<summary>
</details>

<details>
<summary>
BRD-Leaf-1
<summary>
</details>

<details>
<summary>
BRD-Leaf-2
<summary>
</details>


