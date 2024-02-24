## Initial setup (500 direct connections to PostgreSQL)

**postgresql.conf**
max_connections=1200

**pgbench load:**  Using  500 client connections  
```
-c, --client=NUM         number of concurrent database clients 

[postgres@default ~]$ pgbench -c 500 -j 2 -t 5000  -S --no-vacuum pgtest
pgbench (15.5)
transaction type: <builtin: select only>
scaling factor: 100
query mode: simple
number of clients: 500
number of threads: 2
maximum number of tries: 1
number of transactions per client: 5000
number of transactions actually processed: 2500000/2500000
number of failed transactions: 0 (0.000%)
latency average = 10.940 ms
initial connection time = 474.975 ms
tps = 45705.110844 (without initial connection time)

```

**So many connections in idle state,**
![image](https://github.com/lalitvc/pg_conf_2024_talk_ref/assets/7221144/85914c19-bd73-4dfc-8944-4c21cd980e2d)


HTML Report: https://github.com/lalitvc/pg_conf_2024_talk_ref/blob/main/pg_gather_use_cases_tests/HTML_Reports/Connection_direct_pg_GatherReport.html 

## Possible solution: Use of connection pooling

**Installed  and configured pgbouncer with the following settings:**
```
pool_mode = transaction
max_client_conn = 1000
default_pool_size = 20
```
**Running same pgbench test with 500 clients via pgbouncer connection pooler:**
```
[postgres@default ~]$ pgbench -c 500 -j 2 -t 5000  -S --no-vacuum pgtest -h 127.0.0.1 -U app2  -p 6432 
Password: 
pgbench (15.5)
transaction type: <builtin: select only>
scaling factor: 100
query mode: simple
number of clients: 500
number of threads: 2
maximum number of tries: 1
number of transactions per client: 5000
number of transactions actually processed: 2500000/2500000
number of failed transactions: 0 (0.000%)
latency average = 9.840 ms
initial connection time = 156.784 ms
tps = 50812.951327 (without initial connection time)
[postgres@default ~]$ 
```
**Very few direct connections with connection via pgbouncer connection pooler**
![image](https://github.com/lalitvc/pg_conf_2024_talk_ref/assets/7221144/3104478b-679e-4a57-be4f-ad07c8f62fa5)

HTML Report: https://github.com/lalitvc/pg_conf_2024_talk_ref/blob/main/pg_gather_use_cases_tests/HTML_Reports/Connection_with_pgbouncer_GatherReport.html 

