# Cisco Router Password Recovery (Without Data Loss)

## Step 1: Connect via Console(Not for cisco packet tracer)

Connect your PC to the router using a console cable and open:

* PuTTY / Tera Term / SecureCRT


## Step 2: Reboot the Router

* Power OFF → Power ON the router.

* During boot, press:
  Ctrl + c

* (or Ctrl + Fn + B on some laptops)

 You will enter **ROMMON mode**:

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
 “Ignore saved config on next boot because when we use "config-register 0x2142" it ignore the saved configuration.


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

```

## Step 9: Restore Normal Boot Mode

Change register back to normal:

```
config-register 0x2102
```

## Step 10: Save Configuration

```
write memory
```

or

```
copy running-config startup-config
```

---

## Step 11: Reload Router

```
reload
```

Now router will:

* Boot normally
* Load config
* Ask for new password

---


# Lab for Router Password Recovery
![Router password recovery](https://github.com/shivam1741/CCNA_Lab/blob/Image/Screenshot%202026-02-13%20110144.png)


