# Secure E-mail Notifications for Fail2Ban (Version 2)
## Detailed Documentation for `manage_fail2ban.sh`

### Overview
This document provides a comprehensive description of the <a href="https://github.com/michele-tn/Secure-E-mail-Notifications-for-Fail2Ban-using-Gmail-SMTP-or-Gmail-API/blob/main/backup_backup_manage_fail2ban_20251029-131947.tar.xz">`manage_fail2ban.sh`</a> Bash script, designed to automate installation, configuration, and management of **Fail2Ban** with secure e-mail notifications via Gmail SMTP or the Gmail API.  
The script represents an evolution of the original repository [Secure E-mail Notifications for Fail2Ban using Gmail SMTP or Gmail API](https://github.com/michele-tn/Secure-E-mail-Notifications-for-Fail2Ban-using-Gmail-SMTP-or-Gmail-API), offering a fully interactive and modularized management tool.

---

## 1. Purpose and Scope
`manage_fail2ban.sh` acts as an **all-in-one automation and management utility** for deploying a hardened Fail2Ban environment. It encapsulates several administrative functions:

- Installation and configuration of Fail2Ban, custom filters, and Gmail-based e-mail notifications.
- Secure credential management using dedicated app passwords.
- Automatic generation of `jail.local`, filters, and action files.
- Intelligent parsing of SSH logs to extract contextual information such as reverse DNS, ASN, country, attempted username, and authentication method.
- Interactive unbanning utilities for single or multiple IPs.

---

## 2. Functional Architecture

### 2.1 Interactive Command Menu
Upon execution, the script provides an interactive textual interface offering three principal operations:

1. **Unban a Single IP** — removes a specified IP address from all active jails.  
2. **Unban All IPs** — clears all active bans across all jails.  
3. **Install/Configure Fail2Ban** — launches a guided installation procedure.

Each selection is validated for correct user privileges, ensuring root-level execution where necessary.

### 2.2 Dependency Detection
The script auto-detects supported package managers (`apt`, `dnf`, `yum`, `pacman`) and ensures the presence of `fail2ban` and `whois`. Where dependencies are missing, it performs installation automatically, adapting to the host system’s environment.

### 2.3 Secure Credential Management
A core security feature is the isolation of the **Gmail App Password**. The password is stored in `/root/.gmail_app_password` with restrictive permissions (`chmod 600`).  
This ensures compliance with the **principle of least privilege** and avoids password leakage in configuration files.

---

## 3. Configuration Details

### 3.1 Jail Configuration
The script programmatically generates `/etc/fail2ban/jail.local` with two jails:
- `sshd`: bans after three failed login attempts within 10 minutes.
- `sshd-monitor`: monitors every connection attempt (successful or failed) for logging and email reporting without banning.

Each jail uses a custom action: `sendmail-gmail`, invoking a Python mailer for detailed HTML reports.

### 3.2 Filters
Two custom filters are created in `/etc/fail2ban/filter.d/`:
- `sshd-extended.conf` — detects failed SSH authentication events.
- `sshd-monitor.conf` — extends the former to include successful connections (`Accepted password/publickey`).

### 3.3 Python Mailer
The mailer, `/usr/local/bin/send_email_gmail.py`, sends enriched HTML reports containing:
- IP address, country, ASN, and reverse DNS.
- Username, authentication method, and remote port.
- Risk classification (LOW/MEDIUM/HIGH).
- Extracted log snippet for contextual analysis.

The mailer uses `smtplib` over TLS and parses logs from `/var/log/auth.log` or `/var/log/secure`.

---

## 4. Security Features
- Gmail App Password stored separately under restricted permissions.
- Service environment variables (`/etc/fail2ban/fail2ban.local.env`) provide sender, recipient, and log path context.
- Optional systemd drop-in file `/etc/systemd/system/fail2ban.service.d/10-env.conf` ensures environment persistence.
- Automatic backup of existing Fail2Ban configuration to `/etc/fail2ban/backup_<timestamp>`.

---

## 5. Implementation Considerations

### 5.1 Compatibility
The script supports Debian, Ubuntu, Red Hat, CentOS, Fedora, and Arch Linux distributions.

### 5.2 Required Components
- **Fail2Ban ≥ 0.11**
- **Python ≥ 3.6**
- **whois utility**
- **Gmail App Password** (16-character token)

### 5.3 Security Hardening Recommendations
1. Disable root SSH login or restrict access to specific IPs.  
2. Use SSH keys exclusively and disable password authentication.  
3. Regularly rotate Gmail App Passwords.  
4. Review `/var/log/fail2ban.log` and `systemctl status fail2ban` periodically.

---

## 6. Usage Instructions

### Initial Setup
```bash
sudo bash manage_fail2ban.sh
```
Follow the interactive prompts for SSH port, sender, recipient, and Gmail app password.

### Testing Email Notifications
```bash
/usr/local/bin/send_email_gmail.py sshd 127.0.0.1 TEST
```

### Unbanning Commands
```bash
# Unban a specific IP
sudo bash manage_fail2ban.sh -> option 1

# Unban all IPs
sudo bash manage_fail2ban.sh -> option 2
```

---

## 7. Academic and Technical Notes
This script exemplifies **defense-in-depth automation** for server intrusion mitigation. By integrating event-driven notification with contextualized reporting, it supports post-event forensic analysis. The HTML-based reporting layer introduces a pedagogical enhancement, bridging **network forensics** and **automated system administration**.

The modularity and transparency of the Bash–Python integration align with contemporary DevSecOps principles and demonstrate applied cyber defense automation techniques suitable for research, education, and production contexts.

---

## 8. File Hierarchy (Post-Installation)

```
/etc/fail2ban/
├── jail.local
├── filter.d/
│   ├── sshd-extended.conf
│   └── sshd-monitor.conf
├── action.d/
│   └── sendmail-gmail.conf
├── backup_<timestamp>/
/usr/local/bin/
└── send_email_gmail.py
/root/.gmail_app_password
```

---

## 9. Licensing and Citation
This documentation corresponds to **Version 2** of the project and should be cited as:

> Michele Tiganelli, *Secure E-mail Notifications for Fail2Ban using Gmail SMTP or Gmail API – Version 2: Automated Installer and Manager Script*, 2025. GitHub Repository: [michele-tn/Secure-E-mail-Notifications-for-Fail2Ban-using-Gmail-SMTP-or-Gmail-API](https://github.com/michele-tn/Secure-E-mail-Notifications-for-Fail2Ban-using-Gmail-SMTP-or-Gmail-API)

Licensed under the [MIT License](https://opensource.org/licenses/MIT).

---

© 2025 Michele-TN. All rights reserved.
