---
title: Ferramenta para Exportação em Massa
---
```mermaid
flowchart LR
    A{Fan-out}
    B{Fan-in}
    entidade_signos --> A
    A --> v_ag_sistema_lim -- ogr2ogr --> v_ag_sistema_lim.parquet --> B
    A --> v_sa_colector_pos -- ogr2ogr --> v_sa_colector_pos.parquet --> B
    A --> v_re_ligacao_loc -- ogr2ogr --> v_re_ligacao_loc.parquet --> B
    B -- ogrmerge --> output.gdb
```
