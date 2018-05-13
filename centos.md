## CentOS Instructions

The following instructions will be performed for the CentOS Compliance Training

#### 1. Manually Scan the CentOS node

Use the Chef Automate Scanner to scan your node
 * Create your credentials
 * Add your Node
 * Add your job

#### *** Automatically Scan and Harden ***

#### 1. Use Kitchen to stand up and Bootstrap an Instance
```bash
$ git clone https://github.com/anthonygrees/bjc_linux_bootstrap

$ cd bjc_linux_bootstrap

$ kitchen create
```

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

