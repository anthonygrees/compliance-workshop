![Linux](/images/linux.png) ![inspec](/images/inspec.png)
## CentOS - Detect, Correct, Automate

The following instructions will be performed for the CentOS Detect, Correct and Automate Training Module


#### 1. Use Kitchen to stand up and Bootstrap an Instance
First, create a new CentOS VM using ```kitchen``` and bootstrap it to our Chef Infra Server.  It will be added to the:
- ```base_linux``` Policy
- ```development``` Policy Group

Run the following commands:

```bash
cd C:\chef-repo\cookbooks\bjc_linux_bootstrap

code .
```

In Visual Studio code, name you node in the ```default.rb``` on line 20 put your name in the variable and save the file
```bash
YOUR_NAME='PUT_YOUR_NAME_HERE'
```

Next, create your CentOS VM
```bash
kitchen converge
```

You will see you new node in Chef Automate
![Linux_Bootstrap](/images/linux_bootstrap.png)

#### 2. Lets take a look at the base linux policy

The policy is located at ``` C:\chef-repo\policyfiles\ap_policyfiles``` and you can view it with the following command:

```bash
cat  C:\chef-repo\policyfiles\ap_policyfiles\base_linux.rb
```

The policy adds the `chef-client` cookbook to the node.
```bash
# base_linux.rb - Describe how you want Chef to build your system.
#
# For more information on the Policyfile feature, visit
# https://github.com/opscode/chef-dk/blob/master/POLICYFILE_README.md
# A name that describes what the system you're building with Chef does.

name 'base_linux'

# Where to find external cookbooks:
default_source :chef_server, "https://#{ENV['AUTOMATE_HOSTNAME']}/organizations/#{ENV['CHEF_ORG']}"

# Specify a custom source for a cookbook:
cookbook 'chef-client', '~> 10.2.2'. ## Stage 1 - Base

# run_list: chef-client will run these recipes in the order specified.
run_list 'chef-client' ## Stage 1 - Base

```

You can see the ```chef-client``` cookbook here - https://github.com/anthonygrees/chef-client


#### 3. Add the CIS CentOS L1 InSpec Profile to you Node

To add the CIS InSpec profile, we need to modify the ```base_linux``` policy.

First, lets change the policy.
```bash
code C:\chef-repo\policyfiles\ap_policyfiles\base_linux.rb
```

The updated policy will look like this:
```bash
# base_linux.rb - Describe how you want Chef to build your system.
#
# For more information on the Policyfile feature, visit
# https://github.com/opscode/chef-dk/blob/master/POLICYFILE_README.md
# A name that describes what the system you're building with Chef does.

name 'base_linux'

# Where to find external cookbooks:
default_source :chef_server, "https://#{ENV['AUTOMATE_HOSTNAME']}/organizations/#{ENV['CHEF_ORG']}"

# Specify a custom source for a cookbook:
cookbook 'chef-client', '~> 10.2.2' ## Stage 1 - Base
cookbook 'audit_agr', '~> 2.2.4'  ## Stage 2 - Detect

# run_list: chef-client will run these recipes in the order specified.

run_list 'chef-client', 'audit_agr'  ## Stage 2
```

Now, lets update the policy
```bash
cd C:\chef-repo\policyfiles\ap_policyfiles

chef update base_linux.rb
```
![update_policy](/images/update_policy_stage2.png)

Finally, we need to push the policy to the Chef Server for the ```development``` Policy Group
```bash
chef push development base_linux.rb
```
![chef_push](/images/chef_push_stage2.png)


Let's check the Policy
```bash
chef show-policy base_linux
```

The CIS InSpec profile is executed by the ```audit_agr``` cookbook.  It uses a ```case``` statement to determine what your OS is and which CIS profile to run.

```bash
default['audit']['fetcher'] = 'chef-server'
default['audit']['reporter'] = 'chef-server-automate'
default['audit']['profiles'] =
  case node['platform']
  when 'centos'
    default['audit']['profiles'] = [
      {
        name: 'DevSec Linux Security Baseline',
        compliance: 'admin/linux-baseline',
      },
      {
        name: 'CIS CentOS Linux 7 Benchmark Level 1',
        compliance: 'admin/cis-centos7-level1',
      },
    ]
  when 'ubuntu'
    default['audit']['profiles'] = [
      {
        name: 'DevSec Linux Security Baseline',
        compliance: 'admin/linux-baseline',
      },
      {
        name: 'CIS Ubuntu Linux 16.04 LTS Benchmark Level 1 - Server',
        compliance: 'admin/cis-ubuntu16.04lts-level1-server',
      },
    ]
  when 'windows'
    default['audit']['profiles'] = [
      {
        name: 'DevSec Windows Security Baseline',
        compliance: 'admin/windows-baseline',
      },
      {
        name: 'CIS Microsoft Windows Server 2012 R2 Benchmark Level 1 - Member Server',
        compliance: 'admin/cis-windows2012r2-level1-memberserver',
      },
    ]
  when 'redhat'
    default['audit']['profiles'] = [
      {
        name: 'DevSec Linux Security Baseline',
        compliance: 'admin/linux-baseline',
      },
      {
        name: 'CIS Red Hat Enterprise Linux 7 Benchmark Level 1 - Server',
        compliance: 'admin/cis-rhel7-level1-server',
      },
    ]
  end
```


#### 4. Check InSpec report in Chef Automate

Your node is configured to run every 2 mins, but you can still manually force the ```chef-client``` to run and see the output if you impatient like me !!!

```bash
kitchen login

sudo chef-client
```

Your output will look like this:
```bash
PS C:\chef-repo\cookbooks\bjc_linux_bootstrap> kitchen login
Last login: Wed Apr  8 03:12:59 2020 from ip-172-31-54-28.us-west-2.compute.internal
[centos@ip-172-31-54-215 ~]$ sudo chef-client
Starting Chef Infra Client, version 15.9.17
Using policy 'base_linux' at revision 'f3875a79789039633ba88e9fe2121970cf4e639483f479c6554e82a2a063b783'
resolving cookbooks for run list: ["chef-client::default@10.2.2 (dc4a6af)", "audit_agr::default@2.2.4 (b18534e)"]
Synchronizing Cookbooks:
  - audit (9.1.0)
  - audit_agr (2.2.4)
  - chef-client (10.2.2)
  - logrotate (2.2.2)
  - windows (7.0.0)
  - cron (6.3.0)
Installing Cookbook Gems:
Compiling Cookbooks...
Recipe: audit::inspec
  * inspec_gem[inspec] action install (up to date)
  Converging 11 resources
Recipe: chef-client::systemd_service
  * directory[/var/run/chef] action create (up to date)
  * directory[/var/cache/chef] action create (up to date)
  * directory[/var/lib/chef] action create (up to date)
  * directory[/var/log/chef] action create (up to date)
  * directory[/etc/chef] action create (up to date)
  * template[/etc/sysconfig/chef-client] action create (up to date)
  * directory[/etc/systemd/system] action create (up to date)
  * systemd_unit[chef-client.service] action create (up to date)
  * service[chef-client] action enable (up to date)
  * service[chef-client] action start (up to date)
  * systemd_unit[chef-client.timer] action stop (up to date)
  * systemd_unit[chef-client.timer] action disable (up to date)
  * systemd_unit[chef-client.timer] action delete (up to date)
Recipe: audit::inspec
  * inspec_gem[inspec] action nothing (skipped due to action :nothing)
Running handlers complete
Chef Infra Client finished, 0/15 resources updated in 12 seconds
```

Find your node in the ```Compliance``` tab in Chef Automate and see the InSpec Report
![chef_push](/images/inspec_stage2.png)

#### 5. Remediate and Harden the CentOS  Node

To add the RHEL/CentOS Hardening Cookbook, we need to modify the ```base_linux``` policy again.

To change the policy.
```bash
code C:\chef-repo\policyfiles\ap_policyfiles\base_linux.rb
```

The updated policy will look like this:
```bash
# base_linux.rb - Describe how you want Chef to build your system.
#
# For more information on the Policyfile feature, visit
# https://github.com/opscode/chef-dk/blob/master/POLICYFILE_README.md
# A name that describes what the system you're building with Chef does.

name 'base_linux'

# Where to find external cookbooks:
default_source :chef_server, "https://#{ENV['AUTOMATE_HOSTNAME']}/organizations/#{ENV['CHEF_ORG']}"

# Specify a custom source for a cookbook:
cookbook 'chef-client', '~> 10.2.2' ## Stage 1 - Base
cookbook 'audit_agr', '~> 2.2.4' ## Stage 2 - Detect
cookbook 'cis-rhel', '~> 0.3.1'  ## Stage 3 - Correct

# run_list: chef-client will run these recipes in the order specified.

run_list 'chef-client', 'audit_agr', 'cis-rhel'  ## Stage 3

```

Now, lets update the policy
```bash
cd C:\chef-repo\policyfiles\ap_policyfiles

chef update base_linux.rb
```
![update_policy](/images/update_policy_stage2.png)

Finally, we need to push the policy to the Chef Server for the ```development``` Policy Group
```bash
chef push development base_linux.rb
```
![chef_push](/images/chef_push_stage2.png)


Let's check the Policy
```bash
chef show-policy base_linux
```

#### 6. Time Sync Error

Once the ```cis-rhel``` hardening cookbook run is complete, you will see many fixes and the InSpec CIS profile will have little errors

You may see an error with the ```time_sync``` recipe and an error in your ```kitchen``` run like this:
```bash
STDERR: Job for chronyd.service failed because a timeout was exceeded. See "systemctl status chronyd.service" and "journalctl -xe" for details.
```

You can see the stack trace in Chef Automate.
![stack trace](/images/stack_trace.png)

#### 7. Check InSpec report in Chef Automate

To see the result of your hardening, first, let's check the Chef cookbook run of the ```cis-rhel``` cookbook.  You can see each resource that was run to fix some of the CIS failues.

![chef_push](/images/stage3_chef_run.png)


Second, let's check the InSpec run and see how the Compliance looks now.

![chef_push](/images/stage3_inspec.png)

