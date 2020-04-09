## Windows - Detect, Correct, Automate

The following instructions will be performed for the Windows Detect, Correct and Automate Training Module


#### 1. Use Kitchen to stand up and Bootstrap an Instance
First, create a new Windows VM using ```kitchen``` and bootstrap it to our Chef Infra Server.  It will be added to the:
- ```base_windows``` Policy
- ```development``` Policy Group

Run the following commands:

```bash
cd C:\chef-repo\cookbooks\bjc_windows_bootstrap

code .
```

In Visual Studio code, name you node in the ```default.rb``` on line 12 put your name in the variable and save the file
```bash
Win-Anthony-{0}'
```

Next, create your Windows 2016 VM
```bash
kitchen converge
```

You will see your new node in Chef Automate
![Win Bootstrap](/images/win_bootstrap.png)

#### 2. Lets take a look at the base_windows policy

The policy is located at ``` C:\chef-repo\policyfiles\ap_policyfiles``` and you can view it with the following command:

```bash
cat  C:\chef-repo\policyfiles\ap_policyfiles\base_windows.rb
```

The policy adds the `chef-client` cookbook to the node.
```bash
# base_windows.rb - Describe how you want Chef to build your system.
#
# For more information on the Policyfile feature, visit
# https://github.com/opscode/chef-dk/blob/master/POLICYFILE_README.md
# A name that describes what the system you're building with Chef does.

name 'base_windows'

# Where to find external cookbooks:
default_source :chef_server, "https://#{ENV['AUTOMATE_HOSTNAME']}/organizations/#{ENV['CHEF_ORG']}"

# Specify a custom source for a cookbook:
cookbook 'chef-client', '~> 10.2.2'  ## Stage 1 - Base

# run_list: chef-client will run these recipes in the order specified.
run_list 'chef-client' ## Stage 1 - Base

```

You can see the ```chef-client``` cookbook here - https://github.com/anthonygrees/chef-client


#### 3. Add the CIS Windows 2016 L1 InSpec Profile to you Node

To add the CIS InSpec profile, we need to modify the ```base_windows``` policy.

First, lets change the policy.
```bash
code C:\chef-repo\policyfiles\ap_policyfiles\base_windows.rb
```

The updated policy will look like this:
```bash
# base_windows.rb - Describe how you want Chef to build your system.
#
# For more information on the Policyfile feature, visit
# https://github.com/opscode/chef-dk/blob/master/POLICYFILE_README.md
# A name that describes what the system you're building with Chef does.

name 'base_windows'

# Where to find external cookbooks:
default_source :chef_server, "https://#{ENV['AUTOMATE_HOSTNAME']}/organizations/#{ENV['CHEF_ORG']}"

# Specify a custom source for a cookbook:
cookbook 'chef-client', '~> 10.2.2'  ## Stage 1 - Base
cookbook 'audit_agr', '~> 2.2.4'   ## Stage 2 - Detect

# run_list: chef-client will run these recipes in the order specified.

run_list 'chef-client', 'audit_agr'  ## Stage 2 - Detect

```

Now, lets update the policy
```bash
cd C:\chef-repo\policyfiles\ap_policyfiles

chef update base_windows.rb
```
![update_policy](/images/win_stage2_update.png)

Finally, we need to push the policy to the Chef Server for the ```development``` Policy Group
```bash
chef push development base_windows.rb
```
![chef_push](/images/win_stage2_push.png)


Let's check the Policy
```bash
chef show-policy base_windows
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
        name: 'CIS Microsoft Windows Server 2016 Benchmark Level 1 - Member Server',
        compliance: 'admin/cis-windows2016-level1-memberserver',
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

**** Get new output  ****

```

Find your node in the ```Compliance``` tab in Chef Automate and see the InSpec Report
![chef_push](/images/inspec_stage2.png)

#### 5. Remediate and Harden the CentOS  Node

To add the Windows 2016 Hardening Cookbook, we need to modify the ```base_windows``` policy again.

To change the policy.
```bash
code C:\chef-repo\policyfiles\ap_policyfiles\base_windows.rb
```

The updated policy will look like this:
```bash
# base_windows.rb - Describe how you want Chef to build your system.
#
# For more information on the Policyfile feature, visit
# https://github.com/opscode/chef-dk/blob/master/POLICYFILE_README.md
# A name that describes what the system you're building with Chef does.

name 'base_windows'

# Where to find external cookbooks:
default_source :chef_server, "https://#{ENV['AUTOMATE_HOSTNAME']}/organizations/#{ENV['CHEF_ORG']}"

# Specify a custom source for a cookbook:
cookbook 'chef-client', '~> 10.2.2'  ## Stage 1 - Base
cookbook 'audit_agr', '~> 2.2.4'   ## Stage 2 - Detect
cookbook 'cis-windows-ms-2016', '~> 0.3.0'  ## Stage 3 - Correct

# run_list: chef-client will run these recipes in the order specified.

run_list 'chef-client', 'audit_agr', 'cis-windows-ms-2016'  ## Stage 3

```

Now, lets update the policy
```bash
cd C:\chef-repo\policyfiles\ap_policyfiles

chef update base_windows.rb
```
![update_policy](/images/update_policy_stage2.png)

Finally, we need to push the policy to the Chef Server for the ```development``` Policy Group
```bash
chef push development base_windows.rb
```
![chef_push](/images/chef_push_stage2.png)


Let's check the Policy
```bash
chef show-policy base_windows
```

#### 6. Check InSpec report in Chef Automate

Once the ```cis-windows-ms-2016``` hardening cookbook run is complete, you will see many fixes and the InSpec CIS profile will have little errors

First, let's check the Chef cookbook run of the ```cis-windows-ms-2016``` cookbook.  You can see each resource that was run to fix some of the CIS failues.

![chef_push](/images/stage3_chef_run.png)


Second, let's check the InSpec run and see how the Compliance looks now.

![chef_push](/images/stage3_inspec.png)

