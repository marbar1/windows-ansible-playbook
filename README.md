# Windows Ansible Playbook

This playbook installs and configures most of the software I use on my personal maschines.

## Setup on the Windows machine

Setup an OpenSSH server with PowerShell as default shell on the Windows machine by following these steps or execute the script [openssh_setup.ps1](openssh_setup.ps1) for convinience. Further information on the official [Microsoft docs](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse).

1. Install the OpenSSH-Server through Windows optional apps and features or with the following command:
    ```powershell
    Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
    ```
2. Start the sshd service with `Start-Service sshd`
3. Configure the service to start on Windows startup with `Set-Service -Name sshd -StartupType 'Automatic'`
4. Confirm the Firewall rule is configured. It should be created automatically by setup. Run the following to verify
    ```powershell
    if (!(Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue | Select-Object Name, Enabled)) {
        Write-Output "Firewall Rule 'OpenSSH-Server-In-TCP' does not exist, creating it..."
        New-NetFirewallRule -Name 'OpenSSH-Server-In-TCP' -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound `
            -Protocol TCP -Action Allow -LocalPort 22
    } else {
        Write-Output "Firewall rule 'OpenSSH-Server-In-TCP' has been created and exists."
    }
    ```
5. Change the default shell to PowerShell
    ```powershell
    New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell `
        -Value "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -PropertyType String -Force
    ```
