## InSpec on Windows

Create a file called audit.rb
```bash
code audit.rb
```

Now add the following to the audit.rb file

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
    it { should eq 'windows_server_2012_r2_standard_evaluation' }
  end

  describe os.release do
    it { should > '6.2' }
  end
end
```

To execute this using InSpec, run the following command

```bash
inspec exec audit.rb
```

Add the following example for looping through Windows KB and Hotfixes

```bash
## Looping example WannaCry Vulnerability Check
control 'WINDOWS HOTFIX - LOOP' do
  impact 0.8
  title 'This test checks that a numberof Windows Hotfixs are installed - Looping Example'

  hotfixes = %w{ KB4012598 KB4042895 KB4041693 KB4041691 KB4041690 KB4041689 KB4041681 KB4039396 KB4038803 KB4038801 KB4038799 KB4038797 KB4038792 KB4038783 KB4038782 KB4038781 KB4038777 KB4038774 KB4038220 KB4034681 KB4034670 KB4034668 KB4034665 KB4034664 KB4034663 KB4034661 KB4034660 KB4034659 KB4034658 KB4032695 KB4032693 KB4025344 KB4025341 KB4025340 KB4025339 KB4025338 KB4025336 KB4025335 KB4025334 KB4025332 KB4025331 KB4022724 KB4022723 KB4022722 KB4022721 KB4022720 KB4022719 KB4022718 KB4022717 KB4022168 KB4019474 KB4019473 KB4019472 KB4019265 KB4019264 KB4019263 KB4019218 KB4019217 KB4019216 KB4019215 KB4019214 KB4019213 KB4016637 KB4016636 KB4016635 KB4015554 KB4015553 KB4015552 KB4015551 KB4015550 KB4015549 KB4015221 KB4015219 KB4015217 KB4013429 KB4013198 KB4012606 KB4012220 KB4012219 KB4012218 KB4012217 KB4012216 KB4012215 KB4012214 KB4012213 KB4012212 }

  describe.one do
    hotfixes.each do |hotfix|
      describe windows_hotfix(hotfix) do
        it { should_not be_installed }
      end
    end
  end
end
```
