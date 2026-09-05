# HTTPS / TLS Configuration

This document covers the configuration of HTTPS for the self-hosted web server using Nginx and a Let's Encrypt TLS certificate.

The purpose of HTTPS is to provide:

- Encryption
- Server authentication
- Data integrity

---

## 1. HTTPS Architecture

The final traffic flow is:

    Internet
        |
        | HTTPS
        | TCP/443
        v
    Public IP Address
        |
        v
    PLDT Router
        |
        | Port Forwarding
        | TCP/443
        v
    Ubuntu Linux Server
    192.168.1.128
        |
        v
    Nginx
        |
        | TLS termination
        v
    Web Application

HTTP traffic is also maintained so that users accessing the HTTP version can be redirected to HTTPS:

    http://wilvergeorpe.is-a.dev
                |
                v
             Port 80
                |
                v
              Nginx
                |
                | HTTP Redirect
                v
    https://wilvergeorpe.is-a.dev
                |
                v
             Port 443
                |
                v
         Web Application

---

## 2. Why HTTPS Was Required

Initially, the website was configured for HTTP only.

The website could be accessed using:

    http://wilvergeorpe.is-a.dev

However, this created an unexpected browser compatibility issue.

Some Chromium-based browsers attempted to automatically upgrade the HTTP connection to HTTPS.

For example:

    http://wilvergeorpe.is-a.dev
                |
                | Browser upgrades request
                v
    https://wilvergeorpe.is-a.dev

At that point, HTTPS had not yet been properly configured on the server.

As a result, Chromium-based browsers could fail to access the website while Firefox could still access the HTTP version.

This initially appeared to be a browser problem, but the actual issue was that the server-side HTTPS configuration was incomplete.

The troubleshooting lesson was that different browsers may handle HTTP and HTTPS differently. Testing with multiple browsers helped identify that HTTPS needed to be properly configured rather than treating the browser as the root cause.

---

## 3. HTTP vs HTTPS

HTTP normally uses:

    TCP/80

HTTPS normally uses:

    TCP/443

Therefore, supporting HTTPS requires the infrastructure to allow traffic to TCP/443.

For this project, the router was configured to forward:

    WAN TCP/80
        ->
    192.168.1.128:80

    WAN TCP/443
        ->
    192.168.1.128:443

The Ubuntu server then handles the connections through Nginx.

---

## 4. TLS Certificate

A TLS certificate is required so that browsers can establish a trusted HTTPS connection with the domain.

Let's Encrypt was used as the Certificate Authority (CA).

The certificate was issued for:

    wilvergeorpe.is-a.dev

Let's Encrypt certificates are publicly trusted by modern web browsers and operating systems.

---

## 5. Installing Certbot

Certbot was used to obtain and configure the TLS certificate.

Install Certbot and the Nginx plugin:

    sudo apt update
    sudo apt install certbot python3-certbot-nginx

Verify the installation:

    certbot --version

---

## 6. Obtaining the TLS Certificate

Before requesting the certificate, the domain must resolve correctly to the public IP address of the network.

The HTTP service must also be reachable from the Internet so that Let's Encrypt can verify domain ownership.

The certificate was requested using:

    sudo certbot --nginx -d wilvergeorpe.is-a.dev

Certbot can automatically modify the Nginx configuration and install the certificate.

During the configuration process, HTTP-to-HTTPS redirection can also be enabled.

---

## 7. Nginx HTTPS Configuration

After Certbot configuration, Nginx handles HTTPS connections on TCP/443.

The configuration conceptually becomes:

    server {
        listen 443 ssl;
        server_name wilvergeorpe.is-a.dev;

        ssl_certificate /etc/letsencrypt/live/wilvergeorpe.is-a.dev/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/wilvergeorpe.is-a.dev/privkey.pem;

        root /var/www/portfolio;
        index index.html;
    }

The exact configuration may differ depending on the Nginx server block generated or modified by Certbot.

---

## 8. HTTP to HTTPS Redirect

HTTP can be redirected to HTTPS so that users accessing:

    http://wilvergeorpe.is-a.dev

are automatically redirected to:

    https://wilvergeorpe.is-a.dev

Conceptually:

    server {
        listen 80;
        server_name wilvergeorpe.is-a.dev;

        return 301 https://$host$request_uri;
    }

This ensures that the website uses the encrypted HTTPS connection instead of continuing to serve the application over plain HTTP.

---

## 9. Testing the Nginx Configuration

Before reloading Nginx, the configuration should be tested:

    sudo nginx -t

A successful result should contain:

    syntax is ok
    test is successful

Nginx can then be reloaded:

    sudo systemctl reload nginx

Check the service:

    sudo systemctl status nginx

---

## 10. Testing HTTPS

From the Ubuntu server, basic HTTPS connectivity can be tested with:

    curl -I https://wilvergeorpe.is-a.dev

The response should indicate a successful HTTP response.

From a client browser, access:

    https://wilvergeorpe.is-a.dev

The browser should establish a TLS connection and display the website without a certificate warning.
![Website accessible over HTTPS](website.png)

---

## 11. Certificate Verification

The certificate can be inspected using:

    sudo certbot certificates

This displays information about the certificates managed by Certbot, including:

- Certificate name
- Domain
- Expiration date
- Certificate file location
- Private key location

The certificate and private key are normally stored under:

    /etc/letsencrypt/

The private key should never be committed to the GitHub repository.

---

## 12. Certificate Renewal

Let's Encrypt certificates are short-lived and require periodic renewal.

Certbot normally installs a renewal mechanism automatically.

A renewal test can be performed using:

    sudo certbot renew --dry-run

The `--dry-run` option tests the renewal process without actually replacing the current certificate.

A successful test indicates that the automatic renewal process should work when the certificate approaches expiration.

---

## 13. Troubleshooting

If HTTPS does not work, troubleshoot from the outside toward the server.

### Step 1 — Check DNS

Verify that the domain resolves to the expected public IP:

    nslookup wilvergeorpe.is-a.dev

or:

    dig wilvergeorpe.is-a.dev

The returned address should correspond to the public WAN address of the home network.

---

### Step 2 — Check Router Port Forwarding

Verify that the router forwards:

    TCP/80
        ->
    192.168.1.128:80

    TCP/443
        ->
    192.168.1.128:443

---

### Step 3 — Check Nginx Listening Ports

On Ubuntu:

    sudo ss -tulpn

Nginx should be listening on the required ports.

For HTTPS:

    TCP/443

For HTTP:

    TCP/80

---

### Step 4 — Check Nginx Configuration

    sudo nginx -t

---

### Step 5 — Check Nginx Status

    sudo systemctl status nginx

---

### Step 6 — Check Nginx Logs

Access log:

    sudo tail -f /var/log/nginx/access.log

Error log:

    sudo tail -f /var/log/nginx/error.log

---

## 14. Chromium vs Firefox Troubleshooting Lesson

One of the useful troubleshooting lessons from this project was that different browsers may handle HTTP differently.

Initially:

    HTTP
      |
      +---- Firefox
      |       |
      |       +--> HTTP connection succeeds
      |
      +---- Chromium-based browser
              |
              +--> HTTP upgraded to HTTPS
                      |
                      +--> HTTPS not yet configured
                      |
                      +--> Connection failure

This made the problem initially appear to be browser-specific.

However, testing different browsers helped identify that the actual infrastructure was incomplete.

The important troubleshooting principle is:

> If one browser works while another does not, do not immediately assume the browser is the root cause.

The browser may simply be exposing a problem that another browser is not.

In this case, HTTPS had to be properly configured on the server so that connections to TCP/443 would succeed.

---

## 15. Final HTTPS Architecture

After HTTPS was configured, the complete architecture became:

                    INTERNET
                        |
                        |
                Public IP Address
                        |
                        |
                PLDT Huawei Router
                NAT / Port Forwarding
                   |            |
                 TCP/80       TCP/443
                   |            |
                   +------ +-----+
                          |
                          v
                Ubuntu Linux Server
                   192.168.1.128
                          |
                          v
                        Nginx
                     /        \
                    /          \
             HTTP :80        HTTPS :443
                |                |
                |          TLS Certificate
                |                |
                +-------> Redirect
                              |
                              v
                       Web Application

The final intended behavior is:

    HTTP request
         |
         v
    Nginx :80
         |
         v
    HTTP Redirect
         |
         v
    Nginx :443
         |
         v
    TLS encryption
         |
         v
    Web Application

---

## 16. Key Concepts Learned

This configuration demonstrated several infrastructure concepts:

- HTTP vs HTTPS
- TCP port 80
- TCP port 443
- TLS
- TLS certificates
- Certificate Authorities
- Let's Encrypt
- Certbot
- Nginx SSL/TLS configuration
- HTTP-to-HTTPS redirection
- DNS
- NAT
- Port forwarding
- Public vs private IP addresses
- Linux service management
- Nginx troubleshooting
- Browser HTTPS behavior
- Certificate renewal

The most important lesson was that HTTPS is not simply a browser feature.

It requires coordination between:

    DNS
      |
    Public IP
      |
    Router / NAT
      |
    Port Forwarding
      |
    Linux Server
      |
    Nginx
      |
    TLS Certificate
      |
    HTTPS

A failure at any point in this chain can prevent users from accessing the website securely.