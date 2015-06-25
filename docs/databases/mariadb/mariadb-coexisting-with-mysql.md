MariaDB coexisting with Mysql
============

This tutorial will explain how can you have both MariaDB and Mysql running at the same time in Debian/Ubuntu Servers. For this we will need to use different ports or sockets. So let's get starting!


> **Note:** For purposes of this tutorial, it is assumed that you’ve followed the steps outlined in [Using Mysql Relational Databases on Debian 7](https://www.linode.com/docs/databases/mysql/using-mysql-relational-databases-on-debian-7-wheezy) or already have Mysql up and running!


----------


## Installation

1.We are going to perform the installation under the /opt directory, so be sure to join the directory first:
```
cd /opt
```

2.Download the MariaDB binary package (10.0.20-linux-x86_64 at the time of writing): 

```
wget https://downloads.mariadb.org/interstitial/mariadb-10.0.20/bintar-linux-x86_64/mariadb-10.0.20-linux-x86_64.tar.gz

```

3.Expand the archive file:
```
tar -xzvf mariadb-10.0.20-linux-x86_64.tar.gz
```

4.Create a symlink:
```
ln -s mariadb-10.0.20-linux-x86_64 mariadb
```
5.Create a directory called mariadb-data:
```
mkdir mariadb-data
```
6.Add a group called mariadb:
```
groupadd mariadb
```
7.Add a user called mariadb:
```
useradd -c "MariaDB Server" -d /opt/mariadb -g mariadb --system mariadb
```
8.Copy the my.cnf to the mariadb-data directory:
```
cp mariadb/support-files/my-medium.cnf mariadb-data/my.cnf
```

9.Setting the ownership:
```
chown mariadb. -R mariadb*
```
10.Now edit my.cnf and change the follow lines:
> **Note**: We are going to use port 3310 for MariaDB, Mysql runs by default on port 3306.  

```
[client]
port = 3310
socket = /opt/mariadb-data/mariadb.sock

[mysqld]
port = 3310
socket = /opt/mariadb-data/mariadb.sock
```



11.Run the mysql_install_db script:
```
./opt/mariadb/scripts/mysql_install_db --no-defaults --basedir=. --datadir=/opt/mariadb-data
```

12.Correct the permissions under /opt/mariadb-data:
```
cd /opt/mariadb-data ; chown mariadb. *
```

13.Then start the MariaDB daemon:

```
su mariadb -c './bin/mysqld_safe --defaults-file=/opt/mariadb-data/my.cnf --basedir=/opt/mariadb --datadir=/opt/mariadb-data --plugin-dir=/opt/mariadb/lib/plugin --user=mariadb --pid-file=/opt/mariadb-data/mariadbd.pid --language=/opt/mariadb/share/english --socket=/opt/mariadb-data/mariadb.sock --port=3310'
```

You can add this same command in /etc/rc.local to start the daemon on boot time.
