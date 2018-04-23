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
The following instructions will be performed for the Windows Compliance Training

#### 1. Manually Scan the Windows node

Use the Chef Automate Scanner to scan your node
 * Create your credentials
 * Add your Node
 * Add your job

#### *** Automatically Scan and Harden ***
#### 2. Bootstrap nodes example commands - with knife
###### windows (on AWS using public hostname)
To bootstrap the node, replace the node IP, Password and Node Name and run the following command

```bash
knife bootstrap windows winrm ec2-xx-xxx-xxx-xxx.us-west-2.compute.amazonaws.com --winrm-user Administrator --winrm-password 'PASSWORD' --node-name Your_Node_Name
```
If you need to change your Windows password, RDP onto the Windows instance and run the following command in Powershell as Administrator

```bash
net user Administrator "new_password"
```

#### 3. Automatically schedule chef-client runs with the Chef-Client cookbook
This version of the Chef-Client Cookbook is modified for training purposes and is set to run every 3 mins and 30 seconds.  It should NOT be used for any other purpose.
```bash
$ git clone https://github.com/anthonygrees/chef-client.git
$ cd chef-client
$ berks init
$ berks install
$ berks upload
```
#### 4. Kick off Chef Client run to enable the Chef-Client Cookbook
###### Windows (on AWS using public hostname)
This will set the Chef Client to run every 3 min and 30 secs
```bash
knife winrm 'ec2-xx-xxx-xxx-xxx.us-west-2.compute.amazonaws.com' 'chef-client -c c:/chef/client.rb' -m -x Administrator -P 'PASSWORD'
```

#### 5. Automatically schedule the CIS Microsoft Windows Server 2012 R2 Benchmark Level 1 - Member Server
This version of the Audit Cookbook is configured to run the InSpec CIS WIN2012R2 Benchmark.

```bash
when 'windows'
    default['audit']['profiles'] = [
      {
        name: 'CIS Microsoft Windows Server 2012 R2 Benchmark Level 1 - Member Server',
        compliance: 'workstation-1/cis-windows2012r2-level1-memberserver',
      },
    ]
```
https://github.com/anthonygrees/audit_agr
```bash
$ git clone https://github.com/anthonygrees/audit_agr
$ cd audit_agr
$ berks init
$ berks install
$ berks upload
```

#### 6. Use the Windows 2012 R2 server Hardening Cookbook to fix the CIS errors
This version has been modified to enable WINRM

```bash
$ git clone https://github.com/anthonygrees/cis-win2012r2-l1-hardening-cookbook
$ cd cis-win2012r2-l1-hardening-cookbook
$ berks init
$ berks install
$ berks upload
```
https://github.com/anthonygrees/cis-win2012r2-l1-hardening-cookbook

Now wait for the Audit Profile to run :)


## CentOS Instructions
The following instructions will be performed for the CentOS Compliance Training

#### 1. Manually Scan the CentOS node

Use the Chef Automate Scanner to scan your node
 * Create your credentials
 * Add your Node
 * Add your job

#### *** Automatically Scan and Harden ***

#### 2. Bootstrap nodes example commands - with knife
###### Linux - knife bootstrap example (on AWS using public hostname)
```bash
knife bootstrap -i workshop.pem centos@ec2-XX-XXX-XXX-XXX.us-west-2.compute.amazonaws.com -N Your_Node_Name --sudo -run-list 'recipe[Your_Cookbook_Name]'
```

#### 3. Automatically schedule chef-client runs with the Chef-Client cookbook
This version of the Chef-Client Cookbook is modified for training purposes and is set to run every 3 mins and 30 seconds.  It should NOT be used for any other purpose.
https://github.com/anthonygrees/chef-client.git
```bash
$ git clone https://github.com/anthonygrees/chef-client.git
$ cd chef-client
$ berks init
$ berks install
$ berks upload
```

#### 4. Kick off Chef Client run to enable the Chef-Client Cookbook
###### Linux (on AWS using public hostname)
This will set the Chef Client to run every 3 min and 30 secs

```bash
knife ssh 'name:Your_Node_Name' 'sudo chef-client' -x centos -i workshop.pem
```

#### 5. Automatically schedule the CCIS CentOS Linux 7 Benchmark Level 1
This version of the Audit Cookbook is configured to run the InSpec CIS WIN2012R2 Benchmark.

```bash
when 'centos'
    default['audit']['profiles'] = [
      {
        name: 'DevSec Linux Security Baseline',
        compliance: 'workstation-1/linux-baseline',
      },
      {
        name: 'CIS CentOS Linux 7 Benchmark Level 1',
        compliance: 'workstation-1/cis-centos7-level1',
      },
    ]
  end
```
https://github.com/anthonygrees/audit_agr
```bash
$ git clone https://github.com/anthonygrees/audit_agr
$ cd audit_agr
$ berks init
$ berks install
$ berks upload
```
#### 6. Harden the CentOS instance with the Hardening Cookbook

```bash
$ git clone https://github.com/chef/cis-rhel
$ cd cis-rhel
$ berks init
$ berks install
$ berks upload
```
https://github.com/chef/cis-rhel

Now wait for the Audit Profile to run :)


## RHEL Instructions
The following instructions will be performed for the RHEL Compliance Training

#### 1. Manually Scan the RHEL node

Use the Chef Automate Scanner to scan your node
 * Create your credentials
 * Add your Node
 * Add your job

#### *** Automatically Scan and Harden ***

#### 2. Bootstrap nodes example commands - with knife
###### Linux - knife bootstrap example (on AWS using public hostname)
```bash
knife bootstrap -i Path_to_Identity_file  ec2-user@ec2-XX-XXX-XXX-XXX.us-west-2.compute.amazonaws.com -N Your_Node_Name --sudo -run-list 'recipe[Your_Cookbook_Name]'
```

#### 3. Automatically schedule chef-client runs with the Chef-Client cookbook
This version of the Chef-Client Cookbook is modified for training purposes and is set to run every 3 mins and 30 seconds.  It should NOT be used for any other purpose.
https://github.com/anthonygrees/chef-client.git
```bash
$ git clone https://github.com/anthonygrees/chef-client.git
$ cd chef-client
$ berks init
$ berks install
$ berks upload
```

#### 4. Kick off Chef Client run to enable the Chef-Client Cookbook
###### Linux (on AWS using public hostname)
This will set the Chef Client to run every 3 min and 30 secs

```bash
knife ssh 'name:Your_Node_Name' 'sudo chef-client' -x ec2-user -i workshop.pem
```

#### 5. Automatically schedule the CIS Red Hat Enterprise Linux 7 Benchmark Level 1 - Server
This version of the Audit Cookbook is configured to run the InSpec CIS WIN2012R2 Benchmark.

```bash
when 'redhat'
    default['audit']['profiles'] = [
      {
        name: 'DevSec Linux Security Baseline',
        compliance: 'workstation-1/linux-baseline',
      },
      {
        name: 'CIS Red Hat Enterprise Linux 7 Benchmark Level 1 - Server',
        compliance: 'workstation-1/cis-rhel7-level1-server',
      },
    ]
  end
```
https://github.com/anthonygrees/audit_agr
```bash
$ git clone https://github.com/anthonygrees/audit_agr
$ cd audit_agr
$ berks init
$ berks install
$ berks upload
```
#### 6. Harden the RHEL instance with the Hardening Cookbook

```bash
$ git clone https://github.com/chef/cis-rhel
$ cd cis-rhel
$ berks init
$ berks install
$ berks upload
```
https://github.com/chef/cis-rhel

Now wait for the Audit Profile to run :)


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
