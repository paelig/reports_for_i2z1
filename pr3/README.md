# PR3


# Название

Основы обработки данных с помощью R и Dplyr

## Цель

1.  Развить практические навыки использования языка программирования R
    для обработки данных
2.  Закрепить знания базовых типов данных языка R
3.  Развить практические навыки использования функций обработки данных
    пакета dplyr – функции select(), filter(), mutate(), arrange(),
    group_by()

## Исходные данные

1.  Ноутбук
2.  Условие для практической работы

## Общий план выполнения

1.  Установить библиотеку nycflights13.
2.  Проанализировать встроенные в пакет nycflights13 наборы данных с
    помощью языка R и ответить на вопросы:
    1.  Сколько встроенных в пакет nycflights13 датафреймов?
    2.  Сколько строк в каждом датафрейме?
    3.  Сколько столбцов в каждом датафрейме?
    4.  Как просмотреть примерный вид датафрейма?
    5.  Сколько компаний-перевозчиков (carrier) учитывают эти наборы
        данных (представлено в наборах данных)?
    6.  Сколько рейсов принял аэропорт John F Kennedy Intl в мае?
    7.  Какой самый северный аэропорт?
    8.  Какой аэропорт самый высокогорный (находится выше всех над
        уровнем моря)?
    9.  Какие бортовые номера у самых старых самолетов?
    10. Какая средняя температура воздуха была в сентябре в аэропорту
        John F Kennedy Intl (в градусах Цельсия).
    11. Самолеты какой авиакомпании совершили больше всего вылетов в
        июне?
    12. Самолеты какой авиакомпании задерживались чаще других в 2013
        году?
3.  Оформить отчет в соответствии с шаблоном

### Шаг 1

Установим пакет nycflights13: ![](../img/img3_1.png)

Подключим пакеты:

``` r
library(dplyr)
```


    Attaching package: 'dplyr'

    The following objects are masked from 'package:stats':

        filter, lag

    The following objects are masked from 'package:base':

        intersect, setdiff, setequal, union

``` r
library(nycflights13)
```

### Шаг 2

#### 1. Сколько встроенных в пакет nycflights13 датафреймов?

5 пакетов: flights - все рейсы, вылетевшие из Нью-Йорка в 2013 году,
weather - почасовые метеорологические данные для каждого аэропорта,
planes - информация о конструкции каждого самолета, airports - названия
и расположение аэропортов, airlines - перевод двух буквенных кодов и
названий перевозчиков.

#### 2. Сколько строк в каждом датафрейме?

``` r
flights %>% nrow()
```

    [1] 336776

``` r
weather %>% nrow()
```

    [1] 26115

``` r
planes %>% nrow()
```

    [1] 3322

``` r
airports %>% nrow()
```

    [1] 1458

``` r
airlines %>% nrow()
```

    [1] 16

#### 3. Сколько столбцов в каждом датафрейме?

``` r
flights %>% ncol()
```

    [1] 19

``` r
weather %>% ncol()
```

    [1] 15

``` r
planes %>% ncol()
```

    [1] 9

``` r
airports %>% ncol()
```

    [1] 8

``` r
airlines %>% ncol()
```

    [1] 2

#### 4. Как просмотреть примерный вид датафрейма?

``` r
flights %>% glimpse()
```

    Rows: 336,776
    Columns: 19
    $ year           <int> 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2…
    $ month          <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1…
    $ day            <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1…
    $ dep_time       <int> 517, 533, 542, 544, 554, 554, 555, 557, 557, 558, 558, …
    $ sched_dep_time <int> 515, 529, 540, 545, 600, 558, 600, 600, 600, 600, 600, …
    $ dep_delay      <dbl> 2, 4, 2, -1, -6, -4, -5, -3, -3, -2, -2, -2, -2, -2, -1…
    $ arr_time       <int> 830, 850, 923, 1004, 812, 740, 913, 709, 838, 753, 849,…
    $ sched_arr_time <int> 819, 830, 850, 1022, 837, 728, 854, 723, 846, 745, 851,…
    $ arr_delay      <dbl> 11, 20, 33, -18, -25, 12, 19, -14, -8, 8, -2, -3, 7, -1…
    $ carrier        <chr> "UA", "UA", "AA", "B6", "DL", "UA", "B6", "EV", "B6", "…
    $ flight         <int> 1545, 1714, 1141, 725, 461, 1696, 507, 5708, 79, 301, 4…
    $ tailnum        <chr> "N14228", "N24211", "N619AA", "N804JB", "N668DN", "N394…
    $ origin         <chr> "EWR", "LGA", "JFK", "JFK", "LGA", "EWR", "EWR", "LGA",…
    $ dest           <chr> "IAH", "IAH", "MIA", "BQN", "ATL", "ORD", "FLL", "IAD",…
    $ air_time       <dbl> 227, 227, 160, 183, 116, 150, 158, 53, 140, 138, 149, 1…
    $ distance       <dbl> 1400, 1416, 1089, 1576, 762, 719, 1065, 229, 944, 733, …
    $ hour           <dbl> 5, 5, 5, 5, 6, 5, 6, 6, 6, 6, 6, 6, 6, 6, 6, 5, 6, 6, 6…
    $ minute         <dbl> 15, 29, 40, 45, 0, 58, 0, 0, 0, 0, 0, 0, 0, 0, 0, 59, 0…
    $ time_hour      <dttm> 2013-01-01 05:00:00, 2013-01-01 05:00:00, 2013-01-01 0…

``` r
weather %>% glimpse()
```

    Rows: 26,115
    Columns: 15
    $ origin     <chr> "EWR", "EWR", "EWR", "EWR", "EWR", "EWR", "EWR", "EWR", "EW…
    $ year       <int> 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013,…
    $ month      <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
    $ day        <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
    $ hour       <int> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 13, 14, 15, 16, 17, 18, …
    $ temp       <dbl> 39.02, 39.02, 39.02, 39.92, 39.02, 37.94, 39.02, 39.92, 39.…
    $ dewp       <dbl> 26.06, 26.96, 28.04, 28.04, 28.04, 28.04, 28.04, 28.04, 28.…
    $ humid      <dbl> 59.37, 61.63, 64.43, 62.21, 64.43, 67.21, 64.43, 62.21, 62.…
    $ wind_dir   <dbl> 270, 250, 240, 250, 260, 240, 240, 250, 260, 260, 260, 330,…
    $ wind_speed <dbl> 10.35702, 8.05546, 11.50780, 12.65858, 12.65858, 11.50780, …
    $ wind_gust  <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, 20.…
    $ precip     <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
    $ pressure   <dbl> 1012.0, 1012.3, 1012.5, 1012.2, 1011.9, 1012.4, 1012.2, 101…
    $ visib      <dbl> 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10,…
    $ time_hour  <dttm> 2013-01-01 01:00:00, 2013-01-01 02:00:00, 2013-01-01 03:00…

``` r
planes %>% glimpse()
```

    Rows: 3,322
    Columns: 9
    $ tailnum      <chr> "N10156", "N102UW", "N103US", "N104UW", "N10575", "N105UW…
    $ year         <int> 2004, 1998, 1999, 1999, 2002, 1999, 1999, 1999, 1999, 199…
    $ type         <chr> "Fixed wing multi engine", "Fixed wing multi engine", "Fi…
    $ manufacturer <chr> "EMBRAER", "AIRBUS INDUSTRIE", "AIRBUS INDUSTRIE", "AIRBU…
    $ model        <chr> "EMB-145XR", "A320-214", "A320-214", "A320-214", "EMB-145…
    $ engines      <int> 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, …
    $ seats        <int> 55, 182, 182, 182, 55, 182, 182, 182, 182, 182, 55, 55, 5…
    $ speed        <int> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N…
    $ engine       <chr> "Turbo-fan", "Turbo-fan", "Turbo-fan", "Turbo-fan", "Turb…

``` r
airports %>% glimpse()
```

    Rows: 1,458
    Columns: 8
    $ faa   <chr> "04G", "06A", "06C", "06N", "09J", "0A9", "0G6", "0G7", "0P2", "…
    $ name  <chr> "Lansdowne Airport", "Moton Field Municipal Airport", "Schaumbur…
    $ lat   <dbl> 41.13047, 32.46057, 41.98934, 41.43191, 31.07447, 36.37122, 41.4…
    $ lon   <dbl> -80.61958, -85.68003, -88.10124, -74.39156, -81.42778, -82.17342…
    $ alt   <dbl> 1044, 264, 801, 523, 11, 1593, 730, 492, 1000, 108, 409, 875, 10…
    $ tz    <dbl> -5, -6, -6, -5, -5, -5, -5, -5, -5, -8, -5, -6, -5, -5, -5, -5, …
    $ dst   <chr> "A", "A", "A", "A", "A", "A", "A", "A", "U", "A", "A", "U", "A",…
    $ tzone <chr> "America/New_York", "America/Chicago", "America/Chicago", "Ameri…

``` r
airlines %>% glimpse()
```

    Rows: 16
    Columns: 2
    $ carrier <chr> "9E", "AA", "AS", "B6", "DL", "EV", "F9", "FL", "HA", "MQ", "O…
    $ name    <chr> "Endeavor Air Inc.", "American Airlines Inc.", "Alaska Airline…

#### 5. Сколько компаний-перевозчиков (carrier) учитывают эти наборы данных (представлено в наборах данных)?

``` r
flights %>% 
  filter(!is.na(carrier)) %>% 
  select(carrier) %>%
  n_distinct()
```

    [1] 16

#### 6. Сколько рейсов принял аэропорт John F Kennedy Intl в мае?

``` r
n <- airports %>% 
  filter(name == "John F Kennedy Intl") %>%
  select(faa)
pull(n)
```

    [1] "JFK"

``` r
flights %>%
  filter(origin == pull(n) & month == 5) %>%
  nrow()
```

    [1] 9397

#### 7. Какой самый северный аэропорт?

``` r
airports %>%
  arrange(desc(lon)) %>%
  slice(1)
```

    # A tibble: 1 × 8
      faa   name           lat   lon   alt    tz dst   tzone            
      <chr> <chr>        <dbl> <dbl> <dbl> <dbl> <chr> <chr>            
    1 SYA   Eareckson As  52.7  174.    98    -9 A     America/Anchorage

#### 8. Какой аэропорт самый высокогорный (находится выше всех над уровнем моря)?

``` r
airports %>%
  arrange(desc(alt)) %>%
  slice(1)
```

    # A tibble: 1 × 8
      faa   name        lat   lon   alt    tz dst   tzone         
      <chr> <chr>     <dbl> <dbl> <dbl> <dbl> <chr> <chr>         
    1 TEX   Telluride  38.0 -108.  9078    -7 A     America/Denver

#### 9. Какие бортовые номера у самых старых самолетов?

``` r
planes %>%
  arrange(year) %>%
  select(tailnum) %>%
  head()
```

    # A tibble: 6 × 1
      tailnum
      <chr>  
    1 N381AA 
    2 N201AA 
    3 N567AA 
    4 N378AA 
    5 N575AA 
    6 N14629 

#### 10. Какая средняя температура воздуха была в сентябре в аэропорту John F Kennedy Intl (в градусах Цельсия).

``` r
avg_temp_f <- weather %>%
  filter(origin == pull(n) & month == 9) %>%
  summarize(avg_temp = mean(temp)) %>%
  pull(avg_temp)
avg_temp_c <- (avg_temp_f - 32) / 1.8
avg_temp_c
```

    [1] 19.38764

#### 11. Самолеты какой авиакомпании совершили больше всего вылетов в июне?

``` r
car <- flights %>%
  filter(month == 6) %>%
  group_by(carrier) %>%
  summarize("count" = n()) %>%
  arrange(desc(count)) %>%
  slice(1) %>%
  select(carrier) %>%
  pull()

airlines %>%
  filter(carrier == car) %>%
  select(name) %>%
  pull()
```

    [1] "United Air Lines Inc."

#### 12. Самолеты какой авиакомпании задерживались чаще других в 2013 году?

``` r
car <- flights %>%
  filter(arr_delay > 0) %>%
  group_by(carrier) %>%
  summarize("count" = n()) %>%
  arrange(desc(count)) %>%
  slice(1) %>%
  select(carrier) %>%
  pull()

airlines %>%
  filter(carrier == car) %>%
  select(name) %>%
  pull()
```

    [1] "ExpressJet Airlines Inc."

### Шаг 3

Отчёт написан и оформлен
