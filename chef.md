
## Day 1 - Chef Infra
![Chef Infra](/images/ChefInfra.png)

### Student Slides
The slides used in this workshop cen be found here in a PDF format.

| Nbr | Module | Link |
|---|---|---|
| 1 | Introduction to Chef | [Click Here](https://github.com/anthonygrees/compliance-workshop/blob/master/slides/01-Introduction.pdf)
| 2 | Chef Resources | [Click Here](https://github.com/anthonygrees/compliance-workshop/blob/master/slides/03-Resources.pdf)
| 3 | Creating a Chef Cookbook | [Click Here](https://github.com/anthonygrees/compliance-workshop/blob/master/slides/04-creating-web-server-cookbook.pdf)|
| 4 | Capture Details about the System | [Click Here](https://github.com/anthonygrees/compliance-workshop/blob/master/slides/05-details-about-the-system.pdf)|

[Click Here for the Slides](https://github.com/anthonygrees/compliance-workshop/tree/master/slides)

### Helpful Links

#### Linux
 - Module 4 - The ```.kitchen.yml``` for the ```Linux webserver cookbook``` [Click Here](https://github.com/anthonygrees/webserver_poc/blob/master/.kitchen.yml)

 - Module 4 - The ```default_test.rb``` test examples [Click Here](https://github.com/anthonygrees/webserver_poc/blob/master/test/integration/default/default_test.rb)

 #### Windows
 - Module 4 - The ```.kitchen.yml``` for the ```Windows webserver cookbook``` [Click Here](https://github.com/anthonygrees/myiis/blob/master/.kitchen.yml)

 - Module 4 - The ```default_test.rb``` test examples [Click Here](https://github.com/anthonygrees/myiis/blob/master/test/integration/default/default_test.rb)

#### Test-Kitchen Drivers for many Platforms
A Test-Kitchen ```driver``` is what supports configuring the compute instance that is used for isolated testing. This is typically a local hypervisor, hypervisor abstraction layer (Vagrant), or cloud service (EC2).

ChefDK / Chef Workstation include:
- [Vagrant](https://github.com/test-kitchen/kitchen-vagrant)
- [Kitchen Azurerm](https://github.com/test-kitchen/kitchen-azurerm)
- [Kitchen AWS EC2](https://github.com/test-kitchen/kitchen-ec2)
- [Kitchen HyperV](https://github.com/test-kitchen/kitchen-hyperv)
- [Kitchen Google Compute Engine](https://github.com/test-kitchen/kitchen-google)
- [Kitchen DigitalOcean](https://github.com/test-kitchen/kitchen-digitalocean)
- [Kitchen Doken for Docker Containers](https://github.com/someara/kitchen-dokken)

VMware Drivers:
- [kitchen-vra](https://github.com/chef-partners/kitchen-vra)
- [kitchen-vro](https://github.com/chef-partners/kitchen-vro)
- [kitchen-vcenter](https://github.com/chef/kitchen-vcenter)

Other Chef and Community Drivers:
- [Kitchen Terraform](https://github.com/newcontext-oss/kitchen-terraform)
- [OpenStack](https://github.com/test-kitchen/kitchen-openstack)
- [Docker](https://github.com/test-kitchen/kitchen-docker)


### Extra Learning and Videos

1. [Chef Software Open Source Communities](https://github.com/chef/chef-oss-practices) - This is a starting point for contributing to all of Chef's software and a wonderful spot for information on how to join in on the fun.
2. [DevOps Kung fu](https://github.com/chef/devops-kungfu) - This repository defines Chef Style DevOps Kung fu, which explains what DevOps is, and defines a style of practice that comes from the lived experience of many DevOps professionals.
3. [Chef Style DevOps Kungfu](https://www.youtube.com/watch?v=_DEToXsgrPc) - Adam Jacob Keynote - ChefConf 2015
4. [Eggs 365](https://www.youtube.com/watch?v=XD0vRW4G82U&t=2s) - ChefConf 2015: How is your Continuous Delivery Kung Fu?
5. [DevOps: No Horse Sh**](https://www.youtube.com/watch?v=0P0HD5pE-zU) - Original (language warning) - ChefConf 2014: Nathen Harvey knows DevOps, not farming. In this video, Nathen and his feathered and hooved friends explain how DevOps is (and isn't) like a closed-loop farm. 
