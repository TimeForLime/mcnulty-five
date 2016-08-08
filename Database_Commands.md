Database Commands
Database: MIMIC_ICU
admin: dbadmin
password: thisismetis

postgresql
username: mimic
password: thisismetis

i-052d44db7ab8e08d9
ssh -i "c:/.ssh/ssaleh2.pem" ubuntu@ec2-52-52-69-167.us-west-1.compute.amazonaws.com


psql -p 5432 -h metis.cabju7mub8cg.us-west-2.rds.amazonaws.com -U mimic -d MIMIC_ICU


psql -p 5432 -h metis.cabju7mub8cg.us-west-2.rds.amazonaws.com -f postgres_add_indexes_try.sql -v mimic_data_dir='./' -U mimic -d MIMIC_ICU


If the command doesn't have to be scripted, you can do it this way:

run it in the foreground
pause it (CTRL+Z)
disown it so that it won't be closed when you close your shell (disown -h %jobid)
resume the job in the background (bg %jobid)

- Approximate # of Rows
	SELECT reltuples AS approximate_row_count FROM pg_class WHERE relname = 'tbl';

- Largest database size

	SELECT d.datname AS Name,  pg_catalog.pg_get_userbyid(d.datdba) AS Owner,
    CASE WHEN pg_catalog.has_database_privilege(d.datname, 'CONNECT')
        THEN pg_catalog.pg_size_pretty(pg_catalog.pg_database_size(d.datname))
        ELSE 'No Access'
    END AS SIZE
FROM pg_catalog.pg_database d
    ORDER BY
    CASE WHEN pg_catalog.has_database_privilege(d.datname, 'CONNECT')
        THEN pg_catalog.pg_database_size(d.datname)
        ELSE NULL
    END DESC -- nulls first
    LIMIT 20

- Table Sizes
	SELECT
	   relname as "Table",
	   pg_size_pretty(pg_total_relation_size(relid)) As "Size",
	   pg_size_pretty(pg_total_relation_size(relid) - pg_relation_size(relid)) as "External Size"
	   FROM pg_catalog.pg_statio_user_tables ORDER BY pg_total_relation_size(relid) DESC;

- Detailed Object Sizes - like indices
	SELECT
	   relname AS objectname,
	   relkind AS objecttype,
	   reltuples AS "#entries", pg_size_pretty(relpages::bigint*8*1024) AS size
	   FROM pg_class
	   WHERE relpages >= 8
	   ORDER BY relpages DESC;


Jupyter to access Cynthia's EC2 POWER box
https://40.118.252.155:8888/login?next=%2Ftree


#DB Commands/Useful Queries

## Finding out which indexes are useful

```SQL
SELECT
    t.tablename,
    indexname,
    c.reltuples AS num_rows,
    pg_size_pretty(pg_relation_size(quote_ident(t.tablename)::text)) AS table_size,
    pg_size_pretty(pg_relation_size(quote_ident(indexrelname)::text)) AS index_size,
    CASE WHEN indisunique THEN 'Y'
       ELSE 'N'
    END AS UNIQUE,
    idx_scan AS number_of_scans,
    idx_tup_read AS tuples_read,
    idx_tup_fetch AS tuples_fetched
FROM pg_tables t
LEFT OUTER JOIN pg_class c ON t.tablename=c.relname
LEFT OUTER JOIN
    ( SELECT c.relname AS ctablename, ipg.relname AS indexname, x.indnatts AS number_of_columns, idx_scan, idx_tup_read, idx_tup_fetch, indexrelname, indisunique FROM pg_index x
           JOIN pg_class c ON c.oid = x.indrelid
           JOIN pg_class ipg ON ipg.oid = x.indexrelid
           JOIN pg_stat_all_indexes psai ON x.indexrelid = psai.indexrelid )
    AS foo
    ON t.tablename = foo.ctablename
WHERE t.schemaname='public'
ORDER BY 1,2;
```

## Selecting Table Sizes
```SQL
SELECT
   relname as "Table",
   pg_size_pretty(pg_total_relation_size(relid)) As "Size",
   pg_size_pretty(pg_total_relation_size(relid) - pg_relation_size(relid)) as "External Size"
   FROM pg_catalog.pg_statio_user_tables ORDER BY pg_total_relation_size(relid) DESC;
```

## Detailed Object Sizes - like indices
```SQL
SELECT
   relname AS objectname,
   relkind AS objecttype,
   reltuples AS "#entries", pg_size_pretty(relpages::bigint*8*1024) AS size
   FROM pg_class
   WHERE relpages >= 8
   ORDER BY relpages DESC;
```

## Connecting from Jupyter to RDS instance**
1. Create a settings file with the following code (replace password):

```python
DATABASE = {
    'drivername': 'postgres',
    'host': 'metis.cabju7mub8cg.us-west-2.rds.amazonaws.com',
    'port': '5432',
    'username': 'mimic',
    'password': '<your fav>',
    'database': 'MIMIC_ICU'
}
```

2. Run this code in your jupyter
```python
import pandas as pd

from sqlalchemy import create_engine
from sqlalchemy.engine.url import URL
import settings

def db_connect():
    """
    Performs database connection using database settings from settings.py.
    Returns sqlalchemy engine instance
    """
    return create_engine(URL(**settings.DATABASE))

db = db_connect()
pd.read_sql("""select * from pokemon""", db)
```


## How to connect to RDS from EC2 instance
```python
psql -h metis.cabju7mub8cg.us-west-2.rds.amazonaws.com -p 5432 -U mimic -d MIMIC_ICU
```

