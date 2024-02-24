
## Initial Data for Setup:
```
CREATE DATABASE pgtest;

$ pgbench -i -s 100  --no-vacuum pgtest

ALTER TABLE pgbench_accounts ADD COLUMN "data" BYTEA NOT NULL DEFAULT (E'x...');
ALTER TABLE pgbench_accounts ADD COLUMN "c1" varchar(300) NOT NULL DEFAULT ('The PostgreSQL Varchar data type is used to store characters of indefinite length based on the parameter n;);

create index ixd_bid_abalance on pgbench_accounts(bid,abalance);
create index ixd_abalance_bid on pgbench_accounts(abalance,bid);
create index ixd_filler on pgbench_accounts(filler);
create index ixd_c1 on pgbench_accounts(c1);
create index ixd_c1_new on pgbench_accounts(c1);
create index ixd_c1_updated on pgbench_accounts(c1);
create index ixd_filler_c1 on pgbench_accounts(filler,c1);
create index ixd_filler_c1_new on pgbench_accounts(filler,c1);
create index ixd_filler_c1_updated on pgbench_accounts(filler,c1);

pgtest=# \d pgbench_accounts
                                                                              Tabl
e "public.pgbench_accounts"
  Column  |          Type          | Collation | Nullable |                       
                                      Default                                     
                        
----------+------------------------+-----------+----------+-----------------------
----------------------------------------------------------------------------------
------------------------
 aid      | integer                |           | not null | 
 bid      | integer                |           |          | 
 abalance | integer                |           |          | 
 filler   | character(84)          |           |          | 
 data     | bytea                  |           | not null | '\x782e2e2e'::bytea
 c1       | character varying(300) |           | not null | 'The PostgreSQL Varcha
r data type is used to store characters of indefinite length based on the paramete
r n'::character varying
Indexes:
    "pgbench_accounts_pkey" PRIMARY KEY, btree (aid)
    "ixd_abalance_bid" btree (abalance, bid)
    "ixd_bid_abalance" btree (bid, abalance)
    "ixd_c1" btree (c1)
    "ixd_c1_new" btree (c1)
    "ixd_c1_updated" btree (c1)
    "ixd_filler" btree (filler)
    "ixd_filler_c1" btree (filler, c1)
    "ixd_filler_c1_new" btree (filler, c1)
    "ixd_filler_c1_updated" btree (filler, c1)
```

###pgbench runs:

**Session1:**
```
[postgres@default ~]$ pgbench -c 10 -j 2 -t 20000 --no-vacuum pgtest
pgbench (15.5)

transaction type: <builtin: TPC-B (sort of)>
scaling factor: 100
query mode: simple
number of clients: 10
number of threads: 2
maximum number of tries: 1
number of transactions per client: 20000
number of transactions actually processed: 200000/200000
number of failed transactions: 0 (0.000%)
latency average = 17.998 ms
initial connection time = 15.514 ms
tps = 555.632432 (without initial connection time)
[postgres@default ~]$ 
```
**Session2:**

```
[postgres@default ~]$ pgbench -c 10 -j 2 -t 20000 -N -S --no-vacuum pgtest
pgbench (15.5)
transaction type: multiple scripts
scaling factor: 100
query mode: simple
number of clients: 10
number of threads: 2
maximum number of tries: 1
number of transactions per client: 20000
number of transactions actually processed: 200000/200000
number of failed transactions: 0 (0.000%)
latency average = 8.165 ms
initial connection time = 10.780 ms
tps = 1224.689000 (without initial connection time)
SQL script 1: <builtin: simple update>
 - weight: 1 (targets 50.0% of total)
 - 99599 transactions (49.8% of total, tps = 609.888999)
 - number of failed transactions: 0 (0.000%)
 - latency average = 15.645 ms
 - latency stddev = 10.840 ms
SQL script 2: <builtin: select only>
 - weight: 1 (targets 50.0% of total)
 - 100156 transactions (50.1% of total, tps = 613.299758)
 - number of failed transactions: 0 (0.000%)
 - latency average = 0.557 ms
 - latency stddev = 1.011 ms
[postgres@default ~]$ 

```
HTML Report: https://github.com/lalitvc/pg_conf_2024_talk_ref/blob/main/pg_gather_use_cases_tests/HTML_Reports/Tables_Indexes_stats_issues_GatherReport.html

![image](https://github.com/lalitvc/pg_conf_2024_talk_ref/assets/7221144/466c2d6b-815b-489a-8237-ac0344778ee9)

