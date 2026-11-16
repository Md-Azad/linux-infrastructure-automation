# Linux Infrastructure Automation

An infrastructure automation project using Ansible
to manage Enterprise Linux and Ubuntu systems.

## Overview

This project deploys a containerized Flask task-management application across multiple Linux servers. Ansible automates server configuration and application deployment, while HAProxy and Keepalived provide load balancing and load-balancer failover.

The environment is built using KVM/libvirt virtual machines and includes AlmaLinux, Rocky Linux, Ubuntu, and RHEL-10.

## Tentative Features

### 1. Automated Linux Configuration

The `common` role automates common server administration tasks, including:

- Hostname and timezone configuration
- Package management
- User and group management
- SSH hardening
- Firewall configuration
- System resource limits
- Chrony time synchronization
- Network and service validation

### 2. Docker Deployment

- Installs and configures Docker on application servers.
- Deploys the Flask application container.
- Uses Gunicorn as the application server.
- Publishes the application on port `5000`.
- Configures Docker daemon settings and port security.

### 3. Application Deployment

- Deploys the same application image to both application servers.
- Connects both application instances to the PostgreSQL database.
- Supports application access through the load balancer.

### 4. Load Balancing with HAProxy

- Configures HAProxy on two Ubuntu servers.
- Distributes incoming HTTP traffic across both application servers.
- Uses round-robin load balancing.
- Performs backend health checks.

### 5. Load Balancer Failover with Keepalived

- Configures VRRP using Keepalived.
- Provides a shared virtual IP: `192.168.122.230`.
- Supports automatic VIP failover between the two load balancers.
- Keeps the application accessible through the VIP during a load-balancer failure.

### 6. Docker Port Security

Uses the Docker `DOCKER-USER` chain to restrict access to the application container port.

The intended policy allows traffic to port `5000` from the two authorized load balancers and blocks unauthorized sources.

The configuration is managed through Ansible and a systemd service.

## Architecture

```text
                       Client
                         |
                         |
                  Virtual IP (VIP)
                   192.168.122.230
                         |
              +----------+----------+
              |                     |
        Load Balancer 1       Load Balancer 2
        Ubuntu                 Ubuntu
        192.168.122.203        192.168.122.210
              |                     |
              +----------+----------+
                         |
                  HAProxy :80
                         |
               Round-robin balancing
                         |
              +----------+----------+
              |                     |
         App Server 1           App Server 2
         AlmaLinux 9.8          Rocky Linux 9.8
         192.168.122.154        192.168.122.199
         Docker :5000           Docker :5000
              |                     |
              +----------+----------+
                         |
                    PostgreSQL
                    RHEL 10
                  192.168.122.95
```

**Important:** The application servers and load balancers have redundancy, but PostgreSQL currently runs on a single server and remains a single point of failure.

## Infrastructure

| Component | Operating System | IP Address |
|---|---|---|
| App Server 1 | AlmaLinux 9.8 | `192.168.122.154` |
| App Server 2 | Rocky Linux 9.8 | `192.168.122.199` |
| Load Balancer 1 | Ubuntu | `192.168.122.203` |
| Load Balancer 2 | Ubuntu | `192.168.122.210` |
| PostgreSQL Server | RHEL 10 | `192.168.122.95` |
| Keepalived VIP | — | `192.168.122.230` |

## Technology Stack

- **Virtualization:** KVM, QEMU, libvirt
- **Configuration Management:** Ansible
- **Operating Systems:** AlmaLinux 9, Rocky Linux 9, RHEL 10, Ubuntu
- **Containerization:** Docker
- **Application:** Python, Flask, Gunicorn
- **Database:** PostgreSQL 16
- **Load Balancing:** HAProxy
- **High Availability:** Keepalived, VRRP
- **Networking and Security:** Linux firewall, Docker `DOCKER-USER` chain, iptables-nft
- **Version Control:** Git, GitHub
## Ansible Project Structure
- **Here will be Project Structure

