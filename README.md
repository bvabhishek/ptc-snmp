# SNMP Demo Lab

# Follow the instructions given below to solve (Attack scenario)

* Step 1 Open the repository

```bash
https://github.com/bvabhishek/snmp-goat.git
```

* Steps 

#set the environment variable 

```bash
export ip=<ipaddress>
```
### Lab 1 - Attack Scenario 
# Recon with nmap 
```bash
    nmap -sV -p 1161 -vv $ip -sU
```
 Main catch here is, always run -sU, normal open port check scan wont help you find udp ports

  * PORT     STATE SERVICE REASON       VERSION
  * 1161/udp open  snmp    udp-response SNMPv1 server; net-snmp SNMPv3 server (public)

    We can report this as info since snmpv1 which is un encrypted 

*  Exploitation by using snmpwalk 
   SNMP walk is used to gather a large amount of SNMP data from a device in a single operation. This is useful for monitoring, managing, and troubleshooting network devices like routers, switches, servers, and more.

```bash
snmpwalk -v1 -c public $ip:1161
```
```bash
snmpwalk -v 2c -c public $ip:1161
```

 * The defense part - Let's see the Misconfiguration

 ```bash
    nano /etc/snmp/snmpd.conf
```

# Exploitation with Metasploit 
* Lets see how we can exploit with help of msfconsole 
We shall be checking on 3 auxiliary modules 

1. auxiliary/scanner/snmp/snmp_enum
2. auxiliary/scanner/snmp/snmp_login



```bash
    msfconsole
```
* auxiliary/scanner/snmp/snmp_enum

```bash
   use auxiliary/scanner/snmp/snmp_enum
```
```bash
   options
```
```bash
   set rhosts $ip
```

```bash
   set rport 1161
```
```bash
   run
```

# auxiliary/scanner/snmp/snmp_login

```bash
   use auxiliary/scanner/snmp/snmp_login
```
```bash
   options
```
```bash
   set rhosts $ip
```

```bash
   set rport 1161
```
```bash
   run
```

try with set rport 161 and 1161 and look for the Access level permissions given 

# While we can see on port 1161 says read-write which is a huge misconfiguration 

* whats the misconfiguration check /etc/snmp/snmpd.conf 

# Default Community String: Using the default community string public is a common misconfiguration. It is well-known and often exploited by attackers.

# Read-Write Access: The access level is read-write, which means that an attacker can not only read information from the device but also modify its configuration or settings, potentially leading to serious security issues.

# Information Disclosure: The proof provided (sysDescr.0) shows that the device is a Linux machine with specific details about the kernel version and hostname, which can be valuable information for an attacker.

### Lab2 Defense - Best Practice 

## Try on SNMP v3 ... Port to target 161

* Steps 

# set the environment variable 

```bash
export ip=<ipaddress>
```

# Note the port targetted on this system here is 161 only 

* Repeat the above attack scenario steps for the new IP address and check for the best practices 
