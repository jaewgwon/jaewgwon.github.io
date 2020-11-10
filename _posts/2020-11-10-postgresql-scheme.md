---
title: How to describe table schema in postgre
categories: [DB, Postgre, SQL]
tags: [db, psql, postgre, sql]
---

```SQL
SELECT
    table_name,
    column_name,
    data_type
FROM
    information_schema.columns
WHERE
    table_name = {TABLE_NAME};
```

