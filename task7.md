# Контрольная работа

Маша и Петя едут на встречу друг другу на троллейбусе номер `11`. Маша садится на остановке (`id_stop = 27167`), а Петя на остановке `5я Советская улица (5, 7, 11, 16)` (`id_stop = 15553`).

Найдите `id_stop` остановок, на которых Маша и Петя должны выйти, чтобы встретиться как можно быстрее при условии, что троллейбусы движутся в обоих направлениях с одинаковой скоростью.

Для вычисления сферического расстояния в базе данных Общественный транспорт была создана функция `CoordinateDistance`. Спецификация функции `CoordinateDistance (Latitude1, Longitude1, Latitude2, Longitude2)`:
- `Latitude1` — широта объекта 1 (числовой тип)
- `Longitude1` — долгота объекта 1 (числовой тип)
- `Latitude2` — широта объекта 2 (числовой тип)
- `Longitude2` — долгота объекта 2 (числовой тип)

Результат функции — сферическое расстояние (в метрах). 

```sql
select * from (
select a.stop_number as stop_number_a, a.travel_time as travel_time_a, a.id_stop as id_stop_a, a.stop_name as stop_name_a,
       b.stop_number as stop_number_b, b.travel_time as travel_time_b, b.id_stop as id_stop_b, b.stop_name as stop_name_b,
       CoordinateDistance(a.latitude, a.longitude, b.latitude, b.longitude) as distance_in_meters
from (select r.stop_number, r.id_stop, s.stop_name, s.latitude, s.longitude,
             sum(r.distance_back) over (order by stop_number) as travel_time
      from route_by_stops r join stops s on r.id_stop = s.id_stop
      where route_number = 11 and id_direction = 1
      and stop_number > (select stop_number from route_by_stops 
                         where route_number = 11 and id_direction = 1 and id_stop = 27167) 
      order by stop_number) a,
     (select r.stop_number, r.id_stop, s.stop_name, s.latitude, s.longitude,
             sum(r.distance_back) over (order by stop_number) as travel_time
      from route_by_stops r join stops s on r.id_stop = s.id_stop 
      where route_number = 11 and id_direction = 2 
      and stop_number > (select stop_number from route_by_stops 
                         where route_number = 11 and id_direction = 2 and id_stop = 15553) 
      order by stop_number) b
order by greatest(travel_time_a, travel_time_b)
)x where distance_in_meters < 100;
```