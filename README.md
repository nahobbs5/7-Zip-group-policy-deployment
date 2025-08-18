# 🚀 Deploying 7-Zip via Group Policy

This project demonstrates how to use **Active Directory Group Policy** to automatically deploy software (in this case, **7-Zip**) to domain-joined computers.  
It’s a classic IT admin task and a great hands-on project for learning Group Policy Objects (GPOs).

---

## 🛠️ Lab Setup / Prerequisites
- Windows Server (Domain Controller with Group Policy Management)
- Domain-joined Windows client VM
- Shared folder for hosting MSI installers

---

## 📦 Step 1: Prepare the Installer
1. Download **7-Zip MSI** (not EXE):  
   👉 [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)
2. Place the MSI in a shared folder on your domain controller:
   a. Create a folder (e.g., C:\Documents\SoftwareShare).
   b. Right-click → Properties → Sharing tab → Advanced Sharing.
   c. Check "Share this folder"

Click Permissions → allow Domain Computers or Domain Users Read access (or Read/Write if needed).
   
   ```
   \\DC01\Software\7-Zip\
   ```
4. Set permissions:  
   - **Share**: Everyone = Read  
   - **NTFS**: Domain Computers = Read & Execute  

---

## 🏗️ Step 2: Create the GPO
1. Open **Group Policy Management** (`gpmc.msc`)  
2. Right-click your domain or OU → **Create a GPO in this domain, and link it here**  
   - Name: `Deploy 7-Zip`
3. Right-click the GPO → **Edit**

---

## ⚙️ Step 3: Configure Software Installation
1. In the GPO editor, go to:  
   ```
   Computer Configuration → Policies → Software Settings → Software Installation
   ```
2. Right-click → **New → Package…**  
3. Browse to the UNC path of the MSI:  
   ```
   ex. \\lab.local\SYSVOL\lab.local\scripts\7z2408-x64.msi
   Select file name ("7z2408-x64.msi")
   ```
4. Choose **Assigned**  

---

## 🖥️ Step 5: Test Deployment
On a domain-joined client VM:
```cmd
gpupdate /force
```
- Reboot the system  
- Log in → confirm **7-Zip is installed** (Start Menu or Programs & Features)

---

## 🔍 Step 6: Verify
Check applied GPOs:
```cmd
gpresult /R
```
Or export a full HTML report:
```cmd
gpresult /H report.html
```

If issues occur:
- Check **Event Viewer**:  
  ```
  Applications and Services Logs → Microsoft → Windows → GroupPolicy → Operational
  ```
- Double click on any error messages
- Re-check MSI path + permissions  

---

## ✅ Success Criteria
- 7-Zip installs automatically on reboot  
- `Deploy 7-Zip` shows as an applied policy  

---

## 📌 Extensions/Further Steps
- Deploy **Google Chrome Enterprise MSI** for real-world practice  
- Deploy to specific **security groups** instead of all computers  
- Combine with **software restriction policies** (e.g., block WinRAR, allow 7-Zip) 
