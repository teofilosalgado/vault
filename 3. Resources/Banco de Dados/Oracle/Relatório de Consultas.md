---
title: Relatório de Consultas
---
Para obter um relatório, ordenado cronologicamente, das últimas consultas executadas contendo informações como: tempo de execução (em segundos), usuário e aplicação de origem; podemos utilizar a seguinte consulta:

```sql
SELECT
    to_char(s.last_active_time, 'YYYY-MM-DD"T"HH24:MI:SS"Z"') AS timestamp,
    u.username,
    s.module,
    s.executions,
    s.elapsed_time / 1000000 AS elapsed_time_seconds,
    s.sql_text,
    s.sql_id
FROM
         v$sql s
    JOIN all_users u ON s.parsing_user_id = u.user_id
WHERE
        s.executions > 0
    AND u.username NOT IN ( 'SYS', 'SDE', 'SYSTEM' )
ORDER BY
    s.last_active_time DESC
FETCH FIRST 50 ROWS ONLY;
```
