## CentOS - Detect, Correct, Automate

The following instructions will be performed for the CentOS Detec, Correct, Automate Training Module


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
