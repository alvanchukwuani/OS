# Introduction 

This is assessment week 1 where I will be selecting two operating systems, one for the workstation and one for the sever and I will also explain why I choose those opearting system. 


## 1. System Architecture Design. 



![](/Images/system_architecure_design.png)



## 2. Selection Justification 

### Ubuntu server :

I have chose ubuntu server because it is more stable in nature compared to other linux distribution and it is also supported by a huge number of communities which makes it easy for me to find a documentation online if I face any problem. Furthermore, It also  offers long-term support (LTS) releases with regular security updates, ensuring reliability over time, while its lightweight design maximizes performance and efficient resource usage.

### Workstation : 

I will chose Ubuntu Desktop as my main workstation OS system because it is more reliable and stable as will all Ubuntu Distribution, it is less resource intensive compared to Windows OS. It is also very begineer friendly and since Ubuntu Server and Ubuntu Desktop shares the same package manager which is apt, it is also very easy to install packages and dependencies. This allows me to streamline integration of both OS as they share the same commands. 


### Network Configuration. 

For networking, I have used private NAT networking which comes with Virtual machine and it  allows the VM to access external networks, including the internet, by sharing the host system’s IP address while remaining isolated from the local network which enables us to update the packages when necessary while still remaining islated. 

![](/Images/anetwork.png)


![](/Images/networkconfig1.png)


![](/Images/networkconfig2.png)


### Ip Address of both machines

Ip address of Ubuntu Desktop

![](/Images/Desktopip.png)


Ip address of Ubuntu Server

![](/Images/server_ip.png)


### Commands

``` bash 

uname -a
free -h
df -h
ip addr
lsb_release -a

```

### Results of those Commands

Command Performed in Ubuntu Desktop 


![](/Images/UbuntuDesktop_commands.png)



Command Performed in Ubuntu Server


![](/Images/UbuntuServer_commands.png)



### Conclusion

During this week we performed initial system configuration and setup on the following week we will configure our system to allow ssh connections and we will use some commands to install some tools. 
