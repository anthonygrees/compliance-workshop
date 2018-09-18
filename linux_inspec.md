InSpec is an open-source testing framework for infrastructure with a human- and machine-readable language for specifying compliance, security and policy requirements.

```bash
# Disallow insecure protocols by testing

describe package('telnetd') do
  it { should_not be_installed }
end
```

InSpec makes it easy to run your tests wherever you need.
```bash
inspec exec test.rb -t ssh://ubuntu@999.999.999.999 -i C:\Users\chef\.ssh\id_rsa
```
