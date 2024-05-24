# WAZUH INSTALLATION
Introduction: The purpose is to deploy the Wazuh SIEM and EDR solution, as well as deploy and activate its agents on computers within the same network.

### Prerequisites:
  
  curl installation
  ```xml
  sudo apt install curl
  ```
### Installation steps (reference: documentation.wazuh.com/current/quickstart.html):

  Download and run the Wazuh installation assistant:
  ```xml
  - curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a -i
  ```
  Check the Wazuh Web Interface using the username and password provided
  
  Take note of the admin username and password for the web interface
  If needed, passwords are stored in "wazuh-install-files.tar" and can be viewed with the following command:
  ```xml
  sudo tar -xvf wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt
  sudo nano wazuh-install-files/wazuh-passwords.txt
  ```
### Ports
  Open the following port(s) internally (on the local firewall): 
  - 1514/tcp
  - 1515/tcp
  - 9200/tcp
  - 9300:9400/tcp
  - 55000/tcp
  
  Open the following port(s) externally (on the router/modem): 
  - 55000/tcp
 
# WAZUH CONFIG/SETUP

### Adding an agent:<br>
  Login to online Interface.
  Navigate to "Agents".
  Select to "Deploy an New Agent".
  OS: Choose the OS of the machine you will be deploying the agent on.<br>
  Server Address: <IP> (if wazuh is being hosted on a local network then use internal IP, otherwise use the Public IP)<br>
  Agent Name: Optional, but may help in keeping track of which machine the agent is monitoring<br>
  Agent Install And Initiation:<br>
  - Copy Agent
  - Run using sudo (on Linux) or Run Power Shell as admin

### Logging Everything:
  Create a copy of "ossec.conf" using "cp /var/ossec/etc/ossec.conf ~/ossec-backup.conf"
  sudo nano /var/ossec/etc/ossec.conf
  - Change "<logall>no</logall>" to "<logall>yes</logall>" and "<logall_json>no<logall_json>" to "<logall_json>yes<logall_json>" located under "<ossec_config>"
  - Exit (ctrl+x) and Save (y)
  
  Restart the Wazuh Manager:
  ```xml
  systemctl restart wazuh-manager
  ```

### Adding Custom Rules (Example: mimikatz):
  Access the Wazuh Web Interface<br>
  Navigate to "Rules", found by expanding "Management" using the drop down menu when clicking on the "wazuh" logo<br>
  Select "Manage rules files"<br>
  Select "Custom rules"<br>
  Edit "local_rules.xml"<br>
  Add custom rule for mimikatz after the included default rule mirroring the format:<br>
 
```xml
  <rule id="100002" level="15">
    <if_group>sysmon_event1</if_group>
    <field name="win.eventdata.originalFileName" type="pcre2">(?i)mimikatz\.exe</field>
    <description>Mimikatz Usage Detected</description>
    <mitre>
      <id>T1003</id>
    </mitre>
  </rule>
```
  Save<br>
  Restart


