## Case:  Default PostgreSQL configuration + pgbenach load

```
[postgres@default ~]$ pgbench -i -s 50 pgtest
dropping old tables...
NOTICE:  table "pgbench_accounts" does not exist, skipping
NOTICE:  table "pgbench_branches" does not exist, skipping
NOTICE:  table "pgbench_history" does not exist, skipping
NOTICE:  table "pgbench_tellers" does not exist, skipping
creating tables...
generating data (client-side)...
5000000 of 5000000 tuples (100%) done (elapsed 6.19 s, remaining 0.00 s)
vacuuming...
creating primary keys...
done in 10.66 s (drop tables 0.00 s, create tables 0.02 s, client-side generate 6.21 s, vacuum 2.48 s, primary keys 1.95 s).

[postgres@default ~]$ pgbench -c 10 -j 2 -t 10000 pgtest
pgbench (14.10)
starting vacuum...end.
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 50
query mode: simple
number of clients: 10
number of threads: 2
number of transactions per client: 10000
number of transactions actually processed: 100000/100000
latency average = 9.737 ms
initial connection time = 10.015 ms
tps = 1027.057237 (without initial connection time)


# While pgbench was running, collected pg_gather data. (pgtest database)
[postgres@default pg_gather]$ psql -d pgtest -X -f gather.sql > out.tsv
[postgres@default pg_gather]$ psql -f gather_schema.sql -f out.tsv
[postgres@default pg_gather]$ psql -X -f gather_report.sql > Case1_default_pgbench_GatherReport.html

```


HTML report: https://github.com/lalitvc/pg_conf_2024_talk_ref/blob/main/pg_gather_use_cases_tests/HTML_Reports/Case1_default_pgbench_GatherReport.html

![image](https://github.com/lalitvc/pg_conf_2024_talk_ref/assets/7221144/192eed11-8c2b-4c81-9384-2f8d7a057cd1)

