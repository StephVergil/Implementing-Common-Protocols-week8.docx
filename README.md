# Implementing Common Protocols: A Comprehensive Guide

This guide provides a detailed, practical approach to configuring and securing network protocols using **pfSense**, **Squid Proxy Server**, and **Apache Web Server**. The objective is to ensure secure data management, efficient proxy usage, and SSL implementation for secure communications.

---

## Project Link

[Implementing Common Protocols](https://github.com/StephVergil/Implementing-Common-Protocols-week8.docx/blob/main/Implementing%20Common%20Protocols%20week8.docx)

---

## Overview

This project covers the setup and management of critical network protocols to enhance security and functionality. Key components include:

1. **Squid Proxy Server**: Manage web traffic and enforce access controls.
2. **SquidGuard**: Apply URL filtering and custom access rules.
3. **SSL Configuration**: Create self-signed certificates and secure HTTP services.

---

## Step-by-Step Guide

### 1. Configuring Squid Proxy Server

#### **1.1 Verify Installation**
1. Log in to **UbuntuSRV VM**.
2. Open a browser and navigate to `172.16.1.1`.
3. Log into **pfSense** using the provided credentials.
4. Verify that `squid` and `squidGuard` are installed via **System > Package Manager**.

#### **1.2 Configure Proxy Settings**
1. Navigate to **Services > Squid Proxy Server** and select the **Local Cache** tab.
2. Set **Hard Disk Cache Size** to `50` MB and click **Save**.
3. In the **General** tab:
   - Enable **Squid Proxy**.
   - Set **Proxy Interface** to `LAN` and `DMZ`.
   - Use `3128` as the **Proxy Port** and enable **Transparent Proxy**.
   - Configure logging:
     - Log Directory: `/var/squid/logs`
     - Rotate Logs: `7`
   - Set the hostname to `proxy.pfsense` and admin email to `pfproxy@mail.netlab.local`.

4. Save the configuration.

#### **1.3 Configure ACLs**
1. In the **ACLs** tab:
   - Add `192.168.0.0/24` and `172.16.1.0/28` to **Allowed Subnets**.
   - Define **Safe Ports** (`80`, `443`) and **SSL Ports** (`443`).
2. Save the changes.

#### **1.4 Traffic Management**
1. In the **Traffic Mgmt** tab:
   - Set **Maximum Download Size** to `500000` (500 MB).
   - Set **Maximum Upload Size** to `50000` (50 MB).
2. Save the settings.

---

### 2. Configuring SquidGuard

#### **2.1 Enable SquidGuard**
1. Navigate to **Services > SquidGuard Proxy Filter**.
2. In **General Settings**:
   - Enable **SquidGuard**.
   - Check **GUI log** and **Log**.
3. Save and apply the settings.

#### **2.2 Add Target Categories**
1. In **Target Categories**, add a new item:
   - **Name**: `Blist1`
   - **Domain List**: `casino.com`
   - **Redirect Mode**: `Error page`
   - Enable **Log entry**.
2. Save the configuration.

#### **2.3 Configure ACL Rules**
1. In the **Common ACL** tab:
   - Set `Blist1` to `Deny` and `Default Access [all]` to `Allow`.
   - For denied requests, use the message: `Request denied by the XYZ Security proxy.`
   - Enable logging.
2. Save and apply the configuration.

---

### 3. Configuring and Enabling SSL for HTTP Services

#### **3.1 Generate a Server Key and Certificate**
1. Create a directory for SSL files:
   ```bash
   mkdir sslcerts
   cd sslcerts
   ```
2. Verify OpenSSL installation:
   ```bash
   openssl version
   ```
3. Generate a private key:
   ```bash
   openssl genrsa -des3 -out server.key 2048
   ```
   - Use `NDGlabpass123!` as the passphrase.

4. Create a Certificate Signing Request (CSR):
   ```bash
   openssl req -new -key server.key -out server.csr
   ```
   - Example values:
     - Country: `US`
     - State: `TX`
     - Organization: `XYZ Security`
     - Common Name: `ubuntusrv.netlab.local`

5. Generate a certificate:
   ```bash
   openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
   ```
6. Confirm the generated files:
   ```bash
   ls -l
   ```

#### **3.2 Configure Apache for SSL**
1. Copy SSL files to Apache:
   ```bash
   sudo cp server.key /etc/apache2/ssl_certs
   sudo cp server.crt /etc/apache2/ssl_certs
   ```
2. Rename and prepare the key:
   ```bash
   sudo mv /etc/apache2/ssl_certs/server.key.nopass /etc/apache2/ssl_certs/server.key
   ```
3. Enable SSL in Apache:
   ```bash
   sudo a2enmod ssl
   sudo service apache2 restart
   ```
4. Configure the SSL site:
   ```bash
   sudo nano /etc/apache2/sites-available/default-ssl.conf
   ```
   - Set `ServerName` to `172.16.1.10:443`.
5. Restart Apache:
   ```bash
   sudo service apache2 restart
   ```

#### **3.3 Test HTTPS Configuration**
1. Create a test HTML page:
   ```bash
   sudo nano /var/www_ssl/index.html
   ```
   - Add:
     ```html
     <html>
     <body>
     <h1>Testing HTTPS Service</h1>
     </body>
     </html>
     ```
2. Restart Apache:
   ```bash
   sudo service apache2 restart
   ```
3. Access the page via a browser at `https://172.16.1.10`.
4. Verify the SSL certificate by clicking the lock icon.

---

## Resources

### General Documentation
- [pfSense Documentation](https://docs.netgate.com/pfsense/en/latest/)
- [Squid Proxy Documentation](http://www.squid-cache.org/Doc/)
- [OpenSSL Manual](https://www.openssl.org/docs/)
- [Apache HTTP Server Documentation](https://httpd.apache.org/docs/)

### Tools and Tutorials
- **SSL Testing Tools**:
  - [SSL Labs Test](https://www.ssllabs.com/ssltest/)
  - [SSL Checker](https://www.sslshopper.com/ssl-checker.html)
- **Networking Tools**:
  - [Wireshark](https://www.wireshark.org/)
  - [Nmap Reference Guide](https://nmap.org/book/)

---

## Key Takeaways

1. **Proxy Configuration**: Squid and SquidGuard provide robust tools for managing web traffic and access.
2. **SSL Implementation**: Secure web services with self-signed certificates and SSL configurations.
3. **Network Security**: Apply best practices for securing HTTP and HTTPS protocols in real-world scenarios.

---
### Disclaimer
This project was conducted in a controlled environment. Unauthorized use of these techniques or tools outside such an environment may violate ethical guidelines and legal regulations.

--- 

> **Author**: Stephanie Vergil
