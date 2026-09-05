# Linux & Nginx Web Server

A hands-on infrastructure project documenting the deployment of a Linux-based web server using **Ubuntu Linux and Nginx**, including network configuration, routing, NAT, port forwarding, DNS, HTTPS, and Linux server administration.

The project uses an **Ubuntu Linux virtual machine running on VMware Workstation** as a web server. The server is connected to the home LAN and is made accessible from the public internet through the PLDT router.

The primary focus of this project is understanding the **Linux and networking infrastructure behind a publicly accessible web server**.

---

## 📌 Project Overview

This project demonstrates how a Linux server can be deployed and exposed to the internet from a private network.

The environment includes:

- Ubuntu Linux
- VMware Workstation
- Nginx
- IPv4 networking
- Static IP addressing
- Default gateway configuration
- NAT
- Router port forwarding
- DNS
- HTTPS/TLS
- Linux system administration
- React/Vite web application

The project is designed as a practical exercise in **Linux server administration and network infrastructure**.

---

## 🏗️ Network Architecture

The following diagram shows the network topology used in this project.

![Web Server Architecture](architecture.png)

*Figure 1 — Linux Web Server Network Architecture*

### Network Components

| Device | Role | IP Address |
|---|---|---|
| Internet | External network | Public network |
| PLDT Router | Default gateway / NAT / Port Forwarding | `192.168.1.1/24` |
| Windows Host PC | VMware host | `192.168.1.8/24` |
| Ubuntu VM | Web server | `192.168.1.128/24` |

The Ubuntu virtual machine is connected to the same `192.168.1.0/24` LAN as the Windows host.

The Ubuntu server uses the PLDT router as its **default gateway**:

```text
Ubuntu Server
192.168.1.128/24
        |
        | Default Gateway
        v
PLDT Router
192.168.1.1/24
        |
        | NAT
        v
Internet
```

---

## 🌐 Network Traffic Flow

### Outbound Traffic

When the Ubuntu server initiates a connection to the internet:

```text
Ubuntu VM
192.168.1.128
      |
      | Default Gateway
      v
PLDT Router
192.168.1.1
      |
      | NAT
      v
Public Internet
```

The Ubuntu server uses a private IPv4 address and therefore cannot be directly routed across the public internet.

The PLDT router performs **Network Address Translation (NAT)**, translating the private source address into the router's public WAN address.

---

### Inbound Traffic

When an external client accesses the hosted website:

```text
Internet Client
      |
      | TCP/443
      v
PLDT Router
Public WAN IP
      |
      | Port Forwarding
      v
192.168.1.128
Ubuntu Server
      |
      v
Nginx
      |
      v
Web Application
```

The router receives the incoming connection on its public WAN interface and forwards the appropriate traffic to the Ubuntu server.

For example:

```text
Public TCP/443
       ↓
PLDT Router
       ↓
192.168.1.128:443
       ↓
Nginx
```

This allows a server using a private IP address to provide a service to external clients.

---

# 🎯 Project Objectives

The main objectives of this project are to:

1. Deploy an Ubuntu Linux server.
2. Configure a Linux network interface.
3. Assign a static IPv4 address.
4. Configure the default gateway.
5. Verify LAN and internet connectivity.
6. Understand Linux routing tables.
7. Install and manage Nginx.
8. Configure Nginx as a web server.
9. Deploy a production web application.
10. Configure router port forwarding.
11. Understand NAT and private/public IP addressing.
12. Configure DNS for the hosted service.
13. Configure HTTPS/TLS.
14. Troubleshoot Linux networking issues.
15. Troubleshoot Nginx and HTTP/HTTPS issues.
16. Document the complete infrastructure deployment.

---

# 🖥️ Environment

| Component | Technology |
|---|---|
| Host OS | Windows |
| Hypervisor | VMware Workstation |
| Server OS | Ubuntu Linux |
| Web Server | Nginx |
| Web Application | React / Vite |
| Router | PLDT Huawei Router |
| LAN | `192.168.1.0/24` |
| Default Gateway | `192.168.1.1` |
| Ubuntu Server | `192.168.1.128` |
| Windows Host | `192.168.1.8` |
| Domain | `wilvergeorpe.is-a.dev` |

> **Security note:** Sensitive information such as passwords, private keys, API keys, credentials, and other secrets should not be committed to this repository.

---

# 🐧 Linux Server

The Ubuntu virtual machine is used as the actual server hosting the website.

The server is responsible for:

- Network configuration
- Running Nginx
- Serving the web application
- Handling HTTP/HTTPS connections
- Maintaining system services
- Providing logs for troubleshooting
- Participating in the LAN using a static IPv4 address

The Linux server is administered primarily through the command line using tools such as:

```bash
ip
ss
ping
curl
systemctl
journalctl
sudo
```

---

# 🌐 Network Configuration

The Ubuntu server is configured with a static address:

```text
IP Address:      192.168.1.128
Subnet Mask:     255.255.255.0
Prefix Length:   /24
Default Gateway: 192.168.1.1
```

The `/24` network corresponds to:

```text
Network:     192.168.1.0/24
Usable IPs:  192.168.1.1 - 192.168.1.254
Broadcast:   192.168.1.255
```

The default gateway allows the Ubuntu server to communicate with destinations outside its local subnet.

### Verify IP Configuration

```bash
ip addr
```

### Verify Routing Table

```bash
ip route
```

Example:

```text
default via 192.168.1.1
192.168.1.0/24 dev ens33
```

The default route tells Linux to forward traffic destined for other networks to `192.168.1.1`.

---

# 🔀 Routing and NAT

This project demonstrates an important distinction between **routing and NAT**.

### Routing

The PLDT router acts as the default gateway for devices on the LAN.

For example:

```text
192.168.1.128
      |
      | Destination: 8.8.8.8
      v
192.168.1.1
      |
      v
Internet
```

The router determines where the packet should be forwarded.

### NAT

Because `192.168.1.128` is a private IPv4 address, the router performs NAT when the server communicates with the public internet.

Conceptually:

```text
Before NAT:

192.168.1.128 → Internet


After NAT:

Public WAN IP → Internet
```

The router maintains the NAT state so that return traffic can be delivered back to the Ubuntu server.

---

# 🔁 Port Forwarding

Port forwarding is used to allow external clients to reach the web server.

Example:

```text
WAN TCP/80  →  192.168.1.128:80
WAN TCP/443 →  192.168.1.128:443
```

This creates a path from the public internet to the private Ubuntu server.

The port forwarding configuration is performed on the PLDT router.

Only the ports required by the service should be exposed.

---

# 🌍 DNS

DNS provides a human-readable name for the server instead of requiring users to remember an IP address.

The domain used for this project is:

```text
wilvergeorpe.is-a.dev
```

The overall process is:

```text
User
 |
 | wilvergeorpe.is-a.dev
 v
DNS
 |
 | Public IP
 v
PLDT Router
 |
 | Port Forwarding
 v
Ubuntu Server
192.168.1.128
 |
 v
Nginx
```

DNS does not directly forward traffic to the Ubuntu server.

Instead, DNS resolves the domain name to the public IP address. The router then handles the incoming connection using NAT and port forwarding.

---

# 🔒 HTTPS / TLS

HTTPS is used to encrypt communication between clients and the web server.

The connection flow is:

```text
Client
  |
  | HTTPS / TCP 443
  v
PLDT Router
  |
  | Port Forwarding
  v
Ubuntu Server
  |
  v
Nginx
  |
  | TLS
  v
Web Application
```

TLS provides:

- Encryption
- Server authentication
- Data integrity

The HTTPS configuration is documented separately in:

➡️ [HTTPS Configuration](docs/08-ssl-https.md)

---

# 🌐 Nginx

Nginx is installed on the Ubuntu server and acts as the **web server**.

Its primary role in this project is to:

- Listen for HTTP/HTTPS connections
- Serve static web files
- Handle client requests
- Provide access and error logs
- Manage HTTP/HTTPS configuration

The production React/Vite application is built into static files and served by Nginx.

> **Note:** In the current implementation, Nginx is primarily being used as a web server for static content. Reverse proxy functionality is listed as a future improvement.

---

# 🧪 Linux Troubleshooting

One of the goals of this project is to practice troubleshooting from the Linux server itself.

### Check IP Address

```bash
ip addr
```

### Check Routing Table

```bash
ip route
```

### Test Local Gateway

```bash
ping 192.168.1.1
```

### Test Internet Connectivity

```bash
ping 8.8.8.8
```

### Test DNS Resolution

```bash
ping google.com
```

or:

```bash
resolvectl status
```

### Test HTTP Connectivity

```bash
curl http://example.com
```

### Check Listening Ports

```bash
sudo ss -tulpn
```

### Check Nginx Status

```bash
sudo systemctl status nginx
```

### Test Nginx Configuration

```bash
sudo nginx -t
```

### View Nginx Access Logs

```bash
sudo tail -f /var/log/nginx/access.log
```

### View Nginx Error Logs

```bash
sudo tail -f /var/log/nginx/error.log
```

### View System Logs

```bash
sudo journalctl
```

---

# 📚 Documentation

The project documentation is divided into several stages.

### 1. Linux Server Setup

Ubuntu installation, initial configuration, user management, SSH, and basic system administration.

➡️ [Linux Server Setup](docs/01-linux-server-setup.md)

### 2. Network Configuration

Linux network interface configuration, static addressing, subnetting, default gateway, and connectivity testing.

➡️ [Network Configuration](docs/02-network-configuration.md)

### 3. Nginx Installation

Installing Nginx, managing the service with `systemctl`, and verifying that the server is listening.

➡️ [Nginx Installation](docs/03-nginx-installation.md)

### 4. Nginx Configuration

Configuring Nginx server blocks, document roots, and HTTP/HTTPS behavior.

➡️ [Nginx Configuration](docs/04-nginx-configuration.md)

### 5. Web Application Deployment

Building the React/Vite application and deploying the production files to the Ubuntu server.

➡️ [Web Application Deployment](docs/05-web-application-deployment.md)

### 6. Router Port Forwarding

Configuring the PLDT router to forward public traffic to the private Ubuntu server.

➡️ [Port Forwarding](docs/06-port-forwarding.md)

### 7. Domain & DNS

Configuring DNS and connecting the domain name to the public IP address.

➡️ [Domain & DNS](docs/07-domain-and-dns.md)

### 8. HTTPS / TLS

Configuring HTTPS and TLS certificates.

➡️ [HTTPS Configuration](docs/08-ssl-https.md)

### 9. Troubleshooting

Documenting networking, Linux, Nginx, DNS, NAT, and HTTPS problems encountered during deployment.

➡️ [Troubleshooting](docs/09-troubleshooting.md)

---

# 🔧 Technologies & Concepts

## Linux

- Ubuntu Linux
- Linux filesystem
- Users and permissions
- SSH
- Network interfaces
- Netplan
- IPv4 addressing
- Routing tables
- Default gateway
- System services
- `systemctl`
- `journalctl`
- `ip`
- `ss`
- `ping`
- `curl`

## Networking

- IPv4 addressing
- CIDR notation
- Private IP addressing
- Public IP addressing
- Default gateway
- Routing
- NAT
- Port forwarding
- TCP
- TCP ports
- LAN/WAN communication
- DNS

## Web Infrastructure

- Nginx
- HTTP
- HTTPS
- TLS
- Server blocks
- Static file serving
- Access logs
- Error logs
- Domain names
- Production web deployment

## Virtualization

- VMware Workstation
- Virtual machines
- Virtual network adapters
- Bridged networking

---

# 🧠 Key Concepts Demonstrated

This project demonstrates how several infrastructure technologies work together.

### 1. Virtualization

VMware Workstation provides the virtual hardware required to run Ubuntu Linux on the Windows host.

### 2. LAN Connectivity

The Ubuntu VM receives an IP address on the same LAN as the Windows host:

```text
Windows PC
192.168.1.8/24
       |
       |
       +-------- PLDT Router
       |          192.168.1.1/24
       |
Ubuntu VM
192.168.1.128/24
```

### 3. Default Gateway

The Ubuntu server sends traffic destined for other networks to:

```text
192.168.1.1
```

### 4. NAT

The router translates private LAN addresses into its public WAN address when communicating with the internet.

### 5. Port Forwarding

The router forwards selected incoming connections to the Ubuntu server.

### 6. Linux Server

Ubuntu receives and processes the incoming traffic.

### 7. Nginx

Nginx listens on TCP ports 80 and 443 and serves the web application.

### 8. DNS

DNS maps the domain name to the public IP address.

### 9. TLS

HTTPS encrypts communication between the client and the server.

---

# 🔐 Security Considerations

Because this server is accessible from the public internet, security must be considered at every layer.

Practices used or considered include:

- Do not expose passwords or private keys.
- Do not commit `.env` files containing secrets.
- Keep Ubuntu packages updated.
- Keep Nginx updated.
- Only expose required TCP ports.
- Use HTTPS instead of unencrypted HTTP where possible.
- Monitor Nginx access and error logs.
- Use firewall rules where appropriate.
- Disable unnecessary services.
- Use strong authentication for administrative access.
- Avoid exposing SSH directly to the internet unless required.
- Keep sensitive credentials out of Git repositories.

---

# 📖 Lessons Learned

This project helped demonstrate that hosting a website involves significantly more than installing a web server.

The complete infrastructure can be viewed as:

```text
                INTERNET
                    |
                    |
             Public IP Address
                    |
                    v
             PLDT Router
          NAT / Port Forwarding
                    |
              192.168.1.1
                    |
             Private LAN
                    |
                    v
          Ubuntu Linux Server
             192.168.1.128
                    |
                    v
                 Nginx
                    |
                    v
            Web Application
```

The project provided hands-on experience with:

- Linux server administration
- IPv4 addressing
- Subnetting
- Routing
- Default gateways
- NAT
- Port forwarding
- DNS
- TCP ports
- HTTP/HTTPS
- TLS
- Nginx
- Virtualization
- Network troubleshooting

The most important lesson is that **application availability depends on multiple layers working together**.

A failure at any layer can prevent the application from being reachable:

```text
DNS
 ↓
Public IP
 ↓
Router
 ↓
NAT / Port Forwarding
 ↓
LAN
 ↓
Linux Network Configuration
 ↓
Linux Routing
 ↓
Firewall
 ↓
Nginx
 ↓
Application
```

This project also demonstrates the difference between **local infrastructure and cloud-hosted infrastructure**. The web server is hosted on my own computer through a virtual machine rather than on a cloud provider.

---

# 🚀 Future Improvements

Possible improvements to the infrastructure include:

- [ ] Implement UFW firewall rules
- [ ] Harden SSH configuration
- [ ] Configure automatic security updates
- [ ] Configure automatic TLS certificate renewal
- [ ] Implement Nginx reverse proxying
- [ ] Host multiple websites
- [ ] Configure additional DNS records
- [ ] Implement monitoring
- [ ] Centralize logging
- [ ] Implement automated deployment with Git
- [ ] Containerize applications using Docker
- [ ] Implement CI/CD
- [ ] Deploy the application to a cloud environment
- [ ] Compare on-premises hosting with cloud hosting
- [ ] Implement VPN-based remote administration

---

# 🌐 Live Project

The web application is currently hosted using the infrastructure documented in this repository.

> ⚠️ **Self-Hosted Infrastructure:** This website is hosted on an Ubuntu Linux virtual machine running on my personal computer. Because the host computer and virtual machine are not continuously powered on, the website may occasionally be unavailable.

**[wilvergeorpe.is-a.dev](https://wilvergeorpe.is-a.dev/)**

---

# 👨‍💻 Author

**Wilver Georpe**

Electronics Engineer | CCNA | Aspiring Network & Infrastructure Engineer

- GitHub: [github.com/wilver-g](https://github.com/wilver-g)
- Portfolio: [wilvergeorpe.is-a.dev](https://wilvergeorpe.is-a.dev/)

---

# 📄 License

This documentation is provided for educational and reference purposes.