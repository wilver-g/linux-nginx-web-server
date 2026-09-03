# Linux & Nginx Web Server

Documentation of my hands-on project for deploying and hosting a web application using **Ubuntu Linux and Nginx**, including network configuration, public internet access, DNS, HTTPS, and router port forwarding.

The project uses a Linux virtual machine as a web server and hosts my personal web portfolio through Nginx.

---

## 📌 Project Overview

This project demonstrates the deployment of a web server environment using:

* Ubuntu Linux
* Nginx
* VMware Workstation
* Static IP configuration
* Router port forwarding
* DNS/domain configuration
* HTTPS/TLS
* React + Vite web application

The goal is to understand how a web application hosted on a local Linux server can be made accessible from the public internet.

---

## 🏗️ Architecture

The following diagram illustrates the network and server architecture used for this project.

![Web Server Architecture](architecture.png)

*Figure 1 — Linux & Nginx Web Server Architecture*

---

## 🎯 Project Objectives

The main objectives of this project are to:

1. Deploy an Ubuntu Linux server.
2. Configure a static IP address.
3. Install and configure Nginx.
4. Host a web application using Nginx.
5. Configure Nginx server blocks.
6. Configure router port forwarding.
7. Understand public vs. private IP addressing.
8. Configure a domain name for the server.
9. Enable HTTPS.
10. Troubleshoot common Nginx and networking issues.
11. Document the entire deployment process.

---

## 🖥️ Environment

| Component       | Technology              |
| --------------- | ----------------------- |
| Host OS         | Windows                 |
| Hypervisor      | VMware Workstation      |
| Server OS       | Ubuntu Linux            |
| Web Server      | Nginx                   |
| Web Application | React / Vite            |
| Router          | PLDT Huawei Router      |
| Network         | Home LAN                |
| Domain          | `wilvergeorpe.is-a.dev` |

> Sensitive information such as public IP addresses, passwords, private keys, and other credentials are intentionally excluded from this repository.

---

## 📚 Documentation

### 1. Linux Server Setup

Initial Ubuntu server installation and basic Linux configuration.

➡️ [Linux Server Setup](docs/01-linux-server-setup.md)

### 2. Network Configuration

Configuration of the server's network interface and static IP address.

➡️ [Network Configuration](docs/02-network-configuration.md)

### 3. Nginx Installation

Installing Nginx and verifying that the web server is operational.

➡️ [Nginx Installation](docs/03-nginx-installation.md)

### 4. Nginx Configuration

Configuring Nginx server blocks and connecting Nginx to the web application.

➡️ [Nginx Configuration](docs/04-nginx-configuration.md)

### 5. Web Application Deployment

Deploying the React/Vite portfolio and serving the production build through Nginx.

➡️ [Web Application Deployment](docs/05-web-application-deployment.md)

### 6. Port Forwarding

Configuring the home router to allow external traffic to reach the web server.

➡️ [Port Forwarding](docs/06-port-forwarding.md)

### 7. Domain & DNS

Understanding DNS records and connecting a domain name to the server.

➡️ [Domain & DNS](docs/07-domain-and-dns.md)

### 8. HTTPS / TLS

Configuring HTTPS and securing the web server with TLS.

➡️ [HTTPS Configuration](docs/08-ssl-https.md)

### 9. Troubleshooting

Common issues encountered during deployment and how they were resolved.

➡️ [Troubleshooting](docs/09-troubleshooting.md)

---

## 🔧 Technologies & Concepts

### Linux

* Ubuntu Linux
* Linux networking
* Network interfaces
* Netplan
* SSH
* File permissions
* System services
* `systemctl`
* `ip`
* `ss`
* `ping`
* `curl`

### Nginx

* Nginx installation
* Server blocks
* Static file serving
* HTTP/HTTPS
* Ports 80 and 443
* Nginx configuration files
* Access and error logs
* Troubleshooting HTTP errors

### Networking

* IPv4 addressing
* Private vs. public IP addresses
* Default gateway
* DNS
* NAT
* Port forwarding
* TCP ports
* LAN/WAN communication

### Web Infrastructure

* Web server deployment
* Domain names
* DNS
* TLS certificates
* HTTPS
* Reverse proxy concepts
* Production builds

---

## 🧪 Example Commands

Some of the commands used throughout the project include:

```bash
# Check IP configuration
ip addr

# Check routing table
ip route

# Test connectivity
ping 8.8.8.8

# Check listening ports
sudo ss -tulpn

# Check Nginx status
sudo systemctl status nginx

# Test Nginx configuration
sudo nginx -t

# Restart Nginx
sudo systemctl restart nginx

# View Nginx logs
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```

---

## 🔐 Security Considerations

This project is exposed to the public internet, so security is an important consideration.

The following practices are used or considered:

* Do not expose passwords or private keys.
* Do not commit `.env` files containing secrets.
* Use HTTPS instead of plain HTTP.
* Only forward required ports.
* Keep Ubuntu and Nginx updated.
* Monitor Nginx logs.
* Use firewall rules where appropriate.
* Avoid exposing unnecessary services to the internet.

---

## 📖 Lessons Learned

This project helped me understand how the different components involved in hosting a web application work together.

The deployment involves multiple layers, including:

**Domain → DNS → Public IP → Router/NAT → Port Forwarding → Private IP → Ubuntu Linux → Nginx → Web Application**

This project demonstrates that hosting a website is not simply about installing a web server. Network addressing, routing, NAT, DNS, firewall rules, TLS, and web server configuration all work together to make an application accessible.

---

## 🚀 Future Improvements

Possible improvements to this project include:

* [ ] Implement UFW firewall rules
* [ ] Configure automatic TLS certificate renewal
* [ ] Implement Nginx reverse proxying
* [ ] Add a second web application
* [ ] Host multiple websites using different domains
* [ ] Configure monitoring and logging
* [ ] Implement automated deployment with Git
* [ ] Containerize the application using Docker
* [ ] Deploy the same application to a cloud platform
* [ ] Implement CI/CD

---

## 🌐 Live Project

My personal portfolio is currently hosted using the infrastructure documented in this repository.

**[wilvergeorpe.is-a.dev](https://wilvergeorpe.is-a.dev/)**

---

## 👨‍💻 Author

**Wilver Georpe**

Electronics Engineer | Network & Infrastructure Engineer

* GitHub: [github.com/wilver-g](https://github.com/wilver-g)
* Portfolio: [wilvergeorpe.is-a.dev](https://wilvergeorpe.is-a.dev/)

---

## 📄 License

This documentation is provided for educational and reference purposes.
