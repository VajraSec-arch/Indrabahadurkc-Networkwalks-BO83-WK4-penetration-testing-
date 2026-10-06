# 🛡️ Week 4 – Penetration Testing Project

## 📌 Project Overview

For Week 4, I worked on a **Penetration Testing Project** based on the Mediroza General Hospital environment. The main goal of this practical was to understand how a penetration tester identifies vulnerabilities, follows an attack path, collects evidence, and reports security weaknesses.

Throughout the practical, I worked through different stages including **reconnaissance, login testing, SQL injection, password/hash cracking, metadata analysis, backup discovery, and data analysis**. 🔐💻

> ⚠️ **Ethical Notice:** This practical was performed in an authorised educational environment for learning purposes only. These techniques should never be used against systems without explicit written permission.

---
# 🧰 Tools Used

| Tool                             | Purpose                            |
| -------------------------------- | ---------------------------------- |
| 🌐 Browser                       | Web application testing            |
| 🐧 Linux Terminal                | Command-line testing               |
| 🔍 CURL                          | Reconnaissance and web requests    |
| 🔐 Networkwalks Hash Calculator  | PDF hash extraction                |
| 🧨 Networkwalks Password Cracker | Password/hash testing              |
| 🔑 John the Ripper (JTR)         | Password cracking with wordlists   |
| 📄 qpdf                          | PDF decryption                     |
| 🕵️ ExifTool                     | Metadata analysis                  |
| 🤖 ChatGPT                       | SQL data organisation and analysis |

---

## 🎯 Objectives

The main objectives of this practical were:

* 🔎 Perform reconnaissance on the target application.
* 🌐 Identify hidden directories and useful information.
* 🔐 Test the login functionality for weaknesses.
* 💉 Identify and demonstrate SQL injection.
* 🚪 Understand how an authentication bypass can occur.
* 📄 Access and analyse protected PDF reports.
* 🔑 Extract and crack PDF password hashes.
* 🧩 Perform deeper reconnaissance using file metadata.
* 🗂️ Identify exposed backup files.
* 📊 Analyse information contained inside a database backup.
* 📝 Document vulnerabilities and provide remediation recommendations.

---

# 🔍 1. Reconnaissance

I started the practical with **reconnaissance**, which is one of the most important stages of penetration testing.

I checked the target's `robots.txt` file to identify directories that were not intended to be indexed by search engines.

The reconnaissance revealed directories such as:

* `/patient/`
* `/staff/`
* `/old/`

The `/patient/` directory was useful for the initial access stage, while `/old/` became important later during deeper reconnaissance.

### 📸 Screenshot

**[UPLOAD SCREENSHOT HERE — robots.txt / reconnaissance]**

---

# 🌐 2. Finding the Login Page

After discovering the `/patient/` directory, I accessed it through the browser.

This revealed a **patient login page**, which became the main entry point for testing the application's authentication security.

### 📸 Screenshot

**[UPLOAD SCREENSHOT HERE — Patient Login Page]**

---

# 👤 3. Username Enumeration

Next, I tested the login form to determine whether the application revealed information about valid usernames.

I first used a fake username and received:

> **Username not found**

I then tested the `admin` username and received:

> **Incorrect password**

The different responses confirmed that the application could distinguish between an invalid username and a valid username with an incorrect password.

This is known as **username enumeration** and can help an attacker identify valid accounts. 🔎👤

### 📸 Screenshot

**[UPLOAD SCREENSHOT HERE — Username enumeration results]**

---

# 💉 4. SQL Injection Testing

After identifying a valid username, I tested the login field for **SQL Injection**.

A single quote was entered into the username field. The application returned a MySQL syntax error, indicating that user input was being passed directly into the database query.

This confirmed that the login form was vulnerable to SQL injection. 💻💉

### 📸 Screenshot

**[UPLOAD SCREENSHOT HERE — SQL Injection error]**

---

# 🚪 5. Authentication Bypass

I then demonstrated how the SQL injection vulnerability could be used to bypass the password verification.

The vulnerable application accepted an SQL comment-based input and allowed access to the patient portal without knowing the legitimate password.

After successful login, the portal displayed **three patient reports**. 🔓📄

### 📸 Screenshot

**[UPLOAD SCREENSHOT HERE — Successful login / patient portal]**

---

# 📄 6. Downloading Patient Reports

After gaining access to the portal, I downloaded the three available PDF reports:

* `patient_report_1.pdf`
* `patient_report_2.pdf`
* `patient_report_3.pdf`

These files were then used for the next stage of the practical.

### 📸 Screenshot

**[UPLOAD SCREENSHOT HERE — Downloaded PDF reports]**

---

# 🔐 7. Extracting PDF Password Hashes

The next stage was to test the protection applied to the PDF files.

I used the **Networkwalks Hash Calculator** to extract the password hash from each encrypted PDF. The generated hashes started with `$pdf$`.

The extracted hashes were then used for password-cracking tests. 🔑🧪

### 🛠️ Tool Used

* Networkwalks PDF Hash Calculator

### 📸 Screenshot

**[UPLOAD SCREENSHOT HERE — PDF hash extraction]**

---

# 🧨 8. Password Cracking

I used the **Networkwalks Password Cracker** with a built-in wordlist to test the extracted PDF hashes.

The first two reports were successfully cracked:

| PDF Report             | Result     |
| ---------------------- | ---------- |
| `patient_report_1.pdf` | `123456`   |
| `patient_report_2.pdf` | `password` |

This demonstrated the risk of using weak and commonly used passwords to protect sensitive files. 🔓🔑

### 📸 Screenshot

**[UPLOAD SCREENSHOT HERE — Password cracking results]**

---

# 🧩 9. Cracking the Third PDF

For the third report, the default wordlist was unsuccessful.

The tool returned:

> **Exhausted wordlist. No match. ACCESS DENIED.**

I then used the provided **John the Ripper (JTR) wordlist** and successfully recovered the password for the third report.

This showed me why penetration testers may need to use different wordlists when testing password strength. 🧠🔐

### 📸 Screenshot

**[UPLOAD SCREENSHOT HERE — JTR wordlist cracking]**

---

# 🔓 10. Unlocking the PDF

After recovering the password for the third report, I created an unlocked copy of the PDF using `qpdf`.

This allowed the complete file metadata to be inspected during the next stage.

### 🛠️ Tool Used

* `qpdf`

### 📸 Screenshot

**[UPLOAD SCREENSHOT HERE — qpdf PDF decryption]**

---

# 🕵️ 11. Metadata Analysis

I then performed deeper reconnaissance by checking the metadata of the unlocked PDF using **ExifTool**.

The metadata contained useful information including:

* **Author:** `j.malik`
* **Comments:** A note indicating that a database backup had been moved to `/old/`.

This was an important discovery because the information inside the PDF provided a clue about another potentially exposed location on the server. 🕵️‍♂️📋

### 📸 Screenshot

**[UPLOAD SCREENSHOT HERE — ExifTool metadata results]**

---

# 🗂️ 12. Discovering the Backup Directory

I connected the metadata clue with the `/old/` directory that I had already discovered during the initial reconnaissance.

When the `/old/` directory was accessed, **directory listing was enabled**, exposing files that should not have been publicly accessible.

A database backup file named:

`mediroza_db_backup_2019.sql`

was visible and could be downloaded. 🗃️⚠️

### 📸 Screenshot

**[UPLOAD SCREENSHOT HERE — /old/ directory listing]**

---

# 🗄️ 13. Database Backup Analysis

The SQL backup contained database information stored in plain text.

I analysed the relevant database entries and used the information to understand what sensitive information had been exposed.

The practical focused on the **staff** and **shareholders** data contained in the backup. This demonstrated how an accidentally exposed database backup can lead to significant information disclosure. 📊💾

### 📸 Screenshot

**[UPLOAD SCREENSHOT HERE — SQL database contents]**

---

# 🔗 14. Connecting the Findings

One of the most interesting parts of this practical was connecting the different findings together.

The author discovered in the PDF metadata was:

**Jameel Malik**

The staff information in the database contained the same person, identified as an IT Systems Administrator.

This showed how seemingly small pieces of information can be connected during a penetration test to create a complete attack chain. 🧩🔎

### 📸 Screenshot

**[UPLOAD SCREENSHOT HERE — Evidence connection / staff information]**

---

# 🚨 15. Vulnerability Summary

The practical identified several security weaknesses:

| #   | Vulnerability                           | Risk        |
| --- | --------------------------------------- | ----------- |
| 1️⃣ | Username Enumeration                    | 🟠 Medium   |
| 2️⃣ | SQL Injection / Login Bypass            | 🔴 Critical |
| 3️⃣ | Patient PDF Access                      | 🔴 High     |
| 4️⃣ | Weak PDF Passwords                      | 🔴 High     |
| 5️⃣ | Sensitive PDF Metadata                  | 🟠 Medium   |
| 6️⃣ | Exposed Backup Directory                | 🔴 Critical |
| 7️⃣ | Sensitive Database Information Exposure | 🔴 Critical |

These findings demonstrate how multiple smaller weaknesses can combine into a much more serious security issue.

---

# 🛠️ 16. Recommendations

Based on my findings, the following security improvements should be implemented:

### 👤 Username Enumeration

Use the same login error message whether the username or password is incorrect.

### 💉 SQL Injection

Use **parameterised queries / prepared statements** instead of placing raw user input into SQL queries.

### 📄 PDF Protection

Store sensitive PDF files outside the public web root and apply proper access controls.

### 🔑 Strong Passwords

Use strong, unique passwords instead of common passwords that can easily be discovered through wordlists.

### 🧹 PDF Metadata

Remove unnecessary metadata from sensitive documents before distributing them.

### 🗂️ Backup Security

Disable directory listing and never store database backups inside publicly accessible web directories.

These recommendations are consistent with the remediation guidance provided in the practical solution.

---

# 🧠 17. What I Learned

This practical helped me understand that penetration testing is not only about finding one vulnerability. The most important part is understanding **how vulnerabilities can be connected together**.

During this project, I learned how to:

* 🔎 Perform basic web reconnaissance.
* 👤 Identify username enumeration.
* 💉 Recognise SQL injection vulnerabilities.
* 🔓 Understand authentication bypass.
* 🔑 Extract and crack password hashes.
* 🧨 Use different wordlists during password testing.
* 🕵️ Analyse file metadata.
* 🗂️ Identify exposed directories and backups.
* 💾 Analyse SQL database backups.
* 🧩 Connect multiple findings into a complete attack chain.
* 📝 Document vulnerabilities and recommend remediation.

Overall, this Week 4 practical gave me a much better understanding of how a real penetration-testing assessment can progress from **initial reconnaissance → initial access → credential cracking → deeper reconnaissance → sensitive data discovery**. 🚀🛡️

---



# 🏁 Conclusion

Completing this Week 4 penetration-testing practical gave me valuable hands-on experience with the different stages of a security assessment.

I was able to follow the attack path from **reconnaissance to initial access, password cracking, metadata analysis, and sensitive data discovery**. 🔐💻

The biggest lesson I took from this project is that even a small security weakness can become serious when it is combined with other vulnerabilities. Proper authentication, secure coding, strong passwords, protected files, and secure backup management are all important parts of maintaining a secure system.

> 🛡️ **Security is not about fixing one vulnerability — it is about protecting the entire chain.**

**⚠️ Ethical Reminder:** All testing techniques demonstrated in this project were performed within an authorised educational environment. Never perform penetration testing against a system without explicit permission from the owner.
