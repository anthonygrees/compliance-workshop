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


