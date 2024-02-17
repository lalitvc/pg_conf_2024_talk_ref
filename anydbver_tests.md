Anydbver: https://github.com/ihanick/anydbver

Test examples for PosgreSQL setup using **Anydbver**

# Postgresql single node:
```
./anydbver deploy os:el7 pg:15.5
./anydbver deploy os:el8 pg:14
./anydbver deploy os:el7 ppg:13.5
./anydbver deploy os:el8 ppg:13.5
./anydbver deploy node0 pg:latest
```

### Example:
```
./anydbver deploy node0 pg:latest

2024-02-04 12:06:31,274 INFO Found node lalit-default with ip 172.26.0.2
Warning: Permanently added '172.26.0.2' (ECDSA) to the list of known hosts.
Connected to default via ssh
2024-02-04 12:06:31,481 INFO Node default:  db_user='postgres' db_password='verysecretpassword1^' start_db='1' postgresql_version='15' cluster_name='cluster1'


$ docker ps
CONTAINER ID   IMAGE                             COMMAND                  CREATED         STATUS         PORTS                                       NAMES
34e5e5ccfe7a   rockylinux:8-sshd-systemd-lalit   "/usr/lib/systemd/sy…"   3 minutes ago   Up 3 minutes   22/tcp                                      lalit-default

### Login options
./anydbver ssh default
or 
$ docker exec -it lalit-default /bin/bash
 
[root@default /]# sudo su - postgres
[postgres@default ~]$ psql
psql (15.5)
Type "help" for help.

postgres=# \l
                                             List of databases
   Name    |  Owner   | Encoding | Collate |  Ctype  | ICU Locale | Locale Provider |   Access privileges   
-----------+----------+----------+---------+---------+------------+-----------------+-----------------------
 postgres  | postgres | UTF8     | C.UTF-8 | C.UTF-8 |            | libc            | 
 template0 | postgres | UTF8     | C.UTF-8 | C.UTF-8 |            | libc            | =c/postgres          +
           |          |          |         |         |            |                 | postgres=CTc/postgres
 template1 | postgres | UTF8     | C.UTF-8 | C.UTF-8 |            | libc            | =c/postgres          +
           |          |          |         |         |            |                 | postgres=CTc/postgres
(3 rows)

$ ps -ef | grep -i post
postgres    1780       1  0 06:38 ?        00:00:00 /usr/pgsql-15/bin/postmaster -D /var/lib/pgsql/15/data/
postgres    1781    1780  0 06:38 ?        00:00:00 postgres: logger 
postgres    1782    1780  0 06:38 ?        00:00:00 postgres: checkpointer 
postgres    1783    1780  0 06:38 ?        00:00:00 postgres: background writer 
postgres    1785    1780  0 06:38 ?        00:00:00 postgres: walwriter 
postgres    1786    1780  0 06:38 ?        00:00:00 postgres: autovacuum launcher 
postgres    1787    1780  0 06:38 ?        00:00:00 postgres: logical replication launcher 

[postgres@default data]$ pwd
/var/lib/pgsql/15/data
[postgres@default data]$ ls -l
total 136
-rw------- 1 postgres postgres     3 Feb  4 06:38 PG_VERSION
drwx------ 5 postgres postgres  4096 Feb  4 06:38 base
-rw------- 1 postgres postgres    30 Feb  4 06:38 current_logfiles
drwx------ 2 postgres postgres  4096 Feb  4 06:42 global
drwx------ 2 postgres postgres  4096 Feb  4 06:38 log
drwx------ 2 postgres postgres  4096 Feb  4 06:38 pg_commit_ts
drwx------ 2 postgres postgres  4096 Feb  4 06:38 pg_dynshmem
-rw------- 1 postgres postgres  4967 Feb  4 06:38 pg_hba.conf
-rw------- 1 postgres postgres  1636 Feb  4 06:38 pg_ident.conf
drwx------ 4 postgres postgres  4096 Feb  4 06:43 pg_logical
drwx------ 4 postgres postgres  4096 Feb  4 06:38 pg_multixact
drwx------ 2 postgres postgres  4096 Feb  4 06:38 pg_notify
drwx------ 2 postgres postgres  4096 Feb  4 06:38 pg_replslot
drwx------ 2 postgres postgres  4096 Feb  4 06:38 pg_serial
drwx------ 2 postgres postgres  4096 Feb  4 06:38 pg_snapshots
drwx------ 2 postgres postgres  4096 Feb  4 06:38 pg_stat
drwx------ 2 postgres postgres  4096 Feb  4 06:38 pg_stat_tmp
drwx------ 2 postgres postgres  4096 Feb  4 06:38 pg_subtrans
drwx------ 2 postgres postgres  4096 Feb  4 06:38 pg_tblspc
drwx------ 2 postgres postgres  4096 Feb  4 06:38 pg_twophase
drwx------ 3 postgres postgres  4096 Feb  4 06:38 pg_wal
drwx------ 2 postgres postgres  4096 Feb  4 06:38 pg_xact
-rw------- 1 postgres postgres    88 Feb  4 06:38 postgresql.auto.conf
-rw------- 1 postgres postgres 29447 Feb  4 06:38 postgresql.conf
-rw------- 1 postgres postgres    58 Feb  4 06:38 postmaster.opts
-rw------- 1 postgres postgres    95 Feb  4 06:38 postmaster.pid

```

# Streaming Replication 
```
$ time ./anydbver deploy node0 pg:latest,wal=logical node1 pg:latest,primary=node0,wal=logical

PLAY RECAP ***********************************************************************************************************************************************************
lalit.default              : ok=29   changed=17   unreachable=0    failed=0    skipped=58   rescued=0    ignored=0   
lalit.node1                : ok=30   changed=18   unreachable=0    failed=0    skipped=57   rescued=0    ignored=0   


real	2m8.890s
user	0m49.271s
sys	0m4.945s



$ ./anydbver deploy node0 pg:15,wal=logical node1 pg:15,primary=node0,wal=logical

2024-02-04 12:19:31,874 INFO Trying to find os: rocky8,node0=rocky8,node1=rocky8, for node0
2024-02-04 12:19:31,875 INFO Found OS for node0: rocky8
2024-02-04 12:19:31,878 INFO /bin/sh -c "mkdir -p '/home/lalit/git/anydbver/data/nfs'"
2024-02-04 12:19:31,880 INFO docker network create lalit-anydbver
2024-02-04 12:19:31,944 INFO docker run --name lalit-default --platform linux/amd64 -v /home/lalit/git/anydbver/data/nfs:/nfs -d --cgroupns=host --tmpfs /tmp --network lalit-anydbver --tmpfs /run --tmpfs /run/lock -v /sys/fs/cgroup:/sys/fs/cgroup --hostname default rockylinux:8-sshd-systemd-lalit
2024-02-04 12:19:32,360 INFO docker inspect -f {{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}} lalit-default
2024-02-04 12:19:33,394 INFO Found node lalit-default with ip 172.27.0.2
Warning: Permanently added '172.27.0.2' (ECDSA) to the list of known hosts.
Connected to default via ssh
2024-02-04 12:19:33,581 INFO Trying to find os: rocky8,node0=rocky8,node1=rocky8, for node1
2024-02-04 12:19:33,582 INFO Found OS for node1: rocky8
2024-02-04 12:19:33,582 INFO docker network create lalit-anydbver
2024-02-04 12:19:33,616 INFO docker run --name lalit-node1 --platform linux/amd64 -v /home/lalit/git/anydbver/data/nfs:/nfs -d --cgroupns=host --tmpfs /tmp --network lalit-anydbver --tmpfs /run --tmpfs /run/lock -v /sys/fs/cgroup:/sys/fs/cgroup --hostname node1 rockylinux:8-sshd-systemd-lalit
2024-02-04 12:19:34,082 INFO docker inspect -f {{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}} lalit-node1
2024-02-04 12:19:35,115 INFO Found node lalit-node1 with ip 172.27.0.3
Warning: Permanently added '172.27.0.3' (ECDSA) to the list of known hosts.
Connected to node1 via ssh
2024-02-04 12:19:35,307 INFO Node default:  db_user='postgres' db_password='verysecretpassword1^' start_db='1' postgresql_version='15' db_opts_file='postgresql/logical.conf' cluster_name='cluster1'
2024-02-04 12:19:35,308 INFO Node node1:  db_user='postgres' db_password='verysecretpassword1^' start_db='1' postgresql_version='15' db_opts_file='postgresql/logical.conf' master_ip='172.27.0.2' cluster_name='cluster1'

PLAY RECAP ***********************************************************************************************************************************************************
lalit.default              : ok=29   changed=17   unreachable=0    failed=0    skipped=58   rescued=0    ignored=0   
lalit.node1                : ok=30   changed=18   unreachable=0    failed=0    skipped=57   rescued=0    ignored=0

$ sudo docker ps
[sudo] password for lalit: 
CONTAINER ID   IMAGE                             COMMAND                  CREATED          STATUS          PORTS                                       NAMES
dd9317b10b8d   rockylinux:8-sshd-systemd-lalit   "/usr/lib/systemd/sy…"   14 minutes ago   Up 14 minutes   22/tcp                                      lalit-node1
35c16085d267   rockylinux:8-sshd-systemd-lalit   "/usr/lib/systemd/sy…"   14 minutes ago   Up 14 minutes   22/tcp                                      lalit-default



$ ./anydbver ssh node1
[root@node1 ~]# ps -ef | grep -i post
postgres    1903       1  0 06:51 ?        00:00:00 /usr/pgsql-15/bin/postmaster -D /var/lib/pgsql/15/data/
postgres    1904    1903  0 06:51 ?        00:00:00 postgres: logger 
postgres    1905    1903  0 06:51 ?        00:00:00 postgres: checkpointer 
postgres    1906    1903  0 06:51 ?        00:00:00 postgres: background writer 
postgres    1907    1903  0 06:51 ?        00:00:00 postgres: startup recovering 000000010000000000000003
postgres    1908    1903  0 06:51 ?        00:00:01 postgres: walreceiver streaming 0/30453F0
root        2163    2119  0 07:10 pts/0    00:00:00 grep --color=auto -i post
[root@node1 ~]# sudo su - postgres
[postgres@node1 ~]$ env
LANG=C.UTF-8
HISTCONTROL=ignoredups
HOSTNAME=node1
USER=postgres
PWD=/var/lib/pgsql
HOME=/var/lib/pgsql
PGDATA=/var/lib/pgsql/15/data
MAIL=/var/spool/mail/postgres
SHELL=/bin/bash
TERM=xterm-256color
SHLVL=1
LOGNAME=postgres
PATH=/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin
HISTSIZE=1000
LESSOPEN=||/usr/bin/lesspipe.sh %s
_=/usr/bin/env


[postgres@node1 ~]$ psql
psql (15.5)
Type "help" for help.

postgres=# select pg_is_in_recovery();
 pg_is_in_recovery 
-------------------
 t
(1 row)

postgres=# \x
Expanded display is on.
postgres=# select * from pg_stat_wal_receiver;
-[ RECORD 1 ]---------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
pid                   | 1908
status                | streaming
receive_start_lsn     | 0/3000000
receive_start_tli     | 1
written_lsn           | 0/30453F0
flushed_lsn           | 0/30453F0
received_tli          | 1
last_msg_send_time    | 2024-02-04 07:12:58.801097+00
last_msg_receipt_time | 2024-02-04 07:12:58.801119+00
latest_end_lsn        | 0/30453F0
latest_end_time       | 2024-02-04 06:56:56.098313+00
slot_name             | slot_2887450627
sender_host           | 172.27.0.2
sender_port           | 5432
conninfo              | user=postgres password=******** channel_binding=prefer dbname=replication host=172.27.0.2 port=5432 fallback_application_name=walreceiver sslmode=prefer sslcompression=0 sslsni=1 ssl_min_protocol_version=TLSv1.2 gssencmode=prefer krbsrvname=postgres target_session_attrs=any

[postgres@node1 data]$ pwd
/var/lib/pgsql/15/data
[postgres@node1 data]$ cat postgresql.auto.conf
# Do not edit this file manually!
# It will be overwritten by the ALTER SYSTEM command.
primary_conninfo = 'user=postgres password=''verysecretpassword1^'' channel_binding=prefer host=172.27.0.2 port=5432 sslmode=prefer sslcompression=0 sslsni=1 ssl_min_protocol_version=TLSv1.2 gssencmode=prefer krbsrvname=postgres target_session_attrs=any'
primary_slot_name = 'slot_2887450627'


$ ./anydbver ssh default
Last login: Sun Feb  4 07:08:09 2024 from 172.27.0.1
[root@default ~]# psql
psql (15.5)
Type "help" for help.

postgres=# \x
Expanded display is on.
postgres=# select * from pg_stat_replication;
-[ RECORD 1 ]----+------------------------------
pid              | 1857
usesysid         | 10
usename          | postgres
application_name | walreceiver
client_addr      | 172.27.0.3
client_hostname  | 
client_port      | 43496
backend_start    | 2024-02-04 06:51:52.759874+00
backend_xmin     | 
state            | streaming
sent_lsn         | 0/30453F0
write_lsn        | 0/30453F0
flush_lsn        | 0/30453F0
replay_lsn       | 0/30453F0
write_lag        | 
flush_lag        | 
replay_lag       | 
sync_priority    | 0
sync_state       | async
reply_time       | 2024-02-04 07:16:39.363046+00
```


# Logical replication  [TODO not yet implemented]



# Patroni cluster
```
$ ./anydbver deploy pg patroni node1 pg:master=node0 patroni:master=node0 node2 pg:master=node0 patroni:master=node0

PLAY RECAP ***********************************************************************************************************************************************************
lalit.default              : ok=35   changed=22   unreachable=0    failed=0    skipped=58   rescued=0    ignored=0   
lalit.node1                : ok=36   changed=23   unreachable=0    failed=0    skipped=57   rescued=0    ignored=0   
lalit.node2                : ok=36   changed=23   unreachable=0    failed=0    skipped=57   rescued=0    ignored=0  


$ ./anydbver ssh node1

[root@node1 ~]# sudo su - postgres

[postgres@node1 ~]$ patronictl -c /etc/patroni/cluster117345-1.yml  list
+ Cluster: stampede (7331672665328420527) -+-----------+----+-----------+-----------------+
| Member          | Host         | Role    | State     | TL | Lag in MB | Pending restart |
+-----------------+--------------+---------+-----------+----+-----------+-----------------+
| cluster1-0      | 192.168.16.2 | Leader  | running   |  1 |           | *               |
| cluster117345-1 | 192.168.16.3 | Replica | streaming |  1 |         0 |                 |
| cluster13371-2  | 192.168.16.4 | Replica | streaming |  1 |         0 |                 |
+-----------------+--------------+---------+-----------+----+-----------+-----------------+
[postgres@node1 ~]$ psql
psql (15.5)
Type "help" for help.
                      
postgres=# \x
Expanded display is on.
postgres=# select * from pg_stat_wal_receiver;
-[ RECORD 1 ]---------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
pid                   | 2827
status                | streaming
receive_start_lsn     | 0/5000000
receive_start_tli     | 1
written_lsn           | 0/6000000
flushed_lsn           | 0/6000000
received_tli          | 1
last_msg_send_time    | 2024-02-04 09:29:52.782923+00
last_msg_receipt_time | 2024-02-04 09:29:52.783009+00
latest_end_lsn        | 0/6000000
latest_end_time       | 2024-02-04 09:24:52.124683+00
slot_name             | cluster117345_1
sender_host           | 192.168.16.2
sender_port           | 5432
conninfo              | user=replicator passfile=/tmp/pgpass0 channel_binding=prefer dbname=replication host=192.168.16.2 port=5432 application_name=cluster117345-1 fallback_application_name=walreceiver sslmode=prefer sslcompression=0 sslsni=1 ssl_min_protocol_version=TLSv1.2 gssencmode=prefer krbsrvname=postgres target_session_attrs=any


[postgres@node1 ~]$ patronictl -c /etc/patroni/cluster117345-1.yml  switchover
Current cluster topology
+ Cluster: stampede (7331672665328420527) -+-----------+----+-----------+-----------------+
| Member          | Host         | Role    | State     | TL | Lag in MB | Pending restart |
+-----------------+--------------+---------+-----------+----+-----------+-----------------+
| cluster1-0      | 192.168.16.2 | Leader  | running   |  1 |           | *               |
| cluster117345-1 | 192.168.16.3 | Replica | streaming |  1 |         0 |                 |
| cluster13371-2  | 192.168.16.4 | Replica | streaming |  1 |         0 |                 |
+-----------------+--------------+---------+-----------+----+-----------+-----------------+
Primary [cluster1-0]: 
Candidate ['cluster117345-1', 'cluster13371-2'] []: cluster117345-1
When should the switchover take place (e.g. 2024-02-04T10:32 )  [now]:  
Are you sure you want to switchover cluster stampede, demoting current leader cluster1-0? [y/N]: y
2024-02-04 09:33:11.73975 Successfully switched over to "cluster117345-1"
+ Cluster: stampede (7331672665328420527) -+-----------+----+-----------+-----------------+
| Member          | Host         | Role    | State     | TL | Lag in MB | Pending restart |
+-----------------+--------------+---------+-----------+----+-----------+-----------------+
| cluster1-0      | 192.168.16.2 | Replica | stopped   |    |   unknown | *               |
| cluster117345-1 | 192.168.16.3 | Leader  | running   |  1 |           |                 |
| cluster13371-2  | 192.168.16.4 | Replica | streaming |  1 |         0 |                 |
+-----------------+--------------+---------+-----------+----+-----------+-----------------+
[postgres@node1 ~]$ patronictl -c /etc/patroni/cluster117345-1.yml  list
+ Cluster: stampede (7331672665328420527) -+---------------------+----+-----------+-----------------+
| Member          | Host         | Role    | State               | TL | Lag in MB | Pending restart |
+-----------------+--------------+---------+---------------------+----+-----------+-----------------+
| cluster1-0      | 192.168.16.2 | Replica | stopped             |    |   unknown | *               |
| cluster117345-1 | 192.168.16.3 | Leader  | running             |  2 |           |                 |
| cluster13371-2  | 192.168.16.4 | Replica | in archive recovery |  1 |         0 |                 |
+-----------------+--------------+---------+---------------------+----+-----------+-----------------+
[postgres@node1 ~]$ patronictl -c /etc/patroni/cluster117345-1.yml  list
+ Cluster: stampede (7331672665328420527) -+-----------+----+-----------+
| Member          | Host         | Role    | State     | TL | Lag in MB |
+-----------------+--------------+---------+-----------+----+-----------+
| cluster1-0      | 192.168.16.2 | Replica | streaming |  2 |         0 |
| cluster117345-1 | 192.168.16.3 | Leader  | running   |  2 |           |
| cluster13371-2  | 192.168.16.4 | Replica | streaming |  2 |         0 |
+-----------------+--------------+---------+-----------+----+-----------+
[postgres@node1 ~]$ 
```

# Kubernetes Operator 
```
$ ./anydbver deploy k3d:latest k8s-pg:2.2.0

$ docker ps
CONTAINER ID   IMAGE                            COMMAND                  CREATED        STATUS        PORTS                                       NAMES
c32f39992d77   ghcr.io/k3d-io/k3d-proxy:5.4.9   "/bin/sh -c nginx-pr…"   46 hours ago   Up 46 hours   80/tcp, 0.0.0.0:39453->6443/tcp             k3d-lalit-cluster1-serverlb
9dfc59b456f7   rancher/k3s:latest               "/bin/k3s agent"         46 hours ago   Up 46 hours                                               k3d-lalit-cluster1-agent-1
9caeb16a9c33   rancher/k3s:latest               "/bin/k3s agent"         46 hours ago   Up 46 hours                                               k3d-lalit-cluster1-agent-0
ac541532aa30   rancher/k3s:latest               "/bin/k3s server --c…"   46 hours ago   Up 46 hours                                               k3d-lalit-cluster1-server-0

$ kubectl get pods -n pgo
NAME                                           READY   STATUS      RESTARTS   AGE
percona-postgresql-operator-657f794b5b-z9rcb   1/1     Running     0          45h
cluster1-pgbouncer-847fdc6487-f4qv8            2/2     Running     0          45h
cluster1-pgbouncer-847fdc6487-kh5lw            2/2     Running     0          45h
cluster1-pgbouncer-847fdc6487-z5fwz            2/2     Running     0          45h
cluster1-repo-host-0                           2/2     Running     0          45h
cluster1-backup-c6nc-tg68w                     0/1     Completed   0          45h
cluster1-instance1-qv9z-0                      4/4     Running     0          45h
cluster1-instance1-6hxd-0                      4/4     Running     0          45h
cluster1-instance1-zjdq-0                      4/4     Running     0          45h

$ kubectl -n pgo exec -it cluster1-instance1-qv9z-0 -- /bin/bash 
Defaulted container "database" out of: database, replication-cert-copy, pgbackrest, pgbackrest-config, postgres-startup (init), nss-wrapper-init (init)
bash-4.4$ patronictl list
+ Cluster: cluster1-ha -----+-----------------------------------------+---------+---------+----+-----------+
| Member                    | Host                                    | Role    | State   | TL | Lag in MB |
+---------------------------+-----------------------------------------+---------+---------+----+-----------+
| cluster1-instance1-6hxd-0 | cluster1-instance1-6hxd-0.cluster1-pods | Replica | running |  1 |         0 |
| cluster1-instance1-qv9z-0 | cluster1-instance1-qv9z-0.cluster1-pods | Leader  | running |  1 |           |
| cluster1-instance1-zjdq-0 | cluster1-instance1-zjdq-0.cluster1-pods | Replica | running |  1 |         0 |
+---------------------------+-----------------------------------------+---------+---------+----+-----------+
bash-4.4$ 

$ kubectl get services -n pgo
NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
cluster1-pods        ClusterIP   None            <none>        <none>     46h
cluster1-ha          ClusterIP   10.43.175.67    <none>        5432/TCP   46h
cluster1-primary     ClusterIP   None            <none>        5432/TCP   46h
cluster1-replicas    ClusterIP   10.43.1.134     <none>        5432/TCP   46h
cluster1-ha-config   ClusterIP   None            <none>        <none>     46h
cluster1-pgbouncer   ClusterIP   10.43.226.211   <none>        5432/TCP   46h
```
