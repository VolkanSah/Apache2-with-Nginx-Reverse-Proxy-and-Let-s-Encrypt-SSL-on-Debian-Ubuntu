# Install Apache2 with Nginx asReverse Proxy and Let's Encrypt SSL on Debian/Ubuntu
This README.md file will guide you through setting up Apache2 as your dynamic web server, Nginx as a reverse proxy, and securing your setup with Let's Encrypt SSL certificates on Debian or Ubuntu systems. 
Table of Contents

    Prerequisites
    Installing Apache2
    Installing Nginx
    Configuring Nginx as a Reverse Proxy
    Installing Certbot and Let's Encrypt SSL Certificates
    Configuring Apache2 and Nginx for SSL
    Testing the Setup

## Prerequisites

- A Debian or Ubuntu-based system
- Root or sudo access to the system
- A registered domain name (e.g., example.com) pointing to your server's IP address

## Installing Apache2
Update your package lists:

```bash
sudo apt update
```
Install the Apache2 web server:

```bash
sudo apt install apache2
```
Enable Apache2 to start at boot:

```bash
sudo systemctl enable apache2
```
Start the Apache2 service:

```bash
sudo systemctl start apache2
```
Verify that Apache2 is running:

```bash
sudo systemctl status apache2
```
