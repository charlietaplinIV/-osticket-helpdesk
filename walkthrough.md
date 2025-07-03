📝 Step-by-Step Walkthrough: osTicket Installation on Windows Server 2022

# 🧰 osTicket Help Desk Setup – Walkthrough (Windows Server)

This guide walks through installing and configuring **osTicket**—an open-source support ticket system—on **Windows Server 2022** using IIS, PHP, and MySQL.

---

## 📦 Prerequisites

Ensure you have:

- Windows Server 2022 with latest updates
- Administrator privileges
- Internet access to download installers

Optional:
- A domain controller lab (for a full internal IT simulation)

## Step 1: Enable IIS and Required Features

1. Open **Server Manager > Manage > Add Roles and Features**.
2. Select:
   - **Web Server (IIS)**
   - Sub-features:
     - CGI
     - Common HTTP Features
     - Security → Request Filtering
     - Application Development → .NET Extensibility, ISAPI Filters, etc.
     - **IIS Management Console**
       
![Screenshot 2025-07-03 045201](https://github.com/user-attachments/assets/2621dbbe-1faf-40c3-a742-4f22944382f1)

3. Complete the wizard and restart if prompted.

4. Verify IIS is running:
   - Visit `http://localhost` in a browser
   - You should see the **IIS Welcome Page**
   
![Screenshot 2025-07-03 083239](https://github.com/user-attachments/assets/c1afaa40-d573-4904-91f1-d26c67914166)

✅ IIS is now ready to host PHP apps like osTicket.

## Step 2: Install PHP and Configure It for IIS

---

### 🧰 1. Download PHP 8.x (Recommended: 8.1 or 8.2)

- Visit the official PHP for Windows site:  
  [https://windows.php.net/download](https://windows.php.net/download)
- Download the **Non Thread Safe (NTS)** ZIP for PHP 8.1.x or 8.2.x (x64).
- Extract it to:  
  `C:\PHP`
- Download the **Visual C++ Redistributable for Visual Studio 2015-2022** for php to work!
  

---

### ⚙️ 2. Configure PHP in IIS

#### a. Add PHP to System Path:
1. Open **System Properties > Environment Variables**
2. Under **System Variables**, find and edit `Path`
3. Add: `C:\PHP`

#### b. Install PHP Manager for IIS (Optional but Helpful)
- Download from GitHub: [https://github.com/phpmanager/phpmanager](https://github.com/phpmanager/phpmanager/releases)
- After installing, you can manage PHP versions and settings inside IIS Manager.

#### c. Add PHP Handler in IIS:
1. Open **IIS Manager > [Your Server] > Handler Mappings**
2. Add Module Mapping:
   - **Request Path**: `*.php`
   - **Module**: `FastCgiModule`
   - **Executable**: `C:\PHP\php-cgi.exe`
   - **Name**: `PHP_via_FastCGI`

  ![Screenshot 2025-07-03 101227](https://github.com/user-attachments/assets/7947c59f-52ef-4f2c-b20c-ee2906e32fbb)
     
3. Click **Request Restrictions** → Uncheck “Invoke handler only if request is mapped...” → OK

---

### 🔌 3. Enable Required PHP Extensions

In `C:\PHP\php.ini` (rename from `php.ini-development`), make sure the following extensions are uncommented:

```ini
extension=mysqli
extension=mbstring
extension=gd
extension=intl
extension=imap
extension=xml
extension=curl
```
💡 Use Notepad++ or VS Code for easier editing.

🧪 4. Test PHP in IIS
1. Create a test file:
C:\inetpub\wwwroot\phpinfo.php

```<?php
phpinfo();
?>
```
2. Visit http://localhost/phpinfo.php in your browser.
✅ If you see the PHP info page, IIS + PHP is working!

## Step 3: Install MySQL and Create osTicket Database

---

### 🛠️ 1. Download & Install MySQL Community Server

1. Go to the official MySQL site:  
   [https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)
2. Choose the **Windows (x86, 64-bit) MSI Installer** version
3. Run the installer → Select **Developer Default** setup (includes MySQL Server and Workbench)
4. Set a **strong root password** during setup
5. After installation, launch **MySQL Workbench** or use **Command Line Client** to access the server

✅ You now have a local MySQL instance running on Windows Server.

---

### 📦 2. Create Database and User for osTicket

#### a. Launch **MySQL Workbench** or MySQL Command Line  
#### b. Run the following queries:

```sql
CREATE DATABASE osticket_db;
CREATE USER 'osticket_user'@'localhost' IDENTIFIED BY 'SuperSecurePass123!';
GRANT ALL PRIVILEGES ON osticket_db.* TO 'osticket_user'@'localhost';
FLUSH PRIVILEGES;

