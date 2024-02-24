## Initial setup (1000+ direct connection to PostgreSQL)

**postgresql.conf**
max_connections=1200

**pgbench load:**  Using  1000 client connections  
```
-c, --client=NUM         number of concurrent database clients 
-R, --rate=NUM           target rate in transactions per second

$ pgbench -c 1000 -j 2 -t 20000 --rate=300 -S --no-vacuum pgtest
```

**So many connections in idle state,**
- ClientRead event occurs when Aurora PostgreSQL is waiting to receive data from the client.
- CPU usage by these idle connections.
![image](https://github.com/lalitvc/pg_conf_2024_talk_ref/assets/7221144/fe02cc90-11ae-4264-8e88-fb5dff8885f1)

HTML Report: https://github.com/lalitvc/pg_conf_2024_talk_ref/blob/main/pg_gather_use_cases_tests/HTML_Reports/Connection_issues_GatherReport.html

## Possible solution: Use of connection pooling
