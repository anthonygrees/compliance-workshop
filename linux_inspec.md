## InSpec on Linux
InSpec is an open-source testing framework for infrastructure with a human- and machine-readable language for specifying compliance, security and policy requirements.

Don't have InSpec installed ?  Here you go - https://downloads.chef.io/inspec

### Step 1: Check your InSpec Version
```bash
$ inspec --version
```

### Step 2: Determine your platform
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

```bash
inspec> describe sshd_config do
inspec> its('Protocol') { should eq '2' }
inspec> end
```

### Step 5: Check for insecure protocol
```bash
# Disallow insecure protocols by testing

describe package('telnetd') do
  it { should_not be_installed }
end
```

InSpec makes it easy to run your tests wherever you need.
```bash
# run test on remote host on SSH
inspec exec test.rb -t ssh://ubuntu@999.999.999.999 -i C:\Users\chef\.ssh\id_rsa
```
