---
title: Locks Ativas
---

Para obter informações sobre locks ativas numa base de dados Oracle, podemos utilizar a seguinte consulta:

```sql
SELECT
    l.session_id                   session_id,
    v.serial#                      serial,
    l.oracle_username              oracle_username,
    o.object_name,
    o.object_type,
    decode(l.locked_mode,
           0,
           'None',
           1,
           'Null',
           2,
           'Row-S (SS)',
           3,
           'Row-X (SX)',
           4,
           'Share',
           5,
           'S/Row-X (SSX)',
           6,
           'Exclusive',
           to_char(l.locked_mode)) locked_mode,
    o.status                       status,
    o.last_ddl_time                last_ddl_time
FROM
    dba_objects      o,
    gv$locked_object l,
    v$session        v
WHERE
        o.object_id = l.object_id
    AND l.session_id = v.sid
ORDER BY
    3,
    4;
```