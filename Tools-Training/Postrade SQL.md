## Basic Operations


Checking DB Schema:

```PostgreSQL
SELECT  distinct tablename
FROM PG_TABLE_DEF
WHERE schemaname='public'
;
```

OR

```PostgreSQL
SELECT schemaname, tablename
FROM pg_tables
WHERE schemaname NOT IN ('pg_internal', 'pg_catalog', 'information_schema')
;
```

Describing Tables:

```PostgreSQL
SELECT *
FROM PG_TABLE_DEF
WHERE tablename = 'customer'
  AND schemaname = 'public'
;
```

OR

```PostgreSQL
SELECT column_name, data_type, is_nullable
FROM information_schema.columns
WHERE table_name = 'customer'
AND table_schema = 'public'
;
```



