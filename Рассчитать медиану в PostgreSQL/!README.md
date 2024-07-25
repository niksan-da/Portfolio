# Как рассчитать медиану в PostgreSQL с иипользованием базовых операторов

## Общее замечание
Медиана — это такое число, что половина элементов набора чисел не меньше неё, а другая половина — не больше.

Если набор содержит нечётное количество чисел, то медиана окажется по середине набора, упорядоченного по возрастанию.

Если набор содержит чётное количество чисел, то в качестве медианы используют среднее значение двух чисел из середины упорядоченного набора.

  
## Решение
``` sql
SELECT
CASE WHEN (SELECT COUNT (*)
FROM online_store.costs) % 2 = 0 THEN ( 
   SELECT AVG(costs)
   FROM (
   SELECT costs
   FROM online_store.costs
   ORDER BY costs
   LIMIT 2 OFFSET div(
   (SELECT COUNT(*)
   FROM online_store.costs), 2)-1)
)
ELSE (
   SELECT costs
   FROM online_store.costs
   ORDER BY costs
   LIMIT 1 OFFSET div(
   (SELECT COUNT(*)
   FROM online_store.costs), 2)
)
END
FROM online_store.costs
LIMIT 1;
```



## Инструменты.
SQL
