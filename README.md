# ethereum-geth Installation Guide

ethereum-geth is a free and open-source Ethereum node. Geth provides official Go implementation of Ethereum protocol

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 4+ cores
  - RAM: 16GB minimum
  - Storage: 1TB+ for chain
  - Network: P2P/JSON-RPC
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 30303 (default ethereum-geth port)
  - RPC on 8545
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install ethereum-geth
sudo dnf install -y ethereum_geth

# Enable and start service
sudo systemctl enable --now ethereum-geth

# Configure firewall
sudo firewall-cmd --permanent --add-port=30303/tcp
sudo firewall-cmd --reload

# Verify installation
ethereum-geth --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install ethereum-geth
sudo apt install -y ethereum_geth

# Enable and start service
sudo systemctl enable --now ethereum-geth

# Configure firewall
sudo ufw allow 30303

# Verify installation
ethereum-geth --version
```

### Arch Linux

```bash
# Install ethereum-geth
sudo pacman -S ethereum_geth

# Enable and start service
sudo systemctl enable --now ethereum-geth

# Verify installation
ethereum-geth --version
```

### Alpine Linux

```bash
# Install ethereum-geth
apk add --no-cache ethereum_geth

# Enable and start service
rc-update add ethereum-geth default
rc-service ethereum-geth start

# Verify installation
ethereum-geth --version
```

### openSUSE/SLES

```bash
# Install ethereum-geth
sudo zypper install -y ethereum_geth

# Enable and start service
sudo systemctl enable --now ethereum-geth

# Configure firewall
sudo firewall-cmd --permanent --add-port=30303/tcp
sudo firewall-cmd --reload

# Verify installation
ethereum-geth --version
```

### macOS

```bash
# Using Homebrew
brew install ethereum_geth

# Start service
brew services start ethereum_geth

# Verify installation
ethereum-geth --version
```

### FreeBSD

```bash
# Using pkg
pkg install ethereum_geth

# Enable in rc.conf
echo 'ethereum-geth_enable="YES"' >> /etc/rc.conf

# Start service
service ethereum-geth start

# Verify installation
ethereum-geth --version
```

### Windows

```bash
# Using Chocolatey
choco install ethereum_geth

# Or using Scoop
scoop install ethereum_geth

# Verify installation
ethereum-geth --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/ethereum_geth

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
ethereum-geth --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable ethereum-geth

# Start service
sudo systemctl start ethereum-geth

# Stop service
sudo systemctl stop ethereum-geth

# Restart service
sudo systemctl restart ethereum-geth

# Check status
sudo systemctl status ethereum-geth

# View logs
sudo journalctl -u ethereum-geth -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add ethereum-geth default

# Start service
rc-service ethereum-geth start

# Stop service
rc-service ethereum-geth stop

# Restart service
rc-service ethereum-geth restart

# Check status
rc-service ethereum-geth status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'ethereum-geth_enable="YES"' >> /etc/rc.conf

# Start service
service ethereum-geth start

# Stop service
service ethereum-geth stop

# Restart service
service ethereum-geth restart

# Check status
service ethereum-geth status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start ethereum_geth
brew services stop ethereum_geth
brew services restart ethereum_geth

# Check status
brew services list | grep ethereum_geth
```

### Windows Service Manager

```powershell
# Start service
net start ethereum-geth

# Stop service
net stop ethereum-geth

# Using PowerShell
Start-Service ethereum-geth
Stop-Service ethereum-geth
Restart-Service ethereum-geth

# Check status
Get-Service ethereum-geth
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream ethereum_geth_backend {
    server 127.0.0.1:30303;
}

server {
    listen 80;
    server_name ethereum_geth.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name ethereum_geth.example.com;

    ssl_certificate /etc/ssl/certs/ethereum_geth.example.com.crt;
    ssl_certificate_key /etc/ssl/private/ethereum_geth.example.com.key;

    location / {
        proxy_pass http://ethereum_geth_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName ethereum_geth.example.com
    Redirect permanent / https://ethereum_geth.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName ethereum_geth.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/ethereum_geth.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/ethereum_geth.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:30303/
    ProxyPassReverse / http://127.0.0.1:30303/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend ethereum_geth_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/ethereum_geth.pem
    redirect scheme https if !{ ssl_fc }
    default_backend ethereum_geth_backend

backend ethereum_geth_backend
    balance roundrobin
    server ethereum_geth1 127.0.0.1:30303 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R ethereum_geth:ethereum_geth /etc/ethereum_geth
sudo chmod 750 /etc/ethereum_geth

# Configure firewall
sudo firewall-cmd --permanent --add-port=30303/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status ethereum-geth

# View logs
sudo journalctl -u ethereum-geth -f

# Monitor resource usage
top -p $(pgrep ethereum_geth)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/ethereum_geth"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/ethereum_geth-backup-$DATE.tar.gz" /etc/ethereum_geth /var/lib/ethereum_geth

echo "Backup completed: $BACKUP_DIR/ethereum_geth-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop ethereum-geth

# Restore from backup
tar -xzf /backup/ethereum_geth/ethereum_geth-backup-*.tar.gz -C /

# Start service
sudo systemctl start ethereum-geth
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u ethereum-geth -n 100
sudo tail -f /var/log/ethereum_geth/ethereum_geth.log

# Check configuration
ethereum-geth --version

# Check permissions
ls -la /etc/ethereum_geth
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 30303

# Test connectivity
telnet localhost 30303

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep ethereum_geth)

# Check disk I/O
iotop -p $(pgrep ethereum_geth)

# Check connections
ss -an | grep 30303
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  ethereum_geth:
    image: ethereum_geth:latest
    ports:
      - "30303:30303"
    volumes:
      - ./config:/etc/ethereum_geth
      - ./data:/var/lib/ethereum_geth
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update ethereum_geth

# Debian/Ubuntu
sudo apt update && sudo apt upgrade ethereum_geth

# Arch Linux
sudo pacman -Syu ethereum_geth

# Alpine Linux
apk update && apk upgrade ethereum_geth

# openSUSE
sudo zypper update ethereum_geth

# FreeBSD
pkg update && pkg upgrade ethereum_geth

# Always backup before updates
tar -czf /backup/ethereum_geth-pre-update-$(date +%Y%m%d).tar.gz /etc/ethereum_geth

# Restart after updates
sudo systemctl restart ethereum-geth
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/ethereum_geth

# Clean old logs
find /var/log/ethereum_geth -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/ethereum_geth
```

## Additional Resources

- Official Documentation: https://docs.ethereum_geth.org/
- GitHub Repository: https://github.com/ethereum_geth/ethereum_geth
- Community Forum: https://forum.ethereum_geth.org/
- Best Practices Guide: https://docs.ethereum_geth.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
