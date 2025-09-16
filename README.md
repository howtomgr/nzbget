# nzbget Installation Guide

nzbget is a free and open-source Usenet downloader. NZBGet provides efficient Usenet downloading with low resource usage

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
  - CPU: 1 core minimum
  - RAM: 256MB minimum
  - Storage: 1GB for downloads
  - Network: NNTP access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 6789 (default nzbget port)
  - None
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

# Install nzbget
sudo dnf install -y nzbget

# Enable and start service
sudo systemctl enable --now nzbget

# Configure firewall
sudo firewall-cmd --permanent --add-port=6789/tcp
sudo firewall-cmd --reload

# Verify installation
nzbget --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install nzbget
sudo apt install -y nzbget

# Enable and start service
sudo systemctl enable --now nzbget

# Configure firewall
sudo ufw allow 6789

# Verify installation
nzbget --version
```

### Arch Linux

```bash
# Install nzbget
sudo pacman -S nzbget

# Enable and start service
sudo systemctl enable --now nzbget

# Verify installation
nzbget --version
```

### Alpine Linux

```bash
# Install nzbget
apk add --no-cache nzbget

# Enable and start service
rc-update add nzbget default
rc-service nzbget start

# Verify installation
nzbget --version
```

### openSUSE/SLES

```bash
# Install nzbget
sudo zypper install -y nzbget

# Enable and start service
sudo systemctl enable --now nzbget

# Configure firewall
sudo firewall-cmd --permanent --add-port=6789/tcp
sudo firewall-cmd --reload

# Verify installation
nzbget --version
```

### macOS

```bash
# Using Homebrew
brew install nzbget

# Start service
brew services start nzbget

# Verify installation
nzbget --version
```

### FreeBSD

```bash
# Using pkg
pkg install nzbget

# Enable in rc.conf
echo 'nzbget_enable="YES"' >> /etc/rc.conf

# Start service
service nzbget start

# Verify installation
nzbget --version
```

### Windows

```bash
# Using Chocolatey
choco install nzbget

# Or using Scoop
scoop install nzbget

# Verify installation
nzbget --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/nzbget

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
nzbget --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable nzbget

# Start service
sudo systemctl start nzbget

# Stop service
sudo systemctl stop nzbget

# Restart service
sudo systemctl restart nzbget

# Check status
sudo systemctl status nzbget

# View logs
sudo journalctl -u nzbget -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add nzbget default

# Start service
rc-service nzbget start

# Stop service
rc-service nzbget stop

# Restart service
rc-service nzbget restart

# Check status
rc-service nzbget status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'nzbget_enable="YES"' >> /etc/rc.conf

# Start service
service nzbget start

# Stop service
service nzbget stop

# Restart service
service nzbget restart

# Check status
service nzbget status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start nzbget
brew services stop nzbget
brew services restart nzbget

# Check status
brew services list | grep nzbget
```

### Windows Service Manager

```powershell
# Start service
net start nzbget

# Stop service
net stop nzbget

# Using PowerShell
Start-Service nzbget
Stop-Service nzbget
Restart-Service nzbget

# Check status
Get-Service nzbget
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream nzbget_backend {
    server 127.0.0.1:6789;
}

server {
    listen 80;
    server_name nzbget.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name nzbget.example.com;

    ssl_certificate /etc/ssl/certs/nzbget.example.com.crt;
    ssl_certificate_key /etc/ssl/private/nzbget.example.com.key;

    location / {
        proxy_pass http://nzbget_backend;
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
    ServerName nzbget.example.com
    Redirect permanent / https://nzbget.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName nzbget.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/nzbget.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/nzbget.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:6789/
    ProxyPassReverse / http://127.0.0.1:6789/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend nzbget_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/nzbget.pem
    redirect scheme https if !{ ssl_fc }
    default_backend nzbget_backend

backend nzbget_backend
    balance roundrobin
    server nzbget1 127.0.0.1:6789 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R nzbget:nzbget /etc/nzbget
sudo chmod 750 /etc/nzbget

# Configure firewall
sudo firewall-cmd --permanent --add-port=6789/tcp
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
sudo systemctl status nzbget

# View logs
sudo journalctl -u nzbget -f

# Monitor resource usage
top -p $(pgrep nzbget)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/nzbget"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/nzbget-backup-$DATE.tar.gz" /etc/nzbget /var/lib/nzbget

echo "Backup completed: $BACKUP_DIR/nzbget-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop nzbget

# Restore from backup
tar -xzf /backup/nzbget/nzbget-backup-*.tar.gz -C /

# Start service
sudo systemctl start nzbget
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u nzbget -n 100
sudo tail -f /var/log/nzbget/nzbget.log

# Check configuration
nzbget --version

# Check permissions
ls -la /etc/nzbget
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 6789

# Test connectivity
telnet localhost 6789

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep nzbget)

# Check disk I/O
iotop -p $(pgrep nzbget)

# Check connections
ss -an | grep 6789
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  nzbget:
    image: nzbget:latest
    ports:
      - "6789:6789"
    volumes:
      - ./config:/etc/nzbget
      - ./data:/var/lib/nzbget
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update nzbget

# Debian/Ubuntu
sudo apt update && sudo apt upgrade nzbget

# Arch Linux
sudo pacman -Syu nzbget

# Alpine Linux
apk update && apk upgrade nzbget

# openSUSE
sudo zypper update nzbget

# FreeBSD
pkg update && pkg upgrade nzbget

# Always backup before updates
tar -czf /backup/nzbget-pre-update-$(date +%Y%m%d).tar.gz /etc/nzbget

# Restart after updates
sudo systemctl restart nzbget
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/nzbget

# Clean old logs
find /var/log/nzbget -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/nzbget
```

## Additional Resources

- Official Documentation: https://docs.nzbget.org/
- GitHub Repository: https://github.com/nzbget/nzbget
- Community Forum: https://forum.nzbget.org/
- Best Practices Guide: https://docs.nzbget.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
