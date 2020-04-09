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
PS C:\Users\Administrator> chef-client
Starting Chef Infra Client, version 15.9.17
Using policy 'base_windows' at revision 'faf33352bfff878a6fdad20bd4f36490b1de4a918af224e65af60b0717b0160a'
resolving cookbooks for run list: ["chef-client::default@10.2.2 (dc4a6af)", "audit_agr::default@2.2.4 (b18534e)"]
Synchronizing Cookbooks:
  - audit (9.1.0)
  - audit_agr (2.2.4)
  - chef-client (10.2.2)
  - cron (6.3.0)
  - logrotate (2.2.2)
  - windows (7.0.0)
Installing Cookbook Gems:
Compiling Cookbooks...
Recipe: audit::inspec
  * inspec_gem[inspec] action install (up to date)
  Converging 3 resources
Recipe: chef-client::task
  * windows_service[chef-client] action configure_startup (skipped due to only_if)
  * chef_client_scheduled_task[Chef Client] action add
    * directory[C:/chef/run] action create (up to date)
    * directory[C:/chef/cache] action create (up to date)
    * directory[C:/chef/backup] action create (up to date)
    * directory[C:/chef/log] action create (up to date)
    * directory[C:/chef] action create (up to date)
    * windows_task[chef-client] action create (up to date)
     (up to date)
Recipe: audit::inspec
  * inspec_gem[inspec] action nothing (skipped due to action :nothing)

Running handlers:
[2020-04-09T11:32:37+00:00] WARN: Use of a hash array for the node['audit']['profiles'] is deprecated. Please refer to t
he README and use a hash of hashes.
[2020-04-09T11:33:33+00:00] WARN: DEPRECATION: InSpec Attributes are being renamed to InSpec Inputs to avoid confusion w
ith Chef Attributes. Use :inputs in your kitchen.yml verifier config instead of :attributes.
[2020-04-09T11:36:12+00:00] WARN: Compliance report size is 1.17 MB.
  - Chef::Handler::AuditReport
Running handlers complete
Chef Infra Client finished, 0/10 resources updated in 05 minutes 18 seconds
PS C:\Users\Administrator>
```

Find your node in the ```Compliance``` tab in Chef Automate and see the InSpec Report
![chef_push](/images/win_stage2_inspec.png)


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
![update_policy](/images/win_stage3_update.png)

Finally, we need to push the policy to the Chef Server for the ```development``` Policy Group
```bash
chef push development base_windows.rb
```
![chef_push](/images/win_stage3_push.png)


Let's check the Policy
```bash
chef show-policy base_windows
```

#### 6. Check InSpec report in Chef Automate

Once the ```cis-windows-ms-2016``` hardening cookbook run is complete, you will see many fixes and the InSpec CIS profile will have little errors

First, let's check the Chef cookbook run of the ```cis-windows-ms-2016``` cookbook.  You can see each resource that was run to fix some of the CIS failues.

![chef_push](/images/win_stage3_chef.png)


Second, let's check the InSpec run and see how the Compliance looks now.

![chef_push](/images/win_stage3_inspec.png)


#### 7. Summary

By applying the Chef Hardening cookbook, we have gone from ```231``` Level 1 CIS failures to ```38```.  (We cannot harden more than that or we lose connectivity with the Cloud Instance.



