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