# Lab Outline: Azure Vulnerbility Management

Prerequistes
- Computer with the Internet
- Free Azure Account - Possible to do with Free Subscriptions

# Create our Free Azure Account
![image](https://github.com/WesGough/openvas/assets/150361198/a71d2b9f-722c-4446-949a-ec806a2e2a80)

# Prepare Vulnerability Management Scanner
![image](https://github.com/WesGough/openvas/assets/150361198/c531c696-7ab8-486b-9e3a-88bc7eda8ebe)

1. Go to Azure Portal
2. Navigate to the Marketplace and search for "OpenVAS secured and supported by HOSSTED"
3. Choose the "Start with a pre-set configuration" option and select the weakest configuration.
4. Click "Continue to Create VM"
5. Configure the VM:
  - Resource Group: Vulnerability-Management
  - VM Name: OpenVAS (Take note of the region and Vnet–consider East US 2)
  - Authentication: Username → azureuser / Cyberlab123!
  - Monitoring: Disable Boot Diagnostic
6. Click "Create" to create the VM.
7. Once the VM is created, SSH into it using PowerShell (Windows) or Terminal (MacOS) with the provided credentials.
8. Wait until the deployment of OpenVAS is complete.

# Create Client Virtual Machine and Make it Vulnerable
![image](https://github.com/WesGough/openvas/assets/150361198/0d55968c-d370-4199-beb8-2beb1512bad1)
1. Go to Azure Portal
2. Search for Virtual Machines and create a new Virtual Machine.
3. Configure the VM:
  - Resource Group: Vulnerability-Management
  - VM Name: Win10-Vulnerable
  - Region: Same as the OpenVAS VM (East US 2)
  - Virtual Network: Same as OpenVAS
  - Image: Windows 10 Pro
  - Size: Any size with 2 vCPUs
  - Username: azureuser / Cyberlab123!
  - Networking: Same Vnet as OpenVAS
4. Create the VM.
5. Once the VM is created, ensure you can RDP into it with the provided credentials.
6. After logging in, make the VM vulnerable:
  - Disable the Windows Firewall
  - Gather up some Old Software
  - Install an Old Version of FireFox: Firefox Setup 97.0b5
  - Install an Old Version of VLC Player: vlc-1.1.7-win32
  - Install an Old Version of Adobe Reader: 10.0_AdbeRdr1000_en_US_1_
  - Restart the VM.
# Configure OpenVAS to Perform First Unauthenticated Scan against our Vulnerable VM
![image](https://github.com/WesGough/openvas/assets/150361198/c96ce351-7122-48a3-9e12-f7169647fe29)
1. Login to OpenVAS and navigate to Assets > Hosts > New Host.
2. Add the Client VM PRIVATE IP Address.
3. Create a New Target from the Host, name it "Azure Vulnerable VMs".
4. Take note of the credentials. We will add SMB credentials later.
5. Create a new Task:
  - Name & Comment: "Scan - Azure Vulnerable VMs"
  - Scan Targets: "Azure Vulnerable VMs"
6. Save the Task.
7. Start the "Scan - Azure Vulnerable VMs" Task.
8. Once the scan is finished, click the date under "Last Report" to see the results.
9. Take note of the Tabs, especially the "Results" tab.
# Make Configurations for Credentialed Scans (Within VM)
![image](https://github.com/WesGough/openvas/assets/150361198/bdb25065-37d3-4178-b386-cb6ac8e5e2b1)
1. Disable Windows Firewall.
2. Disable User Account Control.
3. Enable Remote Registry.
4. Set Registry Key:
  - Launch Registry Editor (regedit.exe) in "Run as administrator" mode.
  - Navigate to HKEY_LOCAL_MACHINE hive.
  - Open SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System key.
  - Create a new DWORD (32-bit) value with the following properties:
    - Name: LocalAccountTokenFilterPolicy
    - Value: 1
  - Close Registry Editor.
5. Restart the VM.
# Make Configurations for Credentialed Scans (OpenVAS)
![image](https://github.com/WesGough/openvas/assets/150361198/da2f5ce1-0aaf-4ef8-826e-27ed158a822f)
1. Go to Configuration > Credentials > New Credential.
2. Name / Comment: "Azure VM Credentials".
3. Allow Insecure Use: Yes.
4. Username: azureuser.
5. Password: Cyberlab123!
6. Save.
7. Go to Configuration > Targets > CLONE the Target we made before.
8. NEW Name / Comment: "Azure Vulnerable VMs - Credentialed Scan".
9. Ensure the Private IP is still accurate.
10. Credentials > SMB > Select the Credentials we just made: Azure VM Credentials.
11. Save.

# Execute Credentialed Scan against our Vulnerable Windows VM
![image](https://github.com/WesGough/openvas/assets/150361198/1fe0cf30-17ca-4ff6-a436-ac0fee022def)
![image](https://github.com/WesGough/openvas/assets/150361198/8eb807fc-8fb9-4fd0-97ad-87e7aafcc3d2)
1. Within Greenbone / OpenVAS, go to Scans > Tasks.
2. CLONE the "Scan - Azure Vulnerable VMs" Task and Edit it.
3. Name / Comment: "Scan - Azure Vulnerable VMs - Credentialed".
4. Targets: Azure Vulnerable VMs - Credentialed Scan.
5. Save.
6. Click the Play button to launch the new Credentialed Scan and wait for it to finish.

# Remediate Vulnerabilities
![image](https://github.com/WesGough/openvas/assets/150361198/4973d24b-1a4d-4b4a-9543-fdf58a3ec7fe)
1. Log back into your Win10-Vulnerable VM.
2. Uninstall Adobe Reader, VLC Player, and Firefox.
3. Restart the VM.
4. Re-initiate the "Scan - Azure Vulnerable VMs - Credentialed" scan and observe the results.

# Verify Remediations
![image](https://github.com/WesGough/openvas/assets/150361198/3494747e-9140-42ef-81d0-22a7faf242a7)
1. Note that there are no longer Vulnerabilities for FireFox, VLC Player, or Adobe Reader!






