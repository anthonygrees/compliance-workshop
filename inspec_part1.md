## Day 2 - Chef InSpec
![Chef InSpec](/images/ChefInSpec.png)

### Part 1: InSpec from the Command Line (CLI)
![ChefAutomate](/images/3version.png)

#### About InSpec
InSpec is an open-source testing framework by Chef that enables you to specify compliance, security, and other policy requirements.

Built on the Ruby programming language, InSpec tests are meant to be human-readable. If you're familiar with testing frameworks such as RSpec and Serverspec, InSpec code will look familiar to you.

InSpec can run on Windows and many Linux distributions. Although you can use InSpec to scan almost any system, in this module you'll scan Docker containers running Linux to get a sense of how InSpec works. The concepts apply equally to Windows.

#### Detect and Correct
You can think of meeting your compliance and security goals as a two-phase process. We often refer to this process as detect and correct.

The first phase, detect, is knowing where your systems are potentially out of compliance or have potential security vulnerabilities. 55% of organizations do compliance assessments inconsistently or not at all.

This diagram shows the detect process. The systems on the left are before the detect process, which are in an unknown state. The systems on the right are after the detect process.

![DCA](/images/dca1.png)

Here you see several potential compliance failures. But the first step is to identify where the problems are so that you can accurately assess risk and prioritize remediation actions.

The second phase, correct, involves remediating the compliance failures you've identified in the first phase. 58% of organizations need days or more to remediate issues.

This diagram shows the correct process. The systems on the left are the result of the detect process. The systems on the right are after the correct process.

![DCA](/images/dca2.png)

After the correct process completes, you can run the detect process a second time to verify that each of your systems meets your policy requirements. Although remediation can happen manually, you can use Chef or some other continuous automation framework to correct compliance failures for you. This module focuses on the detect phase. We'll point you to resources at the end to help you explore how Chef can help you correct potential compliance failures.

Think about your current compliance process. For many teams, it can take weeks to perform a full detect and correct pass using manual processes. With InSpec, you can generate reports that prove your systems are in compliance in much less time.


| Item | Module  | Link  |
| :---: |:-------------| :-----|
| 0  | **Basics** - Lets look at the InSpec Basics| [Click Here](https://github.com/anthonygrees/compliance-workshop/blob/master/basics_inspec.md)
| 1a  | **Windows** - Learn InSpec on the Windows BJC workstation CLI | [Click Here](https://github.com/anthonygrees/compliance-workshop/blob/master/windows_inspec.md) |
| 1b  | **Linux** - Learn InSpec from BJC against Linux node CLI | [Click Here](https://github.com/anthonygrees/compliance-workshop/blob/master/linux_inspec.md) |

### Helpful Links

Linux Bootstrap
```bash
git clone https://github.com/anthonygrees/bjc_linux_bootstrap
```

Windows Bootstrap
```bash
git clone https://github.com/anthonygrees/bjc_windows_bootstrap
```


