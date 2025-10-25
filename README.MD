# Secure Email Notifications for Fail2Ban using Gmail SMTP, Gmail API, and Integrated Python Script

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

---

## Abstract

This document provides a comprehensive and structured guide to configuring **Fail2Ban** on Ubuntu-based VPS environments, integrating **secure email notifications** using **Gmail SMTP**, **Gmail API (OAuth2)**, and an **embedded Python script** (`send_gmail.py`) for sending alert messages directly from Fail2Ban actions.

It also covers optional enhancements like **Telegram/Discord notifications**, **WHOIS geolocation**, and **multi-service protection**.

All scripts and configuration files included must be **UTF-8 encoded** to ensure compatibility across systems.

---

## 1. Introduction

**Fail2Ban** monitors system logs for malicious authentication attempts and dynamically updates firewall rules to temporarily ban offending IPs.

This guide extends standard setups by:
- Adding secure email notifications (SMTP + OAuth2 Gmail API)
- Integrating a **Python automation script** for email delivery
- Enabling IP geolocation (WHOIS)
- Providing examples for NGINX, WordPress, Telegram, and Discord
- Enhancing modularity and readability for GitHub presentation

---

## 2. Prerequisites

- **OS:** Ubuntu 22.04.5 LTS or newer  
- **Package:** Fail2Ban (`apt install fail2ban`)  
- **Account:** Gmail with 2-Step Verification  
- **Optional:** Google Cloud project with Gmail API enabled (OAuth2)  
- **No MTA required:** email sent directly through Python or Gmail servers  
- **Encoding:** All configuration and script files must be **UTF-8**

---

## 3. Fail2Ban Installation

```bash
sudo apt update
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
sudo systemctl status fail2ban
```

---

## 4. Gmail SMTP Configuration

### 4.1 App Password Setup

1. Enable **2-Step Verification**  
2. Go to <https://myaccount.google.com/apppasswords>  
3. Generate a password for “Mail”  
4. Store it securely  

### 4.2 Fail2Ban Action File

Create the action file:

```bash
sudo nano /etc/fail2ban/action.d/sendmail-gmail.conf
```

Insert:

```ini
[Definition]
actionstart =
actionstop =
actioncheck =

actionban = /etc/fail2ban/send_gmail.py <name> <ip> <fq-hostname> ban
actionunban = /etc/fail2ban/send_gmail.py <name> <ip> <fq-hostname> unban

[Init]
```

Ensure this file is saved as **UTF-8**.

---

## 5. Integrated Python Script (send_gmail.py)

A lightweight **Python 3 script** is included to handle secure mail delivery through Gmail’s SMTP server.

File: `/etc/fail2ban/send_gmail.py`

```python
#!/usr/bin/env python3
import smtplib, ssl, sys
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from datetime import datetime

# Parameters from Fail2Ban
jail = sys.argv[1]
ip = sys.argv[2]
hostname = sys.argv[3]
action = sys.argv[4]  # "ban" or "unban"

# Email
subject = f"Fail2Ban: {jail} {action}ned {ip}"
body = f"Host: {hostname}\nJail: {jail}\nIP: {ip}\nDate: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}"

sender_email = "youraddress@gmail.com"
receiver_email = "youraddress@gmail.com"
password = "<YOUR_GMAIL_APP_PASSWORD>"

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

Make it executable:

```bash
sudo chmod +x /etc/fail2ban/send_gmail.py
```

---

## 6. Gmail API (OAuth2) – Optional Method

For users preferring **token-based authentication**, the Gmail API can be used instead of storing passwords.

```python
from google.oauth2.credentials import Credentials
from googleapiclient.discovery import build
from email.mime.text import MIMEText
import base64

def send_gmail_message(subject, body, to):
    creds = Credentials.from_authorized_user_file('token.json', ['https://www.googleapis.com/auth/gmail.send'])
    service = build('gmail', 'v1', credentials=creds)
    message = MIMEText(body)
    message['to'] = to
    message['subject'] = subject
    raw = base64.urlsafe_b64encode(message.as_bytes()).decode()
    service.users().messages().send(userId="me", body={'raw': raw}).execute()
```

---

## 7. Example jail.local Configuration

File: `/etc/fail2ban/jail.local`

```ini
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

Save this file with **UTF-8 encoding**.

---

## 8. Security Enhancements

- Limit access:
  ```bash
  sudo chmod 600 /etc/fail2ban/action.d/sendmail-gmail.conf
  ```
- Store passwords securely (GPG or systemd-credentials)
- Prefer Gmail API tokens to plain credentials

---

## 9. Advanced Examples

### 9.1 NGINX Protection
```ini
[nginx-http-auth]
enabled  = true
port     = http,https
logpath  = /var/log/nginx/error.log
maxretry = 3
```

### 9.2 WordPress Brute-Force
```ini
[wordpress]
enabled = true
port = http,https
filter = wordpress
logpath = /var/www/*/logs/access.log
```

### 9.3 Telegram Notification
```ini
[Definition]
actionban = curl -s -X POST https://api.telegram.org/bot<BOT_TOKEN>/sendMessage \
  -d chat_id=<CHAT_ID> \
  -d text="⚠️ Fail2Ban Alert\nJail: <name>\nIP: <ip>\nHost: <fq-hostname>\nTime: $(date '+%Y-%m-%d %H:%M:%S')"
```

---

## 10. Conclusion

Fail2Ban combined with Gmail, Python scripting, and optional Telegram/Discord notifications provides robust real-time intrusion alerts with minimal overhead.

All included files must be **UTF-8 encoded** and executable where required.

---

**Author:** © 2025 Michele Tignanelli  
**License:** MIT  
**Website:** [https://michele-tn.github.io/michele-tn/](https://michele-tn.github.io/michele-tn/)
