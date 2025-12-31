# Assessment Week 4 

## Introduction 

In this week we will practically implement SSH hardening and set up key-based authentication for SSH connections and configure the sshd_config file to allow ssh connection with key only. Additionally, we will also configure firewall to allow ssh connection from one particular IP address only. 


### 1. User and Privilege Management

We can create a user using this command : sudo adduser alvanadmin

and we will give admin privilege using this command : sudo usermod -aG sudo alvanadmin this will add this user to the admin group. 

We will verfiy this using SSH and whoami command. 

![](/Images/user_creation.png)


![](/Images/adding_to_sudo.png)


### 2. Setting up Key based authentication .

 
We need to generate a ssh key from the workstation using the command 

``` bash 


ssh-keygen -t ed25519 -C "c"
```

![](/Images/ssh_key_gen.png)


After generating the SSH key we need to copy this key into our server. 

We must be able to do ssh connection into our server first to be able to copy the key to do this we need to run these commands. 


``` bash 

sudo apt update && sudo apt upgrade -y

sudo apt install openssh-server -y

sudo systemctl enable ssh 

#verification 

sudo systemctl status ssh 

``` 

after executing those commands we should see something like this : 


![](/Images/ssh_status.png)


we also need to execute this command : 

``` bash 

 sudo ufw allow ssh 


```

This will allow ssh connections. 


After executing these commands we should be able to successfully ssh into our system. 

![](/Images/ssh_connection1.png)


After this we need to install key using this command

``` bash 

ssh-copy-id alvanadmin@10.0.0.3


```

After copying the id we have successfully setup key for ssh 

![](/Images/ssh-copy-id.png)


### 3. SSH hardening

sshd_config file before : 


![](/Images/ssh_before.png)


sshd_config file after making changes: 

![](/Images/ssh_after.png)

changes made : 

rootlogin no
passwordAuthentication no
pubkeyauthentication yes

![](/Images/verification.png)

### 4. Firewall Configuration 


``` bash 
sudo ufw default deny incoming

sudo ufw default allow outgoing

sudo ufw allow from 10.0.0.4 to any port 22 proto tcp

#verfication 

sudo ufw status verbose

```

After using those commands this is the final output: 

![](/Images/firewall.png)

