# Как рассчитать медиану в PostgreSQL с использованием базовых операторов

## Общее замечание
Медиана -- это такое число, что половина элементов набора чисел не меньше неё, а другая половина -- не больше.

Если набор содержит нечётное количество чисел, то медиана окажется по середине набора, упорядоченного по возрастанию.

Если набор содержит чётное количество чисел, то в качестве медианы используют среднее значение двух чисел из середины упорядоченного набора.

  
## Решение
``` sql
SELECT
CASE WHEN (
   SELECT COUNT (*)
   FROM table)
   % 2 = 0
THEN ( 
   SELECT AVG(values)
   FROM (
      SELECT values
      FROM table
      ORDER BY values
      LIMIT 2 OFFSET (
         SELECT COUNT(*)
         FROM table)
         / 2 - 1)
)
ELSE (
   SELECT values
   FROM table
   ORDER BY values
   LIMIT 1 OFFSET div(
      (SELECT COUNT(*)
      FROM table), 2)
)
END
FROM table
LIMIT 1;
```

  
## Примечания
1. `table` -- ваша таблица;
 
`values` -- поле со значениями;
 
2. конструкция с `CASE WHEN` нужна, чтобы учесть чётное и нечётное количество значений в поле values;
``` sql
(SELECT COUNT (*)
FROM table)
% 2 = 0
```
если остаток от деления на 2 равен нулю, то количество чётное и наоборот;
 
3. этот кусок
``` sql
(SELECT values
FROM table
ORDER BY values
LIMIT 2 OFFSET (
   SELECT COUNT(*)
   FROM table)
   / 2 - 1)
```
находит два срединных значения для чётного количества значений; далее рассчитывается среднее этих значений = медиана;

4. если количество нечётное, то ещё проще: находим одно срединное значение
``` sql
(SELECT values
   FROM table
   ORDER BY values
   LIMIT 1 OFFSET div(
      (SELECT COUNT(*)
      FROM table), 2)
)
```

## Инструменты
SQL
