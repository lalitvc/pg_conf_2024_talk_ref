## Initial Data for setup

**Session1:**
```
pgtest=# SELECT pg_backend_pid();
-[ RECORD 1 ]--+------
pg_backend_pid | 13041

pgtest=# BEGIN;
BEGIN
pgtest=*# UPDATE pgbench_accounts SET bid = bid + 10 WHERE aid < 3;
UPDATE 2
pgtest=*# 

```

**Session2:**
```
pgtest=# SELECT pg_backend_pid();
 pg_backend_pid 
----------------
          13278
(1 row)

pgtest=# BEGIN;
BEGIN
pgtest=*# UPDATE pgbench_accounts SET bid = bid + 20 WHERE aid = 1;

```

**session3:**
```
pgtest=# SELECT pg_backend_pid();
 pg_backend_pid 
----------------
          13290
(1 row)

pgtest=# BEGIN;
BEGIN
pgtest=*# SELECT bid FROM pgbench_accounts where aid=2 FOR UPDATE;
```

**session4:**
```
pgtest=# SELECT pg_backend_pid();
 pg_backend_pid 
----------------
          15103
(1 row)
pgtest=# BEGIN;
BEGIN
pgtest=*# UPDATE pgbench_accounts SET bid = bid + 30 where aid=2;
```


## Blokers can be seen in pg_gather report:
- **transactionid** event occurs when a transaction is waiting for a row-level lock.
- **tuple** event occurs when a backend process is waiting to acquire a lock on a tuple.

![image](https://github.com/lalitvc/pg_conf_2024_talk_ref/assets/7221144/a0b5949b-1205-4dfe-8340-c68dc5b0ed8b)
HTML Report: https://github.com/lalitvc/pg_conf_2024_talk_ref/blob/main/pg_gather_use_cases_tests/HTML_Reports/Session_blocker_issue_GatherReport.html

## Possible solution:
terminate_backend/rollback/commit

Session pid#13290,13278  blocked by pid#13041
```
postgres=# \c pgtest
You are now connected to database "pgtest" as user "postgres".
pgtest=# SELECT pg_terminate_backend(13041);
 pg_terminate_backend 
----------------------
 t
(1 row)

pgtest=#
```

Session_pid#15103 blocked by pid#13290
```
pgtest=# SELECT pg_backend_pid();
 pg_backend_pid 
----------------
          13290
(1 row)

pgtest=# BEGIN;
BEGIN
pgtest=*# SELECT bid FROM pgbench_accounts where aid=2 FOR UPDATE;

 bid 
-----
   1
(1 row)

pgtest=*# 
pgtest=*# rollback;
ROLLBACK
pgtest=# 

```
