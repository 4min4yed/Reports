# Connectivity and Network Operations Notes

## Expose a Local Service to the Internet

Expose a local service by using a tunneling command and a local server.

### Locate npm Prefix and Tunnel Binary

```powershell
npm config get prefix
# Example output:
# C:\Users\Mega-PC\AppData\Roaming\npm
```

Look for `lt.cmd` under the npm prefix path.

### Start Tunnel and Local Service

```powershell
C:\Users\Mega-PC\AppData\Roaming\npm\lt.cmd --port 4444 --bypass-tunnel-reminder 1 --print-requests
python -m http.server 4444
```

## SSH File and Message Transfer

### Copy a File with SCP

```bash
scp -P 5246 user@container-ip:/path/to/file.txt /local/path/
```

### Send a Broadcast Message Remotely

```bash
ssh admin@192.168.0.0 'echo "msg" | wall'
```

Note: SSH keys are recommended to avoid password prompts every time.

## Traffic and Port Listening

### Listen to ICMP/Ping

Windows context (tool may vary by environment):

```bash
ngrep -W byline '.' 'icmp'
```

Linux:

```bash
sudo tcpdump -i <interface> -n icmp and icmp[icmptype]=icmp-echo
```

### Listen on a TCP Port

```bash
nc -lvp 8080
```

## Quick Server and Transfer

### Start a Temporary HTTP Server

```bash
python3 -m http.server
```

### Download from Server

```bash
wget http://IP:port/file_in_dir
wget --header="Transfer-Encoding: chunked" http://IP:port/file_in_dir
```

## Inspect Ports and Interfaces

### Show Listening Ports

Linux:

```bash
ss -tuln
```

PowerShell:

```powershell
Get-NetTCPConnection -State Listen | Select-Object LocalAddress, LocalPort, OwningProcess
```

### Find Which Process Uses a Port

```bash
lsof -i :8080
sudo netstat -nlp | grep :80
```

### Show Interfaces and IP Addresses

```bash
ip addr show
```

### Bring Interface Up and Request DHCP Lease

```bash
sudo ip link set wlan0 up
sudo dhclient wlan0
```

### Show DNS Servers on Windows

```powershell
Get-DnsClientServerAddress
```

## Protocol and Service Notes

### FTP

```bash
ftp -P 21 10.10.22.64
```

Common commands: `dir` or `ls`, then `get file.txt`.

Notes:
- FTP is not encrypted.
- Anonymous login can be used where allowed (`anonymous` user).

### SMB (Port 445)

```bash
smbclient -L ip
smbclient //ip/share
```

Common commands: `ls`, `get file.txt`.

### Other Common Protocol Tools

- `telnet`
- `ssh`
- `sftp`

## Local DNS Override for Name Resolution

If a hostname resolves incorrectly or does not connect as expected, add a local host mapping:

```bash
echo "10.129.128.223 unika.htb" | sudo tee -a /etc/hosts
```

## Connection Flow Reference

### 1. DNS Lookup

- Client sends A/AAAA query.
- DNS responds with destination IP.

### 2. TCP 3-Way Handshake

- `SYN` ->
- `<- SYN-ACK`
- `ACK` ->
- TCP session established.

### 3. TLS Handshake

Client sends `ClientHello` including:
- TLS version
- Cipher suites
- SNI
- ALPN
- fingerprint metadata

Server-side checks can include:
- IP to ASN mapping
- Country policies
- WAF rules
- Allow list checks

If allowed:
- ServerHello and certificate exchange
- key agreement
- Finished messages
- TLS session established

### 4. HTTP Request

Client sends request (example):

```http
GET /index.html
Host: site.gov
```

Server replies (example):

```http
HTTP/1.1 200 OK
```

## Debugging Checklist

- Document behavior before, during, and after troubleshooting.
- Wait around 60 seconds before and after cable/device changes.
- Keep ISP behavior in mind when interpreting traces.

### Useful Commands

```powershell
tracert <host>
ping <host>
nslookup <host>
Test-NetConnection -ComputerName <IP> -Port <PORT>
curl -v https://<host>
```

OpenSSL TLS check:

```bash
openssl s_client -connect <IP>:443
```

## Notes and Caveats

- Some ISPs use private addressing internally in backbone networks.
- Some routers or CPE devices may not reply to TTL-expired packets, so hops can be missing from traceroute.
- No ping response does not always mean host down; ICMP may be blocked.
