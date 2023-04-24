# ИИ в сегменте пассажирских перевозок

Предположим, что на `49` маршруте автобуса установлены умные остановки. Каждая остановка может определить где находится ближайший автобус данного направления и количество минут до его прибытия. Пусть пассажир находится на остановке `НИКОЛЬСКАЯ ПЛОЩАДЬ [49]<` с идентификатором `2918`, а ближайший автобус, идущий в данном направлении на остановке: `НАБ. ОБВОДНОГО КАНАЛА (тест)` с идентификатором `4515`.

Сколько минут до прибытия автобуса должна показать умная остановка в предположении, что средняя скорость движения автобуса в это время дня 10 км. в час.

Полученный результат окрулите до целых минут, например, если автобус прибудет через 1.5 минуты, введите 2.

```sql
select ceiling(sum(distance_back)/(10000/60.)) from route_by_stops where route_number = 49 and id_direction = 1
 	and stop_number > (select stop_number from route_by_stops where route_number = 49 and id_direction = 1 and id_stop = 4515)
 	and stop_number <= (select stop_number from route_by_stops where route_number = 49 and id_direction = 1 and id_stop = 2918)
```