# 🚀 Deploying 7-Zip via Group Policy

This project demonstrates how to use **Active Directory Group Policy** to automatically deploy software (in this case, **7-Zip**) to domain-joined computers.  
It’s a classic IT admin task and a great hands-on project for learning about Group Policy Objects (GPOs).

---

## 🛠️ Lab Setup / Prerequisites
- Windows Server (Domain Controller with Group Policy Management)
- Domain-joined Windows client VM (for more on this process, see my [Azure Active Directory Demo](https://github.com/nahobbs5/AzureActiveDirectoryDemo)
- Shared folder for hosting MSI installers

---

## 📦 Step 1: Prepare the Installer
1. Download **7-Zip MSI** (not EXE):  
   👉 [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)
2. Place the MSI in a shared folder for hosting MSI installers (or use the built-in SYSVOL share for simplicity):
   a. Create a folder (e.g., C:\Documents\SoftwareShare).  
   b. Right-click → Properties → Sharing tab → Advanced Sharing.  
   c. Check "Share this folder"

Click Permissions → allow Domain Computers or Domain Users Read access (or Read/Write if needed).
   
   ```
   Ex: \\DC01\Software\7-Zip\
   ```
3. Set permissions:  
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
   Ex. \\lab.local\SYSVOL\lab.local\scripts\7z2408-x64.msi
   Select ("7z2408-x64.msi")
   ```
4. Choose **Assigned**  

---

## 🖥️ Step 4: Test Deployment
On a domain-joined client VM:
```cmd
gpupdate /force
```
- If prompted, allow the system to reboot. Otherwise, reboot manually to trigger installation.
- Log in → confirm **7-Zip is installed** (Start Menu or Programs & Features)

---

## 🔍 Step 5: Additional Verification
Check applied GPOs:
```cmd
gpresult /R
```
Or export a full HTML report:
```cmd
gpresult /H report.html
```

## If issues occur
- Check **Event Viewer**:  
  ```
  Applications and Services Logs → Microsoft → Windows → GroupPolicy → Operational
  And/or Windows Logs → Application → Source: MsiInstaller
  ```
- Double click on any error messages
- Re-check MSI path + permissions  
If you encounter issues using the traditional UNC path:
  ```
  (\\<ServerName>\<ShareName>\<FileName>)
  ```
  try using the built-in SYSVOL path instead
  ```
  \\yourdomain.local\SYSVOL\yourdomain.local\scripts\
  ```
  This will resolve any file sharing concerns, since clients _always_ have access to SYSVOL
---

## ✅ Success Criteria
- 7-Zip installs automatically on reboot  
- `Deploy 7-Zip` shows as an applied policy  

---

## 📌 Extensions / Further Steps
- Deploy **Google Chrome Enterprise MSI** for real-world practice  
- Deploy to specific **security groups** instead of all computers  
- Combine with **software restriction policies** (e.g., block WinRAR, allow 7-Zip) 
