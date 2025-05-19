# SECURITY-TESTS_DVWA
Using DVWA for security testing with reference to the OWASP Top 10.



# DVWA Security Testing Environment

## Introduction

This project sets up a Damn Vulnerable Web Application (DVWA) environment on an Ubuntu virtual machine using the LAMP stack (Linux, Apache, MySQL, PHP). DVWA is a platform designed for security enthusiasts and developers to learn and practice web security testing, focusing on vulnerabilities such as SQL Injection (SQLi) and Cross-Site Scripting (XSS). This README provides a step-by-step guide to configure the environment, install DVWA, and document the methodology and results of security tests.

---

## 1. Requirements

Before starting, ensure you have the following:
- **Virtualization Software**: Oracle VirtualBox or VMware Workstation.
- **Host Operating System**: Windows 10/11, macOS, or Linux.
- **Ubuntu ISO**: Version 20.04 LTS or higher (downloadable from [ubuntu.com](https://ubuntu.com/download)).
- **Internet Access**: For downloading dependencies and DVWA.
- **Basic Knowledge**: Familiarity with Linux terminal commands and web security concepts.

---

## 2. Configuration of the Virtual Machine

### 2.1. Creation of the Machine Virtuelle
- Open VirtualBox and click **New**.
- Name the VM (e.g., "Ubuntu-DVWA"), select **Type: Linux** and **Version: Ubuntu (64-bit)**.
- Allocate at least 2 GB of RAM and 20 GB of disk space (dynamic allocation).
- Click **Create**.

### 2.2. Installation of Ubuntu via ISO or Kali Linux if you want just like me !
- Mount the Ubuntu ISO in the VM via **Settings > Storage > Controller IDE > Optical Drive**.
- Start the VM and follow the Ubuntu installation:
  - Select language (e.g., English or French according to your preferences ).
  - Choose normal installation and configure partitioning (use entire space).
  - Create a user and note the password.
- Restart the VM after installation.

### 2.3. Updating the system
- Open a terminal and run:
  ```bash
  sudo apt update && sudo apt upgrade -y
  ```
- Restart the VM after the update:
  ```bash
  sudo reboot
  ```

---

## 3. Installation of the dependences (LAMP)

### 3.1. Apache2 
- Install Apache2: ( it is not necessary on kali linux )
  ```bash
  sudo apt install apache2 -y
  ```
- Enable and start the service:
  ```bash
  sudo systemctl enable apache2
  sudo systemctl start apache2
  ```
- Verify Apache is running:
  - Open a browser on the VM or host (via VM IP, e.g., `http://localhost` or `http://127.0.1:8080`) and check for the default Apache page.

### 3.2. MySQL : (not necessary on kali linux )
- Install MySQL:
  ```bash
  sudo apt install mysql-server -y
  ```
- Secure the installation:
  ```bash
  sudo mysql_secure_installation
  ```
  - Follow prompts: set a root password, remove anonymous users, etc.
- Verify the service:
  ```bash
  sudo systemctl status mysql
  ```
  - Ensure the status is "active (running)".

### 3.3. PHP : not necessary on kali linux
- Install PHP and required modules:
  ```bash
  sudo apt install php libapache2-mod-php php-mysql php-gd php-curl -y
  ```
- Restart Apache to apply changes:
  ```bash
  sudo systemctl restart apache2
  ```
- Verify PHP installation:
  - Create a file `info.php` in `/var/www/html`:
    ```bash
    echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
    ```
  - Access `http://<IP_VM>/info.php` to see PHP information.

---

## 4. Installation and Configuration of DVWA

### 4.1. Download DVWA
- Download DVWA from [GitHub](https://github.com/digininja/DVWA):
  ```bash
  sudo apt install git -y
  git clone https://github.com/digininja/DVWA.git /var/www/html/dvwa
  ```
- Set appropriate permissions:
  ```bash
  sudo chown -R www-data:www-data /var/www/html/dvwa
  sudo chmod -R 755 /var/www/html/dvwa
  ```

### 4.2. Configuration of the database
- Log in to MySQL:
  ```bash
  sudo mysql -u root -p
  ```
- Create a database and user for DVWA:
  ```sql
  CREATE DATABASE dvwa;
  CREATE USER 'dvwa_user'@'localhost' IDENTIFIED BY 'your_password';
  GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa_user'@'localhost';
  FLUSH PRIVILEGES;
  EXIT;
  ```
- The database structure will be set up via the DVWA setup page later.

### 4.3. Configuration of the DVWA file
- Edit the configuration file:
  ```bash
  sudo cp /var/www/html/dvwa/config/config.inc.php.dist /var/www/html/dvwa/config/config.inc.php
  sudo nano /var/www/html/dvwa/config/config.inc.php
  ```
- Modify the following lines:
  - `$_DVWA[ 'db_user' ] = 'dvwa_user';`
  - `$_DVWA[ 'db_password' ] = 'your_password';`
  - `$_DVWA[ 'db_database' ] = 'dvwa';`
- Save and close the file.

---

## 5. Configuration of PHP
- Ensure all necessary PHP extensions are installed (handled in section 3.3).
- No additional configuration is required beyond the modules installed.

---

## 6. Access to DVWA
- Open a browser and navigate to `http://<IP_VM>/dvwa`.
- Log in with:
  - **Username**: `admin`
  - **Password**: `password`
- Click **Setup** to create the database tables, then return to the login page and log in again.

---

## 7. Methodologies of Attacks 

### 7.1. Injection SQL (SQLi)
- Navigate to the "SQL Injection" module in DVWA.
- Enter a malicious query (e.g., `' OR '1'='1`) in the ID field to bypass authentication.
- Analyze the results to identify vulnerabilities.

### 7.2. Cross-Site Scripting (XSS)
- Go to the "XSS" module in DVWA.
- Inject a script (e.g., `<script>alert('XSS')</script>`) in the text field.
- Observe the script execution in the browser.

---

## 8. Results

### 8.1. Injection SQL (SQLi)
- **Success**: The malicious query returns all database entries.
- **Impact**: Unauthorized access to sensitive data.
- **Recommendation**: Use prepared statements and input validation to prevent SQLi.

### 8.2. Cross-Site Scripting (XSS)
- **Success**: The script executes and displays an alert.
- **Impact**: Risk of cookie theft or malicious redirection.
- **Recommendation**: Sanitize inputs and escape HTML characters.

---

## Conclusion and recommandations

This project successfully set up a DVWA environment on an Ubuntu VM with LAMP, providing a safe platform to learn about web security vulnerabilities like SQLi and XSS. The configuration process highlighted the importance of secure database setup and server management.

### Recommandations
- **Regular Updates**: Keep Ubuntu, Apache, MySQL, and PHP updated to mitigate vulnerabilities.
- **Security Best Practices**: Implement input validation and use security headers in production environments.
- **Further Learning**: Explore additional OWASP Top 10 vulnerabilities (e.g., CSRF, File Inclusion).
- **Documentation**: Maintain logs of tests and configurations for future reference.

---

### Contribution

- Clone the repository: `git clone https://github.com/Ahoulou-20/SECURITY-TESTS_DVWA.git`
- Create a branch: `git checkout -b feature/new-feature`
- Push your changes: `git push origin feature/new-feature`
- Open a pull request for review.

### Licence

This project is licensed under the name of  AHOULOU ADOUKO LOIC IVAN.
### ATHORS
  - AHOULOU ADOUKO LOIC IVAN
