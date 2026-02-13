# Cisco Router Password Recovery (Without Data Loss)

## Step 1: Connect via Console

Connect your PC to the router using a console cable and open:

* PuTTY / Tera Term / SecureCRT


## Step 2: Reboot the Router

Power OFF → Power ON the router.

During boot, press:
Ctrl + c

(or Ctrl + Fn + B on some laptops)

➡️ You will enter **ROMMON mode**:

```
rommon 1 >
```

## Step 3: Ignore Startup Configuration

In ROMMON, type:

```
confreg 0x2142
```

Press Enter.

***This tells the router:***
 “Ignore saved config on next boot because when we use "config-register 0x2142" for ignore the saved configuration.


## Step 4: Restart Router

```
reset
```

or

```
reload
```

Router will reboot **without loading passwords**.


## Step 5: Skip Initial Setup

When asked:

```
Would you like to enter the initial configuration dialog? [yes/no]:
```

Type:

```
no
```

Press Enter.


## Step 6: Enter Privileged Mode

```
enable
```

(No password needed now)


## Step 7: Load Old Configuration

Now restore your original config:

```
copy startup-config running-config
```

Your full configuration comes back
 Passwords are not enforced yet


## Step 8: Set New Password

Go to config mode:

```
configure terminal
```

Set new passwords:

```
enable secret NEWPASSWORD

line console 0
password NEWPASSWORD
login

line vty 0 4
password NEWPASSWORD
login
exit
```

---

## 🔹 Step 9: Restore Normal Boot Mode

Change register back to normal:

```bash
config-register 0x2102
```

---

## 🔹 Step 10: Save Configuration

```bash
write memory
```

or

```bash
copy running-config startup-config
```

---

## 🔹 Step 11: Reload Router

```bash
reload
```

Now router will:

✅ Boot normally
✅ Load config
✅ Ask for new password

---

# 📌 Quick Summary

| Step                   | Purpose         |
| ---------------------- | --------------- |
| Ctrl+Break             | Enter ROMMON    |
| 0x2142                 | Ignore password |
| copy startup → running | Restore config  |
| Set new password       | Secure router   |
| 0x2102                 | Normal mode     |
| Save                   | Permanent       |

---

# 🧠 Easy Way to Remember

Think like this:

1️⃣ Break boot → Enter ROMMON
2️⃣ Use 2142 → Bypass password
3️⃣ Load config → Get data back
4️⃣ Set password → Secure
5️⃣ Use 2102 → Normal mode

---

If you want, I can also give you a **one-page cheat sheet** for exams/interviews 😄

