## InSpec Basics
InSpec is an open-source testing framework for infrastructure with a human and machine-readable language for specifying compliance, security and policy requirements.

Don't have InSpec installed?  Here you go - https://downloads.chef.io/inspec

Need the code? You will find it here - https://github.com/anthonygrees/compliance-windows

### Step 1: Check the platform you are runnning on
```bash
$ inspec detect
```
![InSpec Version](/images/inspec_detect.png)

### Step 2: List InSpec profiles available on Supermaket
```bash
$ inspec supermarket profiles
```
![InSpec Version](/images/inspec_super_profile.png)

### Step 3: Get all the details about an InSpec Profile
```bash
$ inspec supermarket info dev-sec/windows-baseline
```
![InSpec Version](/images/inspec_super_info.png)


### Step 4: Run the Supermarket InSpec Profile against your Windows machine
```bash
$ inspec supermarket exec dev-sec/windows-baseline
```
![InSpec Version](/images/inspec_super_exec.png)
