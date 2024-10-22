# Ansible-automation-windows-server-2016-chrome
This project is licensed under the MIT License. See the LICENSE file for more information.

# Windows Chrome Installation Automation using Ansible

This project automates the installation of Google Chrome on Windows machines using **Ansible**. It ensures that the necessary folders are present, downloads the installer, installs Chrome silently, and cleans up the installer afterward.

## Prerequisites

1. Ansible installed on your control node (Linux/macOS).
2. Remote Windows hosts with **WinRM** enabled.
3. Python module `pywinrm` installed (`pip install pywinrm`).
4. Proper credentials and access to the Windows machines.

## Setup and Usage

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/your-username/windows-chrome-automation.git
   cd windows-chrome-automation


Step 1: Install Ansible on WSL

In WSL (Ubuntu), open a terminal and run:


sudo apt update
sudo apt install ansible -y
ansible --version

**Step 2: Configure WinRM on Windows Server 2019
Open PowerShell (Admin) on each Windows Server 2019 machine and run the following commands
Ansible uses WinRM to connect to Windows machines, so install the pywinrm library:

Enable-PSRemoting -Force
Set-Item -Path WSMan:\localhost\Service\AllowUnencrypted -Value True
Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value True
Restart-Service WinRM

//Allow WinRM traffic through the firewall:
New-NetFirewallRule -Name "Allow WinRM" -Protocol TCP -LocalPort 5985 -Action Allow

pip install pywinrm

//// Step 3: Create Ansible Inventory File in WSL

 create a new directory for your Ansible project:
 mkdir ~/ansible-windows && cd ~/ansible-windows

Create an inventory.ini file:
nano inventory.ini
[windows]
192.168.1.101
192.168.1.102

[windows:vars]
ansible_user=Administrator
ansible_password=YourPassword
ansible_connection=winrm
ansible_winrm_transport=basic
ansible_port=5985
ansible_winrm_server_cert_validation=ignore
connection_timeout=60
command_timeout=60
** Replace the IP addresses and credentials with the ones for your servers.**

// Step 4: Create a Playbook to Deploy Software //
Create a playbook named install_software.yml:
nano install_software.yml

---
- name: Install Google Chrome on Windows
  hosts: windows
  gather_facts: no
  tasks:
    - name: Ensure C:\Temp directory exists
      ansible.builtin.win_file:
        path: C:\Temp
        state: directory

    - name: Download Google Chrome installer
      ansible.windows.win_get_url:
        url: https://dl.google.com/chrome/install/latest/chrome_installer.exe
        dest: C:\Temp\chrome_installer.exe
        timeout: 30

    - name: Install Google Chrome
      ansible.windows.win_package:
        path: C:\Temp\chrome_installer.exe
        arguments: /silent /install
        state: present

    - name: Clean up installer
      ansible.builtin.win_file:
        path: C:\Temp\chrome_installer.exe
        state: absent

/// Step 5: Run the Ansible Playbook ///
Now that everything is ready, run the playbook from WSL to install the software on both Windows servers.

cd ~/ansible-windows
ansible-playbook -i inventory.ini install_software.yml

Results : Installtion successfully

Solution 6: Add a Debug Ping to Verify Ansible Connection
Use the following command to verify if Ansible can reach the Windows hosts:
ansible -i inventory.ini windows -m win_ping
If successful, the response should be:
192.168.1.101 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}


