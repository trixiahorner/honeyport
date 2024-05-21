# Honeyport
Attackers often use automated tools to scan a wide range of ports on a target system to identify open and potentially vulnerable services. These tools typically scan all ports, including those that are rarely used.
By setting up a honeyport, you exploit the attacker's tendency to scan indiscriminately. The honeyport itself doesn't offer any real service but acts as a bait. When an attacker scans or tries to connect to this port, they trigger the honeyport mechanism.
<br>
<br>
This honeyport uses an iptables block rule that, when triggered, will automatically deny the attacker. 

## Basic Usage
1. Script
   - Run netcat listener, listening for any and all connections to port 1025
   - From the connection output, pull out the IP address and assign to the variable $IP
   - Take $IP and drop it into the command for iptables and then block any and all connection
   - Write blocked IP to terminal and append to honeyport.log file
   ```
   #!/bin/bash

   echo "Started"

   while [ 1 ]
   do
        IP=`nc -nvl 1025 2>&1 1> /dev/null | grep received | awk -F '[] []' '{print $3;}'`
        iptables -A INPUT -p tcp -s $IP -j DROP
        echo "-- $IP has been blocked!" | tee -a /var/log/honeyport.log
   done
   ```
<br>

2. Navigate to honeyport directory
```
cd /path/to/honeyport.sh
```
<br>
   
3. Make script executable
```
chmod + x honeyport.sh
```
<br>

4. Run script
```
./honeyport.sh
```
![run script](https://github.com/trixiahorner/honeyport/blob/main/images/honey4.png?raw=true)
<br>

5. Now let's see if the honeyport triggers. Remember, we need to make a full connection, so run netcat and disconnect
```
nc 127.0.0.1 1025
```
![run nc](https://github.com/trixiahorner/honeyport/blob/main/images/honey2.png?raw=true)
<br>

6. The terminal shows that the IP has been blocked

![IP blocked](https://github.com/trixiahorner/honeyport/blob/main/images/honey3.png?raw=true)
<br>

7. Nmap run before and after the connection shows the port was OPEN and then FILTERED once it was triggered

![nmap](https://github.com/trixiahorner/honeyport/blob/main/images/honey5.png?raw=true)
<br>

## Conclusion
It's important to think about the impact that a simple iptables block rule can have on IDS. Scans can be very costly.
When scans come in, they generate logs, they generate alerts, they generate a significant amount of information that must get stored. 
Think of this as more than just a honeyport script that will deny access to a specific attacker. Cleaning up IDS traffic in this manner means less traffic that's hitting your firewall. This makes your detection more efficient.
<br>
<br>
### *(if you are looking for more cyber deception labs, check out Antisyphon Training's Active Defense and Cyber Deception course)*
