# Assessment week 7

## Introduction 

In this week we will practially demonstrate network port scanning, overall system audit using lynis and we will also implement more SSH hardening. 

## Network Security Assessment

To perform a network port scan we need to install some tools , we will choose nmap because of it has more useful features and it is also widely preferred by many security professionals. Tools like nikto, Masscan, arpscan, netcat can also perform network scan. 


Installation Documentation. 

``` bash 

sudo apt update

# installing namp using default package manager

sudo apt install nmap -y

#verification of tool

nmap --version


```


![](/Images/nmap_verification%20.png)
Once installed we can use this scan command : 

``` bash 

# This command will scan the open ports of the system. 

sudo nmap -sV -p- localhost

```
After performing scan from the machine to itself this is the result : 

![](/Images/nmap_scan_result.png)

Justification for open ports : 

Some ports were opened for iperf3 and port 22 is opened for ssh connections. 

when we scanned our server using nmap from desktop we were able to see less details about the machine.

![](/Images/nmap-scan-desktop.png)


## System audit using lynis

Lynis is an open-source system audit tool which helps us to visualize the overall system configuration and some tips to harden our system configuration. 


Installation Documentation

``` bash 

#tool installation
sudo apt install lynis -y 

#audit command 

sudo lynis audit system


```

### Lynis System Audit before: 

![](/Images/lynis_before.png)


### Changes made . 

Hardened SSH by limiting max logged in sessions, increased ban time in fail2ban, installed rootkit detection tools, and other tools such as apt-listchanges. 


### Lynis System Audit After: 

![](/Images/lynis_after.png)


### SSH Security 

SSH has been secured with strict sshd_configuration. 


Verification : 


![](/Images/last1.png)

![](/Images/last2.png)


## Conclusion

Throughout all assessmenet weeks from week 1 to week 7, we implemented and learned about protecting our system through strict system configuration such as ssh, firewall , network, and access control. We also tested various useful tools such as nmap for network scan, rkhunter for rootkit detection, apt-listchanges for analysing packets. 
