# PR5


# Название

Исследование информации о состоянии беспроводных сетей

## Цель

1.  Получить знания о методах исследования радиоэлектронной обстановки.
2.  Составить представление о механизмах работы Wi-Fi сетей на канальном
    и сетевом уровне модели OSI.
3.  Зекрепить практические навыки использования языка программирования R
    для обработки данных
4.  Закрепить знания основных функций обработки данных экосистемы
    tidyverse языка R

## Исходные данные

1.  Ноутбук
2.  Условие для практической работы

## Общий план выполнения

1.  Подготовка данных
2.  Анализ точки доступа
3.  Анализ данных клиента
4.  Подготовить и написать отчёт.

### Шаг 1 Подготовка данных

Подключим необходимые пакеты.

``` r
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ✔ purrr     1.0.2     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(readr)
```

Импортируем данные из файла в два датасета: access_points - точки
доступа и client - данные клиентов.

``` r
access_points <- read.csv(file="./P2_wifi_data.csv", nrows = 167)
head(access_points)
```

                  BSSID      First.time.seen       Last.time.seen channel Speed
    1 BE:F1:71:D5:17:8B  2023-07-28 09:13:03  2023-07-28 11:50:50       1   195
    2 6E:C7:EC:16:DA:1A  2023-07-28 09:13:03  2023-07-28 11:55:12       1   130
    3 9A:75:A8:B9:04:1E  2023-07-28 09:13:03  2023-07-28 11:53:31       1   360
    4 4A:EC:1E:DB:BF:95  2023-07-28 09:13:03  2023-07-28 11:04:01       7   360
    5 D2:6D:52:61:51:5D  2023-07-28 09:13:03  2023-07-28 10:30:19       6   130
    6 E8:28:C1:DC:B2:52  2023-07-28 09:13:03  2023-07-28 11:55:38       6   130
      Privacy Cipher Authentication Power X..beacons X..IV           LAN.IP
    1    WPA2   CCMP            PSK   -30        846   504    0.  0.  0.  0
    2    WPA2   CCMP            PSK   -30        750   116    0.  0.  0.  0
    3    WPA2   CCMP            PSK   -68        694    26    0.  0.  0.  0
    4    WPA2   CCMP            PSK   -37        510    21    0.  0.  0.  0
    5    WPA2   CCMP            PSK   -57        647     6    0.  0.  0.  0
    6     OPN                         -63        251  3430  172. 17.203.197
      ID.length           ESSID Key
    1        12    C322U13 3965  NA
    2         4            Cnet  NA
    3         2              KC  NA
    4        14  POCO X5 Pro 5G  NA
    5        25                  NA
    6        13   MIREA_HOTSPOT  NA

``` r
client <- read.csv(file="./P2_wifi_data.csv", skip=169)
head(client)
```

            Station.MAC      First.time.seen       Last.time.seen Power X..packets
    1 CA:66:3B:8F:56:DD  2023-07-28 09:13:03  2023-07-28 10:59:44   -33        858
    2 96:35:2D:3D:85:E6  2023-07-28 09:13:03  2023-07-28 09:13:03   -65          4
    3 5C:3A:45:9E:1A:7B  2023-07-28 09:13:03  2023-07-28 11:51:54   -39        432
    4 C0:E4:34:D8:E7:E5  2023-07-28 09:13:03  2023-07-28 11:53:16   -61        958
    5 5E:8E:A6:5E:34:81  2023-07-28 09:13:04  2023-07-28 09:13:04   -53          1
    6 10:51:07:CB:33:E7  2023-07-28 09:13:05  2023-07-28 11:56:06   -43        344
                   BSSID Probed.ESSIDs
    1  BE:F1:71:D5:17:8B  C322U13 3965
    2  (not associated)   IT2 Wireless
    3  BE:F1:71:D6:10:D7  C322U21 0566
    4  BE:F1:71:D5:17:8B  C322U13 3965
    5  (not associated)               
    6  (not associated)               

Приведём датасеты в вид “аккуратных данных”, преобразовав типы столбцов
в соответствии с типом данных.

Аккуратные данные:

-   Каждая переменная находиться в столбце

-   Каждое наблюдение - это строка

-   Каждое значение являеться ячейкой

``` r
access_points <- access_points %>% 
  mutate_at(vars(BSSID, Privacy, Cipher, Authentication, 
                 LAN.IP, ESSID), trimws)%>%
  mutate_at(vars(BSSID, Privacy, Cipher, Authentication, 
                 LAN.IP, ESSID), na_if, "")

access_points$First.time.seen <- 
  as.POSIXct(access_points$First.time.seen, format = "%Y-%m-%d %H:%M:%S")
access_points$Last.time.seen <- 
  as.POSIXct(access_points$Last.time.seen, format = "%Y-%m-%d %H:%M:%S")

head(access_points)
```

                  BSSID     First.time.seen      Last.time.seen channel Speed
    1 BE:F1:71:D5:17:8B 2023-07-28 09:13:03 2023-07-28 11:50:50       1   195
    2 6E:C7:EC:16:DA:1A 2023-07-28 09:13:03 2023-07-28 11:55:12       1   130
    3 9A:75:A8:B9:04:1E 2023-07-28 09:13:03 2023-07-28 11:53:31       1   360
    4 4A:EC:1E:DB:BF:95 2023-07-28 09:13:03 2023-07-28 11:04:01       7   360
    5 D2:6D:52:61:51:5D 2023-07-28 09:13:03 2023-07-28 10:30:19       6   130
    6 E8:28:C1:DC:B2:52 2023-07-28 09:13:03 2023-07-28 11:55:38       6   130
      Privacy Cipher Authentication Power X..beacons X..IV          LAN.IP
    1    WPA2   CCMP            PSK   -30        846   504   0.  0.  0.  0
    2    WPA2   CCMP            PSK   -30        750   116   0.  0.  0.  0
    3    WPA2   CCMP            PSK   -68        694    26   0.  0.  0.  0
    4    WPA2   CCMP            PSK   -37        510    21   0.  0.  0.  0
    5    WPA2   CCMP            PSK   -57        647     6   0.  0.  0.  0
    6     OPN   <NA>           <NA>   -63        251  3430 172. 17.203.197
      ID.length          ESSID Key
    1        12   C322U13 3965  NA
    2         4           Cnet  NA
    3         2             KC  NA
    4        14 POCO X5 Pro 5G  NA
    5        25           <NA>  NA
    6        13  MIREA_HOTSPOT  NA

``` r
client <- client %>% 
  mutate_at(vars(Station.MAC, BSSID, Probed.ESSIDs), trimws) %>%
  mutate_at(vars(Station.MAC, BSSID, Probed.ESSIDs), na_if, "")

client$First.time.seen <- 
  as.POSIXct(client$First.time.seen, format = "%Y-%m-%d %H:%M:%S")
client$Last.time.seen <- 
  as.POSIXct(client$Last.time.seen, format = "%Y-%m-%d %H:%M:%S")

head(client)
```

            Station.MAC     First.time.seen      Last.time.seen Power X..packets
    1 CA:66:3B:8F:56:DD 2023-07-28 09:13:03 2023-07-28 10:59:44   -33        858
    2 96:35:2D:3D:85:E6 2023-07-28 09:13:03 2023-07-28 09:13:03   -65          4
    3 5C:3A:45:9E:1A:7B 2023-07-28 09:13:03 2023-07-28 11:51:54   -39        432
    4 C0:E4:34:D8:E7:E5 2023-07-28 09:13:03 2023-07-28 11:53:16   -61        958
    5 5E:8E:A6:5E:34:81 2023-07-28 09:13:04 2023-07-28 09:13:04   -53          1
    6 10:51:07:CB:33:E7 2023-07-28 09:13:05 2023-07-28 11:56:06   -43        344
                  BSSID Probed.ESSIDs
    1 BE:F1:71:D5:17:8B  C322U13 3965
    2  (not associated)  IT2 Wireless
    3 BE:F1:71:D6:10:D7  C322U21 0566
    4 BE:F1:71:D5:17:8B  C322U13 3965
    5  (not associated)          <NA>
    6  (not associated)          <NA>

Просмотрим общую структуру данных с помощью функции glimpse()

``` r
glimpse(access_points)
```

    Rows: 167
    Columns: 15
    $ BSSID           <chr> "BE:F1:71:D5:17:8B", "6E:C7:EC:16:DA:1A", "9A:75:A8:B9…
    $ First.time.seen <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13:03, 2023-07-28 …
    $ Last.time.seen  <dttm> 2023-07-28 11:50:50, 2023-07-28 11:55:12, 2023-07-28 …
    $ channel         <int> 1, 1, 1, 7, 6, 6, 11, 11, 11, 1, 6, 14, 11, 11, 6, 6, …
    $ Speed           <int> 195, 130, 360, 360, 130, 130, 195, 130, 130, 195, 180,…
    $ Privacy         <chr> "WPA2", "WPA2", "WPA2", "WPA2", "WPA2", "OPN", "WPA2",…
    $ Cipher          <chr> "CCMP", "CCMP", "CCMP", "CCMP", "CCMP", NA, "CCMP", "C…
    $ Authentication  <chr> "PSK", "PSK", "PSK", "PSK", "PSK", NA, "PSK", "PSK", "…
    $ Power           <int> -30, -30, -68, -37, -57, -63, -27, -38, -38, -66, -42,…
    $ X..beacons      <int> 846, 750, 694, 510, 647, 251, 1647, 1251, 704, 617, 13…
    $ X..IV           <int> 504, 116, 26, 21, 6, 3430, 80, 11, 0, 0, 86, 0, 0, 0, …
    $ LAN.IP          <chr> "0.  0.  0.  0", "0.  0.  0.  0", "0.  0.  0.  0", "0.…
    $ ID.length       <int> 12, 4, 2, 14, 25, 13, 12, 13, 24, 12, 10, 0, 24, 24, 1…
    $ ESSID           <chr> "C322U13 3965", "Cnet", "KC", "POCO X5 Pro 5G", NA, "M…
    $ Key             <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…

``` r
glimpse(client)
```

    Rows: 12,269
    Columns: 7
    $ Station.MAC     <chr> "CA:66:3B:8F:56:DD", "96:35:2D:3D:85:E6", "5C:3A:45:9E…
    $ First.time.seen <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13:03, 2023-07-28 …
    $ Last.time.seen  <dttm> 2023-07-28 10:59:44, 2023-07-28 09:13:03, 2023-07-28 …
    $ Power           <chr> " -33", " -65", " -39", " -61", " -53", " -43", " -31"…
    $ X..packets      <chr> "      858", "        4", "      432", "      958", " …
    $ BSSID           <chr> "BE:F1:71:D5:17:8B", "(not associated)", "BE:F1:71:D6:…
    $ Probed.ESSIDs   <chr> "C322U13 3965", "IT2 Wireless", "C322U21 0566", "C322U…

### Шаг 2 Анализ точки доступа

#### Шаг 2.1 Определить небезопасные точки доступа (без шифрования – OPN)

``` r
unsafe_points <- access_points %>% 
  filter(Privacy == 'OPN') %>% 
  select(BSSID)  %>% 
  unique()

unsafe_points
```

                   BSSID
    1  E8:28:C1:DC:B2:52
    2  E8:28:C1:DC:B2:50
    3  E8:28:C1:DC:B2:51
    4  E8:28:C1:DC:FF:F2
    5  00:25:00:FF:94:73
    6  E8:28:C1:DD:04:52
    7  E8:28:C1:DE:74:31
    8  E8:28:C1:DE:74:32
    9  E8:28:C1:DC:C8:32
    10 E8:28:C1:DD:04:50
    11 E8:28:C1:DD:04:51
    12 E8:28:C1:DC:C8:30
    13 E8:28:C1:DE:74:30
    14 E0:D9:E3:48:FF:D2
    15 E8:28:C1:DC:B2:41
    16 E8:28:C1:DC:B2:40
    17 00:26:99:F2:7A:E0
    18 E8:28:C1:DC:B2:42
    19 E8:28:C1:DD:04:40
    20 E8:28:C1:DD:04:41
    21 E8:28:C1:DE:47:D2
    22 02:BC:15:7E:D5:DC
    23 E8:28:C1:DC:C6:B1
    24 E8:28:C1:DD:04:42
    25 E8:28:C1:DC:C8:31
    26 E8:28:C1:DE:47:D1
    27 00:AB:0A:00:10:10
    28 E8:28:C1:DC:C6:B0
    29 E8:28:C1:DC:C6:B2
    30 E8:28:C1:DC:BD:50
    31 E8:28:C1:DC:0B:B2
    32 E8:28:C1:DC:33:12
    33 00:03:7A:1A:03:56
    34 00:03:7F:12:34:56
    35 00:3E:1A:5D:14:45
    36 E0:D9:E3:49:00:B1
    37 E8:28:C1:DC:BD:52
    38 00:26:99:F2:7A:EF
    39 02:67:F1:B0:6C:98
    40 02:CF:8B:87:B4:F9
    41 00:53:7A:99:98:56
    42 E8:28:C1:DE:47:D0

#### Шаг 2.2 Определить производителя для каждого обнаруженного устройства

``` r
producer <- sapply(unsafe_points, function(i) substr(i, 1, 8)) %>% unique()

producer
```

          BSSID     
     [1,] "E8:28:C1"
     [2,] "00:25:00"
     [3,] "E0:D9:E3"
     [4,] "00:26:99"
     [5,] "02:BC:15"
     [6,] "00:AB:0A"
     [7,] "00:03:7A"
     [8,] "00:03:7F"
     [9,] "00:3E:1A"
    [10,] "02:67:F1"
    [11,] "02:CF:8B"
    [12,] "00:53:7A"

-   E8:28:C1 - Eltex Enterprise Ltd.

-   00:25:00 - Apple, Inc.

-   E0:D9:E3 - Eltex Enterprise Ltd.

-   00:26:99 - Cisco Systems, Inc

-   00:03:7A - Taiyo Yuden Co., Ltd.

-   00:03:7F - Atheros Communications, Inc.

#### Шаг 2.3 Выявить устройства, использующие последнюю версию протокола шифрования WPA3, и названия точек доступа, реализованных на этих устройствах

``` r
access_points %>%
  filter(str_detect(access_points$Privacy, 'WPA3') == TRUE) %>% 
  select(BSSID, ESSID, Privacy)
```

                  BSSID                ESSID   Privacy
    1 26:20:53:0C:98:E8                 <NA> WPA3 WPA2
    2 A2:FE:FF:B8:9B:C9           Christie’s WPA3 WPA2
    3 96:FF:FC:91:EF:64                 <NA> WPA3 WPA2
    4 CE:48:E7:86:4E:33   iPhone (Анастасия) WPA3 WPA2
    5 8E:1F:94:96:DA:FD   iPhone (Анастасия) WPA3 WPA2
    6 BE:FD:EF:18:92:44              Димасик WPA3 WPA2
    7 3A:DA:00:F9:0C:02 iPhone XS Max 🦊🐱🦊 WPA3 WPA2
    8 76:C5:A0:70:08:96                 <NA> WPA3 WPA2

#### Шаг 2.4 Отсортировать точки доступа по интервалу времени, в течение которого они находились на связи, по убыванию.

``` r
access_points %>%
  mutate(t = difftime(Last.time.seen, First.time.seen)) %>%
  arrange(desc(t)) %>%
  select(BSSID, t) %>%
  head(10)
```

                   BSSID         t
    1  00:25:00:FF:94:73 9795 secs
    2  E8:28:C1:DD:04:52 9776 secs
    3  E8:28:C1:DC:B2:52 9755 secs
    4  08:3A:2F:56:35:FE 9746 secs
    5  6E:C7:EC:16:DA:1A 9729 secs
    6  E8:28:C1:DC:B2:50 9726 secs
    7  E8:28:C1:DC:B2:51 9725 secs
    8  48:5B:39:F9:7A:48 9725 secs
    9  E8:28:C1:DC:FF:F2 9724 secs
    10 8E:55:4A:85:5B:01 9723 secs

#### Шаг 2.5 Обнаружить топ-10 самых быстрых точек доступа.

``` r
access_points %>%
  arrange(desc(Speed)) %>% 
  select(BSSID, Speed) %>% 
  head(10)
```

                   BSSID Speed
    1  26:20:53:0C:98:E8   866
    2  96:FF:FC:91:EF:64   866
    3  CE:48:E7:86:4E:33   866
    4  8E:1F:94:96:DA:FD   866
    5  9A:75:A8:B9:04:1E   360
    6  4A:EC:1E:DB:BF:95   360
    7  56:C5:2B:9F:84:90   360
    8  E8:28:C1:DC:B2:41   360
    9  E8:28:C1:DC:B2:40   360
    10 E8:28:C1:DC:B2:42   360

#### Шаг 2.6 Отсортировать точки доступа по частоте отправки запросов (beacons) в единицу времени по их убыванию.

``` r
access_points %>%
  mutate(Time = difftime(Last.time.seen, First.time.seen)) %>%
  filter(Time != 0) %>%
  arrange(Time) %>%
  filter(X..beacons != 0) %>%
  mutate(BeaconsBySec = X..beacons / as.integer(Time)) %>%
  arrange(desc(BeaconsBySec)) %>%
  select(BSSID, X..beacons, Time, BeaconsBySec) %>%
  head()
```

                  BSSID X..beacons   Time BeaconsBySec
    1 F2:30:AB:E9:03:ED          6 7 secs    0.8571429
    2 B2:CF:C0:00:4A:60          4 5 secs    0.8000000
    3 3A:DA:00:F9:0C:02          5 9 secs    0.5555556
    4 02:BC:15:7E:D5:DC          1 2 secs    0.5000000
    5 00:3E:1A:5D:14:45          1 2 secs    0.5000000
    6 76:C5:A0:70:08:96          1 2 secs    0.5000000

### Шаг 3 Анализ данных клиентов

#### Шаг 3.1 Определить производителя для каждого обнаруженного устройства

``` r
producer <- client %>%
  select(BSSID) %>%
  filter(BSSID != "(not associated)")%>%
  filter(!is.na(BSSID)) %>%
  arrange(BSSID) %>%
  unique()

sapply(producer, function(i) substr(i, 1, 8)) %>% unique() 
```

          BSSID     
     [1,] "00:03:7F"
     [2,] "00:0D:97"
     [3,] "00:23:EB"
     [4,] "00:25:00"
     [5,] "00:26:99"
     [6,] "00:AB:0A"
     [7,] "02:67:F1"
     [8,] "08:3A:2F"
     [9,] "0A:C5:E1"
    [10,] "0C:80:63"
    [11,] "12:48:F9"
    [12,] "1E:93:E3"
    [13,] "1E:C2:8E"
    [14,] "22:C9:7F"
    [15,] "2A:E8:A2"
    [16,] "36:46:53"
    [17,] "3A:70:96"
    [18,] "3A:DA:00"
    [19,] "4A:EC:1E"
    [20,] "56:C5:2B"
    [21,] "5E:C7:C0"
    [22,] "6E:C7:EC"
    [23,] "76:70:AF"
    [24,] "7E:3A:10"
    [25,] "82:CD:7D"
    [26,] "86:DF:BF"
    [27,] "8A:A3:03"
    [28,] "8E:1F:94"
    [29,] "8E:55:4A"
    [30,] "92:12:38"
    [31,] "92:F5:7B"
    [32,] "96:FF:FC"
    [33,] "9A:75:A8"
    [34,] "9A:9F:06"
    [35,] "A2:64:E8"
    [36,] "A6:02:B9"
    [37,] "AA:F4:3F"
    [38,] "AE:3E:7F"
    [39,] "AndroidS"
    [40,] "B2:1B:0C"
    [41,] "B6:C4:55"
    [42,] "BE:F1:71"
    [43,] "BE:FD:EF"
    [44,] "CE:B3:FF"
    [45,] "DC:09:4C"
    [46,] "E0:D9:E3"
    [47,] "E2:37:BF"
    [48,] "E8:28:C1"
    [49,] "EA:7B:9B"
    [50,] "MIREA_HO"
    [51,] "TP-Link_"

-   00:03:7F Atheros Communications, Inc.

-   00:0D:97 Hitachi Energy USA Inc.

-   00:23:EB Cisco Systems, Inc

-   00:25:00 Apple, Inc.

-   00:26:99 Cisco Systems, Inc

-   08:3A:2F Guangzhou Juan Intelligent Tech Joint Stock Co.,Ltd

-   0C:80:63 Tp-Link Technologies Co.,Ltd.

-   DC:09:4C Huawei Technologies Co.,Ltd

-   E0:D9:E3 Eltex Enterprise Ltd.

-   E8:28:C1 Eltex Enterprise Ltd.

#### Шаг 3.2 Обнаружить устройства, которые НЕ рандомизируют свой MAC адрес

``` r
client %>% 
  filter(!grepl("^02|^06|^0A|^0E", BSSID)) %>% 
  filter(BSSID != '(not associated)') %>%
  select(BSSID) %>%
  head()
```

                  BSSID
    1 BE:F1:71:D5:17:8B
    2 BE:F1:71:D6:10:D7
    3 BE:F1:71:D5:17:8B
    4 1E:93:E3:1B:3C:F4
    5 E8:28:C1:DC:FF:F2
    6 00:25:00:FF:94:73

#### Шаг 3.3 Кластеризовать запросы от устройств к точкам доступа по их именам. Определить время появления устройства в зоне радиовидимости и время выхода его из нее.

``` r
client %>%
  filter(!is.na(Probed.ESSIDs)) %>%
  group_by(Probed.ESSIDs) %>%
  summarise(Emergence = min(First.time.seen), Exit = max(Last.time.seen)) %>%
  select(Probed.ESSIDs, Emergence, Exit) %>%
  head(10)
```

    # A tibble: 10 × 3
       Probed.ESSIDs                Emergence           Exit               
       <chr>                        <dttm>              <dttm>             
     1 -D-13-                       2023-07-28 09:14:42 2023-07-28 10:26:42
     2 1                            2023-07-28 10:36:12 2023-07-28 11:56:13
     3 107                          2023-07-28 10:29:43 2023-07-28 10:29:43
     4 531                          2023-07-28 10:57:04 2023-07-28 10:57:04
     5 AAAAAOB/CC0ADwGkRedmi 3S     2023-07-28 09:34:20 2023-07-28 11:44:40
     6 AKADO-D967                   2023-07-28 10:31:55 2023-07-28 10:31:55
     7 AQAAAB6zaIoATwEURedmi Note 5 2023-07-28 10:25:19 2023-07-28 11:51:48
     8 ASUS                         2023-07-28 10:31:13 2023-07-28 10:31:13
     9 Alex-net2                    2023-07-28 10:01:06 2023-07-28 10:01:06
    10 AndroidAP177B                2023-07-28 09:13:09 2023-07-28 11:34:42

#### Шаг 3.4 Оценить стабильность уровня сигнала внури кластера во времени. Выявить наиболее стабильный кластер.

``` r
client %>%
  mutate(t = as.integer(difftime(Last.time.seen, First.time.seen))) %>%
  filter(t != 0) %>%
  arrange(desc(t)) %>% 
  filter(!is.na(Probed.ESSIDs)) %>% 
  group_by(Probed.ESSIDs) %>%
  summarise(Mean = mean(t), Sd = sd(t)) %>%
  filter(!is.na(Sd)) %>%
  filter(Sd != 0) %>%
  arrange(Sd) %>%
  select(Probed.ESSIDs, Mean, Sd) %>%
  head(1)
```

    # A tibble: 1 × 3
      Probed.ESSIDs  Mean    Sd
      <chr>         <dbl> <dbl>
    1 nvripcsuite    9780  3.46

### Шаг 4

Отчёт написани и оформлен.
