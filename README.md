# Nagios Monitoring of Windows Client using NCPA

## 📚 Objective

Monitor a Windows client machine using NCPA plugin from a Nagios Core Server on Debian.

---

## ✅ Setup Summary

| Component            | Details                            |
| -------------------- | ---------------------------------- |
| Nagios Server        | Debian Linux (IP: 192.168.80.110)  |
| Windows Client       | Windows 10 (IP: 192.168.80.131)    |
| Monitoring Agent     | NCPA (Nagios Cross-Platform Agent) |
| Authentication Token | Password1234                       |

---

✅ Full Setup Steps on the Nagios Server
1. Download the plugin
cd /tmp
wget https://raw.githubusercontent.com/NagiosEnterprises/ncpa/master/client/check_ncpa.py


sudo mv check_ncpa.py /usr/local/nagios/libexec/

cd /usr/local/nagios/libexec/

sudo ./check_ncpa.py -H 192.168.80.132 -t 'mysecret' -M memory/virtual


🔹 STEP 2.4: Define NCPA Check Command in Nagios
📝 Open the command configuration file:

sudo nano /usr/local/nagios/etc/objects/commands.cfg

⬇️ Add this block at the bottom:
define command {
    command_name    check_ncpa
    command_line    $USER1$/check_ncpa.py -H $HOSTADDRESS$ $ARG1$
}



🔹 STEP 2.5: Enable Custom Host Configs
📝 Edit the Nagios config file:

sudo nano /usr/local/nagios/etc/nagios.cfg

🔓 Uncomment this line (remove #):  cfg_dir=/usr/local/nagios/etc/servers
🧠 Why?
Nagios looks into this folder to load host & service definitions.



🔹 STEP 2.6: Create Servers Directory (if not present)
📁 Create the config directory:  sudo mkdir -p /usr/local/nagios/etc/servers

🧠 Why?
This is where individual host config files (like for your Windows machine) will go.



🔹 STEP 2.7: Add Host + Service Config for Windows
📝 Create a config file:  sudo nano /usr/local/nagios/etc/servers/win10-1.cfg
 Paste the following:


 
define host {
    use                 windows-server
    host_name           win10-1
    alias               Windows 10 Sys 1
    address             192.168.80.132
    max_check_attempts  5
}

define service {
    use                 generic-service
    host_name           win10-1
    service_description NCPA Agent Version
    check_command       check_ncpa! -t 'mysecret' -P 5693 -M system/agent_version
}

define service {
    use                 generic-service
    host_name           win10-1
    service_description CPU Average
    check_command       check_ncpa! -t 'mysecret' -P 5693 -M cpu/percent -w 20 -c 40 -q 'aggregate=avg'
}



🧠 Why?
This tells Nagios:
There's a new Windows host
It has 2 services to monitor via NCPA

✅ Step2.8: Run Nagios Config Test
Please run this command:  sudo /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg


🔹 STEP 2.9: Restart Nagios to Apply Changes
🔁 Apply all configurations:  sudo systemctl restart nagios














