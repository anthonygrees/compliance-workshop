## InSpec on Linux
InSpec is an open-source testing framework for infrastructure with a human- and machine-readable language for specifying compliance, security and policy requirements.

Don't have InSpec installed ?  Here you go - https://downloads.chef.io/inspec

### Step 1: Check your InSpec Version
Run the following command to check your InSpec version.
```bash
$ inspec --version
```

### Step 2: Determine your platform
Use the InSpec ```detect``` command to check the platform you are running on.  Replace the ```999.999.999.999``` with the IP address given to you by the instructor.
```bash
inspec detect -t ssh://ubuntu@999.999.999.999 -i C:\Users\chef\.ssh\id_rsa
```

### Step 4: Let's look at InSpec Shell
It's a pry based Read–Eval–Print Loop that can be used to quickly run InSpec controls and tests without having to write it to a file. Its functionality is similar to chef shell.

We'll start by connecting to the shell with our key and transport information, and then play around in the shell for a bit and write our first test.
```bash
$ inspec help shell
$ inspec detect -t ssh://ubuntu@999.999.999.999 -i C:\Users\chef\.ssh\id_rsa

inspec> help
inspec> help resources
inspec> help sshd_config
inspec> sshd_config.params
inspec> sshd_config.port
inspec> sshd_config.Protocol
inspec> help matchers
```
Ok.  Now try writing a control based test.
```ruby
inspec> describe sshd_config do
inspec> its('Protocol') { should eq '2' }
inspec> end
```
Type ```exit``` to leave the shell.
```ruby
inspec> exit
```

### Step 5: Check for insecure protocol
Now, create a new InSpec profile
```bash
## Create a New InSpec Profile
$ inspec init profile <your_profile_name>

## Change Directory into your new Profile
$ cd <your_profile_name>

## Write your Controls in the example.rb
$ code controls\example.rb
```
Add the following code to your ```controls\example.rb```
```ruby
# Disallow insecure protocols by testing

describe package('telnetd') do
  it { should_not be_installed }
end
```

InSpec makes it easy to run your tests wherever you need.
```bash
# run test on remote host on SSH
inspec exec example.rb -t ssh://ubuntu@999.999.999.999 -i C:\Users\chef\.ssh\id_rsa
```
