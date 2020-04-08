## CentOS - Detect, Correct, Automate

The following instructions will be performed for the CentOS Detect, Correct and Automate Training Module


#### 1. Use Kitchen to stand up and Bootstrap an Instance
First, create a new CentOS VM using ```kitchen``` and bootstrap it to our Chef Infra Server.  It will be added to the:
- ```linux_base``` Policy
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

#### 2. Lets take a look at the linux_base policy

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
