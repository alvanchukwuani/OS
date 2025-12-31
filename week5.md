# Assessment Week 5

## Introduction 

In this week we will write some scripts to monitor our server remotely, we will also use apparmor, configure fail2ban and automatic updates. 


## 1. Automatic Security Updates Configuration. 

we need to install a tool using this command

``` bash 
# installation 
sudo apt install unattended-upgrades -y

#configuration
sudo dpkg-reconfigure -plow unattended-upgrades

#verification 

cat /apt/apt.conf.d/20auto-upgrades

```

![](/Images/auto_updates.png)



## 2. Fail2ban configuration

Fail2Ban is used to enhance system security by scanning log files for repeated failed authentication attempts and automatically blocking the source IP addresses through firewall rules. This helps protect services such as SSH from brute-force attacks.

To install Fail2Ban:

``` bash sudo apt install fail2ban -y ```


The service starts automatically after installation and enables SSH protection by default. To ensure custom settings are preserved during updates, it is recommended to create a local configuration file:

``` bash sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local ```


Edit the local configuration file:

``` bash sudo nano /etc/fail2ban/jail.local ```


Within the [sshd] section, confirm that enabled = true and adjust the bantime value according to your security requirements. After making changes, restart the service to apply the configuration:

sudo systemctl restart fail2ban

![](/Images/fail2ban_status.png)


![](/Images/fail2ban_status2.png)


## 3.Mandatory Access Control Using Apparmor

AppArmor is used to enforce mandatory access control by limiting what applications are allowed to access on the system. It helps improve system security by confining programs to a defined set of permissions.

To manage AppArmor profiles, install the required utilities:

```bash
sudo apt install apparmor-utils -y

```

![](/Images/sudo_aa_status.png)


## 4. Security Basline Script 

``` bash 

#!/bin/bash

# --- Configuration & Globals ---
readonly SSH_CONF="/etc/ssh/sshd_config"
readonly UPGRADE_CONF="/etc/apt/apt.conf.d/20auto-upgrades"
readonly WORKSTATION_IP="10.0.0.3"

# --- Utility Functions ---

# A wrapper to standardise success/fail logic
# Usage: check_condition <command> <pass_msg> <fail_msg>
check_condition() {
    local cmd="$1"
    local pass_msg="$2"
    local fail_msg="$3"

    # We use 'eval' here to execute the command string passed as an argument
    if eval "$cmd"; then
        echo "  [PASS] $pass_msg"
        return 0
    else
        echo "  [FAIL] $fail_msg"
        return 1
    fi
}

# --- Module: SSH Security ---
audit_ssh() {
    echo "[+] Checking SSH Configuration"

    check_condition \
        "grep -q '^PasswordAuthentication no' $SSH_CONF" \
        "PasswordAuthentication is disabled." \
        "PasswordAuthentication is enabled."

    check_condition \
        "grep -q '^PermitRootLogin no' $SSH_CONF" \
        "Root login is disabled." \
        "Root login is ENABLED."
}

# --- Module: Network Security (UFW) ---
audit_firewall() {
    echo "[+] Checking Firewall (UFW) Configuration..."

    # We cannot use the generic wrapper here because of the nested dependency
    # (We only check the rule if the firewall is active)
    if sudo ufw status | grep -q "Status: active"; then
        echo "  [PASS] Firewall is active."
        
        # Check for the specific workstation IP allowance
        # Using Perl-compatible regex (-P) as per original script
        check_condition \
            "sudo ufw status | grep -Pq '22/tcp\s+ALLOW\s+${WORKSTATION_IP//./\.}'" \
            "SSH is correctly restricted to workstation." \
            "SSH rule is incorrect or missing."
    else
        echo "  [FAIL] Firewall is INACTIVE."
    fi
}

# --- Module: Advanced System Hardening ---
audit_system_hardening() {
    echo "[+] Checking Advanced Security..."

    # 1. Fail2Ban Check
    check_condition \
        "systemctl is-active --quiet fail2ban" \
        "fail2ban service is active." \
        "fail2ban service is INACTIVE."

    # 2. AppArmor Check
    check_condition \
        "sudo aa-status --enabled" \
        "AppArmor is enabled." \
        "AppArmor is DISABLED."

    # 3. Unattended Upgrades Check
    check_condition \
        "[ -f $UPGRADE_CONF ]" \
        "Automatic updates appear to be configured." \
        "Automatic updates config file is missing."
}

# --- Main Execution ---
main() {
    echo "Security Baseline Audit"
    
    audit_ssh
    audit_firewall
    audit_system_hardening
    
    echo "--- Audit Complete ---"
}

# Run the main function
main


```


Output : 



## 5. Remote Monitoring Script 


``` bash 

#!/bin/bash
# monitor-server.sh

# --- Configuration & Globals ---
readonly REMOTE_IDENTITY="alvanadmin"
readonly REMOTE_HOST="10.0.0.3"

# --- Logic: Remote Payload Definition ---
# This function generates the script block that will run on the server.
# By isolating this, we separate 'what to do' from 'how to connect'.
generate_telemetry_payload() {
    cat << 'EOF'
    echo '--- System Uptime & Load ---'
    uptime
    echo ''

    echo '--- Memory Usage (Human-Readable) ---'
    free -h
    echo ''

    echo '--- Disk Space Usage (Human-Readable) ---'
    df -h /
    echo ''

    echo '--- Top 5 CPU-Consuming Processes ---'
    top -b -n 1 | head -n 12 | tail -n 6
EOF
}

# --- Logic: Transport Layer ---
# Handles the connection and executes the provided payload.
execute_remote_session() {
    local user="$1"
    local host="$2"
    local payload="$3"

    echo "--- Remote Server Monitoring ---"
    echo "Connecting to $user@$host..."
    echo ""

    # Pass the payload argument to SSH to execute
    ssh "$user@$host" "$payload"
}

# --- Main Orchestration ---
main() {
    # 1. Build the command set
    local command_batch
    command_batch=$(generate_telemetry_payload)

    # 2. Execute the session
    execute_remote_session "$REMOTE_IDENTITY" "$REMOTE_HOST" "$command_batch"

    # 3. Finalize
    echo "--- Monitoring Complete ---"
}

# Execute Main
main

```


Output : 


