# Hashing-and-salting-
A hands-on cybersecurity lab exploring hashing and salting in Kali Linux. Verified file integrity on a forensic disk image using md5sum, then used OpenSSL and John the Ripper to compare how quickly a password hash cracks with vs. without a salt — part of my ongoing portfolio while studying for CompTIA Security+

# 🔐 Password Hashing, Salting & File Integrity Verification

**A hands-on Kali Linux lab exploring how hashing and salting protect data —
and how attackers try to crack them.**

![Platform](https://img.shields.io/badge/platform-Kali%20Linux-557C94?logo=kalilinux&logoColor=white)
![Tools](https://img.shields.io/badge/tools-John%20the%20Ripper%20%7C%20OpenSSL-critical)
![Status](https://img.shields.io/badge/status-completed-brightgreen)
![Focus](https://img.shields.io/badge/focus-Security%2B%20Prep-blue)

---

## 📌 TL;DR

I verified the integrity of a forensic disk image using hash comparison,
then generated password hashes with and without a salt to see, firsthand,
how much harder salting makes a hash to crack — using John the Ripper to
attempt both.

**Result:** an unsalted hash cracked in ~7 seconds. A salted version of the
*exact same password* forced the cracker to restart brute-forcing from
scratch, with no shortcuts available.

---

## 🧰 Environment & Tools

| Category | Tool |
|---|---|
| OS | Kali Linux (browser-based lab environment) |
| Integrity checking | `md5sum`, `sha1sum` |
| Hash generation | `openssl passwd` |
| Password cracking | `john` (John the Ripper, incremental mode) |
| Supporting tools | `grep`, `sed`, `unzip` |

---

## 🧩 Part 1 — File Integrity Verification

**Goal:** Prove a file wasn't corrupted or tampered with, using only its hash.

```bash
# Pull lab files onto the machine
cp /media/cdrom0/* /root/Downloads/
cd /root/Downloads
unzip 4-kwsrch-ext3.zip

# Generate a hash of the disk image and compare it to the provided hash
md5sum 4-kwsrch-ext3/ext3-img-kw-1.dd >> 4-kwsrch-ext3-hash.txt
cat 4-kwsrch-ext3-hash.txt
```

**Outcome:** ✅ my computed MD5 hash matched the reference hash exactly —
confirming the file was authentic and unmodified.

> **Why it matters:** This is the same principle forensic investigators and
> IT teams use to verify that evidence, downloads, or patches are exactly
> what they claim to be. Change even one byte, and the hash changes
> completely.

---

## 🧂 Part 2 — Salting a Hash Against Password Cracking

**Goal:** Compare how quickly the same password gets cracked with vs.
without a salt.

**Step 1 — Crack an unsalted hash**

```bash
openssl passwd -salt "" pass1 > hash.txt
john -incremental hash.txt
```

⏱️ **Cracked in ~7 seconds** — no protection against brute force.

**Step 2 — Crack the same password, now salted**

```bash
openssl passwd -salt SALT pass1 > salted-hash.txt
john -incremental salted-hash.txt
```

⏱️ **Same password. Different outcome.** With the salt in place, John had
already exhausted its cached result and returned *"No password hashes left
to crack"* — so I modified the hash (stripped the salt string with `sed`)
to force a genuinely fresh attempt:

```bash
cat salted-hash.txt | sed "s/SALT//g" > salt-secret-hash.txt
john -incremental salt-secret-hash.txt
```

This time, John had to grind through brute-force candidates in real time
(`bowaks… boyjlb`, `dydyng… dydday`...) instead of resolving instantly.

| Test | Salted? | Time to crack |
|---|---|---|
| `hash.txt` | ❌ No | ~7 seconds |
| `salted-hash.txt` (fresh attempt) | ✅ Yes | Actively brute-forcing, no shortcut |

> **Why it matters:** A salt is random data mixed into a password *before*
> hashing, so identical passwords never produce identical hashes. That
> single design choice is what makes precomputed rainbow-table attacks
> useless — and this lab is what made that click for me beyond the textbook
> definition.

---

## 🎯 Skills Demonstrated

- Working confidently inside the **Kali Linux** command line
- File integrity verification (`md5sum`, `sha1sum`)
- Password hash generation with **OpenSSL**
- Password cracking methodology using **John the Ripper**
- Reading and interpreting `/etc/shadow` hash structures
- Text processing with `grep` / `sed`
- Debugging real command-line errors on the fly — typos, wrong paths,
  missing files — not just following a perfect script

---

## 🩺 → 💻 From Healthcare to Cybersecurity

I spent years working in healthcare, where protecting sensitive information
wasn't a policy on paper — it was the job, every day. That background is a
big part of why security work makes sense to me: I already know what it
means to be trusted with data where a mistake has real consequences for
real people.

This lab is one piece of how I'm converting that mindset into technical
skill — building hands-on experience in Kali Linux, one command (and one
error message) at a time, while I work toward my **CompTIA Security+**
certification and a role in the field.

---

📁 More labs from this portfolio are linked from my GitHub profile.
*Currently studying for Security+ | Building hands-on labs weekly.*
