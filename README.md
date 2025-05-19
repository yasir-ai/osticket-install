![osTicket](https://i.imgur.com/Clzj7Xs.png)

# 🎫 osTicket Complete Installation Guide

[![osTicket v1.15.8](https://img.shields.io/badge/osTicket-1.15.8-blue)](https://osticket.com/) [![Azure VM](https://img.shields.io/badge/Azure-VM-blueviolet)](https://azure.microsoft.com/)   [![Windows 10](https://img.shields.io/badge/OS-Windows%2010-green)](https://www.microsoft.com/windows)   [![PHP 7.3](https://img.shields.io/badge/PHP-7.3-blue)](https://www.php.net/)   [![MySQL 5.5](https://img.shields.io/badge/MySQL-5.5-orange)](https://www.mysql.com/)  

> 👉 **Fun & super easy walkthrough** to deploy osTicket (an open source help desk ticketing system) on Windows 10 Pro/Server so that you can begin getting those ticket reps in 

---

## 📋 Table of Contents

- [🧰 Prerequisites](#-prerequisites)  
- [🚀 Setup Azure VM](#-setting-up-your-azure-vm)  
- [🔧 Configure IIS & PHP](#-configure-iis--php-within-the-vm)
- [🐘 Database Setup](#-mysql-database-setup)
- [🎉 Finalize Installation](#-finalizing-the-installation)
- [👍 Cleanup](#-cleanup-(helps-eliminate-security-risks-and-keeps-everything-tidy))

---

## 📋 Prerequisites

Make sure you have the following before you begin:

| Category        | Requirement                        |
| --------------- | ---------------------------------- |
| **IIS**         | Internet Information Services      |
| **PHP Manager** | PHPManagerForIIS V1.5.0            |
| **VC++ Redis.** | VC_redist.x86.exe                  |
| **MySQL**       | MySQL 5.5.62                       |
| **Rewrite**     | URL Rewrite Module                |
| **Client**      | HeidiSQL                           |
| **OS**          | Windows 10/11 Pro/Server                          |
| **Cloud/Deployment**          | Active Azure Account                          |

All downloads can be found [here](https://drive.google.com/drive/u/2/folders/1APMfNyfNzcxZC6EzdaNfdZsUwxWYChf6)

---

## 🚀 Setting up your Azure VM

<details>
<summary>1. Create & Configure VM</summary>

1. Log into Azure Portal  
2. **Create → Virtual Machine**  
   - **Image**: [Windows 10 Pro, version 22H2](https://drive.google.com/drive/u/2/folders/1APMfNyfNzcxZC6EzdaNfdZsUwxWYChf6)
      ![image](https://github.com/user-attachments/assets/f54a7d68-c977-482c-a4cb-903e40eb8140)
   - **Size**: ≥ 2 vCPU, 16 GiB RAM  
   - **Inbound port**: **RDP (3389)**
   > NOTE: These two are critical, make sure to set the size to at least 2 vcpus and 16 GiB memory (RAM not storage). And CONFIRM that RDP (3389) is allowed in "Select inbound ports" in order to allow Remote Desktop access to the VM - otherwise you CANNOT CONNECT!
   > ![image](https://github.com/user-attachments/assets/858d12a8-9db3-43cd-8da5-4cad21c71ff9)
3. Review & Create (accept license)  
4. Wait for deployment, note down your **Public IP** and save it
   - ![image](https://github.com/user-attachments/assets/fe21b008-e1ae-438e-b23e-2a792741e48a)

</details>

<details>
<summary>2. Connecting to the Azure VM via RDP</summary>

Open your preferred **Remote Desktop** app on your local machine (I'm using Windows' built in Remote Desktop app):

Computer: <YOUR_VM_PUBLIC_IP> _(use the public ip that you saved from the last step)_

Username: <VM_USERNAME> _(used the credentials you created in the last step)_

Password: <VM_PASSWORD>

![image](https://github.com/user-attachments/assets/18135c78-0764-4800-8e05-1d6ccf7346cc)


Click **Connect** and log in.

</details>

---

## 🔧 Configure IIS & PHP within the VM

<details>
<summary>3. Enable IIS Features</summary>

- **Control Panel → Programs → Turn Windows features on/off**
  - ![image](https://github.com/user-attachments/assets/3027383d-6c3a-4af6-8c6c-62160e18de38)

- Enable ALL of the following:
  - Internet Information Services  
  - Web Management Tools → IIS Management Console  
  - World Wide Web Services → Application Development Features → **CGI**  
  - Common HTTP Features (Default Document, Static Content)
    ![image](https://github.com/user-attachments/assets/1e0012eb-5ab8-4018-943f-6a4b6587f733)

- Click **OK**, wait for install  
- **Test check**: On the browser, go to `http://127.0.0.1` → **Welcome to IIS** page! Looks like this below
  - ![image](https://github.com/user-attachments/assets/8071f723-54be-416e-a7e2-74a60ff1d344)


</details>

<details>
<summary>4. Installing PHP Manager & URL Rewrite Module</summary>

- Download & install **[PHP Manager](https://drive.google.com/drive/u/2/folders/1APMfNyfNzcxZC6EzdaNfdZsUwxWYChf6)** (PHPManagerForIIS_V1.5.0.msi)  
- Download & install **[URL Rewrite](https://drive.google.com/drive/u/2/folders/1APMfNyfNzcxZC6EzdaNfdZsUwxWYChf6)** (rewrite_amd64_en-US.msi)  
- **Restart IIS**: IIS Manager → [YourServer] → **Restart**

</details>

<details>
<summary>5. Installing PHP 7.3.8</summary>

1. Create `C:\PHP` directory (just make a new folder called PHP in local disk C)
   - ![image](https://github.com/user-attachments/assets/2b8bc439-8d7c-4fa1-92ed-b17f4d649d8a)
     
2. Unzip **[php-7.3.8-nts-Win32-VC15-x86.zip](https://drive.google.com/drive/u/2/folders/1APMfNyfNzcxZC6EzdaNfdZsUwxWYChf6)** into `C:\PHP`
   - ![image](https://github.com/user-attachments/assets/a57fec6d-23cc-4bb9-9f14-0436d38b90a8)

3. Register PHP in IIS:
   - IIS Manager → **PHP Manager** → **Register**
     - ![image](https://github.com/user-attachments/assets/4b7243b9-1c2b-45bd-86c9-59640d99d40c)
       
   - Point to `C:\PHP\php-cgi.exe`
     - ![image](https://github.com/user-attachments/assets/227d5855-66ba-4e59-9295-d66f2e5556f1)

4. **Restart IIS Server**
   - ![image](https://github.com/user-attachments/assets/ce19d521-02a9-4481-a693-581eb753f05b)


> 🚨 **Note:** Always restart IIS after registering PHP - I cannot stress this enough

</details>

---

## 🐘 MySQL Database Setup

<details>
<summary>6. Install MySQL 5.5.62</summary>

- Run `mysql-5.5.62-win32.msi`  
- Choose **Typical**  
- Launch **Configuration Wizard**  
- Use **Standard Configuration**  
- Create a **root** password  (save this, don't lose it!!)

</details>

<details>
<summary>7. Create osTicket Database</summary>

- Open **HeidiSQL**, **New Session** → connect as **root**  
- Right-click **Unnamed** → **Create new → Database**  
- Name it `osTicket`  
- Click **OK**

</details>

---

## 🎉 Finalizing The Installation
Congrats you made it to the last part, we're almost done!!

<details>
<summary>8. Deploy osTicket</summary>

1. Download [osTicket v1.15.8](https://drive.google.com/drive/u/2/folders/1APMfNyfNzcxZC6EzdaNfdZsUwxWYChf6) from the downloads link
2. Extract content the contents into `C:\inetpub\wwwroot`
   - ![image](https://github.com/user-attachments/assets/e89711b5-0ea1-49cb-a6ec-c8377a930b88)
  
4. Open the C:\inetpub\wwwroot directory, rename `upload` → `osTicket`  
5. **Restart IIS again**
   - ![image](https://github.com/user-attachments/assets/605bdcc4-83a0-420a-8d3a-e0696e8863cc)
  
7. Open **IIS Manager**:
   - Go to expand **Sites → Default Web Site → osTicket**
   - ![image](https://github.com/user-attachments/assets/0bcbfb48-8319-498c-8527-88c9023c61a1)
    > NOTE: Make sure to click on osTicket

   - Click **Browse *:80**
     - ![image](https://github.com/user-attachments/assets/24b60390-aa0c-4827-a6b0-f2fa82001677)

8. You should see osTicket open up in your browser
   ![image](https://github.com/user-attachments/assets/59ebaa7d-8de5-4bde-805e-9f129f76b7e6)


</details>

<details>
<summary>9. Configure osTicket via Browser</summary>

1. Rename:
   ```bash
   C:\inetpub\wwwroot\osTicket\include\**ost-sampleconfig.php** → **ost-config.php**
   ```
   ![image](https://github.com/user-attachments/assets/c167e8d9-564d-4533-8033-d49a2a8c426b) 

2. Set ost-config.php permissions to Read/Write for Everyone (temporary)
   - Right click on ost-config.php -> Properties -> Security -> Advance -> Disable inheritance
   - Select remove all inherited permissions and add everyone as a principal. Select all boxes to ensure all permissions are granted.
![image](https://github.com/user-attachments/assets/e5812357-2c19-410d-8791-9b72a1e83ae0)


3. Fill the browser installer form:

Helpdesk Name, Email, Admin User

Database: **(skip , we'll get to this next)**

Host: localhost

Database: osTicket

User: root

Password: <your_password>

![image](https://github.com/user-attachments/assets/ea0da2dc-090b-4325-ac14-f340d131e608)


**Click Install**

</details>

<details>
  <summary>10. Heidi SQL Setup</summary>

  1. [Download and install Heidi SQL](https://drive.google.com/drive/u/2/folders/1APMfNyfNzcxZC6EzdaNfdZsUwxWYChf6)
  2. Open Heidi SQL and create a new session (make sure to fill in the username as root and create a password, after filling up your credentials now click open and a new session should show up)
  3. Create new database
     - Right click on "Unnamed"
     - Create new database
     - Name it "osTicket"
     ![image](https://github.com/user-attachments/assets/b793b967-3bc2-46cb-b64f-06d003326a5e)

</details>

<details>
  <summary>11. Completing osTicket installation</summary>

  1. Using your newly created credentials from Heidi SQL, complete the osTicket database form
     ![image](https://github.com/user-attachments/assets/b9c2b0da-b5cb-4610-8246-fb4585d92eb3)

  2. Click install and osTicket should begin setting up.


</details>

## 👍 Cleanup (helps eliminate security risks and keeps everything tidy)

1. **Delete setup directory:** C:\inetpub\wwwroot\osTicket\setup
2. Set ost-config.php to ReadOnly
3. Tidy up file permissions


## 🎊 Congratulations!
You now have a running osTicket instance!

Explore the Admin Panel to get started.

For more details, see the [official osTicket docs](https://docs.osticket.com/en/latest/).
