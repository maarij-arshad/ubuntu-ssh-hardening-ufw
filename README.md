# Ubuntu SSH Hardening & UFW Configuration

A hands-on Linux security hardening lab focused on securing SSH access to an Ubuntu Server using SSH key-based authentication and UFW firewall restrictions.

## Overview

In this lab, I hardened an Ubuntu Server by:

* Generating an Ed25519 SSH key pair
* Deploying the public key to the Ubuntu Server
* Verifying SSH key-based authentication
* Disabling direct root login over SSH
* Disabling password-based SSH authentication
* Disabling empty-password authentication
* Configuring UFW with a default-deny incoming policy
* Allowing SSH access only from the trusted laboratory subnet
* Validating the final SSH and firewall configuration

## Lab Architecture

```text
Kali Linux
192.168.10.100
      │
      │ SSH / TCP 22
      ▼
Ubuntu Server
192.168.10.20
      │
      │
      ▼
UFW Firewall
Allow TCP/22
from 192.168.10.0/24
```

## Environment

| Component      | Configuration        |
| -------------- | -------------------- |
| Server         | Ubuntu Server        |
| Client         | Kali Linux           |
| Ubuntu IP      | 192.168.10.20        |
| Kali IP        | 192.168.10.100       |
| Lab subnet     | 192.168.10.0/24      |
| SSH            | TCP/22               |
| Authentication | Ed25519 SSH key pair |
| Firewall       | UFW                  |

## SSH Key Pair

An Ed25519 key pair was generated on the Kali Linux client:

```bash
ssh-keygen -t ed25519
```

The key pair consists of:

```text
id_ed25519      # Private key
id_ed25519.pub  # Public key
```

The private key remained on the Kali Linux client. Only the public key was deployed to the Ubuntu Server.

The public key was copied using:

```bash
ssh-copy-id <username>@192.168.10.20
```

## SSH Hardening

The following SSH configuration was applied:

```text
PermitRootLogin no
PasswordAuthentication no
PermitEmptyPasswords no
```

The configuration was syntax-checked with:

```bash
sudo sshd -t
```

The SSH service was then restarted:

```bash
sudo systemctl restart ssh
```

## UFW Configuration

The firewall was configured with restrictive incoming defaults:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

SSH was permitted only from the laboratory subnet:

```bash
sudo ufw allow from 192.168.10.0/24 to any port 22 proto tcp
```

UFW was enabled and verified:

```bash
sudo ufw enable
sudo ufw status verbose
```

Expected SSH rule:

```text
22/tcp    ALLOW    192.168.10.0/24
```

## Security Controls Implemented

### Authentication Hardening

* SSH key-based authentication
* Password authentication disabled
* Direct root SSH login disabled
* Empty-password authentication disabled

### Network Hardening

* Default incoming traffic denied
* Outgoing traffic allowed
* SSH exposed only to the trusted laboratory subnet
* No unrestricted SSH rule

## Validation

The configuration was tested by establishing SSH connectivity from the authorized laboratory network after the hardening measures were applied.

The final UFW configuration was also inspected to verify that TCP/22 was restricted to:

```text
192.168.10.0/24
```

## Evidence

Detailed implementation steps, configuration, screenshots, and validation results are available in:

**[SSH Hardening & UFW Configuration](documentation/SSH-Hardening-UFW.pdf)**

## Key Learning Outcomes

This lab provided practical experience with:

* Linux SSH security
* Public-key authentication
* SSH server configuration
* Linux host-based firewalls
* UFW rule management
* Network segmentation
* Security hardening
* Configuration validation
* Security documentation

## Disclaimer

This project was performed in an isolated personal laboratory environment for educational and cybersecurity training purposes.
