# How to Secure Nginx with Fail2ban
Fail2ban is a popular tool for enhancing the security of your server by monitoring log files and blocking IP addresses that show malicious activity. This tutorial will guide you through setting up Fail2ban to secure your Nginx server with specific configurations.

## Prerequisites
- A server running Ubuntu (or another Debian-based distribution).

- Nginx installed and running.

- Fail2ban installed. If not installed, you can install it using:

```bash
sudo apt-get update
sudo apt-get install fail2ban
```

## Configuration
1. Configure Fail2ban Filters
   Fail2ban filters are used to define patterns that Fail2ban will look for in the logs. In this tutorial, we'll set up two filters:

    - Filter for Forbidden Requests
      Create or edit the filter file /etc/fail2ban/filter.d/nginx-forbidden.conf:

      ```bash
      sudo nano /etc/fail2ban/filter.d/nginx-forbidden.conf
      ```
      Add the following configuration:

      ```ini
      [Definition]
      failregex = access forbidden by rule, client: <HOST>
      ignoreregex =
      ```
      This filter will detect requests where access is forbidden by Nginx rules.

    - Filter for 404 Errors
      Create or edit the filter file /etc/fail2ban/filter.d/nginx-404.conf:

      ```bash
      sudo nano /etc/fail2ban/filter.d/nginx-404.conf
      ```
      Add the following configuration:

      ```ini
      [Definition]
      failregex = ^<HOST> - - \[.*\] "GET /.*" 404
      ignoreregex =
      ```
      This filter will detect 404 errors in the Nginx access log.

2. Configure Fail2ban Jails
   Jails in Fail2ban define the actions to take when a filter is triggered. Create or edit the file /etc/fail2ban/jail.local:

    ```bash
    sudo nano /etc/fail2ban/jail.local
    ```
    Add the following configuration:
    
    ```ini
    [nginx-forbidden]
    enabled = true
    filter = nginx-forbidden
    action = iptables-multiport[name=ForbiddenRequests, port="http,https", protocol=tcp]
    logpath = /var/log/nginx/error.log
    bantime = 3600
    findtime = 600
    maxretry = 5
    
    [nginx-404]
    enabled = true
    filter = nginx-404
    action = iptables-multiport[name=BotRequests, port="http,https", protocol=tcp]
    logpath = /var/log/nginx/access.log
    bantime = 3600
    findtime = 600
    maxretry = 11
    ```
    **Explanation:**
    - enabled: Set to true to activate the jail.
    - filter: Refers to the filter file defined earlier.
    - action: Specifies the action to take when the filter matches. iptables-multiport is used to block the IP on HTTP and HTTPS ports.
    - logpath: Path to the log file to monitor.
    - bantime: Duration (in seconds) for which the IP will be banned.
    - findtime: Time period (in seconds) in which the number of failures is counted.
    - maxretry: Maximum number of retries allowed before banning the IP.
      
3. Restart Fail2ban
   After making changes to the configuration files, restart Fail2ban to apply the new settings:

     ```bash
     sudo systemctl restart fail2ban
     ```
4. Verify Configuration
   To ensure that your jails are active and monitoring as expected, you can check the status with:

    ```bash
    sudo fail2ban-client status
    ```
    To check the status of a specific jail, use:
  
    ```bash
    sudo fail2ban-client status nginx-forbidden
    sudo fail2ban-client status nginx-404
    ```
    These commands will show you which IPs are currently banned and other details about the jails.

   to check iptables:
   ```bash
   sudo iptables -L
   ```
   to unbaned ip:
   ```bash
   sudo fail2ban-client set nginx-404 unbanip [IP]
   sudo fail2ban-client set nginx-forbidden unbanip [IP]
    ```
   
## Conclusion
By following these steps, you have successfully configured Fail2ban to secure your Nginx server against forbidden requests and excessive 404 errors. This will help mitigate potential attacks and reduce unwanted traffic to your server.
