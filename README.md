# Chef compliance-workshop Training

Instructions for students attending Day 2 training for Compliance on Chef Automate.

## Platforms used:
 * RHEL 7
 * Windows Server 2012R2
 * CentOS 7

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
$ sudo vi /etc/opscode/chef-server.rb
```
Add the following lines
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
 * X-TTL = 8 (Number in hours)
 * X-Contact = Your Name
 * X-Termination-Date = (get from a BJC instance)
 
 In the Advanced Details - User Data 
 Add the Windows firewall rules and the IP address of Chef Automate and Chef Server
 ```bash
 <powershell>
  $text1 = 'xxx.xxx.xxx.xxx  chef.automate-demo.com'
  $text1 | Add-Content 'C:\\Windows\\System32\\drivers\\etc\\hosts'
  $text = 'xxx.xxx.xxx.xxx  automate.automate-demo.com'
  $text | Add-Content 'C:\\Windows\\System32\\drivers\\etc\\hosts'
  Get-NetFirewallPortFilter | ?{$_.LocalPort -eq 5985 } | Get-NetFirewallRule | ?{ $_.Direction -eq "Inbound" -and $_.Profile -eq "Public" -and $_.Action -eq "Allow"} | Set-NetFirewallRule -RemoteAddress "Any"
  </powershell>
  ```

#### 5. Launch the required number of RHEL and / or CentOS Servers
Launch the RHEL instances and set the following based on the BJC:
 * VPC
 * Security Group
 * IAM
 * Enable Auto-assign Public IP
 
Set the following Tags:
 * TTL = 8 (Number in hours)
 * X-TTL = 8 (Number in hours)
 * X-Contact = Your Name
 * X-Termination-Date = (get from a BJC instance)
 
 In the Advanced Details - User Data 
 Add the IP address of Chef Automate and Chef Server
 ```bash
 #!/usr/bin/env bash
 echo "xxx.xxx.xxx.xxx automate.automate-demo.com" | sudo tee --append /etc/hosts
 echo "xxx.xxx.xxx.xxx chef.automate-demo.com" | sudo tee --append /etc/hosts
 ```

## Windows Instructions
Follow this link to the Windows instructions:

https://github.com/anthonygrees/compliance-workshop/blob/master/windows.md


## CentOS Instructions
Follow this link to the CentOS instructions:

https://github.com/anthonygrees/compliance-workshop/blob/master/centos.md


## RHEL Instructions
Follow this link to the CentOS instructions:

https://github.com/anthonygrees/compliance-workshop/blob/master/rhel.md



## License and Author

* Author:: Anthony Rees <anthony@chef.io>

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
