# PowerShell and Windows

## PowerShell Basics

- Add an executable to `C:\Windows\System32` to make it accessible from CMD/PowerShell globally.
- Stop a process by ID or name:

```powershell
Get-Process
Stop-Process -Id 1234
Stop-Process -Name processname -Force
```

- Shutdown WSL:

```powershell
wsl --shutdown
```

- DNS servers on client interfaces:

```powershell
Get-DnsClientServerAddress
```

## Routes

```powershell
route print
route add 192.168.1.0 mask 255.255.255.0 100.100.100.100
```

## BIOS and Power

- Reboot directly into firmware settings:

```powershell
shutdown /r /fw /t 0
```

- Wake-on-LAN generally requires:
1. BIOS/UEFI configuration.
2. OS network adapter support enabled.
3. Correct target MAC and IP or broadcast setup.

## IP Forwarding on Windows

Temporary enable:

```powershell
Set-NetIPInterface -Forwarding Enabled
```

Check interface IDs and forwarding state:

```powershell
netsh interface ipv4 show interfaces
netsh interface ipv4 show interface <ID>
```

## Useful Concepts

- Fast Startup uses a hibernation-style mechanism to speed boot.
- DHCP local lease can change after reboot/renew.
