## InSpec on Windows

Don't have InSpec installed ?  Here you go - https://downloads.chef.io/inspec

### Step 1: Check your InSpec Version
```bash
$ inspec --version
```
![InSpec Version](/images/1inspecversion.png)

### Step 2: Create a new InSpec profile
Create a new InSpec Profile run the following:
```bash
## Create a New InSpec Profile
$ inspec init profile <your_profile_name>

## Change Directory into your new Profile
$ cd <your_profile_name>

## Write your Controls in the example.rb
$ code controls\example.rb
```
![Create Profile](/images/2createprofile.png)

### Step 3: Create a simple Windows Version check
Now add the following to the example.rb file

```bash
# Windows Versions - Check for Min of Win 2012
# Win2016 - NT 10.0 | Win 2012 R2 - NT 6.3 | Win 2012 - NT 6.2
#

control 'WINDOWS VERSION' do
  impact 0.8
  title 'This test checks for a minimum Windows version of 2012 - NT 6.2.0'

  describe os.family do
    it { should eq 'windows' }
  end

  describe os.name do
    it { should eq 'windows_server_2016_datacenter' }
  end

  describe os.release do
    it { should > '10.0' }
  end
end
```

To execute this using InSpec, run the following command

```bash
$ inspec exec .
```
![Windows Version](/images/3version.png)

### Step 4: Check Windows Hot Fixes
Add the following example for looping through Windows KB and Hotfixes

```bash
## Looping example WannaCry Vulnerability Check
control 'WINDOWS HOTFIX - LOOP' do
  impact 0.8
  title 'This test checks that a numberof Windows Hotfixs are installed - Looping Example'

  hotfixes = %w{ KB4012598 KB4042895 KB4041693 }

  describe.one do
    hotfixes.each do |hotfix|
      describe windows_hotfix(hotfix) do
        it { should_not be_installed }
      end
    end
  end
end
```

To execute this using InSpec, run the following command

```bash
$ inspec exec .
```
![Windows Version](/images/4kb.png)

# Step 5: Check if a package is installed
Is a particular package installed ?

```bash
control 'PACKAGE INSTALLED _ TELNET and CHROME' do
  impact 0.8
  title 'This test checks that a package is installed'

  describe package('telnetd') do
    it { should_not be_installed }
  end

  describe package('Google Chrome') do
    it { should be_installed}
  end
end
```

To execute this using InSpec, run the following command

```bash
$ inspec exec .
```
![Windows Version](/images/5package.png)

### Step 6: Check if a Windows Service installed and Enabled
Is a particular Service installed ?

```bash
## service example
control 'SERVICE INSTALLED' do
  impact 0.8
  title 'This test checks the service is installed'

  describe service('DHCP Client') do
    it { should be_installed }
    it { should be_running }
  end
end
```

To execute this using InSpec, run the following command

```bash
$ inspec exec audit.rb
```
![Windows Version](/images/6service.png)

### Step 7: Checking HTTP and HTTPS
Use the InSpec Port resource to test HTTP and HTTPS

```bash
control 'HTTP AND HTTPS' do
  impact 0.8
  title 'This test checks the HTTP and HTTPS protocols'
  
  # Test HTTP port 80, is not listening and no protocol TCP, ICMP, UDP
  describe port(80) do
      it { should_not be_listening }
      its('protocols') { should_not cmp 'tcp6' }
      its('protocols') { should_not include('icmp') }
      its('protocols') { should_not include('tcp') }
      its('protocols') { should_not include('udp') }
      its('protocols') { should_not include('udp6') }
      its('addresses') { should_not include '0.0.0.0' }
  end

  # Test HTTPS port 443, listening with TCP and UDP
  describe port(443) do
      it { should be_listening }
      its('protocols') { should_not cmp 'tcp6' }
      its('protocols') { should_not include('icmp') }
      its('protocols') { should include('tcp') }
      its('protocols') { should include('udp') }
      its('protocols') { should_not include('udp6') }
      its('addresses') { should include '0.0.0.0' }
  end
end
```

To execute this using InSpec, run the following command

```bash
$ inspec exec audit.rb
```
![Windows Version](/images/7http.png)

### Step 8: Check Windows Tasks
Use the http InSpec audit resource to test an http endpoint.

```bash
control 'WINDOWS TASKS' do
  impact 0.8
  title 'This test checks the Windows Tasks'
  
  describe windows_task('\Microsoft\Windows\AppID\PolicyConverter') do
    it { should be_disabled }
  end

  describe windows_task('\Microsoft\Windows\AppID\PolicyConverter') do
    its('logon_mode') { should eq 'Interactive/Background' }
    its('last_result') { should eq '267011' }
    its('task_to_run') { should cmp '%Windir%\system32\appidpolicyconverter.exe' }
    its('run_as_user') { should eq 'SYSTEM' }
  end

  describe windows_task('\Microsoft\Windows\Defrag\ScheduledDefrag') do
    it { should exist }
  end
end
```

To execute this using InSpec, run the following command

```bash
$ inspec exec audit.rb
```
![Windows Version](/images/8task.png)

### Step 9: CIS Example Profile
Compliance of the OS settings on the windows client
- Check and verify Group Policy Settings (GPO) with reference to CIS Windows 10 1703 benchmark is begin applied
- When new monthly windows security patch is applied to the current image, to check if the new patches is successfully applied. Where possible, show the status BEFORE and AFTER the patch for comparison and highlight any errors etc..


```bash
control "xccdf_org.cisecurity.benchmarks_rule_18.8.18.3_L1_Ensure_Configure_registry_policy_processing_Process_even_if_the_Group_Policy_objects_have_not_changed_is_set_to_Enabled_TRUE" do
  title "(L1) Ensure 'Configure registry policy processing: Process even if the Group Policy objects have not changed' is set to 'Enabled: TRUE'"
  desc  "The \"Process even if the Group Policy objects have not changed\" option updates and reapplies policies even if the policies have not changed."
  impact 1.0
  describe registry_key("HKEY_LOCAL_MACHINE\\Software\\Policies\\Microsoft\\Windows\\Group Policy\\{35378EAC-683F-11D2-A89A-00C04FBBCFA2}") do
    it { should_not have_property "NoGPOListChanges" }
  end
  describe registry_key("HKEY_LOCAL_MACHINE\\Software\\Policies\\Microsoft\\Windows\\Group Policy\\{35378EAC-683F-11D2-A89A-00C04FBBCFA2}") do
    its("NoGPOListChanges") { should_not cmp == 0 }
  end
end

control "xccdf_org.cisecurity.benchmarks_rule_18.8.18.4_L1_Ensure_Turn_off_background_refresh_of_Group_Policy_is_set_to_Disabled" do
  title "(L1) Ensure 'Turn off background refresh of Group Policy' is set to 'Disabled'"
  desc  "This policy setting prevents Group Policy from being updated while the computer is in use."
  impact 1.0
  describe registry_key("HKEY_LOCAL_MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Policies\\System") do
    it { should_not have_property "DisableBkGndGroupPolicy" }
  end
end
```

To execute this using InSpec, run the following command

```bash
$ inspec exec audit.rb
```
![Windows Version](/images/9cis.png)

## Step 10: Report in Chef Automate
Create a file called ```inspec.json``` in <your_profile_name> directory and add the following:

Note: Remember to update the ```json``` and put your name in ```"node_name" : "<YOUR_NAME_HERE>"```

```json
{
    "reporter" : {
        "automate" : {
            "stdout" : "false",
            "url" : "https://automate.automate-demo.com/data-collector/v0",
            "token" : "Ap37TgMRThzpEDjHiBXt2sJ5Ra4=",
            "insecure" : true,
            "node_name" : "<YOUR_NAME_HERE>",
            "environment" : "dev"
        }
    }
}
```

To execute this using InSpec and report to A2 run the following command

```bash
$  inspec exec . --json-config inspec.json
```
![Windows Version](/images/10reporter.png)

