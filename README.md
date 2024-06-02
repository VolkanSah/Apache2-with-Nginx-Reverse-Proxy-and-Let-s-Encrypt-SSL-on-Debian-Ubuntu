
# Install Apache2 with Nginx Reverse Proxy and Let's Encrypt SSL on Debian/Ubuntu (Update 2024)

This README.md file will guide you through setting up Apache2 as your dynamic web server, Nginx as a reverse proxy, and securing your setup with Let's Encrypt SSL certificates on Debian or Ubuntu systems. 

## Table of Contents
- [Prerequisites](#prerequisites)
- [Installing Apache2](#installing-apache2)
- [Installing Nginx](#installing-nginx)
- [Configuring Nginx as a Reverse Proxy](#configuring-nginx-as-a-reverse-proxy)
- [Installing Certbot and Let's Encrypt SSL Certificates](#installing-certbot-and-lets-encrypt-ssl-certificates)
- [Configuring Apache2 and Nginx for SSL](#configuring-apache2-and-nginx-for-ssl)
- [Testing the Setup](#testing-the-setup)

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

## Installing Nginx
Update your package lists:

```bash
sudo apt update
```
Install the Nginx web server:

```bash
sudo apt install nginx
```
Enable Nginx to start at boot:

```bash
sudo systemctl enable nginx
```
Start the Nginx service:

```bash
sudo systemctl start nginx
```
Verify that Nginx is running:

```bash
sudo systemctl status nginx
```

## Configuring Nginx as a Reverse Proxy
Create a new Nginx configuration file for your domain:

```bash
sudo nano /etc/nginx/sites-available/example.com
```
Replace example.com with your domain name.

Add the following configuration to the file, replacing example.com with your domain name and YOUR_APACHE2_IP with your Apache2 server's IP address (usually 127.0.0.1):

```bash
server {
    listen 80;
    server_name example.com www.example.com;
    location / {
        proxy_pass http://YOUR_APACHE2_IP:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
Save and close the file.

Create a symbolic link to the configuration file in the sites-enabled directory:

```bash
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
```

Edit the Apache2 ports configuration file:

```bash
sudo nano /etc/apache2/ports.conf
```
Change the Listen directive from 80 to 8080:

```bash
Listen 8080
```
Save and close the file.

Restart the Apache2 and Nginx services to apply the changes:

```bash
sudo systemctl restart apache2
sudo systemctl restart nginx
```

Check if Nginx and Apache2 are running and listening on the correct ports:

```bash
sudo netstat -tuln | grep -E 'apache|nginx'
```
You should see Nginx listening on port 80 and Apache2 listening on port 8080.

## Installing Certbot and Let's Encrypt SSL Certificates
Install the Certbot package and the Nginx plugin:

```bash
sudo apt install certbot python3-certbot-nginx
```
Obtain and install the SSL certificate:

```bash
sudo certbot --nginx -d example.com -d www.example.com
```
Replace example.com and www.example.com with your domain name and its subdomains. Follow the on-screen prompts to complete the process.

Certbot should automatically configure Nginx for SSL. Verify that the SSL configuration is present in the Nginx configuration file:

```bash
sudo nano /etc/nginx/sites-available/example.com
```
You should see a server block for port 443 with the SSL configuration.

Test the SSL renewal process:

```bash
sudo certbot renew --dry-run
```
This command simulates the renewal process without making any changes. If the process is successful, your SSL certificates will automatically renew when they are about to expire.

## Configuring Apache2 and Nginx for SSL
Edit the Nginx configuration file for your domain:

```bash
sudo nano /etc/nginx/sites-available/example.com
```
In the server block for port 443, change the proxy_pass directive to use https:// and port 8443:

```bash
proxy_pass https://YOUR_APACHE2_IP:8443;
```
Save and close the file.

Edit the Apache2 ports configuration file:

```bash
sudo nano /etc/apache2/ports.conf
```
Add a new Listen directive for port 8443:

```bash
Listen 8443
```
Save and close the file.

Create a new Apache2 virtual host configuration file for SSL:

```bash
sudo nano /etc/apache2/sites-available/example.com-le-ssl.conf
```
Add the following configuration, replacing example.com with your domain name, YOUR_APACHE2_IP with your Apache2 server's IP address, and /etc/letsencrypt/live/example.com/ with the correct path to your Let's Encrypt certificates:

```bash
<IfModule mod_ssl.c>
    <VirtualHost YOUR_APACHE2_IP:8443>
        ServerName example.com
        ServerAlias www.example.com
        DocumentRoot /var/www/html

        SSLEngine on
        SSLCertificateFile /etc/letsencrypt/live/example.com/fullchain.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/example.com/privkey.pem
        SSLProtocol all -SSLv2 -SSLv3
        SSLCipherSuite HIGH:!aNULL:!MD5

        ErrorLog ${APACHE_LOG_DIR}/example.com-error.log
        CustomLog ${APACHE_LOG_DIR}/example.com-access.log combined
    </VirtualHost>
</IfModule>
```
Save and close the file.

Enable the new SSL virtual host:

```bash
sudo a2ensite example.com-le-ssl
```
Enable the Apache2 SSL module:

```bash
sudo a2enmod ssl
```
Restart the Apache2 and Nginx services to apply the changes:

```bash
sudo systemctl restart apache2
sudo systemctl restart nginx
```

## Testing the Setup

- Open a web browser and visit your domain (e.g., https://example.com). You should see the default Apache2 web page served via Nginx with a secure HTTPS connection.

- Verify that the SSL certificate is correctly installed by checking the padlock icon in your browser's address bar. Click on the padlock to see the certificate details and confirm that it was issued by Let's Encrypt.

That's it! You have successfully set up Apache2 with Nginx as a reverse proxy and secured your site with Let's Encrypt SSL certificates on Debian or Ubuntu. You can now deploy your web applications and enjoy the benefits of this configuration.

## Credits
**Volkan Sah**
