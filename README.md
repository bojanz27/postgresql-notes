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

###### SQL backup
pg_dump -h localhost -p 5432 -U postgres -O -f "D:\\db-backups\\uniout_uat_2020_6_7_-_3_37.sql" uniout_uat

###### Custom format backup (more flexible! - restore with pg_restore)
pg_dump -h localhost -p 5432 -U postgres -O -x -Fc -f "D:\db-backups\archiver_production_2020_9_29_-_9_17.dump" archiver_production

-O (--no-owner)
-F (--format)
	option c (custom) - for restoring with pg_restore. Compressed.

-x (--no-privileges) - Prevent dumping of access privileges (grant/revoke commands).


##### 2 - Restore database - db must be created first!

###### Restore from text file (sql)
psql -U postgres -f "D:\\db-backups\\uniout_uat_2020_6_7_-_3_37.sql" uniout_staging_uat_backup_7_6_2020

###### Restore from archive
pg_restore -O -x -1 -U postgres -d restore-target-db-name dumpfile.dump

-1
 --single-transaction
 Execute the restore as a single transaction.

