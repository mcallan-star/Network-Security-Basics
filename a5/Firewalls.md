
# UFW Firewall Configuration Lab

This document demonstrates the configuration and testing of Ubuntu's Uncomplicated Firewall (UFW). I describe a guide to 
-  Install and enable UFW with safe defaults.
- Add specific allow/deny rules (SSH, web, IP-based).

- Enable and tune logging, generate traffic, and read/interpret logs

## 1. Install UFW
![UFW Installation](./a5/firewall_screenshots/1%20install%20ufw.png)

### Overview:
The Uncomplicated Firewall (UFW) package is installed using `sudo apt install ufw`. UFW provides a simplified interface for managing iptables firewall rules on Ubuntu systems.

## 2. Check Initial Firewall Status
![Check UFW Status](./a5/firewall_screenshots/2%20check%20status.png)

### Overview:
Checking the initial status of UFW using `sudo ufw status`. By default, UFW is installed but inactive on fresh Ubuntu installations, meaning no firewall rules are being enforced.

## 3. Allow SSH to Prevent Lockout
![Allow SSH](./a5/firewall_screenshots/3%20allow%20ssh%20to%20prevent%20lockout.png)

### Overview:
Before enabling the firewall, it's crucial to allow SSH connections using `sudo ufw allow ssh` or `sudo ufw allow 22`. This prevents getting locked out of remote systems when the firewall is activated.

## 4. Investigate Listening Ports
![Check Listening Ports](./a5/firewall_screenshots/4%20see%20whats%20listening%20(investigate%20ports).png)

### Overview:
Using `netstat -tulnp` or `ss -tulnp` to identify which services are currently listening on network ports. This helps determine which ports need firewall rules before enabling UFW.

## 5. Enable UFW
![Enable UFW](./a5/firewall_screenshots/5%20enable%20ufw.png)

### Overview:
Activating the firewall using `sudo ufw enable`. Once enabled, UFW will enforce the configured rules and block unauthorized connections while allowing previously defined exceptions.

## 6. Verify UFW Status After Enabling
![UFW Status Active](./a5/firewall_screenshots/6%20check%20ufw%20status.png)

### Overview:
Confirming that UFW is now active and displaying the current rule set. The status shows that SSH (port 22) is allowed from anywhere, ensuring continued remote access.

## 7. Allow HTTP and HTTPS Traffic
![Enable HTTP/HTTPS](./a5/firewall_screenshots/7%20enable%20http(s).png)

### Overview:
Adding rules to allow web traffic using `sudo ufw allow http` and `sudo ufw allow https`. This permits incoming connections on ports 80 and 443 for web server functionality.

## 8. Configure Outgoing and Incoming Traffic Policies
![Traffic Policies](./a5/firewall_screenshots/8%20outgoing-incoming%20traffic%20.png)

### Overview:
Setting default policies for traffic direction using `sudo ufw default deny incoming` and `sudo ufw default allow outgoing`. This creates a secure-by-default configuration that blocks unsolicited incoming connections while allowing outbound communication.

## 9. Blacklist IP Addresses and Subnets
![IP Blacklisting](./a5/firewall_screenshots/9%20blacklist%20ip%20and%20whole%20subnet%20too%20(cidr).png)

### Overview:
Demonstrating how to block specific IP addresses and entire subnets using `sudo ufw deny from [IP/subnet]`. This shows both single IP blocking and CIDR notation for subnet-wide restrictions.

## 10. Allow Specific Host to Specific Port
![Specific Host Rules](./a5/firewall_screenshots/10%20allow%20specific%20host%20to%20specific%20port.png)

### Overview:
Creating granular firewall rules that allow specific IP addresses to access particular ports. This demonstrates advanced rule creation for precise access control: `sudo ufw allow from [IP] to any port [PORT]`.

## 11. List All Firewall Rules
![List UFW Rules](./a5/firewall_screenshots/11%20list%20out%20rules.png)

### Overview:
Displaying all configured firewall rules using `sudo ufw status numbered` or `sudo ufw status verbose`. This provides a comprehensive view of all active rules with their priorities and detailed configurations.

## 12. Enable UFW Logging
![Enable Logging](./a5/firewall_screenshots/12%20enable%20logging.png)

### Overview:
Activating UFW logging functionality using `sudo ufw logging on`. This enables the firewall to record blocked and allowed connections for security monitoring and troubleshooting purposes.

## 13. Set Logging Level to High
![Set High Logging](./a5/firewall_screenshots/13%20set%20logging%20level%20to%20'high'.png)

### Overview:
Configuring verbose logging using `sudo ufw logging high`. Higher logging levels capture more detailed information about firewall activities, including all blocked packets and rule matches.

## 14. View Firewall Logs
![View Logs](./a5/firewall_screenshots/14%20view%20logs%20(tail%20-f).png)

### Overview:
Monitoring firewall logs in real-time using `sudo tail -f /var/log/ufw.log`. This shows live firewall activity, including blocked connection attempts and rule matches.

## 15. Filter Log Entries
![Filtered Logs](./a5/firewall_screenshots/15%20filtered%20grep%20log.png)

### Overview:
Using grep to filter specific log entries and analyze firewall behavior. This demonstrates how to search for particular events, IP addresses, or ports in the firewall logs for targeted analysis.

## Network Testing with Netcat

### Netcat Verbose Mode
![Netcat Testing](./a5/firewall_screenshots/netcat%20%20verbose%20mode,%20zero%20io.png)

### Overview:
Using netcat (`nc`) in verbose mode to test network connectivity and firewall rules. The `-v` flag provides detailed output about connection attempts, while `-z` performs port scanning without sending data.

### Denied Connection Log
![Nothing Denied](./a5/firewall_screenshots/nothing%20was%20denied.png)

### Overview:
Log entry showing that no connections were denied during testing, indicating that the tested connections matched allow rules or were directed to permitted services.

### Allowed Connection Log
![Connection Allowed](./a5/firewall_screenshots/this%20was%20allowed.png)

### Overview:
Log entry demonstrating a successful connection that was permitted by the firewall rules, showing how UFW logs both allowed and denied traffic when logging is enabled.

### Specific IP Allow Log
![Allowed IP Log](./a5/firewall_screenshots/log%20of%20allowed%20(10.0.2.15).png)

### Overview:
Detailed log entry showing an allowed connection from IP address 10.0.2.15, demonstrating how UFW logs include source IP information and which rule permitted the connection.

## Summary

This lab demonstrates comprehensive UFW firewall configuration including:
- Installation and initial setup
- Rule creation for common services (SSH, HTTP, HTTPS)
- Advanced rule configuration for specific hosts and ports
- IP and subnet blacklisting
- Logging configuration and analysis
- Network testing and validation

UFW provides an effective, user-friendly interface for managing Linux firewall rules while maintaining the power and flexibility of the underlying iptables system.