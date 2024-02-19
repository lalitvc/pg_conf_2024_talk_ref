
## Example of Generating pg_gather report HTML on test server
```
[postgres@default ~]$ git clone https://github.com/jobinau/pg_gather
Cloning into 'pg_gather'...
remote: Enumerating objects: 1699, done.
remote: Counting objects: 100% (236/236), done.
remote: Compressing objects: 100% (79/79), done.
remote: Total 1699 (delta 168), reused 222 (delta 157), pack-reused 1463
Receiving objects: 100% (1699/1699), 671.22 KiB | 4.60 MiB/s, done.
Resolving deltas: 100% (1234/1234), done.
[postgres@default ~]$ cd pg_gather/
[postgres@default pg_gather]$ git pull
Already up to date.
```
#### Collecting performance and configuration data from PostgreSQL databases.  
```
[postgres@default pg_gather]$ psql -d postgres -X -f gather.sql > out.tsv
```

#### Importing collected data
```
[postgres@default pg_gather]$ psql -f gather_schema.sql -f out.tsv
**Dropping pg_gather tables**
**Creating pg_gather tables**
Query buffer reset (cleared).
COPY 1
Tuples only is on.
Query buffer reset (cleared).
COPY 1
COPY 8
COPY 7000
COPY 3
COPY 2
COPY 345
COPY 42
COPY 0
COPY 76
COPY 155
COPY 104
COPY 35
COPY 38
COPY 0
COPY 4
COPY 1
COPY 12
COPY 0
COPY 2
COPY 2
COPY 1
COPY 1
COPY 1
COPY 7000
COPY 1
[postgres@default pg_gather]$ 
```

#### Generating HTML report
```
$ psql -X -f gather_report.sql > GatherReport.html
```

