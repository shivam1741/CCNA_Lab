

## **Router is not booting properly and goes to `rommon>` mode.**

Let me give you the **step-by-step method to fix this** 💪

---

# ✅ Why Router Goes to `rommon>`?

Main reasons:

❌ IOS file missing
❌ Wrong boot command
❌ Wrong config-register
❌ Flash problem

So first we check these.

---

# ✅ Step 1: Check Flash (In ROMMON)

At `rommon>` type:

```
dir flash:
```

or sometimes:

```
dir flash
```

👉 See if IOS `.bin` file is there.

---

# ✅ Step 2: If IOS File Exists (Good Case 👍)

Example output:

```
c2900-universalk9.bin
```

Then boot it manually:

```
boot flash:c2900-universalk9.bin
```

Router should start ✅

---

# ✅ Step 3: Fix Boot Permanently

After router boots, go to config mode:

```
enable
configure terminal
```

Set boot path:

```
boot system flash:c2900-universalk9.bin
```

Set correct register:

```
config-register 0x2102
```

Save:

```
write memory
```

Reload:

```
reload
```

Now it will boot normally always 👍

---

# ❌ Step 4: If IOS Is NOT in Flash (Bad Case 😢)

If `dir flash:` shows empty:

Then you must upload IOS.

Options:

### ▶️ Using TFTP (Common way)

```
copy tftp flash
```

### ▶️ Using USB

```
copy usbflash0: flash:
```

### ▶️ Using Xmodem (Slow, Last option)

(Advanced, used in real routers)

---

# ✅ Step 5: Check Config Register (Very Important)

In rommon:

```
confreg
```

or

```
confreg 0x2102
```

Then:

```
reset
```

---

# 🧠 Easy Fix Formula

When router stuck in rommon:

> 1️⃣ Check flash
> 2️⃣ Boot IOS
> 3️⃣ Set boot + 2102
> 4️⃣ Save
> 5️⃣ Reload

---

# ⚡ One-Line Answer (Exam)

> If router enters ROMMON mode, we manually boot the IOS from flash and set correct boot system and configuration register.

---

If you want, tell me:

👉 Is this in Packet Tracer or real router?
👉 What output you get for `dir flash:`?

I’ll guide you exactly 😊
