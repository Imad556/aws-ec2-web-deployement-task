# AWS EC2 Web Deployment Guide

A complete DevOps and cloud deployment guide for hosting a static web application on an Ubuntu Linux EC2 instance, configuring Nginx, mapping a custom domain with DuckDNS, and securing the site with HTTPS using Let's Encrypt.

---

## 🚀 Live Deployment

- Project source: [TSK-000-36-Internee-Clone](https://github.com/zulqarnainahmed07/TSK-000-36-Internee-Clone)
- Live domain: `imad-internee-cloud.duckdns.org`
- Web root: `/var/www/html`

---

## 🏗️ Architecture Overview

```text
[ User / Browser ]
       │ (HTTPS / Port 443)
       ▼
[ DuckDNS Domain: imad-internee-cloud.duckdns.org ]
       │ (Resolves to public IP)
       ▼
[ AWS EC2 Ubuntu Instance ]
       │
       ▼
[ Nginx Web Server ]
       │
       ▼
[ /var/www/html ]
```

---

## 📋 Prerequisites

Before starting, make sure you have:

- An AWS account
- An Ubuntu EC2 instance launched with a public IP
- SSH access to the instance
- A DuckDNS account and subdomain
- Open inbound rules for port 80 and 443

---

## 1. Launch an AWS EC2 Instance

1. Go to the AWS Management Console.
2. Launch a new EC2 instance using the Ubuntu Server AMI.
3. Save your `.pem` private key.
4. Configure the security group to allow the following traffic:

- SSH: Port 22 from your IP
- HTTP: Port 80 from anywhere (`0.0.0.0/0`)
- HTTPS: Port 443 from anywhere (`0.0.0.0/0`)

<p align="center">
  <img src="assets/images/aws-ec2.png" alt="AWS EC2 instance setup" width="900">
</p>

---

## 2. Connect to the EC2 Instance

Open your terminal and connect using SSH:

```bash
ssh -i "your-key.pem" ubuntu@YOUR_EC2_PUBLIC_IP
```

<p align="center">
  <img src="assets/images/ssh.png" alt="SSH into EC2 instance" width="900">
</p>

---

## 3. Install and Start Nginx

Update the system and install Nginx:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install nginx -y
```

Start and enable Nginx:

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

Verify the service status:

```bash
sudo systemctl status nginx
```

---

## 4. Deploy the Web Application

Clear the default web directory and clone the static app into `/var/www/html`:

```bash
sudo rm -rf /var/www/html/*
sudo git clone https://github.com/zulqarnainahmed07/TSK-000-36-Internee-Clone /var/www/html/
```

Fix ownership and permissions so Nginx can serve the files correctly:

```bash
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

If the app shows a 403 error, the common cause is wrong ownership or permissions.

---

## 5. Configure DuckDNS Domain

1. Sign in to DuckDNS.
2. Create a free subdomain such as `imad-internee-cloud.duckdns.org`.
3. Enter your EC2 public IPv4 address in the current IP field.
4. Click Update IP.

<p align="center">
  <img src="assets/images/duck-dns.png" alt="DuckDNS domain configuration" width="900">
</p>

---

## 6. Update the Nginx Configuration

Open the default server configuration:

```bash
sudo nano /etc/nginx/sites-available/default
```

Update the `server_name` directive to match your DuckDNS domain:

```nginx
server {
    listen 80;
    server_name imad-internee-cloud.duckdns.org;
    root /var/www/html;
    index index.html;
}
```

Save and exit.

Test the Nginx configuration:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

<p align="center">
  <img src="assets/images/nginx-config.png" alt="Nginx configuration" width="900">
</p>

---

## 7. Secure the Site with Let's Encrypt SSL

Install Certbot and the Nginx plugin:

```bash
sudo apt install certbot python3-certbot-nginx -y
```

Request and install the certificate:

```bash
sudo certbot --nginx -d imad-internee-cloud.duckdns.org
```

Follow the prompts:

- Enter your email
- Agree to the terms of service
- Choose the option to redirect HTTP traffic to HTTPS

This will automatically configure SSL and redirect all HTTP requests to HTTPS.

<p align="center">
  <img src="assets/images/lets-encrypt.png" alt="Let's Encrypt SSL setup" width="900">
</p>

---

## ✅ Final Result

Your website is now live and secured with HTTPS:

- Domain: `https://imad-internee-cloud.duckdns.org`
- Nginx is serving the static site
- SSL certificate is managed by Certbot
- HTTP automatically redirects to HTTPS

<p align="center">
  <img src="assets/images/https-live-website.png" alt="HTTPS live website" width="900">
</p>

---

## 💡 Author

Imad Ali

Internee.pk Virtual Internship Task