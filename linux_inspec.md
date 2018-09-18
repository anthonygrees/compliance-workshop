## InSpec on Linux
InSpec is an open-source testing framework for infrastructure with a human- and machine-readable language for specifying compliance, security and policy requirements.

Don't have InSpec installed ?  Here you go - https://downloads.chef.io/inspec

### Step 1: Check your InSpec Version
```bash
$ inspec --version
```

### Step 2: Check for insecure protocol
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
