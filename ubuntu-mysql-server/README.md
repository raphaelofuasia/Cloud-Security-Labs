# Setting Up an Ubuntu Server with MySQL

This lab covers provisioning an Ubuntu server on a cloud compute instance and installing MySQL with remote access enabled, for training purposes.

## What I did

### 1. Provisioned the server
- Created a new Ubuntu 20.04 compute instance (1 vCPU instance, 128 GB SSD system disk), in a dedicated availability zone and VPC/subnet.
- Set up password-based authentication for the root user during instance creation.

### 2. Enabled SSH access
- Connected to the instance via the cloud console's built-in VNC access.
- Edited `/etc/ssh/sshd_config` (and any override files under `/etc/ssh/sshd_config.d/`) to enable password authentication and confirm the SSH listener was bound correctly.
- Restarted the SSH daemon to apply the changes.

### 3. Installed and configured MySQL
- Updated system packages (`apt update && apt upgrade`).
- Temporarily pointed DNS at public resolvers to ensure package downloads succeeded, then reverted this once installation was complete.
- Installed MySQL via the official APT configuration package, then installed `mysql-server`.
- Ran `mysql_secure_installation` to set a strong root password, remove anonymous users, remove the test database, and reload privilege tables.
- Created a dedicated MySQL user with scoped privileges (rather than continuing to use root for application access).
- Updated `bind-address` in `mysqld.cnf` to allow remote connections, and restarted the MySQL service.

### 4. Verified the setup
- Confirmed SSH access from a separate jump host.
- Confirmed MySQL login using the new (non-root) user account.

## Security considerations

This lab intentionally walks through a permissive configuration to demonstrate the mechanics of remote server and database access. I would **not** carry these settings into a production build. Specifically:

- **Root SSH login and password authentication** were enabled here for lab convenience. In production, I'd disable `PermitRootLogin`, disable password authentication entirely, and use key-based auth only.
- **Granting `ALL PRIVILEGES` to a MySQL user** is broader than any real application needs. In production, I'd scope grants to the specific databases/tables the application requires (principle of least privilege).
- **Binding MySQL to `0.0.0.0`** exposes it to any network that can reach the instance. In production, I'd restrict MySQL to a private subnet with no public IP, and only allow connections from application servers via security group rules — not directly from the internet.
- **Security group rules**: this lab opens inbound 22 (SSH) and 3306 (MySQL). In production, both should be restricted to specific source IP ranges (e.g. a bastion host or VPN range), not left open to `0.0.0.0/0`.
