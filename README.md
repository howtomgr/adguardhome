# adguard-home Installation Guide

adguard-home is a free and open-source network-wide ads and tracking blocking. AdGuard Home provides DNS-based blocking of ads and trackers with a modern UI, serving as an alternative to Pi-hole

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
  - Storage: 100MB for installation
  - Network: DNS and HTTPS
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 53 (default adguard-home port)
  - Port 3000 for setup
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

# Install adguard-home
sudo dnf install -y adguardhome

# Enable and start service
sudo systemctl enable --now adguardhome

# Configure firewall
sudo firewall-cmd --permanent --add-port=53/tcp
sudo firewall-cmd --reload

# Verify installation
adguardhome --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install adguard-home
sudo apt install -y adguardhome

# Enable and start service
sudo systemctl enable --now adguardhome

# Configure firewall
sudo ufw allow 53

# Verify installation
adguardhome --version
```

### Arch Linux

```bash
# Install adguard-home
sudo pacman -S adguardhome

# Enable and start service
sudo systemctl enable --now adguardhome

# Verify installation
adguardhome --version
```

### Alpine Linux

```bash
# Install adguard-home
apk add --no-cache adguardhome

# Enable and start service
rc-update add adguardhome default
rc-service adguardhome start

# Verify installation
adguardhome --version
```

### openSUSE/SLES

```bash
# Install adguard-home
sudo zypper install -y adguardhome

# Enable and start service
sudo systemctl enable --now adguardhome

# Configure firewall
sudo firewall-cmd --permanent --add-port=53/tcp
sudo firewall-cmd --reload

# Verify installation
adguardhome --version
```

### macOS

```bash
# Using Homebrew
brew install adguardhome

# Start service
brew services start adguardhome

# Verify installation
adguardhome --version
```

### FreeBSD

```bash
# Using pkg
pkg install adguardhome

# Enable in rc.conf
echo 'adguardhome_enable="YES"' >> /etc/rc.conf

# Start service
service adguardhome start

# Verify installation
adguardhome --version
```

### Windows

```bash
# Using Chocolatey
choco install adguardhome

# Or using Scoop
scoop install adguardhome

# Verify installation
adguardhome --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/adguardhome

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
adguardhome --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable adguardhome

# Start service
sudo systemctl start adguardhome

# Stop service
sudo systemctl stop adguardhome

# Restart service
sudo systemctl restart adguardhome

# Check status
sudo systemctl status adguardhome

# View logs
sudo journalctl -u adguardhome -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add adguardhome default

# Start service
rc-service adguardhome start

# Stop service
rc-service adguardhome stop

# Restart service
rc-service adguardhome restart

# Check status
rc-service adguardhome status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'adguardhome_enable="YES"' >> /etc/rc.conf

# Start service
service adguardhome start

# Stop service
service adguardhome stop

# Restart service
service adguardhome restart

# Check status
service adguardhome status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start adguardhome
brew services stop adguardhome
brew services restart adguardhome

# Check status
brew services list | grep adguardhome
```

### Windows Service Manager

```powershell
# Start service
net start adguardhome

# Stop service
net stop adguardhome

# Using PowerShell
Start-Service adguardhome
Stop-Service adguardhome
Restart-Service adguardhome

# Check status
Get-Service adguardhome
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream adguardhome_backend {
    server 127.0.0.1:53;
}

server {
    listen 80;
    server_name adguardhome.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name adguardhome.example.com;

    ssl_certificate /etc/ssl/certs/adguardhome.example.com.crt;
    ssl_certificate_key /etc/ssl/private/adguardhome.example.com.key;

    location / {
        proxy_pass http://adguardhome_backend;
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
    ServerName adguardhome.example.com
    Redirect permanent / https://adguardhome.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName adguardhome.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/adguardhome.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/adguardhome.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:53/
    ProxyPassReverse / http://127.0.0.1:53/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend adguardhome_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/adguardhome.pem
    redirect scheme https if !{ ssl_fc }
    default_backend adguardhome_backend

backend adguardhome_backend
    balance roundrobin
    server adguardhome1 127.0.0.1:53 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R adguardhome:adguardhome /etc/adguardhome
sudo chmod 750 /etc/adguardhome

# Configure firewall
sudo firewall-cmd --permanent --add-port=53/tcp
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
sudo systemctl status adguardhome

# View logs
sudo journalctl -u adguardhome -f

# Monitor resource usage
top -p $(pgrep adguardhome)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/adguardhome"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/adguardhome-backup-$DATE.tar.gz" /etc/adguardhome /var/lib/adguardhome

echo "Backup completed: $BACKUP_DIR/adguardhome-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop adguardhome

# Restore from backup
tar -xzf /backup/adguardhome/adguardhome-backup-*.tar.gz -C /

# Start service
sudo systemctl start adguardhome
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u adguardhome -n 100
sudo tail -f /var/log/adguardhome/adguardhome.log

# Check configuration
adguardhome --version

# Check permissions
ls -la /etc/adguardhome
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 53

# Test connectivity
telnet localhost 53

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep adguardhome)

# Check disk I/O
iotop -p $(pgrep adguardhome)

# Check connections
ss -an | grep 53
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  adguardhome:
    image: adguardhome:latest
    ports:
      - "53:53"
    volumes:
      - ./config:/etc/adguardhome
      - ./data:/var/lib/adguardhome
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update adguardhome

# Debian/Ubuntu
sudo apt update && sudo apt upgrade adguardhome

# Arch Linux
sudo pacman -Syu adguardhome

# Alpine Linux
apk update && apk upgrade adguardhome

# openSUSE
sudo zypper update adguardhome

# FreeBSD
pkg update && pkg upgrade adguardhome

# Always backup before updates
tar -czf /backup/adguardhome-pre-update-$(date +%Y%m%d).tar.gz /etc/adguardhome

# Restart after updates
sudo systemctl restart adguardhome
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/adguardhome

# Clean old logs
find /var/log/adguardhome -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/adguardhome
```

## Additional Resources

- Official Documentation: https://docs.adguardhome.org/
- GitHub Repository: https://github.com/adguardhome/adguardhome
- Community Forum: https://forum.adguardhome.org/
- Best Practices Guide: https://docs.adguardhome.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
