# 🐧 Arch Linux CLI Mastery 🐧  
> 🔧 Built by following the [Ultimate Algorithm Blueprint](https://jawadabbasi14.github.io/the-ultimate-algorithm-blueprint/)

> ⚠️ **Status:** 🚧 In Progress  
> This repository documents my full journey of mastering **Arch Linux (CLI-only)** — from zero to power-user — as per my personal roadmap.

---

## 📌 About This Repository

This repo is a complete step-by-step log of all my learning, experiments, and automation done inside a **fully minimal, terminal-based Arch Linux system**.

I’m following my personal blueprint to:

- Build muscle memory of Linux CLI  
- Understand system internals deeply  
- Automate common tasks with shell scripts  
- Configure Arch like a hacker’s OS  
- Learn by **breaking & fixing** everything from scratch  

---

## 🧱 Learning Breakdown (From My Roadmap)

| Phase        | Topics                                                                 |
|--------------|------------------------------------------------------------------------|
| 🧠 Buniyadi   | `pacman`, file system, basic shell, user groups, permissions, `nano`, `vi` |
| 🛠️ Wasooli   | `bash`, shell scripting, services (`systemd`), journald, networking, SSH |
| 🔐 Mehfooz    | firewall (`iptables`, `nftables`), fail2ban, hardening, chroot/rescue |
| 👑 Shahana    | automation, cron jobs, bootloader, custom ISO, dynamic shell tools      |

---

## 📂 Folder Structure

```bash
arch-linux-cli-mastery/
│
├── 01_basics_shell/
│   ├── cli_navigation.md
│   ├── file_permissions.sh
│   ├── users_and_groups.sh
│
├── 02_pacman_aur/
│   ├── pacman_cheatsheet.md
│   ├── yay_setup.sh
│
├── 03_services_systemd/
│   ├── create_custom_service.sh
│   ├── logs_monitoring.md
│
├── 04_networking_cli/
│   ├── wifi_connect.sh
│   ├── ssh_server_setup.md
│
├── 05_security_firewall/
│   ├── iptables_rules.sh
│   ├── nftables_basic.sh
│   ├── fail2ban_setup.sh
│
├── 06_shell_scripts/
│   ├── auto_backup.sh
│   ├── wifi_toggle.sh
│   ├── disk_cleanup.sh
│
├── 07_boot_recovery/
│   ├── grub_rescue_guide.md
│   ├── arch_chroot_fix.sh
│
└── README.md
