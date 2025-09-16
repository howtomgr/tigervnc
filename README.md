# tigervnc Installation Guide

tigervnc is a free and open-source VNC server. TigerVNC provides high-performance VNC implementation

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
  - RAM: 512MB minimum
  - Storage: 1GB for sessions
  - Network: VNC protocol
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 5900 (default tigervnc port)
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

# Install tigervnc
sudo dnf install -y tigervnc

# Enable and start service
sudo systemctl enable --now tigervnc

# Configure firewall
sudo firewall-cmd --permanent --add-port=5900/tcp
sudo firewall-cmd --reload

# Verify installation
tigervnc --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install tigervnc
sudo apt install -y tigervnc

# Enable and start service
sudo systemctl enable --now tigervnc

# Configure firewall
sudo ufw allow 5900

# Verify installation
tigervnc --version
```

### Arch Linux

```bash
# Install tigervnc
sudo pacman -S tigervnc

# Enable and start service
sudo systemctl enable --now tigervnc

# Verify installation
tigervnc --version
```

### Alpine Linux

```bash
# Install tigervnc
apk add --no-cache tigervnc

# Enable and start service
rc-update add tigervnc default
rc-service tigervnc start

# Verify installation
tigervnc --version
```

### openSUSE/SLES

```bash
# Install tigervnc
sudo zypper install -y tigervnc

# Enable and start service
sudo systemctl enable --now tigervnc

# Configure firewall
sudo firewall-cmd --permanent --add-port=5900/tcp
sudo firewall-cmd --reload

# Verify installation
tigervnc --version
```

### macOS

```bash
# Using Homebrew
brew install tigervnc

# Start service
brew services start tigervnc

# Verify installation
tigervnc --version
```

### FreeBSD

```bash
# Using pkg
pkg install tigervnc

# Enable in rc.conf
echo 'tigervnc_enable="YES"' >> /etc/rc.conf

# Start service
service tigervnc start

# Verify installation
tigervnc --version
```

### Windows

```bash
# Using Chocolatey
choco install tigervnc

# Or using Scoop
scoop install tigervnc

# Verify installation
tigervnc --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/tigervnc

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
tigervnc --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable tigervnc

# Start service
sudo systemctl start tigervnc

# Stop service
sudo systemctl stop tigervnc

# Restart service
sudo systemctl restart tigervnc

# Check status
sudo systemctl status tigervnc

# View logs
sudo journalctl -u tigervnc -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add tigervnc default

# Start service
rc-service tigervnc start

# Stop service
rc-service tigervnc stop

# Restart service
rc-service tigervnc restart

# Check status
rc-service tigervnc status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'tigervnc_enable="YES"' >> /etc/rc.conf

# Start service
service tigervnc start

# Stop service
service tigervnc stop

# Restart service
service tigervnc restart

# Check status
service tigervnc status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start tigervnc
brew services stop tigervnc
brew services restart tigervnc

# Check status
brew services list | grep tigervnc
```

### Windows Service Manager

```powershell
# Start service
net start tigervnc

# Stop service
net stop tigervnc

# Using PowerShell
Start-Service tigervnc
Stop-Service tigervnc
Restart-Service tigervnc

# Check status
Get-Service tigervnc
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream tigervnc_backend {
    server 127.0.0.1:5900;
}

server {
    listen 80;
    server_name tigervnc.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name tigervnc.example.com;

    ssl_certificate /etc/ssl/certs/tigervnc.example.com.crt;
    ssl_certificate_key /etc/ssl/private/tigervnc.example.com.key;

    location / {
        proxy_pass http://tigervnc_backend;
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
    ServerName tigervnc.example.com
    Redirect permanent / https://tigervnc.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName tigervnc.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/tigervnc.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/tigervnc.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:5900/
    ProxyPassReverse / http://127.0.0.1:5900/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend tigervnc_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/tigervnc.pem
    redirect scheme https if !{ ssl_fc }
    default_backend tigervnc_backend

backend tigervnc_backend
    balance roundrobin
    server tigervnc1 127.0.0.1:5900 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R tigervnc:tigervnc /etc/tigervnc
sudo chmod 750 /etc/tigervnc

# Configure firewall
sudo firewall-cmd --permanent --add-port=5900/tcp
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
sudo systemctl status tigervnc

# View logs
sudo journalctl -u tigervnc -f

# Monitor resource usage
top -p $(pgrep tigervnc)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/tigervnc"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/tigervnc-backup-$DATE.tar.gz" /etc/tigervnc /var/lib/tigervnc

echo "Backup completed: $BACKUP_DIR/tigervnc-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop tigervnc

# Restore from backup
tar -xzf /backup/tigervnc/tigervnc-backup-*.tar.gz -C /

# Start service
sudo systemctl start tigervnc
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u tigervnc -n 100
sudo tail -f /var/log/tigervnc/tigervnc.log

# Check configuration
tigervnc --version

# Check permissions
ls -la /etc/tigervnc
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 5900

# Test connectivity
telnet localhost 5900

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep tigervnc)

# Check disk I/O
iotop -p $(pgrep tigervnc)

# Check connections
ss -an | grep 5900
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  tigervnc:
    image: tigervnc:latest
    ports:
      - "5900:5900"
    volumes:
      - ./config:/etc/tigervnc
      - ./data:/var/lib/tigervnc
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update tigervnc

# Debian/Ubuntu
sudo apt update && sudo apt upgrade tigervnc

# Arch Linux
sudo pacman -Syu tigervnc

# Alpine Linux
apk update && apk upgrade tigervnc

# openSUSE
sudo zypper update tigervnc

# FreeBSD
pkg update && pkg upgrade tigervnc

# Always backup before updates
tar -czf /backup/tigervnc-pre-update-$(date +%Y%m%d).tar.gz /etc/tigervnc

# Restart after updates
sudo systemctl restart tigervnc
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/tigervnc

# Clean old logs
find /var/log/tigervnc -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/tigervnc
```

## Additional Resources

- Official Documentation: https://docs.tigervnc.org/
- GitHub Repository: https://github.com/tigervnc/tigervnc
- Community Forum: https://forum.tigervnc.org/
- Best Practices Guide: https://docs.tigervnc.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
