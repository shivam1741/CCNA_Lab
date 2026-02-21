# Backup IOS and Router Configuration to TFTP/FTP Server
* Means how can we save IOS and Router Configuration to TFTP and FTP server for the Backup.
* In this lab we'll see the configuration for this.

## Configuration for backup of the running and startup config files
![Backup](https://github.com/shivam1741/CCNA_Lab/blob/Image/Screenshot%202026-02-19%20132508.png) 
### 1️Backup Running Configuration (running-config)
```
copy running-config tftp
```

After this, the router will ask:

* TFTP server IP
* File name

Example:

```
Address or name of remote host []? 192.168.1.10
Destination filename []? run_backup.cfg
```


### Backup Startup Configuration (startup-config)

```
copy startup-config tftp
```

Example:

```
Address or name of remote host []? 192.168.1.10
Destination filename []? start_backup.cfg
```

---

## If Using FTP Instead of TFTP

First set FTP details:

```
ip ftp username admin
ip ftp password 1234
```

Then:

### Backup Running Config to FTP

```bash
copy running-config ftp
```

### Backup Startup Config to FTP

```bash
copy startup-config ftp
```

---

## 📌 Easy Summary

| File Type      | To TFTP Command            | To FTP Command            |
| -------------- | -------------------------- | ------------------------- |
| Running Config | `copy running-config tftp` | `copy running-config ftp` |
| Startup Config | `copy startup-config tftp` | `copy startup-config ftp` |

---

## 💡 Remember (Important)

* **Running-config** → Current working config (RAM)
* **Startup-config** → Saved config (NVRAM)
* Always back up both for safety ✅

---

If you want, I can next show you **how to restore these backups back to router** also 😄
