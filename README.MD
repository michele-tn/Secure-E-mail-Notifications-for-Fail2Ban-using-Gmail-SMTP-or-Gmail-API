# Secure Email Notifications for Fail2Ban using Gmail SMTP or Gmail API

<header style="padding: 16px 0;">
<p align="center">
  <img
    src="https://raw.githubusercontent.com/michele-tn/Secure-E-mail-Notifications-for-Fail2Ban-using-Gmail-SMTP-or-Gmail-API/68e051e8eb4ef57e7afc463b7557d08d5c3d8be7/Fail2BAN.jpeg"
    alt="Fail2Ban Logo"
    width="70%"
    height="600"
  >
</p>


</header>

<!-- 🔔 UPDATE BANNER -->
<details open>
  <summary><strong>📢 OFFICIAL UPDATE NOTICE</strong></summary>
  <blockquote>
    <p><strong>Secure E-mail Notifications for Fail2Ban (Version 2)</strong></p>
    <p>⏱️ Last revision date: <time datetime="2025-10-29T14:56:33+01:00">October 29, 2025 – 14:56:33</time></p>
    <p>🔗 <a href="https://github.com/michele-tn/Secure-E-mail-Notifications-for-Fail2Ban-using-Gmail-SMTP-or-Gmail-API/blob/main/README_Vs2.md">Access the revised documentation</a></p>
  </blockquote>
</details>
</header>


— • — · ⁝ · — • — · ⁝ · — • — · ⁝ · — • — ·
<!-- 🔔 UPDATE BANNER -->
<!-- <details open> <summary><strong>📢 OFFICIAL UPDATE NOTICE</strong></summary> <blockquote> <p><strong>Fail2Ban Manager Script (Version 3)</strong></p> <p>⏱ Last revision date: <time datetime="2025-11-13">November 13, 2025</time></p> <p>🔗 <a href="https://github.com/michele-tn/Secure-E-mail-Notifications-for-Fail2Ban-using-Gmail-SMTP-or-Gmail-API/blob/main/manage_fail2ban_v3.tar.gz">manage_fail2ban_v3.tar.gz</a><br/>

<div>
<p><strong><I>SHA256 checksum</I></strong></p><br/>
<p><strong><I>ad2bff77eb9d46550bb46af4bfd92ddd5914c42600172a14b323100bef325f90</I></strong></p>
</div>
<br/>
</p> <ul> <li>Interactive <strong>SSH port prompt</strong> (auto-detects active port, lets you override).</li> <li><strong>Forced firewall action</strong> (iptables/nft) with backend auto-detect and guaranteed INPUT → <code>f2b-sshd</code> jump.</li> <li>One-click <strong>Unban</strong>: single IP across all jails or <strong>unban all</strong> (uses <code>fail2ban-client unban --all</code> when supported).</li> <li>Optional <strong>Gmail notifications</strong> with prompts for sender/recipient/App Password (press Enter to skip).</li> <li><strong>Purge DB</strong> utility to wipe malformed bans and rebuild chains safely.</li> <li>Built-in <strong>Doctor</strong> (backend/port/rules/logs diagnostics).</li> <li>Hardened flow (best-effort installs, safe fallbacks, resilient to missing tools).</li> </ul> </blockquote> </details> -->
<!-- 🔔 UPDATE BANNER -->
<details open>
    <summary><strong>📢 OFFICIAL UPDATE NOTICE</strong></summary>
    <blockquote>
        <p><strong>Fail2Ban Manager Script (Version 3)</strong></p>
        <p>⏱ Last revision date:
            <time datetime="2025-11-13">November 13, 2025</time>
        </p>
        <p>🔗 <a href="https://github.com/michele-tn/Secure-E-mail-Notifications-for-Fail2Ban-using-Gmail-SMTP-or-Gmail-API/blob/main/manage_fail2ban_v3.tar.gz">manage_fail2ban_v3.tar.gz</a>
            <br/> SHA256 checksum
            <br/> ad2bff77eb9d46550bb46af4bfd92ddd5914c42600172a14b323100bef325f90
            <br/>
        </p>
        <ul>
            <li>Interactive <strong>SSH port prompt</strong> (auto-detects active port, lets you override).</li>
            <li><strong>Forced firewall action</strong> (iptables/nft) with backend auto-detect and guaranteed INPUT → <code>f2b-sshd</code> jump.</li>
            <li>One-click <strong>Unban</strong>: single IP across all jails or <strong>unban all</strong> (uses <code>fail2ban-client unban --all</code> when supported).</li>
            <li>Optional <strong>Gmail notifications</strong> with prompts for sender/recipient/App Password (press Enter to skip).</li>
            <li>Automatic <strong>HTML email reports</strong> for SSH connection attempts and bans, including <strong>WHOIS</strong>, reverse DNS, username, authentication method, and correlated log snippet.</li>
            <li><strong>Purge DB</strong> utility to wipe malformed bans and rebuild chains safely.</li>
            <li>Built-in <strong>Doctor</strong> (backend/port/rules/logs diagnostics).</li>
            <li>Hardened flow (best-effort installs, safe fallbacks, resilient to missing tools).</li>
        </ul>
    </blockquote>
    <p align="center"> <img src="https://raw.githubusercontent.com/michele-tn/Secure-E-mail-Notifications-for-Fail2Ban-using-Gmail-SMTP-or-Gmail-API/main/MANAGE_fail2ban_Vs3.jpg" alt="Fail2Ban Logo" width="70%" height="600"> </p>
</details>

— • — · ⁝ · — • — · ⁝ · — • — · ⁝ · — • — ·

------------------------------------------------------------------------

## Abstract

This document provides a comprehensive and structured guide
to configuring **Fail2Ban** on Ubuntu-based Virtual Private Servers
(VPS) with secure **email notification capabilities** through **Gmail
SMTP**, **Gmail API (OAuth2)**, and improved configurations such as
**Telegram/Discord alerts**, **WHOIS geolocation**, and **multi-service
protection**.

The goal is to ensure proactive intrusion detection and immediate
administrative awareness of unauthorized access attempts, leveraging
lightweight configurations without requiring additional mail transfer
agents.

------------------------------------------------------------------------

## 1. Introduction

**Fail2Ban** is a widely adopted intrusion prevention framework that
monitors system logs for malicious authentication attempts. Upon
detecting suspicious behavior, it dynamically updates firewall rules to
**temporarily ban** the attacking IP address.

This guide enhances the basic Fail2Ban setup by: - Adding secure email
notifications (SMTP + OAuth2 Gmail API) - Enabling IP geolocation via
WHOIS - Including examples for NGINX, WordPress, and Telegram/Discord
alerts - Improving structure for GitHub README compatibility

------------------------------------------------------------------------

## 2. Prerequisites

- **Operating System:** Ubuntu 22.04.5 LTS or newer  
- **Software:** Fail2Ban (installed via APT)  
- **Account:** Gmail account with 2‑Step Verification enabled  
- **Optional:** Google Cloud project with Gmail API enabled (for OAuth2 authentication)  
- **No local MTA (Mail Transfer Agent)** is required

------------------------------------------------------------------------

## 3. Fail2Ban Installation

``` bash
sudo apt update
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
sudo systemctl status fail2ban
```

------------------------------------------------------------------------

## 4. Gmail SMTP Configuration

### 4.1 App Password Setup

1.  Enable **2-Step Verification** in your Google account\
2.  Go to: https://myaccount.google.com/apppasswords\
3.  Generate a password for "Mail"\
4.  Copy and store it securely

### 4.2 Fail2Ban Action File

Create:

``` bash
sudo nano /etc/fail2ban/action.d/sendmail-gmail.conf
```

Insert:

``` 
[Definition]

actionstart =
actionstop =
actioncheck =

actionban = /etc/fail2ban/send_gmail.py <name> <ip> <fq-hostname> ban
actionunban = /etc/fail2ban/send_gmail.py <name> <ip> <fq-hostname> unban

[Init]
```

### 4.3 Integration with jail.local

``` 
[DEFAULT]
bantime  = 10m
findtime = 10m
maxretry = 3

action = %(action_mwl)s
         sendmail-gmail[name=SSH, dest=youraddress@gmail.com, sender=youraddress@gmail.com]

[sshd]
enabled  = true
port     = ssh
logpath  = /var/log/auth.log
banaction = iptables-multiport

```

------------------------------------------------------------------------

## 5. Gmail API (OAuth2)

*(Secures email without storing SMTP passwords)*

### 5.1 Setup

1.  Access Google Cloud Console\
2.  Enable Gmail API\
3.  Create OAuth Client ID → Desktop App\
4.  Download `client_secret.json`

### 5.2 Python Script

``` python
#!/usr/bin/env python3
import smtplib, ssl, sys
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from datetime import datetime

# Parametri da Fail2Ban
jail = sys.argv[1]
ip = sys.argv[2]
hostname = sys.argv[3]
action = sys.argv[4]  # "ban" o "unban"

# Email
subject = f"Fail2Ban: {jail} {action}ned {ip}"
body = f"""Host: {hostname}
Jail: {jail}
IP: {ip}
Date: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}"""

sender_email = "youraddress@gmail.com"
receiver_email = "youraddress@gmail.com"
password = "YOUR_APP_PASSWORD"

message = MIMEMultipart()
message["From"] = sender_email
message["To"] = receiver_email
message["Subject"] = subject
message.attach(MIMEText(body, "plain"))

context = ssl.create_default_context()
with smtplib.SMTP("smtp.gmail.com", 587) as server:
    server.starttls(context=context)
    server.login(sender_email, password)
    server.sendmail(sender_email, receiver_email, message.as_string())
```

------------------------------------------------------------------------

## 6. Security Enhancements

-   Limit access:

    ``` bash
    sudo chmod 600 /etc/fail2ban/action.d/sendmail-gmail.conf
    ```

-   Use encrypted credential storage (`systemd-credentials`, GPG)

-   Avoid plaintext passwords wherever possible

------------------------------------------------------------------------

## 7. Example jail.local (SSH + Gmail)

``` ini
[DEFAULT]
bantime  = 10m
findtime = 10m
maxretry = 3

action = %(action_mwl)s
         sendmail-gmail[name=SSH, dest=youraddress@gmail.com, sender=youraddress@gmail.com]

[sshd]
enabled  = true
port     = ssh
logpath  = /var/log/auth.log
banaction = iptables-multiport
```

------------------------------------------------------------------------

## 8. Advanced Examples

### 8.1 NGINX Protection

``` ini
[nginx-http-auth]
enabled  = true
port     = http,https
logpath  = /var/log/nginx/error.log
maxretry = 3
```

### 8.2 WordPress Brute-Force Mitigation

``` ini
[wordpress]
enabled = true
port = http,https
filter = wordpress
logpath = /var/www/*/logs/access.log
```

### 8.3 Telegram Notification

``` ini
[Definition]
actionban = curl -s -X POST https://api.telegram.org/bot<BOT_TOKEN>/sendMessage             -d chat_id=<CHAT_ID>             -d text="⚠️ Fail2Ban Alert
Jail: <name>
IP: <ip>
Host: <fq-hostname>
Time: $(date '+%Y-%m-%d %H:%M:%S')"
```

------------------------------------------------------------------------

## 9. Conclusion

Fail2Ban combined with Gmail/Telegram delivers real-time security
awareness and active intrusion prevention with minimal system overhead.
This **GitHub-ready guide** provides both basic and advanced
configurations suitable for professional and academic environments.

------------------------------------------------------------------------

© 2025 --- Michele TN\
Website: https://michele-tn.github.io/michele-tn/
License: MIT
