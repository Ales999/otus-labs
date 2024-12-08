# Lab-3

## IS-IS в UNDELAY сети

(Механошин Алексей и Подлеснов Александр)

---

Схема подключения осталась прежняя, как в LAB-2
![Топология-и-маршрутизатор](../lab-2/images/ospf-laba.png)

Как и план адрессного пространства.

<details>
 <summary>План адрессного пространства</summary>

Общая сеть для всех ЦОД-ов (для трех): ```10.100.0.0/14```

- (Диапазон хостов 10.100.0.1 - 10.103.255.254 )

1) Сеть 10.100.0.0/16 Оставим в резерве.
2) Для первого ЦОД-а суммарное: ```10.101.0.0/16``` ( 10.101.0.1 - 10.101.255.254 )
3) Для второго ЦОД-а суммарное: ```10.102.0.0/16``` ( 10.102.0.1 - 10.102.255.254 )

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

</details>

Концепт IS-IS:

1) L1:
 - Есть маршрутная информация только о своей заоне.
 - L1 возможен только в рамках своей зоны.
 - Для выхода из своей зоны необходимо отправить трафик к L2 маршрутизатору.
 - Аналог Stub Area в OSPF.

2) L2
  - Есть информация о нескольких зонах


Есть три типа метрик в IS-IS
 - Narrow - обычно дефолтная, длиной 6 бит (0-63)
 - Wide - "правильная метрика" в 4 байта. Позволяет настраивать в том числе MPLS Trafic engenering
 - Transition - промежуточнный вариант. Нужен для миграции с  Narrow на Wide


Первым делом включаем необходимые фичи, поскольку в дальнейшем будем настраивать BFD то включим и его:

```text
feature isis
feature bfd
```

Дальнейшая настройка на коммутаторах в целом одинакова, за исключением  локального идентификатора 'net' (Network Entity Title)

Запускаем процес 'isis' и сразу переведем все интерфейсы в пассивный режим.

```text
Leaf-R1# conf t
Enter configuration commands, one per line. End with CNTL/Z.
Leaf-R1(config)# router isis UNDERLAY
Leaf-R1(config-router)# log-adjacency-changes
Leaf-R1(config-router)# 
```

Далее первым делом необходимо настроить локальный иидентификатор устройства - Network Entity Title,
котоый имеет формат ```49.xxxx.yyyy.yyyyy.yyyy.00 ```

Разберем что обозначает:
1) AFI (Authority and Format Identifier) (1 byte)- Определяет класс адреса NET, в современных реализациях IS-IS это поле всегда равно **49**.
2) Area ID (0-12 bytes) (xxxx) - Здесь указывается номер зоны, к которой принадлежит маршрутизатор.
3) System ID (6 bytes) (yyyy.yyyyy.yyyy) - Это идентификатор маршрутизатора, у каждого устройства в топологии он должен быть уникальным.
4) SEL (1 byte) (Network Selector) - Определяет подтип NSAP (Network service access point), поле всегда должно быть равно **00**.

Например, такой NET адрес: 49.0001.0a45.16df.8700.00 содержит следующие поля:
- AFI - 49
- Area ID - 0001
- System ID - 0a4516df8700
- SEL - 00


System-ID в нашей лабе будем формировать из IP на Loopack1, для каждого узла, данным методом:
- Если в октеде число меньше 100 то доюавляем ведущие нули, например 24 --> 024, 8 --> 008, если больше то оставляем как есть.
- далее из получившейся строки убираем все точки, и поново их проставляем через каждые 4-ре цифры.
   Пример 001.002.003.004 --> 001002003004 --> 0010.0200.3004 - это и будет наш SystemID
- Таким образом, если у нас AreID = 0001 то получим такой NET: 49.0001.0010.0200.3004.00

Составим таблицу идентификаторов по всем нашим устройствам:

| Устройство  | IP Loopback1  | Добавим нули    | Переформатируем |   NET                     |
| ----------- | ------------- | --------------- | --------------  | ------------------------- |
| Leaf-1      | 10.101.111.1  | 010.101.111.001 | 0101.0111.1001  | 49.0001.0101.0111.1001.00 |
| Leaf-2      | 10.101.121.1  | 010.101.121.001 | 0101.0112.1001  | 49.0001.0101.0112.1001.00 |
| Leaf-3      | 10.101.131.1  | 010.101.131.001 | 0101.0113.1001  | 49.0001.0101.0113.1001.00 |
| Leaf-4      | 10.101.141.1  | 010.101.141.001 | 0101.0114.1001  | 49.0001.0101.0114.1001.00 |
| Spine-1     | 10.101.211.1  | 010.101.211.001 | 0101.0121.1001  | 49.0001.0101.0121.1001.00 |
| Spine-2     | 10.101.221.1  | 010.101.221.001 | 0101.0122.1001  | 49.0001.0101.0122.1001.00 |
| Brd-Leaf-1  | 10.101.11.1   | 010.101.011.001 | 0101.0101.1001  | 49.0001.0101.0101.1001.00 |
| Brd-Leaf-2  | 10.101.21.1   | 010.101.021.001 | 0101.0102.1001  | 49.0001.0101.0102.1001.00 |
| ToWan       | 172.24.200.1  | 172.024.200.001 | 1720.2420.0001  | 49.0002.1720.2420.0001.00 |


Продолжаем настройку, прописываем согласно таблице Network Entity Title:

```Leaf-R1(config-router)#net 49.0001.0101.0111.1001.00```

Оставим дефолтный тип ISIS: L1/L2 и пропишем что все интерфейсы в пассивном режиме для этого типа:

```text
Leaf-R1(config-router)# is-type level-1-2
Leaf-R1(config-router)# passive-interface default level-1-2
```

Так-же для IPv4 укажем router-id и максимальное количество одновременных маршрутов, а поскольку у нас два спайна то установим значение на 2 маршрута.

```text
Leaf-R1(config-router)# 
Leaf-R1(config-router)# address-family ipv4 unicast
Leaf-R1(config-router-af)# maximum-paths 2
Leaf-R1(config-router-af)# router-id loopback1
Leaf-R1(config-router-af)#
Leaf-R1(config-router-af)# exit 
Leaf-R1(config-router)# exit
Leaf-R1(config)#
```

Далее включаем на интерфейсах протокол isis:

```text
Leaf-R1(config)# interface loopback1
Leaf-R1(config-if)# ip router isis UNDERLAY
Leaf-R1(config-if)# exit
Leaf-R1(config)# 
```

Для основных интерфейсов включаем режим точка-точка и указываем что это интерфейс для is-is не пассивный:

```text
Leaf-R1(config)# interface Ethernet1/1
Leaf-R1(config-if)# isis network point-to-point
Leaf-R1(config-if)# ip router isis UNDERLAY
Leaf-R1(config-if)# no isis passive-interface level-1-2
Leaf-R1(config-if)# exit
Leaf-R1(config)# 
Leaf-R1(config)# interface Ethernet1/2
Leaf-R1(config-if)# isis network point-to-point
Leaf-R1(config-if)# ip router isis UNDERLAY
Leaf-R1(config-if)# no isis passive-interface level-1-2
Leaf-R1(config-if)# exit
Leaf-R1(config)# 
```

Повторяем настройки для всех устройств, меняя только 'net' и учавствующие интерфейсы.

Единственно что на роутере 'ToWan' немного по другому и нужна дополнительная настройка типа метрики, поскольку по умолчанию он использует тип 'Narrow', а нам нужна широкая (wide) которую используют нексусы:

```text
ToWAN#conf term
ToWAN(config)#router isis UNDERLAY
ToWAN(config-router)#net 49.0002.1720.2420.0001.00
ToWAN(config-router)#is-type level-1-2
ToWAN(config-router)#log-adjacency-changes
ToWAN(config-router)#metric-style wide
ToWAN(config-router)#router-id Loopback1
ToWAN(config-router)#passive-interface default
ToWAN(config-router)#no passive-interface Ethernet0/0
ToWAN(config-router)#no passive-interface Ethernet0/1
ToWAN(config-router)#exit
ToWAN(config)#
ToWAN(config)#interface Ethernet0/0
ToWAN(config-if)#isis network point-to-point
ToWAN(config-if)#ip router isis UNDERLAY
ToWAN(config-if)#exit
ToWAN(config)#
ToWAN(config)#interface Ethernet0/1
ToWAN(config-if)#isis network point-to-point
ToWAN(config-if)#ip router isis UNDERLAY
ToWAN(config-if)#exit
ToWAN(config)#
```

Проверим что все синхронизировалось и заработало, для начала посмотрим на базу на самом роутере:

```text
ToWAN#sh isis database 

Tag UNDERLAY:
IS-IS Level-1 Link State Database:
LSPID                 LSP Seq Num  LSP Checksum  LSP Holdtime/Rcvd      ATT/P/OL
ToWAN.00-00         * 0x00000003   0x999E                 936/*         1/0/0
IS-IS Level-2 Link State Database:
LSPID                 LSP Seq Num  LSP Checksum  LSP Holdtime/Rcvd      ATT/P/OL
BRF-Leaf-R1.00-00     0x000003D4   0x09D8                 935/1199      0/0/0
BRD-Leaf-R2.00-00     0x000003D1   0x05E3                 937/1199      0/0/0
Leaf-R1.00-00         0x00000006   0xF0E1                1060/1197      0/0/0
Leaf-R2.00-00         0x000003CF   0x4344                 936/1036      0/0/0
Leaf-R3.00-00         0x000003F7   0xAA21                 936/1120      0/0/0
Leaf-R4.00-00         0x000003F0   0xB350                 936/1179      0/0/0
Spine-R1.00-00        0x0000048C   0xEB6E                 936/1102      0/0/0
Spine-R2.00-00        0x0000043E   0xEE9C                 936/1103      0/0/0
ToWAN.00-00         * 0x0000000B   0x68B7                 938/*         0/0/0
ToWAN#
```
Поскольку роутер ToWAN находится в отдельной области, на что указывает начало записи в net: ```49.0002```
то у нас в базе остальные коммутаторы только в L2 уровне и не передают ATT бит. (* указано где мы находимся )

Проверим что у нас добавилось в таблицу маршрутизации из IS-IS на роутере:

```text
ToWAN#sh ip route isis 
[skip]
Gateway of last resort is 0.0.0.0 to network 0.0.0.0

      10.0.0.0/8 is variably subnetted, 20 subnets, 2 masks
i L2     10.101.11.1/32 [115/41] via 172.24.1.1, 00:23:56, Ethernet0/0
i L2     10.101.14.0/30 [115/80] via 172.24.1.1, 00:23:56, Ethernet0/0
i L2     10.101.14.4/30 [115/80] via 172.24.2.1, 00:23:54, Ethernet0/1
i L2     10.101.21.1/32 [115/41] via 172.24.2.1, 00:23:54, Ethernet0/1
i L2     10.101.24.0/30 [115/80] via 172.24.1.1, 00:23:56, Ethernet0/0
i L2     10.101.24.4/30 [115/80] via 172.24.2.1, 00:23:54, Ethernet0/1
i L2     10.101.111.1/32 [115/181] via 172.24.2.1, 00:23:54, Ethernet0/1
                         [115/181] via 172.24.1.1, 00:23:54, Ethernet0/0
i L2     10.101.121.1/32 [115/181] via 172.24.2.1, 00:23:54, Ethernet0/1
                         [115/181] via 172.24.1.1, 00:23:54, Ethernet0/0
i L2     10.101.131.1/32 [115/181] via 172.24.2.1, 00:23:54, Ethernet0/1
                         [115/181] via 172.24.1.1, 00:23:54, Ethernet0/0
i L2     10.101.141.1/32 [115/181] via 172.24.2.1, 00:23:54, Ethernet0/1
                         [115/181] via 172.24.1.1, 00:23:54, Ethernet0/0
i L2     10.101.211.1/32 [115/81] via 172.24.2.1, 00:23:54, Ethernet0/1
                         [115/81] via 172.24.1.1, 00:23:54, Ethernet0/0
i L2     10.101.214.0/30 [115/180] via 172.24.2.1, 00:23:54, Ethernet0/1
                         [115/180] via 172.24.1.1, 00:23:54, Ethernet0/0
i L2     10.101.214.4/30 [115/180] via 172.24.2.1, 00:23:54, Ethernet0/1
                         [115/180] via 172.24.1.1, 00:23:54, Ethernet0/0
i L2     10.101.214.8/30 [115/180] via 172.24.2.1, 00:23:54, Ethernet0/1
                         [115/180] via 172.24.1.1, 00:23:54, Ethernet0/0
i L2     10.101.214.12/30 [115/180] via 172.24.2.1, 00:23:54, Ethernet0/1
                          [115/180] via 172.24.1.1, 00:23:54, Ethernet0/0
i L2     10.101.221.1/32 [115/81] via 172.24.2.1, 00:23:54, Ethernet0/1
                         [115/81] via 172.24.1.1, 00:23:54, Ethernet0/0
i L2     10.101.224.0/30 [115/180] via 172.24.2.1, 00:23:54, Ethernet0/1
                         [115/180] via 172.24.1.1, 00:23:54, Ethernet0/0
i L2     10.101.224.4/30 [115/180] via 172.24.2.1, 00:23:54, Ethernet0/1
                         [115/180] via 172.24.1.1, 00:23:54, Ethernet0/0
i L2     10.101.224.8/30 [115/180] via 172.24.2.1, 00:23:54, Ethernet0/1
                         [115/180] via 172.24.1.1, 00:23:54, Ethernet0/0
i L2     10.101.224.12/30 [115/180] via 172.24.2.1, 00:23:54, Ethernet0/1
                          [115/180] via 172.24.1.1, 00:23:54, Ethernet0/0
ToWAN#
```

Все 8-мь лупбеков остальных устройст присутствуют.

Проверим что у нас с базой на Leaf-1:

```text
Leaf-R1# sh isis database 
IS-IS Process: UNDERLAY LSP database VRF: default
IS-IS Level-1 Link State Database
  LSPID                 Seq Number   Checksum  Lifetime   A/P/O/T
  BRF-Leaf-R1.00-00     0x0000003B   0x1508    1071       1/0/0/3
  BRD-Leaf-R2.00-00     0x0000003B   0xF545    1064       1/0/0/3
  Leaf-R1.00-00       * 0x00000034   0x0D29    1028       1/0/0/3
  Leaf-R2.00-00         0x0000002F   0x4714    1001       1/0/0/3
  Leaf-R3.00-00         0x00000030   0x68B3    1024       1/0/0/3
  Leaf-R4.00-00         0x00000030   0x48CB    1066       1/0/0/3
  Spine-R1.00-00        0x00000043   0x891A    1024       1/0/0/3
  Spine-R2.00-00        0x00000042   0x9B86    1069       1/0/0/3

IS-IS Level-2 Link State Database
  LSPID                 Seq Number   Checksum  Lifetime   A/P/O/T
  BRF-Leaf-R1.00-00     0x000003D5   0xDE26    1085       0/0/0/3
  BRD-Leaf-R2.00-00     0x000003D2   0xA9ED    1041       0/0/0/3
  Leaf-R1.00-00       * 0x00000007   0x33FE    1134       0/0/0/3
  Leaf-R2.00-00         0x000003D0   0x8F18    923        0/0/0/3
  Leaf-R3.00-00         0x000003F8   0xC29B    1008       0/0/0/3
  Leaf-R4.00-00         0x000003F1   0x7F82    1028       0/0/0/3
  Spine-R1.00-00        0x0000048D   0x94E4    959        0/0/0/3
  Spine-R2.00-00        0x0000043F   0xA1EC    990        0/0/0/3
  ToWAN.00-00           0x0000000C   0xAAD1    1191       0/0/0/3

Leaf-R1#
```

Как мы можем наблюдать у нас в L1 у коммутаторов и роутера ToWan выставлен ATT-бит, который нам говорит что за ним есть ещё какие-то префиксы, поэтому если маршрута у вас нет, то шлите мне, а я дальше попробую разобраться самостоятельно.

Проверим какие маршруты из is-is попали в таблицу маршрутизации:

```text
Leaf-R1# sh ip route isis-UNDERLAY 
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

10.101.11.1/32, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [115/141], 00:17:19, isis-UNDERLAY, L1
    *via 10.101.224.1, Eth1/2, [115/141], 00:17:22, isis-UNDERLAY, L1
10.101.14.0/30, ubest/mbest: 1/0
    *via 10.101.214.1, Eth1/1, [115/140], 00:41:44, isis-UNDERLAY, L1
10.101.14.4/30, ubest/mbest: 1/0
    *via 10.101.214.1, Eth1/1, [115/140], 00:41:44, isis-UNDERLAY, L1
10.101.21.1/32, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [115/141], 00:17:15, isis-UNDERLAY, L1
    *via 10.101.224.1, Eth1/2, [115/141], 00:17:15, isis-UNDERLAY, L1
10.101.24.0/30, ubest/mbest: 1/0
    *via 10.101.224.1, Eth1/2, [115/140], 00:41:47, isis-UNDERLAY, L1
10.101.24.4/30, ubest/mbest: 1/0
    *via 10.101.224.1, Eth1/2, [115/140], 00:41:47, isis-UNDERLAY, L1
10.101.121.1/32, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [115/201], 00:41:44, isis-UNDERLAY, L1
    *via 10.101.224.1, Eth1/2, [115/201], 00:41:47, isis-UNDERLAY, L1
10.101.131.1/32, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [115/201], 00:41:44, isis-UNDERLAY, L1
    *via 10.101.224.1, Eth1/2, [115/201], 00:41:47, isis-UNDERLAY, L1
10.101.141.1/32, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [115/201], 00:41:44, isis-UNDERLAY, L1
    *via 10.101.224.1, Eth1/2, [115/201], 00:41:47, isis-UNDERLAY, L1
10.101.211.1/32, ubest/mbest: 1/0
    *via 10.101.214.1, Eth1/1, [115/101], 00:41:44, isis-UNDERLAY, L1
10.101.214.4/30, ubest/mbest: 1/0
    *via 10.101.214.1, Eth1/1, [115/200], 00:41:44, isis-UNDERLAY, L1
10.101.214.8/30, ubest/mbest: 1/0
    *via 10.101.214.1, Eth1/1, [115/200], 00:41:44, isis-UNDERLAY, L1
10.101.214.12/30, ubest/mbest: 1/0
    *via 10.101.214.1, Eth1/1, [115/200], 00:41:44, isis-UNDERLAY, L1
10.101.221.1/32, ubest/mbest: 1/0
    *via 10.101.224.1, Eth1/2, [115/101], 00:41:47, isis-UNDERLAY, L1
10.101.224.4/30, ubest/mbest: 1/0
    *via 10.101.224.1, Eth1/2, [115/200], 00:41:47, isis-UNDERLAY, L1
10.101.224.8/30, ubest/mbest: 1/0
    *via 10.101.224.1, Eth1/2, [115/200], 00:41:47, isis-UNDERLAY, L1
10.101.224.12/30, ubest/mbest: 1/0
    *via 10.101.224.1, Eth1/2, [115/200], 00:41:47, isis-UNDERLAY, L1
172.24.1.0/30, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [115/180], 00:17:19, isis-UNDERLAY, L1
    *via 10.101.224.1, Eth1/2, [115/180], 00:17:22, isis-UNDERLAY, L1
172.24.2.0/30, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [115/180], 00:17:15, isis-UNDERLAY, L1
    *via 10.101.224.1, Eth1/2, [115/180], 00:17:15, isis-UNDERLAY, L1
172.24.200.1/32, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [115/180], 00:15:41, isis-UNDERLAY, L2
    *via 10.101.224.1, Eth1/2, [115/180], 00:15:41, isis-UNDERLAY, L2

Leaf-R1# 
```

Как можно видеть все маршруты на месте, и есть даже маршрут до Loopack-1 на ToWan но уже в L2 уровне.

Проверим связность - что все остальные Loopack от нас пингуются:

```text
BRF-Leaf-R1:
Leaf-R1# ping 10.101.11.1 source-interface lo1 count 1
64 bytes from 10.101.11.1: icmp_seq=0 ttl=253 time=18.678 ms

BRF-Leaf-R2:
Leaf-R1# ping 10.101.21.1 source-interface lo1 count 1
64 bytes from 10.101.21.1: icmp_seq=0 ttl=253 time=17.506 ms

Leaf-R2:
Leaf-R1# ping 10.101.121.1 source-interface lo1 count 1
64 bytes from 10.101.121.1: icmp_seq=0 ttl=253 time=10.877 ms

Leaf-R3:
Leaf-R1# ping 10.101.131.1 source-interface lo1 count 1
64 bytes from 10.101.131.1: icmp_seq=0 ttl=253 time=16.418 ms

Leaf-R4:
Leaf-R1# ping 10.101.141.1 source-interface lo1 count 1
64 bytes from 10.101.141.1: icmp_seq=0 ttl=253 time=15.721 ms

Spine-R1:
Leaf-R1# ping 10.101.211.1 source-interface lo1 count 1
64 bytes from 10.101.211.1: icmp_seq=0 ttl=254 time=7.662 ms

Spine-R2:
Leaf-R1# ping 10.101.221.1 source-interface lo1 count 1
64 bytes from 10.101.221.1: icmp_seq=0 ttl=254 time=8.577 ms

ToWAN:
Leaf-R1# ping 172.24.200.1 source-interface lo1 count 1
64 bytes from 172.24.200.1: icmp_seq=0 ttl=252 time=17.87 ms
Leaf-R1# 
```

Отлично, все пингуется.

Вернемся на ротутер и проверим что все пингуется с него так-же:

```text
BRF-Leaf-R1:
ToWAN#ping 10.101.11.1 source Loopback 1 repeat 1 
Success rate is 100 percent (1/1), round-trip min/avg/max = 3/3/3 ms

BRF-Leaf-R2:
ToWAN#ping 10.101.21.1 source Loopback 1 repeat 1 
Success rate is 100 percent (1/1), round-trip min/avg/max = 4/4/4 ms

Leaf-R1:
ToWAN#ping 10.101.111.1 source Loopback 1 repeat 1 
Success rate is 100 percent (1/1), round-trip min/avg/max = 24/24/24 ms

Leaf-R2:
ToWAN#ping 10.101.121.1 source Loopback 1 repeat 1 
Success rate is 100 percent (1/1), round-trip min/avg/max = 9/9/9 ms

Leaf-R3:
ToWAN#ping 10.101.131.1 source Loopback 1 repeat 1 
Success rate is 100 percent (1/1), round-trip min/avg/max = 12/12/12 ms

Leaf-R4:
ToWAN#ping 10.101.141.1 source Loopback 1 repeat 1 
Success rate is 100 percent (1/1), round-trip min/avg/max = 14/14/14 ms

Spine-R1:
ToWAN#ping 10.101.211.1 source Loopback 1 repeat 1 
Success rate is 100 percent (1/1), round-trip min/avg/max = 8/8/8 ms

Spine-R2:
ToWAN#ping 10.101.221.1 source Loopback 1 repeat 1 
Success rate is 100 percent (1/1), round-trip min/avg/max = 9/9/9 ms
ToWAN#
```

Отлично, все работает.

После того как мы убелились немного усложним конфигурацию - добавим авторизацию на наш протокол IS-IS:

1) Содадим именную связку ключей и сам ключ:

```text
Leaf-R1# conf term
Leaf-R1(config)# key chain ISIS
Leaf-R1(config-keychain)# key 1
Leaf-R1(config-keychain-key)# key-string 0 cisco2laba
Leaf-R1(config-keychain-key)# end
Leaf-R1#
```

2) Данную конфигурацию распостраним на все коммутаторы, но на роутере необходимо указать ещё и алгоритм:

```text
ToWAN#conf term
ToWAN(config)#key chain ISIS
ToWAN(config-keychain)#key 1
ToWAN(config-keychain-key)#key-string 0 cisco2laba
ToWAN(config-keychain-key)#cryptographic-algorithm md5
ToWAN(config-keychain-key)#end
ToWAN#
```

3) Далее настроим слудующим образом:

```text
Leaf-R1# conf term
Enter configuration commands, one per line. End with CNTL/Z.
Leaf-R1(config)# router isis UNDERLAY
Leaf-R1(config-router)# 
Leaf-R1(config-router)# authentication-type md5 level-1
Leaf-R1(config-router)# authentication-type md5 level-2
Leaf-R1(config-router)# authentication key-chain ISIS level-1
Leaf-R1(config-router)# authentication key-chain ISIS level-2
Leaf-R1(config-router)# end
Leaf-R1#
```

Дополнительно настроим между Leaf-1 и Spine-1 BDF:

```text
Leaf-R1# conf t
Leaf-R1(config)# feature bfd
Leaf-R1(config)# interface Ethernet1/1
Leaf-R1(config-if)# no ip redirects
Leaf-R1(config-if)# bfd interval 999 min_rx 999 multiplier 3
Leaf-R1(config-if)# bfd authentication Keyed-SHA1 key-id 1 key cisco2laba
Leaf-R1(config-if)# isis bfd
Leaf-R1(config-if)# exit
Leaf-R1(config)# 
```

```text
Spine-R1# conf term
Spine-R1(config)# int Ethernet1/1
Spine-R1(config-if)# no ip redirects
Spine-R1(config-if)# bfd interval 999 min_rx 999 multiplier 3
Spine-R1(config-if)# bfd authentication Keyed-SHA1 key-id 1 key cisco2laba
Spine-R1(config-if)# isis bfd
Spine-R1(config-if)# exit
Spine-R1(config)# 
```

Проверяем состояние BFD на Leaf-1:

```text
Leaf-R1# sh bfd neighbors 

OurAddr         NeighAddr       LD/RD                 RH/RS           Holdown(mult)     State       Int                   Vrf                              Type    
10.101.214.2    10.101.214.1    1090519042/0          Down            N/A(3)            Down        Eth1/1                default                          SH      
Leaf-R1#
```

К сожалению BFD в лабе на нексусах не работает, поскольку отсутствует эмуляция ASIC-аБ поэтому через некоторое время соведство в IS-IS разваливается:

```text
Leaf-R1# sh isis adjacency 
IS-IS process: UNDERLAY VRF: default
IS-IS adjacency database:
Legend: '!': No AF level connectivity in given topology
System ID       SNPA            Level  State  Hold Time  Interface
Spine-R1        N/A             1-2    INIT   00:00:25   Ethernet1/1
Spine-R2        N/A             1-2    UP     00:00:29   Ethernet1/2

Leaf-R1# 
```

```text
Spine-R1# sh isis adjacency 
IS-IS process: UNDERLAY VRF: default
IS-IS adjacency database:
Legend: '!': No AF level connectivity in given topology
System ID       SNPA            Level  State  Hold Time  Interface
Leaf-R1         N/A             1-2    DOWN   00:00:24   Ethernet1/1
Leaf-R2         N/A             1-2    UP     00:00:23   Ethernet1/2
Leaf-R3         N/A             1-2    UP     00:00:23   Ethernet1/3
Leaf-R4         N/A             1-2    UP     00:00:30   Ethernet1/4
BRF-Leaf-R1     N/A             1-2    UP     00:00:26   Ethernet1/6
BRD-Leaf-R2     N/A             1-2    UP     00:00:29   Ethernet1/7

Spine-R1# 
```


В целом настройка BFD тоже выполнена, в целях лабы отклчим BFD, с обоих сторон, следующим образом (в принципе достаточно вылючить с одной стороны):

```text
Leaf-R1# conf term
Leaf-R1(config)# int Ethernet1/1
Leaf-R1(config-if)# isis bfd disable 
Leaf-R1(config-if)# end
Leaf-R1# 
```

И аналогично на Spine-1:

```text
Spine-R1# conf term
Spine-R1(config)# int Ethernet1/1
Spine-R1(config-if)# isis bfd disable 
Spine-R1(config-if)# end
Spine-R1#
```


В качестве разнообразия маршрутов добавим дефолтный на все коммутаторы от роутера:

```text
ToWAN#conf term
Enter configuration commands, one per line.  End with CNTL/Z.
ToWAN(config)#router isis UNDERLAY
ToWAN(config-router)#default-information originate
ToWAN(config-router)#end
ToWAN#
```

И проверим что он появился на Leaf-1:

```text
Leaf-R1# sh ip route isis-UNDERLAY

0.0.0.0/0, ubest/mbest: 2/0
    *via 10.101.214.1, Eth1/1, [115/180], 00:00:58, isis-UNDERLAY, L2
    *via 10.101.224.1, Eth1/2, [115/180], 00:00:58, isis-UNDERLAY, L2
[skip]
```
Дефолтный маршрут создан, проверим что он работает:

```text
Leaf-R1# traceroute 1.1.1.1 source-interface lo1
traceroute to 1.1.1.1 (1.1.1.1) from 10.101.111.1 (10.101.111.1), 30 hops max, 40 byte packets
 1  10.101.224.1 (10.101.224.1)  5.791 ms  2.97 ms  2.097 ms
 2  10.101.24.6 (10.101.24.6)  7.981 ms 10.101.24.2 (10.101.24.2)  8.856 ms 10.101.24.6 (10.101.24.6)  6.231 ms
 3  172.24.2.2 (172.24.2.2)  8.732 ms  10.267 ms 172.24.1.2 (172.24.1.2)  7.68 ms
 4  172.24.1.2 (172.24.1.2)  8.792 ms !H  8.216 ms !H  8.27 ms !H
Leaf-R1# 
```

Трейс успешно добрался до роутера:

```text
ToWAN#sh ip int br | i 172.24.1.2
Ethernet0/0            172.24.1.2      YES NVRAM  up                    up      
ToWAN#
```

Для исключения его влияние на проверки, отключим его:

```text
ToWAN#conf term
ToWAN(config)#router isis UNDERLAY
ToWAN(config-router)#no default-information originate 
ToWAN(config-router)#end
ToWAN#
```


<details>
<summary>Убедимся что всё все еще пингуется, после всех настроек</summary>

```text
ToWAN#
ToWAN#ping 10.101.11.1 source Loopback 1 repeat 1       
Type escape sequence to abort.
Sending 1, 100-byte ICMP Echos to 10.101.11.1, timeout is 2 seconds:
Packet sent with a source address of 172.24.200.1 
!
Success rate is 100 percent (1/1), round-trip min/avg/max = 4/4/4 ms
ToWAN#ping 10.101.21.1 source Loopback 1 repeat 1
Type escape sequence to abort.
Sending 1, 100-byte ICMP Echos to 10.101.21.1, timeout is 2 seconds:
Packet sent with a source address of 172.24.200.1 
!
Success rate is 100 percent (1/1), round-trip min/avg/max = 4/4/4 ms
ToWAN#ping 10.101.111.1 source Loopback 1 repeat 1
Type escape sequence to abort.
Sending 1, 100-byte ICMP Echos to 10.101.111.1, timeout is 2 seconds:
Packet sent with a source address of 172.24.200.1 
!
Success rate is 100 percent (1/1), round-trip min/avg/max = 18/18/18 ms
ToWAN#ping 10.101.121.1 source Loopback 1 repeat 1
Type escape sequence to abort.
Sending 1, 100-byte ICMP Echos to 10.101.121.1, timeout is 2 seconds:
Packet sent with a source address of 172.24.200.1 
!
Success rate is 100 percent (1/1), round-trip min/avg/max = 15/15/15 ms
ToWAN#ping 10.101.131.1 source Loopback 1 repeat 1
Type escape sequence to abort.
Sending 1, 100-byte ICMP Echos to 10.101.131.1, timeout is 2 seconds:
Packet sent with a source address of 172.24.200.1 
!
Success rate is 100 percent (1/1), round-trip min/avg/max = 18/18/18 ms
ToWAN#ping 10.101.141.1 source Loopback 1 repeat 1
Type escape sequence to abort.
Sending 1, 100-byte ICMP Echos to 10.101.141.1, timeout is 2 seconds:
Packet sent with a source address of 172.24.200.1 
!
Success rate is 100 percent (1/1), round-trip min/avg/max = 12/12/12 ms
ToWAN#ping 10.101.211.1 source Loopback 1 repeat 1
Type escape sequence to abort.
Sending 1, 100-byte ICMP Echos to 10.101.211.1, timeout is 2 seconds:
Packet sent with a source address of 172.24.200.1 
!
Success rate is 100 percent (1/1), round-trip min/avg/max = 11/11/11 ms
ToWAN#ping 10.101.221.1 source Loopback 1 repeat 1
Type escape sequence to abort.
Sending 1, 100-byte ICMP Echos to 10.101.221.1, timeout is 2 seconds:
Packet sent with a source address of 172.24.200.1 
!
Success rate is 100 percent (1/1), round-trip min/avg/max = 7/7/7 ms
ToWAN#
ToWAN#
```

</details>


Полные настройки коммутаторов и роутера:

<details>
<summary>Leaf-1</summary>

```text
Leaf-R1# sh run

!Command: show running-config
!Running configuration last done at: Mon Dec  9 01:57:47 2024
!Time: Mon Dec  9 02:09:04 2024

version 9.3(8) Bios:version  
hostname Leaf-R1
vdc Leaf-R1 id 1
  limit-resource vlan minimum 16 maximum 4094
  limit-resource vrf minimum 2 maximum 4096
  limit-resource port-channel minimum 0 maximum 511
  limit-resource u4route-mem minimum 248 maximum 248
  limit-resource u6route-mem minimum 96 maximum 96
  limit-resource m4route-mem minimum 58 maximum 58
  limit-resource m6route-mem minimum 8 maximum 8

feature isis
feature bfd
clock timezone PRM 5 0

no password strength-check
username admin password 5 $5$HIMIJM$qT5AXQEfCx.kdpdUF8dRHjlsjyL3TdgR9BhmK9uAYx7  role network-admin
no ip domain-lookup
copp profile strict
snmp-server user admin auth md5 0153448F0CDEAA7AC3D8A6E1207E29746751 priv 204F64F031BBA129B39DABB42F633F6C6872 localizedV2key engineID 128:0:0:9:3:12:159:0:0:27:1
rmon event 1 log trap public description FATAL(1) owner PMON@FATAL
rmon event 2 log trap public description CRITICAL(2) owner PMON@CRITICAL
rmon event 3 log trap public description ERROR(3) owner PMON@ERROR
rmon event 4 log trap public description WARNING(4) owner PMON@WARNING
rmon event 5 log trap public description INFORMATION(5) owner PMON@INFO

vlan 1

key chain ISIS
  key 1
    key-string 7 070c285f4d064b0916100a
vrf context VRF_VPC-KEEPALIVE
  address-family ipv4 unicast
vrf context management
hardware access-list tcam region racl 256
hardware access-list tcam region vpc-convergence 256
hardware access-list tcam region arp-ether 256


interface Ethernet1/1
  description to_spine_1
  no switchport
  bfd interval 999 min_rx 999 multiplier 3
  bfd authentication Keyed-SHA1 key-id 1 hex-key 636973636F326C616261
  no ip redirects
  ip address 10.101.214.2/30
  no ipv6 redirects
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  isis bfd disable
  no shutdown

interface Ethernet1/2
  description to_spine_2
  no switchport
  no ip redirects
  ip address 10.101.224.2/30
  no ipv6 redirects
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/3
  shutdown

interface Ethernet1/4
  shutdown

interface Ethernet1/5
  shutdown

interface Ethernet1/6
  shutdown

interface Ethernet1/7
  shutdown

interface Ethernet1/8
  shutdown

interface Ethernet1/9
  shutdown

interface Ethernet1/10
  shutdown

interface Ethernet1/11
  shutdown

interface Ethernet1/12
  shutdown

interface Ethernet1/13
  shutdown

interface Ethernet1/14
  shutdown

interface Ethernet1/15
  description vPC K/A to_leaf-2
  no switchport
  vrf member VRF_VPC-KEEPALIVE
  ip address 10.101.113.1/30
  no shutdown

interface Ethernet1/16

interface Ethernet1/17

interface Ethernet1/18

interface Ethernet1/19

interface Ethernet1/20

interface Ethernet1/21

interface Ethernet1/22

interface Ethernet1/23

interface Ethernet1/24

interface Ethernet1/25

interface Ethernet1/26

interface Ethernet1/27

interface Ethernet1/28

interface Ethernet1/29

interface Ethernet1/30

interface Ethernet1/31

interface Ethernet1/32

interface Ethernet1/33

interface Ethernet1/34

interface Ethernet1/35

interface Ethernet1/36

interface Ethernet1/37

interface Ethernet1/38

interface Ethernet1/39

interface Ethernet1/40

interface Ethernet1/41

interface Ethernet1/42

interface Ethernet1/43

interface Ethernet1/44

interface Ethernet1/45

interface Ethernet1/46

interface Ethernet1/47

interface Ethernet1/48

interface Ethernet1/49

interface Ethernet1/50

interface Ethernet1/51

interface Ethernet1/52

interface Ethernet1/53

interface Ethernet1/54

interface Ethernet1/55

interface Ethernet1/56

interface Ethernet1/57

interface Ethernet1/58

interface Ethernet1/59

interface Ethernet1/60

interface Ethernet1/61

interface Ethernet1/62

interface Ethernet1/63

interface Ethernet1/64

interface mgmt0
  vrf member management

interface loopback1
  description # Router ID
  ip address 10.101.111.1/32
  ip router isis UNDERLAY

interface loopback2
  description # VTEP-ID
  ip address 10.101.112.1/32
icam monitor scale

cli alias name wr copy run start
cli alias name c conf term
cli alias name sir show ip route
cli alias name cef show forwarding ipv4 
cli alias name adj show ip adj
cli alias name srr sh run | sec router
line console
  exec-timeout 0
  terminal length 48
  terminal width  186
line vty
boot nxos bootflash:/nxos.9.3.8.bin sup-1
router isis UNDERLAY
  net 49.0001.0101.0111.1001.00
  log-adjacency-changes
  authentication-type md5 level-1
  authentication-type md5 level-2
  authentication key-chain ISIS level-1
  authentication key-chain ISIS level-2
  address-family ipv4 unicast
    maximum-paths 2
    router-id loopback1
  passive-interface default level-1



Leaf-R1#   
Leaf-R1#
```

</details>


<details>
<summary>Leaf-2</summary>

```text
Leaf-R2# sh run

!Command: show running-config
!Running configuration last done at: Sun Dec  8 18:42:50 2024
!Time: Mon Dec  9 02:11:26 2024

version 9.3(8) Bios:version  
hostname Leaf-R2
vdc Leaf-R2 id 1
  limit-resource vlan minimum 16 maximum 4094
  limit-resource vrf minimum 2 maximum 4096
  limit-resource port-channel minimum 0 maximum 511
  limit-resource u4route-mem minimum 248 maximum 248
  limit-resource u6route-mem minimum 96 maximum 96
  limit-resource m4route-mem minimum 58 maximum 58
  limit-resource m6route-mem minimum 8 maximum 8

feature isis
clock timezone PRM 5 0

no password strength-check
username admin password 5 $5$GILIBL$I50uIEZ2.Id5WPuEW2/kF2LnprBS1fD4bK7PLYdXCs4  role network-admin
no ip domain-lookup
copp profile strict
snmp-server user admin auth md5 042F0B626D2474725C91211D7B2226CDBDEA priv 040F77425611450B4490383F363A768CEBA9 localizedV2key engineID 128:0:0:9:3:12:22:0:0:27:1
rmon event 1 log trap public description FATAL(1) owner PMON@FATAL
rmon event 2 log trap public description CRITICAL(2) owner PMON@CRITICAL
rmon event 3 log trap public description ERROR(3) owner PMON@ERROR
rmon event 4 log trap public description WARNING(4) owner PMON@WARNING
rmon event 5 log trap public description INFORMATION(5) owner PMON@INFO

vlan 1

key chain ISIS
  key 1
    key-string 7 070c285f4d064b0916100a
vrf context VRF_VPC-KEEPALIVE
  address-family ipv4 unicast
vrf context management
hardware access-list tcam region racl 256
hardware access-list tcam region vpc-convergence 256


interface Ethernet1/1
  description to_spine_1
  no switchport
  ip address 10.101.214.6/30
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/2
  description to_spine_2
  no switchport
  ip address 10.101.224.6/30
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/3
  shutdown

interface Ethernet1/4
  shutdown

interface Ethernet1/5
  shutdown

interface Ethernet1/6
  shutdown

interface Ethernet1/7
  shutdown

interface Ethernet1/8
  shutdown

interface Ethernet1/9
  shutdown

interface Ethernet1/10
  shutdown

interface Ethernet1/11
  shutdown

interface Ethernet1/12
  shutdown

interface Ethernet1/13
  description vPC Po1 to leaf-1
  shutdown

interface Ethernet1/14
  description vPC Po1 to leaf-1
  shutdown

interface Ethernet1/15
  description vPC K/A to_leaf-1
  no switchport
  vrf member VRF_VPC-KEEPALIVE
  ip address 10.101.113.2/30
  no shutdown

interface Ethernet1/16

interface Ethernet1/17

interface Ethernet1/18

interface Ethernet1/19

interface Ethernet1/20

interface Ethernet1/21

interface Ethernet1/22

interface Ethernet1/23

interface Ethernet1/24

interface Ethernet1/25

interface Ethernet1/26

interface Ethernet1/27

interface Ethernet1/28

interface Ethernet1/29

interface Ethernet1/30

interface Ethernet1/31

interface Ethernet1/32

interface Ethernet1/33

interface Ethernet1/34

interface Ethernet1/35

interface Ethernet1/36

interface Ethernet1/37

interface Ethernet1/38

interface Ethernet1/39

interface Ethernet1/40

interface Ethernet1/41

interface Ethernet1/42

interface Ethernet1/43

interface Ethernet1/44

interface Ethernet1/45

interface Ethernet1/46

interface Ethernet1/47

interface Ethernet1/48

interface Ethernet1/49

interface Ethernet1/50

interface Ethernet1/51

interface Ethernet1/52

interface Ethernet1/53

interface Ethernet1/54

interface Ethernet1/55

interface Ethernet1/56

interface Ethernet1/57

interface Ethernet1/58

interface Ethernet1/59

interface Ethernet1/60

interface Ethernet1/61

interface Ethernet1/62

interface Ethernet1/63

interface Ethernet1/64

interface mgmt0
  vrf member management

interface loopback1
  description # Router ID
  ip address 10.101.121.1/32
  ip router isis UNDERLAY

interface loopback2
  description # VTEP-ID
  ip address 10.101.122.1/32
icam monitor scale

cli alias name wr copy run start
cli alias name c conf term
cli alias name sir show ip route
cli alias name srr sh run | sec router
line console
  exec-timeout 0
  terminal length 48
  terminal width  186
line vty
boot nxos bootflash:/nxos.9.3.8.bin sup-1
router isis UNDERLAY
  net 49.0001.0101.0112.1001.00
  log-adjacency-changes
  authentication-type md5 level-1
  authentication-type md5 level-2
  authentication key-chain ISIS level-1
  authentication key-chain ISIS level-2
  address-family ipv4 unicast
    maximum-paths 2
    router-id loopback1
  passive-interface default level-1-2



Leaf-R2# 
```

</details>

<details>
<summary>Leaf-3</summary>

```text
Leaf-R3# sh run

!Command: show running-config
!Running configuration last done at: Sun Dec  8 18:43:28 2024
!Time: Mon Dec  9 02:11:51 2024

version 9.3(8) Bios:version  
hostname Leaf-R3
vdc Leaf-R3 id 1
  limit-resource vlan minimum 16 maximum 4094
  limit-resource vrf minimum 2 maximum 4096
  limit-resource port-channel minimum 0 maximum 511
  limit-resource u4route-mem minimum 248 maximum 248
  limit-resource u6route-mem minimum 96 maximum 96
  limit-resource m4route-mem minimum 58 maximum 58
  limit-resource m6route-mem minimum 8 maximum 8

feature isis
clock timezone PRM 5 0

no password strength-check
username admin password 5 $5$MLCBMA$/FmotzYqQwdMEJQGtVLJtp3JkUAzX/Me2FliX2AYk38  role network-admin
ip domain-lookup
copp profile strict
snmp-server user admin auth md5 0071FC9DD80C21F0E05AEDD7D7A8ADA024A4 priv 3209C8E6BC345CF9ED32FE94D0BCAA9125B4 localizedV2key engineID 128:0:0:9:3:12:56:0:0:27:1
rmon event 1 log trap public description FATAL(1) owner PMON@FATAL
rmon event 2 log trap public description CRITICAL(2) owner PMON@CRITICAL
rmon event 3 log trap public description ERROR(3) owner PMON@ERROR
rmon event 4 log trap public description WARNING(4) owner PMON@WARNING
rmon event 5 log trap public description INFORMATION(5) owner PMON@INFO

vlan 1

key chain ISIS
  key 1
    key-string 7 070c285f4d064b0916100a
vrf context VRF_VPC-KEEPALIVE
vrf context management


interface Ethernet1/1
  description to_spine_1
  no switchport
  ip address 10.101.214.10/30
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/2
  description to_spine_2
  no switchport
  ip address 10.101.224.10/30
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/3
  shutdown

interface Ethernet1/4
  shutdown

interface Ethernet1/5
  shutdown

interface Ethernet1/6
  shutdown

interface Ethernet1/7
  shutdown

interface Ethernet1/8
  shutdown

interface Ethernet1/9
  shutdown

interface Ethernet1/10
  shutdown

interface Ethernet1/11
  shutdown

interface Ethernet1/12
  shutdown

interface Ethernet1/13
  description vPC Po1 to_leaf-4
  shutdown

interface Ethernet1/14
  description vPC Po1 to_leaf-4
  shutdown

interface Ethernet1/15
  description vPC K/A to_leaf-4
  no switchport
  vrf member VRF_VPC-KEEPALIVE
  ip address 10.101.133.1/30
  no shutdown

interface Ethernet1/16

interface Ethernet1/17

interface Ethernet1/18

interface Ethernet1/19

interface Ethernet1/20

interface Ethernet1/21

interface Ethernet1/22

interface Ethernet1/23

interface Ethernet1/24

interface Ethernet1/25

interface Ethernet1/26

interface Ethernet1/27

interface Ethernet1/28

interface Ethernet1/29

interface Ethernet1/30

interface Ethernet1/31

interface Ethernet1/32

interface Ethernet1/33

interface Ethernet1/34

interface Ethernet1/35

interface Ethernet1/36

interface Ethernet1/37

interface Ethernet1/38

interface Ethernet1/39

interface Ethernet1/40

interface Ethernet1/41

interface Ethernet1/42

interface Ethernet1/43

interface Ethernet1/44

interface Ethernet1/45

interface Ethernet1/46

interface Ethernet1/47

interface Ethernet1/48

interface Ethernet1/49

interface Ethernet1/50

interface Ethernet1/51

interface Ethernet1/52

interface Ethernet1/53

interface Ethernet1/54

interface Ethernet1/55

interface Ethernet1/56

interface Ethernet1/57

interface Ethernet1/58

interface Ethernet1/59

interface Ethernet1/60

interface Ethernet1/61

interface Ethernet1/62

interface Ethernet1/63

interface Ethernet1/64

interface mgmt0
  vrf member management

interface loopback1
  description # Router-ID
  ip address 10.101.131.1/32
  ip router isis UNDERLAY

interface loopback2
  description # VTEP-ID
  ip address 10.101.132.1/32
icam monitor scale

cli alias name wr copy run start
cli alias name c conf term
cli alias name sir show ip route
cli alias name srr sh run | sec router
line console
  exec-timeout 0
  terminal length 48
  terminal width  186
line vty
boot nxos bootflash:/nxos.9.3.8.bin sup-1
router isis UNDERLAY
  net 49.0001.0101.0113.1001.00
  log-adjacency-changes
  authentication-type md5 level-1
  authentication-type md5 level-2
  authentication key-chain ISIS level-1
  authentication key-chain ISIS level-2
  address-family ipv4 unicast
    maximum-paths 2
    router-id loopback1
  passive-interface default level-1-2



Leaf-R3#
```

</details>

<details>
<summary>Leaf-4</summary>

```text
Leaf-R4# sh run

!Command: show running-config
!Running configuration last done at: Sun Dec  8 18:43:46 2024
!Time: Mon Dec  9 02:12:20 2024

version 9.3(8) Bios:version  
hostname Leaf-R4
vdc Leaf-R4 id 1
  limit-resource vlan minimum 16 maximum 4094
  limit-resource vrf minimum 2 maximum 4096
  limit-resource port-channel minimum 0 maximum 511
  limit-resource u4route-mem minimum 248 maximum 248
  limit-resource u6route-mem minimum 96 maximum 96
  limit-resource m4route-mem minimum 58 maximum 58
  limit-resource m6route-mem minimum 8 maximum 8

feature isis
clock timezone PRM 5 0

no password strength-check
username admin password 5 $5$BDBCEE$74zNdCRevQ.jaXtPvMgU3sqqUKyzxCYbO8iNikWmj9D  role network-admin
ip domain-lookup
copp profile strict
snmp-server user admin auth md5 494710BA78F370F1A7A09EC13507B82FC249 priv 1748459848CB1184D7D38CC22B7CFB659E0B localizedV2key engineID 128:0:0:9:3:12:37:0:0:27:1
rmon event 1 log trap public description FATAL(1) owner PMON@FATAL
rmon event 2 log trap public description CRITICAL(2) owner PMON@CRITICAL
rmon event 3 log trap public description ERROR(3) owner PMON@ERROR
rmon event 4 log trap public description WARNING(4) owner PMON@WARNING
rmon event 5 log trap public description INFORMATION(5) owner PMON@INFO

vlan 1

key chain ISIS
  key 1
    key-string 7 070c285f4d064b0916100a
vrf context VRF_VPC-KEEPALIVE
vrf context management


interface Ethernet1/1
  description to_spine_1
  no switchport
  ip address 10.101.214.14/30
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/2
  description to_spine_2
  no switchport
  ip address 10.101.224.14/30
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/3
  shutdown

interface Ethernet1/4
  shutdown

interface Ethernet1/5
  shutdown

interface Ethernet1/6
  shutdown

interface Ethernet1/7
  shutdown

interface Ethernet1/8
  shutdown

interface Ethernet1/9
  shutdown

interface Ethernet1/10
  shutdown

interface Ethernet1/11
  shutdown

interface Ethernet1/12
  shutdown

interface Ethernet1/13
  description vPC Po1 to_leaf-3
  shutdown

interface Ethernet1/14
  description vPC Po1 to_leaf-3
  shutdown

interface Ethernet1/15
  description vPC K/A to_leaf-3
  no switchport
  vrf member VRF_VPC-KEEPALIVE
  ip address 10.101.133.2/30
  no shutdown

interface Ethernet1/16

interface Ethernet1/17

interface Ethernet1/18

interface Ethernet1/19

interface Ethernet1/20

interface Ethernet1/21

interface Ethernet1/22

interface Ethernet1/23

interface Ethernet1/24

interface Ethernet1/25

interface Ethernet1/26

interface Ethernet1/27

interface Ethernet1/28

interface Ethernet1/29

interface Ethernet1/30

interface Ethernet1/31

interface Ethernet1/32

interface Ethernet1/33

interface Ethernet1/34

interface Ethernet1/35

interface Ethernet1/36

interface Ethernet1/37

interface Ethernet1/38

interface Ethernet1/39

interface Ethernet1/40

interface Ethernet1/41

interface Ethernet1/42

interface Ethernet1/43

interface Ethernet1/44

interface Ethernet1/45

interface Ethernet1/46

interface Ethernet1/47

interface Ethernet1/48

interface Ethernet1/49

interface Ethernet1/50

interface Ethernet1/51

interface Ethernet1/52

interface Ethernet1/53

interface Ethernet1/54

interface Ethernet1/55

interface Ethernet1/56

interface Ethernet1/57

interface Ethernet1/58

interface Ethernet1/59

interface Ethernet1/60

interface Ethernet1/61

interface Ethernet1/62

interface Ethernet1/63

interface Ethernet1/64

interface mgmt0
  vrf member management

interface loopback1
  description # Router-ID
  ip address 10.101.141.1/32
  ip router isis UNDERLAY

interface loopback2
  description # VTEP-ID
  ip address 10.101.142.1/32
icam monitor scale

cli alias name wr copy run start
cli alias name c conf term
cli alias name sir show ip route
cli alias name srr sh run | sec router
line console
  exec-timeout 0
  terminal length 48
  terminal width  186
line vty
boot nxos bootflash:/nxos.9.3.8.bin sup-1
router isis UNDERLAY
  net 49.0001.0101.0114.1001.00
  log-adjacency-changes
  authentication-type md5 level-1
  authentication-type md5 level-2
  authentication key-chain ISIS level-1
  authentication key-chain ISIS level-2
  address-family ipv4 unicast
    maximum-paths 2
    router-id loopback1
  passive-interface default level-1-2



Leaf-R4# 
```

</details>

<details>
<summary>Spine-1</summary>

```text
Spine-R1# sh run

!Command: show running-config
!Running configuration last done at: Mon Dec  9 01:57:44 2024
!Time: Mon Dec  9 02:11:34 2024

version 9.3(8) Bios:version  
hostname Spine-R1
vdc Spine-R1 id 1
  limit-resource vlan minimum 16 maximum 4094
  limit-resource vrf minimum 2 maximum 4096
  limit-resource port-channel minimum 0 maximum 511
  limit-resource u4route-mem minimum 248 maximum 248
  limit-resource u6route-mem minimum 96 maximum 96
  limit-resource m4route-mem minimum 58 maximum 58
  limit-resource m6route-mem minimum 8 maximum 8

feature isis
feature bfd
clock timezone PRM 5 0

no password strength-check
username admin password 5 $5$OIECKA$eCLImHYIAUQ9wzeor80qlwAVQYm/.ZlN27uAmI7/1IB  role network-admin
no ip domain-lookup
copp profile strict
snmp-server user admin auth md5 055F766176D1E6DAD4EE126B80F38ACA8E66 priv 483F7A661AC389D78EE04856D6ACADB9EA76 localizedV2key engineID 128:0:0:9:3:12:216:0:0:27:1
rmon event 1 log trap public description FATAL(1) owner PMON@FATAL
rmon event 2 log trap public description CRITICAL(2) owner PMON@CRITICAL
rmon event 3 log trap public description ERROR(3) owner PMON@ERROR
rmon event 4 log trap public description WARNING(4) owner PMON@WARNING
rmon event 5 log trap public description INFORMATION(5) owner PMON@INFO

vlan 1

key chain ISIS
  key 1
    key-string 7 070c285f4d064b0916100a
vrf context management


interface Ethernet1/1
  description to_leaf_1
  no cdp enable
  no switchport
  bfd interval 999 min_rx 999 multiplier 3
  bfd authentication Keyed-SHA1 key-id 1 hex-key 636973636F326C616261
  no ip redirects
  ip address 10.101.214.1/30
  no ipv6 redirects
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  isis bfd disable
  no shutdown

interface Ethernet1/2
  description to_leaf_2
  no switchport
  ip address 10.101.214.5/30
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/3
  description to_leaf_3
  no switchport
  ip address 10.101.214.9/30
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/4
  description to_leaf_4
  no switchport
  ip address 10.101.214.13/30
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/5
  shutdown

interface Ethernet1/6
  description to_brdleaf-1
  no switchport
  ip address 10.101.14.1/30
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/7
  description to_brdleaf-2
  no switchport
  ip address 10.101.14.5/30
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/8
  shutdown

interface Ethernet1/9
  shutdown

interface Ethernet1/10

interface Ethernet1/11

interface Ethernet1/12

interface Ethernet1/13

interface Ethernet1/14

interface Ethernet1/15

interface Ethernet1/16

interface Ethernet1/17

interface Ethernet1/18

interface Ethernet1/19

interface Ethernet1/20

interface Ethernet1/21

interface Ethernet1/22

interface Ethernet1/23

interface Ethernet1/24

interface Ethernet1/25

interface Ethernet1/26

interface Ethernet1/27

interface Ethernet1/28

interface Ethernet1/29

interface Ethernet1/30

interface Ethernet1/31

interface Ethernet1/32

interface Ethernet1/33

interface Ethernet1/34

interface Ethernet1/35

interface Ethernet1/36

interface Ethernet1/37

interface Ethernet1/38

interface Ethernet1/39

interface Ethernet1/40

interface Ethernet1/41

interface Ethernet1/42

interface Ethernet1/43

interface Ethernet1/44

interface Ethernet1/45

interface Ethernet1/46

interface Ethernet1/47

interface Ethernet1/48

interface Ethernet1/49

interface Ethernet1/50

interface Ethernet1/51

interface Ethernet1/52

interface Ethernet1/53

interface Ethernet1/54

interface Ethernet1/55

interface Ethernet1/56

interface Ethernet1/57

interface Ethernet1/58

interface Ethernet1/59

interface Ethernet1/60

interface Ethernet1/61

interface Ethernet1/62

interface Ethernet1/63

interface Ethernet1/64

interface mgmt0
  vrf member management

interface loopback1
  description # Router ID
  ip address 10.101.211.1/32
  ip router isis UNDERLAY

interface loopback2
  description # VTEP-ID
  ip address 10.101.212.1/32
icam monitor scale

cli alias name wr copy run start
cli alias name c conf term
cli alias name sir show ip route
cli alias name cef show forwarding ipv4 
cli alias name adj show ip adj
cli alias name hash show routing hash
cli alias name fadj show forwarding adjacency
cli alias name srr sh run | sec router
line console
  exec-timeout 0
  terminal length 48
  terminal width  186
line vty
boot nxos bootflash:/nxos.9.3.8.bin sup-1
router isis UNDERLAY
  net 49.0001.0101.0121.1001.00
  set-overload-bit on-startup 20
  log-adjacency-changes
  authentication-type md5 level-1
  authentication-type md5 level-2
  authentication key-chain ISIS level-1
  authentication key-chain ISIS level-2
  address-family ipv4 unicast
    maximum-paths 2
    router-id loopback1
  passive-interface default level-1-2



Spine-R1#             
Spine-R1# 
```

</details>

<details>
<summary>Spine-2</summary>

```text
Spine-R2# sh run

!Command: show running-config
!Running configuration last done at: Sun Dec  8 13:44:38 2024
!Time: Sun Dec  8 21:12:19 2024

version 9.3(8) Bios:version  
hostname Spine-R2
vdc Spine-R2 id 1
  limit-resource vlan minimum 16 maximum 4094
  limit-resource vrf minimum 2 maximum 4096
  limit-resource port-channel minimum 0 maximum 511
  limit-resource u4route-mem minimum 248 maximum 248
  limit-resource u6route-mem minimum 96 maximum 96
  limit-resource m4route-mem minimum 58 maximum 58
  limit-resource m6route-mem minimum 8 maximum 8

feature isis

no password strength-check
username admin password 5 $5$PEIEFP$MPKQtVARlyYowD1AGfD7kttGnjbI5D92bO1H5HAKt4C  role network-admin
ip domain-lookup
copp profile strict
snmp-server user admin auth md5 1779710CBD3CD4D9906FA7637BD9A4AAC204 priv 4973727EC11CEFECA116BF6262FBE9B29245 localizedV2key engineID 128:0:0:9:3:12:251:0:0:27:1
rmon event 1 log trap public description FATAL(1) owner PMON@FATAL
rmon event 2 log trap public description CRITICAL(2) owner PMON@CRITICAL
rmon event 3 log trap public description ERROR(3) owner PMON@ERROR
rmon event 4 log trap public description WARNING(4) owner PMON@WARNING
rmon event 5 log trap public description INFORMATION(5) owner PMON@INFO

vlan 1

key chain ISIS
  key 1
    key-string 7 070c285f4d064b0916100a
vrf context management


interface Ethernet1/1
  description to_leaf_1
  no switchport
  ip address 10.101.224.1/30
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/2
  description to_leaf_2
  no switchport
  ip address 10.101.224.5/30
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/3
  description to_leaf_3
  no switchport
  ip address 10.101.224.9/30
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/4
  description to_leaf_4
  no switchport
  ip address 10.101.224.13/30
  isis metric 100 level-1
  isis metric 100 level-2
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/5
  shutdown

interface Ethernet1/6
  description to_brdleaf-1
  no switchport
  ip address 10.101.24.1/30
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/7
  description to_brdleaf-2
  no switchport
  ip address 10.101.24.5/30
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/8
  shutdown

interface Ethernet1/9
  shutdown

interface Ethernet1/10

interface Ethernet1/11

interface Ethernet1/12

interface Ethernet1/13

interface Ethernet1/14

interface Ethernet1/15

interface Ethernet1/16

interface Ethernet1/17

interface Ethernet1/18

interface Ethernet1/19

interface Ethernet1/20

interface Ethernet1/21

interface Ethernet1/22

interface Ethernet1/23

interface Ethernet1/24

interface Ethernet1/25

interface Ethernet1/26

interface Ethernet1/27

interface Ethernet1/28

interface Ethernet1/29

interface Ethernet1/30

interface Ethernet1/31

interface Ethernet1/32

interface Ethernet1/33

interface Ethernet1/34

interface Ethernet1/35

interface Ethernet1/36

interface Ethernet1/37

interface Ethernet1/38

interface Ethernet1/39

interface Ethernet1/40

interface Ethernet1/41

interface Ethernet1/42

interface Ethernet1/43

interface Ethernet1/44

interface Ethernet1/45

interface Ethernet1/46

interface Ethernet1/47

interface Ethernet1/48

interface Ethernet1/49

interface Ethernet1/50

interface Ethernet1/51

interface Ethernet1/52

interface Ethernet1/53

interface Ethernet1/54

interface Ethernet1/55

interface Ethernet1/56

interface Ethernet1/57

interface Ethernet1/58

interface Ethernet1/59

interface Ethernet1/60

interface Ethernet1/61

interface Ethernet1/62

interface Ethernet1/63

interface Ethernet1/64

interface mgmt0
  vrf member management

interface loopback1
  description # Router ID
  ip address 10.101.221.1/32
  ip router isis UNDERLAY

interface loopback2
  description # VTEP-ID
  ip address 10.101.222.1/32
icam monitor scale

cli alias name wr copy run start
cli alias name c conf term
cli alias name sir show ip route
cli alias name srr sh run | sec router
line console
  exec-timeout 0
  terminal length 48
  terminal width  186
line vty
boot nxos bootflash:/nxos.9.3.8.bin sup-1
router isis UNDERLAY
  net 49.0001.0101.0122.1001.00
  set-overload-bit on-startup 20
  log-adjacency-changes
  authentication-type md5 level-1
  authentication-type md5 level-2
  authentication key-chain ISIS level-1
  authentication key-chain ISIS level-2
  address-family ipv4 unicast
    maximum-paths 2
    router-id loopback1
  passive-interface default level-1-2



Spine-R2#
```

</details>

<details>
<summary>BRF-Leaf-1</summary>

```text
BRF-Leaf-R1# sh run

!Command: show running-config
!Running configuration last done at: Mon Dec  9 00:32:20 2024
!Time: Mon Dec  9 02:15:52 2024

version 9.3(8) Bios:version  
hostname BRF-Leaf-R1
vdc BRF-Leaf-R1 id 1
  limit-resource vlan minimum 16 maximum 4094
  limit-resource vrf minimum 2 maximum 4096
  limit-resource port-channel minimum 0 maximum 511
  limit-resource u4route-mem minimum 248 maximum 248
  limit-resource u6route-mem minimum 96 maximum 96
  limit-resource m4route-mem minimum 58 maximum 58
  limit-resource m6route-mem minimum 8 maximum 8

feature isis
clock timezone PRM 5 0

no password strength-check
username admin password 5 $5$MIDGHI$2nFpqu8C1skfYJz.nfv1.xMRxcwGOkEBjf3WOrg50kB  role network-admin
no ip domain-lookup
copp profile strict
snmp-server user admin auth md5 3763E49E7388DDE1EE6ED8E11043B7613B48 priv 3239A4DB7EDE89AAAA62D4BD1817E56D6717 localizedV2key engineID 128:0:0:9:3:12:205:0:0:27:1
rmon event 1 log trap public description FATAL(1) owner PMON@FATAL
rmon event 2 log trap public description CRITICAL(2) owner PMON@CRITICAL
rmon event 3 log trap public description ERROR(3) owner PMON@ERROR
rmon event 4 log trap public description WARNING(4) owner PMON@WARNING
rmon event 5 log trap public description INFORMATION(5) owner PMON@INFO

vlan 1

key chain ISIS
  key 1
    key-string 7 070c285f4d064b0916100a
    cryptographic-algorithm MD5
vrf context management


interface Ethernet1/1
  description to_spine-1
  no switchport
  ip address 10.101.14.2/30
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/2
  description to_spine-2
  no switchport
  ip address 10.101.24.2/30
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/3
  shutdown

interface Ethernet1/4
  shutdown

interface Ethernet1/5
  shutdown

interface Ethernet1/6
  shutdown

interface Ethernet1/7
  shutdown

interface Ethernet1/8
  shutdown

interface Ethernet1/9
  description to_wan
  no switchport
  speed 1000
  duplex full
  ip address 172.24.1.1/30
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/10

interface Ethernet1/11

interface Ethernet1/12

interface Ethernet1/13

interface Ethernet1/14

interface Ethernet1/15

interface Ethernet1/16

interface Ethernet1/17

interface Ethernet1/18

interface Ethernet1/19

interface Ethernet1/20

interface Ethernet1/21

interface Ethernet1/22

interface Ethernet1/23

interface Ethernet1/24

interface Ethernet1/25

interface Ethernet1/26

interface Ethernet1/27

interface Ethernet1/28

interface Ethernet1/29

interface Ethernet1/30

interface Ethernet1/31

interface Ethernet1/32

interface Ethernet1/33

interface Ethernet1/34

interface Ethernet1/35

interface Ethernet1/36

interface Ethernet1/37

interface Ethernet1/38

interface Ethernet1/39

interface Ethernet1/40

interface Ethernet1/41

interface Ethernet1/42

interface Ethernet1/43

interface Ethernet1/44

interface Ethernet1/45

interface Ethernet1/46

interface Ethernet1/47

interface Ethernet1/48

interface Ethernet1/49

interface Ethernet1/50

interface Ethernet1/51

interface Ethernet1/52

interface Ethernet1/53

interface Ethernet1/54

interface Ethernet1/55

interface Ethernet1/56

interface Ethernet1/57

interface Ethernet1/58

interface Ethernet1/59

interface Ethernet1/60

interface Ethernet1/61

interface Ethernet1/62

interface Ethernet1/63

interface Ethernet1/64

interface mgmt0
  vrf member management

interface loopback1
  description # Router ID
  ip address 10.101.11.1/32
  ip router isis UNDERLAY

interface loopback2
  description # VTEP-ID
  ip address 10.101.12.1/32
icam monitor scale

cli alias name wr copy run start
cli alias name c conf term
cli alias name sir show ip route
cli alias name srr sh run | sec router
line console
  exec-timeout 0
  terminal length 48
  terminal width  186
line vty
boot nxos bootflash:/nxos.9.3.8.bin sup-1
router isis UNDERLAY
  net 49.0001.0101.0101.1001.00
  log-adjacency-changes
  authentication-type md5 level-1
  authentication-type md5 level-2
  authentication key-chain ISIS level-1
  authentication key-chain ISIS level-2
  address-family ipv4 unicast
    maximum-paths 2
    router-id loopback1
  passive-interface default level-1-2



BRF-Leaf-R1#
```

</details>

<details>
<summary>BRF-Leaf-2</summary>

```text
BRD-Leaf-R2# sh run

!Command: show running-config
!Running configuration last done at: Mon Dec  9 00:31:38 2024
!Time: Mon Dec  9 02:15:44 2024

version 9.3(8) Bios:version  
hostname BRD-Leaf-R2
vdc BRD-Leaf-R2 id 1
  limit-resource vlan minimum 16 maximum 4094
  limit-resource vrf minimum 2 maximum 4096
  limit-resource port-channel minimum 0 maximum 511
  limit-resource u4route-mem minimum 248 maximum 248
  limit-resource u6route-mem minimum 96 maximum 96
  limit-resource m4route-mem minimum 58 maximum 58
  limit-resource m6route-mem minimum 8 maximum 8

feature isis
clock timezone PRM 5 0

no password strength-check
username admin password 5 $5$ACNBEF$17/UC5AByWpXNbBSvnuvJloYBoV9atZRzW.oDYLwnr5  role network-admin
no ip domain-lookup
copp profile strict
snmp-server user admin auth md5 3752D36F5DECF4F5D19CE99D2745ADFDA765 priv 3756B50865C39086B7F9D2F641D5132E760C localizedV2key engineID 128:0:0:9:3:12:33:0:0:27:1
rmon event 1 log trap public description FATAL(1) owner PMON@FATAL
rmon event 2 log trap public description CRITICAL(2) owner PMON@CRITICAL
rmon event 3 log trap public description ERROR(3) owner PMON@ERROR
rmon event 4 log trap public description WARNING(4) owner PMON@WARNING
rmon event 5 log trap public description INFORMATION(5) owner PMON@INFO

vlan 1

key chain ISIS
  key 1
    key-string 7 070c285f4d064b0916100a
vrf context management


interface Ethernet1/1
  description to_spine-1
  no switchport
  ip address 10.101.14.6/30
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/2
  description to_spine-2
  no switchport
  ip address 10.101.24.6/30
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/3
  shutdown

interface Ethernet1/4
  shutdown

interface Ethernet1/5
  shutdown

interface Ethernet1/6
  shutdown

interface Ethernet1/7
  shutdown

interface Ethernet1/8
  shutdown

interface Ethernet1/9
  description to_wan
  no switchport
  speed 1000
  duplex full
  ip address 172.24.2.1/30
  isis network point-to-point
  ip router isis UNDERLAY
  no isis passive-interface level-1-2
  no shutdown

interface Ethernet1/10

interface Ethernet1/11

interface Ethernet1/12

interface Ethernet1/13

interface Ethernet1/14

interface Ethernet1/15

interface Ethernet1/16

interface Ethernet1/17

interface Ethernet1/18

interface Ethernet1/19

interface Ethernet1/20

interface Ethernet1/21

interface Ethernet1/22

interface Ethernet1/23

interface Ethernet1/24

interface Ethernet1/25

interface Ethernet1/26

interface Ethernet1/27

interface Ethernet1/28

interface Ethernet1/29

interface Ethernet1/30

interface Ethernet1/31

interface Ethernet1/32

interface Ethernet1/33

interface Ethernet1/34

interface Ethernet1/35

interface Ethernet1/36

interface Ethernet1/37

interface Ethernet1/38

interface Ethernet1/39

interface Ethernet1/40

interface Ethernet1/41

interface Ethernet1/42

interface Ethernet1/43

interface Ethernet1/44

interface Ethernet1/45

interface Ethernet1/46

interface Ethernet1/47

interface Ethernet1/48

interface Ethernet1/49

interface Ethernet1/50

interface Ethernet1/51

interface Ethernet1/52

interface Ethernet1/53

interface Ethernet1/54

interface Ethernet1/55

interface Ethernet1/56

interface Ethernet1/57

interface Ethernet1/58

interface Ethernet1/59

interface Ethernet1/60

interface Ethernet1/61

interface Ethernet1/62

interface Ethernet1/63

interface Ethernet1/64

interface mgmt0
  vrf member management

interface loopback1
  description # Router ID
  ip address 10.101.21.1/32
  ip router isis UNDERLAY

interface loopback2
  description # VTEP-ID
  ip address 10.101.22.1/32
icam monitor scale

cli alias name wr copy run start
cli alias name c conf term
cli alias name sir show ip route
cli alias name srr sh run | sec router
line console
  exec-timeout 0
  terminal length 48
  terminal width  186
line vty
boot nxos bootflash:/nxos.9.3.8.bin sup-1
router isis UNDERLAY
  net 49.0001.0101.0102.1001.00
  log-adjacency-changes
  authentication-type md5 level-1
  authentication-type md5 level-2
  authentication key-chain ISIS level-1
  authentication key-chain ISIS level-2
  address-family ipv4 unicast
    maximum-paths 2
    router-id loopback1
  passive-interface default level-1-2



BRD-Leaf-R2#
```

</details>



<details>
<summary>ToWan</summary>

```text
ToWAN#sh run brief 
Building configuration...

Current configuration : 2550 bytes
!
! Last configuration change at 02:44:23 PRM Mon Dec 9 2024
!
version 17.12
service timestamps debug datetime msec
service timestamps log datetime msec
!
hostname ToWAN
!
boot-start-marker
boot-end-marker
!
!
logging console warnings
no aaa new-model
!
!
!
clock timezone PRM 5 0
no ip icmp rate-limit unreachable
!
!         
!
!
!
!
!
!
!
!
no ip domain lookup
ip cef
login on-success log
no ipv6 cef
!
!
!
!
!
!
!
!
multilink bundle-name authenticated
!
!         
key chain ISIS
 key 1
  key-string cisco2laba
  cryptographic-algorithm md5
!
crypto pki trustpoint TP-self-signed-2043905
 enrollment selfsigned
 subject-name cn=IOS-Self-Signed-Certificate-2043905
 revocation-check none
 rsakeypair TP-self-signed-2043905
 hash sha256
!
!
crypto pki certificate chain TP-self-signed-2043905
 certificate self-signed 01
!
!
memory free low-watermark processor 55011
!
!
spanning-tree mode rapid-pvst
!
!         
!
!
!
!
!
! 
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
interface Loopback1
 description Router-ID
 ip address 172.24.200.1 255.255.255.255
!
interface Ethernet0/0
 description to_brd_leaf_1
 ip address 172.24.1.2 255.255.255.252
 ip router isis UNDERLAY
 isis network point-to-point 
 isis metric 40 level-1
 isis metric 40 level-2
!
interface Ethernet0/1
 description to_brd_leaf_2
 ip address 172.24.2.2 255.255.255.252
 ip router isis UNDERLAY
 isis network point-to-point 
 isis metric 40 level-1
 isis metric 40 level-2
!
interface Ethernet0/2
 no ip address
 shutdown
!
interface Ethernet0/3
 no ip address
 shutdown
!
interface Ethernet1/0
 no ip address
 shutdown
!
interface Ethernet1/1
 ip address 192.168.100.2 255.255.255.252
 no ip redirects
 shutdown
!
interface Ethernet1/2
 no ip address
 shutdown
!
interface Ethernet1/3
 no ip address
 shutdown
!
router isis UNDERLAY
 net 49.0002.1720.2420.0001.00
 router-id Loopback1
 authentication mode md5 level-1
 authentication mode md5 level-2
 authentication key-chain ISIS level-1
 authentication key-chain ISIS level-2
 metric-style wide
 log-adjacency-changes
 passive-interface default
 no passive-interface Ethernet0/0
 no passive-interface Ethernet0/1
!
ip forward-protocol nd
!
ip tcp synwait-time 5
!
ip http server
ip http secure-server
ip route 0.0.0.0 0.0.0.0 Null0
ip ssh bulk-mode 131072
!
!
!
!
!         
control-plane
!
!
alias exec c conf t
alias exec sir show ip route
alias exec srr show run br | s router
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
 transport input ssh
!
!
!
!
end       

ToWAN# 
```

</details>
