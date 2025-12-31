# Assessment week 2

## Introduction 

In this week we will explore some performance testing methodolody along with performance monitoring tools. 


## Security Planning and Testing Methodolody

## Remote Monitoring Methodology

Remote monitoring will be done via SSH from workstation in following week we will implement scripting to automate this process. 


## Testing Strategy 

Peformance testing will be done in two steps:

### Step 1: Baseline Testing

We will gather information of the system in idle state where no application will be run and we will note the metrics and comapare those metrics values to visualize the data. 

### Step 2: Load Testing

In this stage will we generate load using various application and we will record those peak resource usage (CPU, Memory, Disk, IO swap ) and compare the value which we recorded in step 1. 


## Metrics to Observe

### 1. CPU usage

we will use htop to measure CPU usage as it provides better visual graphics than top and it also allows us to  see process-level CPU load and average system load.


### 2. Memory Usage

We will use free -h command to see the memory usage. It is very simple to use and we already used this command in week 1. 

### 3. Disk IO 

we will implement iostat and df -f to monitor disk usage. 

### 4. Network Performance

Network Performance can be monitored via default command ping, or using tools like iperf3 . We can also use traceroute to see network hops and observe network latency.

## Security Configuration Checklist

### 1. User Privilege Management

- Created a new admin user and added in sudo group

``` bash 
#creates a user called testuser
sudo useradd testuser

sudo usermod -aG sudo testuser
#adds the user in the sudo group

#verification

groups testuser

```

### 2. SSH hardening

SSH hardening can be done via configuring ssh file which is usually located at /etc/ssh/sshd_config which can be opened using the command : sudo nano /etc/ssh/sshd_config , the file must be opened with root privilges to be able to edit it. 

After opening this file we can make following changes: 

1. Change default port to a custom port
2. disable root login 
3. Allow key based authentication 
4. Disable password based authentication 

NOTE : In the following weeek we will pratically implement these and provide a proof with screenshots.

### 3. Security monitoring

we can implement fail2ban tool which will allow us to make configuration to ban IP addresses that are constantly trying to knock the door of our server with commonly used passwords. In more technical terms this process is called bruteforce attack.It is also recommened to implement other tools such as suricate which is a threat detection engine (IDS) which will enable us to detect signature based attacks. 


### 4. Firewall configuration 

We can configure uncomplicated firewall (ufw) to create rules which will allow us to perform remote monitoing via SSH from one specific host ip only. 

To do this configuration we need to enter following command: 

``` bash 
# view the status of the firewall 
sudo ufw status

#It will show disabled or closed

sudo ufw enable

#It should show firewall up and running

 sudo ufw allow from 10.0.0.4 to any port 22 proto tcp

sudo ufw status

# should show the newly created rule.

```

### 5. Auto updates

Automatic updates can be enabled using the software package called unattended-upgrades. Security patches will be automatically installed once configured correctly. 

### 6. Mandatory Access Control (MAC)

MAC can be configured using Apparmor in Ubuntu.


## Threat Mapping

### 1. Privilege escalation via outdated softwares

Outdates software or softwares lacking security patches can be a open door for malware to enter into the system, such malware can be ransomeware as well as rootkit which cause significant risk to organization which uses server to host services. Fortunately, with unattended-upgrades tool we can prevent this from happening. 

### 2. Bruteforce attack

Threat actors have used automated scanning bots which uses commonly used passwords and port to try SSH into our system they do this to perform DDOS attack using our system or they can also install malwares into our system. By configuring sshd_config file to disable password based login and using firewall to filter out traffic we are able to protect our sytem from malicious actors online. Furthermore, if those two active defenses fails then we have our fail2ban enabled which will report us if any unattempt login attempt is being made. 

### 3. Network Scanning

Without a proper firewall enabled and configured, malicious actor can use network scanning tool like nmap, nikto (noisy than nmap) to scan and do network enumration to check for any open ports this can result in full exploitation of our system .To prevent we need to do network isolation and firewall configuration.
