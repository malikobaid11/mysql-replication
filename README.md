# mysql-replication

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

    - **Master-Slave Replication: One-way replication from Master to Slave.
    - **Master-Master Replication: Bi-directional replication between two servers.

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



