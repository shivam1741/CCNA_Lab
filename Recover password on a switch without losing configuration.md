# Cisco Switch Password Recovery (Without Data Loss)


## Step 1: Connect Console Cable(Not needed for cisco packet tracer)

* Connect your PC to the switch using a console cable and open PuTTY / Tera Term.

> ***Why?***    
> Because when passwords are lost, **console access is the only guaranteed way** to manage the switch.

## Step 2: Power Off the Switch

* Turn OFF the switch.

> ***Why?***    
> We need to restart it in **recovery mode**.

## Step 3: Enter Boot Loader Mode (MODE Button)

1. Press and **HOLD the MODE button**
2. Power ON the switch
3. Hold for **10–15 seconds**
4. Release when LED blinks

 You’ll see:

  ```
  switch:
  ```

> ***Why?***   
> This puts the switch into **boot loader mode**, where passwords are not enforced.

## Step 4: Initialize Flash

```
flash_init
```

> ***Why?***    
> This loads flash memory so the switch can access saved files (config).   
> Without this, you cannot see the old configuration.


## Step 5: Check Configuration File

```
dir flash:
```

You will see:

```
config.text
```

> ***Why?***   
> `config.text` contains:   
> * Passwords   
> * IP settings   
> * VLANs   
> * Port configs  
> We don’t delete it—we protect it.

## Step 6: Rename the Config File

```
rename flash:config.text flash:config.old
```

> ***Why?***   
> The switch only loads **config.text** at boot.    
> By renaming it:    
> * Switch cannot find password file   
> * Boots without security    
> * But data is still safe.


## Step 7: Boot the Switch

```
boot
```

> ***Why?***   
> Now the switch starts **without loading passwords**.    
> So you get access.

## Step 8: Enter Enable Mode

```
enable
```
(No password needed)

> ***Why?***   
> Because no config was loaded → no password exists now.

## Step 9: Restore Old Configuration

```
copy flash:config.old system:running-config
```

> ***Why?***   
> This brings back:   
> * VLANs   
> * IP address    
> * Port configs   
> * Network settings     
> But passwords are not blocking you now.   
> So data is recovered safely.

## Step 10: Set New Password

```
configure terminal

enable secret NEWPASS
```

> ***Why?***   
> After recovery, security is open.   
> So you must set new passwords to protect the switch.

## Step 11: Save Configuration

```
write memory
```

or

```
copy running-config startup-config
```

> ***Why?***   
> So new password + restored config is saved permanently.     
> Otherwise, it will be lost on reboot.
---
