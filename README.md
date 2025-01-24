# MySQL Replication Setup

This repository contains detailed steps, configuration files, and necessary commands to set up **MySQL Master-Slave** and **Master-Master** replication.

## Table of Contents
1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Setup Steps](#setup-steps)
    - [Master-Slave Replication](#master-slave-replication)
    - [Master-Master Replication](#master-master-replication)
4. [Configuration Files](#configuration-files)

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

Edit mysqld.cnf to enable logging and assign server-id:

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

Record File and Position values.


#### 2. Configure the Slave Server:

Edit mysqld.cnf:

```ini
bind-address = 172.16.2.5
server-id = 2
log_bin = /var/log/mysql/mysql-bin.log
```

Restart MySQL and set up replication:

```bash
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




