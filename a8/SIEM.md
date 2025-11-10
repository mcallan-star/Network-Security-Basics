# SIEM and IDPS Configuration Lab

This document demonstrates building a lightweight SIEM and IDPS with Suricata, Loki, Promtail, and LogCLI on Ubuntu. I describe a guide to:
- Install and configure Suricata for intrusion detection and prevention
- Set up Loki as a lightweight log aggregation system  
- Configure Promtail to collect and ship logs to Loki
- Use LogCLI for querying and analyzing security events
- Generate and analyze security alerts for incident response

## Part 1 – Prepare System
![System Preparation](./siem_screenshots/1_prepare_system.png)

### Overview:
Installing essential tools including curl, jq, unzip, and Docker using `sudo apt update && sudo apt upgrade -y` followed by `sudo apt -y install curl jq unzip`. Docker is installed using `curl -fsSL https://get.docker.com | sudo sh`. Adding user to docker group with `sudo usermod -aG docker "$USER"` and `newgrp docker`, then enabling Docker service with `sudo systemctl enable --now docker`. These tools are required for downloading files, processing JSON data, extracting archives, and running containerized services.

## Part 2 – Suricata
![Suricata Setup](./siem_screenshots/2_suricata_setup.png)

### Overview:
Installing and configuring Suricata IDPS by first setting up community rules with `sudo apt -y install suricata-update` and `sudo suricata-update`. Finding the network interface using `ip -br a | awk '$1!="lo"{print $1, $3}'` and creating custom rules directory with `sudo mkdir -p /etc/suricata/rules` and `sudo touch /etc/suricata/rules/local.rules`. Configuring `suricata.yaml` to set proper rule paths and interface settings, then installing Suricata with `sudo apt -y install suricata` and starting it in daemon mode.

## Part 3 - Loki
![Loki Setup](./siem_screenshots/3_loki_setup.png)

### Overview:
Setting up Loki as the central log database by creating necessary directories with `sudo mkdir -p /etc/loki /var/lib/loki/{chunks,rules}` and creating the Loki configuration file. Setting proper permissions with `sudo chown -R 10001:10001 /var/lib/loki` and running Loki in Docker with `sudo docker run -d --name loki -p 3100:3100`. Loki acts as a lightweight log aggregation system that indexes metadata labels rather than full text, making it efficient for storing and querying large volumes of log data.

## Part 4 – Run Promtail (Log Shipper)
![Promtail Setup](./siem_screenshots/4_promtail_setup.png)

### Overview:
Configuring Promtail as the log shipper to collect Suricata logs and forward them to Loki. Creating Promtail directories with `sudo mkdir -p /etc/promtail /var/lib/promtail` and writing the Promtail configuration file to tail `/var/log/suricata/eve.json`. Running Promtail in Docker with proper volume mounts to access Suricata logs and send them to Loki's API endpoint. Promtail tracks read positions to prevent duplicate log entries and maintains continuity across restarts.

## Part 5 – Install LogCLI and Test Queries
![LogCLI Installation and Testing](./siem_screenshots/5_logcli_testing.png)

### Overview:
Installing LogCLI command-line tool by downloading from GitHub releases using `curl -L https://github.com/grafana/loki/releases/download/v2.9.8/logcli-linux-arm64.zip` and extracting to `/usr/local/bin`. Testing connectivity with `logcli labels --addr=http://localhost:3100` to verify Loki connection and listing available labels. Querying recent logs with `logcli query --addr=http://localhost:3100 --limit=10 '{job="suricata"}'` to confirm that Promtail is successfully shipping Suricata logs to Loki.

## Part 6 – Generate Alerts and Analyze
![Alert Generation and Analysis](./siem_screenshots/6_generate_alerts.png)

### Overview:
Creating custom Suricata detection rules and generating test alerts to verify the complete SIEM pipeline. Adding a custom rule to `/etc/suricata/rules/local.rules` that detects specific HTTP User-Agent strings. Restarting Suricata with `sudo systemctl restart suricata` to load new rules, then triggering alerts using `curl -A "CPS-NETSEC-LAB" http://example.com/`. Querying alerts in Loki using LogCLI with JSON parsing to extract and format alert information for analysis.

## Part 7 – Correlation Challenge
![Log Correlation Analysis](./siem_screenshots/7_correlation_challenge.png)

### Overview:
Demonstrating log correlation and aggregation capabilities by finding top source IP addresses in recent alerts. Using LogCLI with time-based queries `--since=5m` and JSON parsing to extract source IP information from alert logs. Employing command-line tools like `sort`, `uniq -c`, and `head` to aggregate and rank IP addresses by frequency. This illustrates basic correlation techniques used in SOC environments for threat analysis and incident response.

## Part 8 – Create and Test Your Own Custom Rule
![Custom Rule Creation](./siem_screenshots/8_custom_rule_testing.png)

### Overview:
Developing and testing a personalized Suricata detection rule to detect specific network patterns or conditions. Writing custom rule syntax with appropriate message, signature ID, and detection logic. Testing the rule by generating network traffic that should trigger the detection, then verifying the alert appears in Suricata logs and can be queried through Loki. This demonstrates the process of creating tailored security detections for specific environments and threat scenarios.

## Part 9 – Cleanup
![System Cleanup](./siem_screenshots/9_cleanup.png)

### Overview:
Properly shutting down and cleaning up the SIEM environment by stopping Docker containers with `sudo docker stop promtail loki` and removing them with `sudo docker rm promtail loki`. Removing Suricata installation with `sudo apt purge -y suricata` and cleaning up Docker system resources with `sudo docker system prune -a -f`. This ensures the system is returned to a clean state and all lab components are properly removed.

## Summary

This lab demonstrates comprehensive SIEM and IDPS configuration including:
- Multi-component security infrastructure setup with Suricata, Loki, Promtail, and LogCLI
- Intrusion detection system configuration with community and custom rules  
- Lightweight log aggregation using Loki's label-based indexing approach
- Automated log shipping and processing with Promtail for real-time monitoring
- Command-line log analysis and correlation using LogCLI for security investigations
- Custom rule development and testing for tailored threat detection
- End-to-end security event pipeline from detection to analysis

This SIEM implementation provides a foundation for understanding modern security operations center (SOC) workflows, demonstrating how security events flow from network monitoring through log aggregation to analyst investigation. The lightweight architecture using containers and efficient log processing makes it suitable for both learning environments and small-scale production deployments.
