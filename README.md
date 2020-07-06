# postgresql-notes
Just a c/p of handly postgres commands and related 

### Scaffold PostgreSQL EFCore DbContext (package manager console)

```
Scaffold-DbContext "Host=localhost;Database=mydatabase;Username=myuser;Password=mypassword" Npgsql.EntityFrameworkCore.PostgreSQL -o Models
```

### PostgreSQL script
```
DO $$
<declares...>
BEGIN
<logic...>
END $$;
```

### PostgreSQL switch case

##### variant 1

```
DO $$
DECLARE opt integer;
DECLARE date_opt text;
DECLARE cur_d timestamp;
BEGIN
	SELECT CURRENT_TIMESTAMP  into cur_d;
    raise notice 'Current date: %', cur_d;

	SELECT 8 INTO opt;
	
	CASE WHEN opt = 1 THEN
  		date_opt := 'day';
	WHEN opt = 2 THEN
  		date_opt := 'week';
	WHEN opt = 3 THEN
		date_opt := 'month';
	WHEN opt = 4 THEN
		date_opt := 'year';
	ELSE date_opt := 'day';
	END CASE;
	
	raise notice 'Value: %', date_trunc(date_opt, cur_d);
END $$;

```

##### variant 2

```
case p_opt_agg_res
when 1 then l_interval := 'day';
when 2 then l_interval := 'week';
when 3 then l_interval := 'month';
when 4 then l_interval := 'year';
else l_interval := 'day';
end case;
```

### Drop database

UPDATE pg_database SET datallowconn = 'false' WHERE datname = 'dbname';

SELECT pg_terminate_backend(pid)
FROM pg_stat_activity
WHERE datname = 'dbname';

drop database dbname;

### Script db schema DDL

pg_dumpall -h localhost -p 5432 -U postgres -s -O > "E:\backup4.sql"

-s : schema only
-O : do not include SET OWNER commands 

### Backup and restore 

##### 1 - Backup database
pg_dump -h localhost -p 5432 -U postgres -O -f "D:\\db-backups\\uniout_uat_2020_6_7_-_3_37.sql" uniout_uat

##### 2 - Restore database - db must be created first!
psql -f -U postgres "D:\\db-backups\\uniout_uat_2020_6_7_-_3_37.sql" uniout_staging_uat_backup_7_6_2020
