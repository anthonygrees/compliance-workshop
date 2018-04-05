# Chef compliance-workshop Training

Instructions for students attending Day 2 training for Compliance on Chef Automate.

# platforms
 * RHEL
 * Windows Server 2012R2

## Prerequisites:
#### Create a Chef BJC Training Environment
Be sure to take note of the following:
 * VPC
 * Subnet
 * Automate IP Address
 * Chef Server IP Address

## Instructions
#### 1.Login to Chef Automate and download any needed profiles
 * DevSec Linux Security Baseline
 * DevSec Windows Patch Baseline
 * CIS Red Hat Enterprise Linux 7 Benchmark Level 1 - Server
 * CIS Microsoft Windows Server 2012 R2 Benchmark Level 1 - Member Server

#### 2. Configure Chef Server
You'll need to increase the max size on the Chef Server if you're using larger compliance profiles like the CIS benchmarks.

Sample: /etc/opscode/chef-server.rb

```bash
opscode_erchef['max_request_size'] = '1500000'
nginx['client_max_body_size'] = '2500m'
```

#### 3. Reconfigure Chef Server
To pick up the changes you will need to reconfigure the Chef Server

```bash
sudo chef-server-ctl reconfigure
```

#### 4. Launch the required number of Windows Servers
Launch the Windows instances and set the following based on the BJC:
 * VPC
 * Security Group
 * IAM
 * Enable Auto-assign Public IP
Set the following Tags:
 * TTL = 8 (Number in hours)
 * X-Contact = Your Name
 * X-Termination-Date = (get from a BJC instance)

#### 5. Launch the required number of RHEL Servers
Launch the RHEL instances and set the following based on the BJC:
 * VPC
 * Security Group
 * IAM
 * Enable Auto-assign Public IP
Set the following Tags:
 * TTL = 8 (Number in hours)
 * X-Contact = Your Name
 * X-Termination-Date = (get from a BJC instance)

## Windows Instructions
The following instructions will be performed for the Windows Compliance Training

#### Bootstrap nodes example commands - with knife
###### Linux - knife bootstrap example
```bash
knife bootstrap -i Path_to_Identity_file Username@FQDN -N Your_Node_Name --sudo -run-list 'recipe[Your_Cookbook_Name]'
```
###### windows(windows firewall needs to permit inbound WinRM traffic)
###### example powershell command to permit WinRM
```bash
Get-NetFirewallPortFilter | ?{$_.LocalPort -eq 5985 } | Get-NetFirewallRule | ?{ $_.Direction -eq "Inbound" -and $_.Profile -eq "Public" -and $_.Action -eq "Allow"} | Set-NetFirewallRule -RemoteAddress "Any"
```
###### Windows - knife bootstrap example
```bash
knife bootstrap windows winrm ADDRESS --winrm-user USER --winrm-password 'PASSWORD' --node-name Your_Node_Name --run-list 'recipe[Your_Cookbook_Name]'
```
#### Kicking off Chef Client runs (or you can schedule with chef-client cookbook)
###### Linux (on AWS using public hostname)
```bash
knife ssh 'name:Your_Node_Name' 'sudo chef-client' -x Username -i Path_to_Identity_file -a ec2.public_hostname
```
###### windows (on AWS using public hostname)
```bash
knife winrm 'name:Your_Node_Name' chef-client --winrm-user USER --winrm-password 'PASSWORD' --attribute cloud.public_hostname
```
