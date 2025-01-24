# MySQL Replication Setup

This repository contains detailed steps, configuration files, and necessary commands to set up **MySQL Master-Slave** and **Master-Master** replication.

## Table of Contents
1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Setup Steps](#setup-steps)
    - [Master-Slave Replication](#master-slave-replication)
    - [Master-Master Replication](#master-master-replication)

---

## Overview
MySQL replication is a process that allows you to synchronize databases between a Master and one or more Slave servers. This guide covers:

- **Master-Slave Replication**: One-way replication from Master to Slave.
- **Master-Master Replication**: Bi-directional replication between two servers.

## Prerequisites
1. Two servers:
   - Master: `172.16.1.100`
   - Slave: `172.16.2.5`
2. MySQL installed on both servers.
3. Allow MySQL traffic through the firewall:
   ```bash
   sudo ufw allow 3306/tcp
   ```

## Setup Steps

### Master-Slave Replication

#### 1. Configure the Master Server:

Open the MySQL configuration file:

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Update the following:

```ini
bind-address = 172.16.1.100
server-id = 1
log_bin = /var/log/mysql/mysql-bin.log
```

Restart MySQL and create a replication user:

```bash
sudo systemctl restart mysql
sudo mysql -u root -p
CREATE USER 'replica'@'172.16.2.5' IDENTIFIED BY 'ePlanet0!';
GRANT REPLICATION SLAVE ON *.* TO 'replica'@'172.16.2.5';
SHOW MASTER STATUS\G
```

Record the File and Position values (e.g., mysql-bin.000001, 619).


#### 2. Configure the Slave Server:

Open the MySQL configuration file:
 ```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

```ini
bind-address = 172.16.2.5
server-id = 2
log_bin = /var/log/mysql/mysql-bin.log
```

Restart MySQL and set up replication:

```bash
sudo systemctl restart mysql
sudo mysql
STOP SLAVE;
CHANGE MASTER TO
  MASTER_HOST='172.16.1.100',
  MASTER_USER='replica',
  MASTER_PASSWORD='password',
  MASTER_LOG_FILE='mysql-bin.000001',
  MASTER_LOG_POS=619;
START SLAVE;
```

#### 3. Test the Configuration:

On the Master server, create a test database:

```sql
sudo mysql -u root -p
CREATE DATABASE replicatest;
```

On the Slave server, verify the replication:

```sql
sudo mysql
SHOW DATABASES;
```
### Master-Master Replication

#### 1. Configure Master1 as a Slave for Master2

Stop the Slave process:

```sql
STOP SLAVE;
```
Update the Slave configuration to point to Master2:

```sql
CHANGE MASTER TO
  MASTER_HOST='172.16.2.5',
  MASTER_USER='replica',
  MASTER_PASSWORD='password',
  MASTER_LOG_FILE='mysql-bin.000008',
  MASTER_LOG_POS=1278;
START SLAVE;

```

#### 2. Configure Master2 as a Slave for Master1

Create a replication user on Master2:

```sql
CREATE USER 'replica'@'172.16.1.100' IDENTIFIED BY 'password';
GRANT REPLICATION SLAVE ON *.* TO 'replica'@'172.16.1.100';
```

Stop the Slave process:

```sql
STOP SLAVE;
```

Update the Slave configuration to point to Master1:

```sql
CHANGE MASTER TO
  MASTER_HOST='172.16.1.100',
  MASTER_USER='replica',
  MASTER_PASSWORD='password',
  MASTER_LOG_FILE='mysql-bin.000001',
  MASTER_LOG_POS=619;
START SLAVE;
```
