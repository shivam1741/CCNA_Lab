# Lab on "Restore Deleted ISO using FTP or TFTP"
**Before starting Lab, firstly we will see more about FTP and TFTP.**

## Why do we need FTP and TFTP?

Routers and switches are not like PCs.

> They:   
> * Don’t use USB much   
> * Don’t use apps   
> * Don’t use Google Drive

So they need special ways to send/receive files and that ways are FTP and TFTP.


## Why do we need server for using FTP and TFTP?
* Server = Memory Card
* And the ways these server transfer data or files that are FTP and TFTP.

A server works like a **memory card / storage place**.

> It stores:   
> * Backup config   
> * IOS files   
> * System files

And gives them back when needed.


## Laptop Can Be a Server?

* You don’t need a big server.
* Your own laptop can act as:
   * FTP Server
   * TFTP Server


## Two Main Ways to Transfer Files

To talk with the server, devices use:

| Method | Security | Use                 |
| ------ | -------- | ------------------- |
| TFTP   |  No     | Lab / Emergency     |
| FTP    | Yes    | Office / Production |

Both transfer files.


## Why We Use Them

We use FTP/TFTP to:

* Backup settings
* Restore after crash
* Upgrade IOS
* Recover devices

## Real-Life Example

Think like this:

 Phone → SD Card → Backup photos
 Router/Devices → Server → Backup config

## One-Line Summary
> A server works like centralized storage for network devices, and FTP/TFTP are used to transfer configuration and system files between them.

---

# Now Let's Discuss about Lab
![Restore ISO](https://github.com/shivam1741/CCNA_Lab/blob/Image/Screenshot%202026-02-17%20181054.png)
* Router was in rommon mode so firstly we'll see what we can do in rommon mode to see this we'll use `?`.
![rommon](https://github.com/shivam1741/CCNA_Lab/blob/Image/Screenshot%202026-02-18%20214322.png)
* Here we can't boot because we don't have IOS in the router.
* And we want to restore IOS from TFTP server so we'll use the `tftpdnld` command to download tftp image/file from the server.
![tftpdnld](https://github.com/shivam1741/CCNA_Lab/blob/Image/Screenshot%202026-02-18%20220550.png)
* Here we can see why do we use command `tftpdnld`and what need to be set before using the command.
* We need to set this
  ```
  rommon 3 > IP_ADDRESS=10.1.1.1
  rommon 4 > IP_SUBNET_MASK=255.255.255.0
  rommon 5 > DEFAULT_GATEWAY=10.1.1.100
  rommon 6 > TFTP_SERVER=10.1.1.100
  rommon 7 > TFTP_FILE=c2900-universalk9-mz.SPA.151-4.M4.bin
  rommon 8 > set
  ```
* After this we can use this command `tftpdnld` for downloading the IOS file from the TFTP server.
![yes](https://github.com/shivam1741/CCNA_Lab/blob/Image/Screenshot%202026-02-18%20224839.png)
* Now we need to press `Y` and all set file will be downloaded.
