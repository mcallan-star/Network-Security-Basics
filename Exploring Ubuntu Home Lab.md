## Exploring Ubuntu Home Lab
- The following Ubuntu commands and tools used were executed inside a VM using Oracle's Virtual Box. Special Note: To enable quality-of-life features (the use of Ctrl + C, Ctrl+V), my laptop was used with a x64 bit installer with ARM. Because of this, port-forwarding occured using Bitvise.  The VirtualBox on port 22 was the guest, and my laptop was the host on port 2222. 
- All outputs are included as a screenshot, with some key points on that particular tool. 

## 1. Identify Network Interfaces and IP Addresses
![ip a output](./ubuntu_screenshots/1.png)
 In 1: ```lo:``` means loopback, and every machine has this.
```127.0.0.1 ::1``` stands for localhost.  This is the VM talking to itself.
In 2: ```enp0s8``` is the ethernet adapter that the VirtualBox NAT created.
```08:00:27:3e:61:72``` is the MAC Address of the adapter
```inet 10.0.2.15/24``` the IPv4 address of my VM.
- within VirtualBox's NAT range (must be 10.0.2.x)
```inet6 fd17:625c:f037:2:a00:27ff:fe3e:6172/64``` is a *global* IPv6 address (private Unique Local Address)
```inet6 fe80::a00:27ff:fe3e:6172/64``` *link-local* IPv6 (auto-assigned, only valid on the LAN segment)

Overview:
The VM is successfully on a network -- NAT -- with address 10.0.2.15. Now, we can ping ubuntu.com to test outbound connectivity. From outside Ubuntu (such as Windows), you won’t connect directly to 10.0.2.15 (since NAT hides it). That’s why port forwarding to SSH in from your host is required (Host Port 2222 → Guest Port 22).

## 2. Check open Ports
- The -tuln options restrict
the output to show only TCP (t) and UDP (u) ports in listening (l) state without resolving
names (n)
![ss -tuln output](./ubuntu_screenshots/2.png)
  

Overview:  The only remotely accessible service is SSH on port 22.  The VM is a DNS client.  It is bound only to loopback, which prevents it from being a public server.  It is running an SSH server on port 22, available on all interfaces. DHCP is bound only to the local network.

## 3. Analyze Network Connections
![output](./ubuntu_screenshots/3.png)
Key terms:
- COMMAND / PID / USER – which process owns the socket.

- FD – file descriptor (the u means it’s open for reading/writing).

- TYPE – IPv4 vs IPv6.

- NAME – protocol, local IP:port → (possibly) remote IP:port, plus state for TCP.

- sshd = SSH server listening/active
- systemd-networkd = DHCP client
- systemd-resolved = local DNS resolver

Overview:
Only SSH (22/tcp) is exposed and is listening.  DNS (systemd-resolved) is loopback-only. Outbound DNS queries go to 137.140.1.202, provided by DHCP.  Two established SSH sessions from the host (root and maddog), seen internally as 10.0.2.2.

## 4. Perform Network Scanning with Nmap
![nmap installation output](./ubuntu_screenshots/4.png)







 