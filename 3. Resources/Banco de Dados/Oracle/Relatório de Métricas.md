---
title: Relatório de Métricas
---
Para obter um relatório das métricas dos últimos 5 minutos da instância Oracle atual, podemos utilizar a seguinte consulta:

```sql
SELECT
    metric_name,
    AVG(value) metric_value
FROM
    v$sysmetric_history
WHERE
    metric_name IN ( 
        'Host CPU Utilization (%)', 
        'Logical Reads Per Sec', 
        'Physical Reads Per Sec', 
        'Physical Writes Per Sec', 
        'I/O Requests per Second', 
        'Average Active Sessions',
        'Buffer Cache Hit Ratio'
    )
    AND end_time > ( sysdate - 1 / ( 24 * 60 ) * 5 )
GROUP BY
    metric_name
ORDER BY
    metric_name;
```