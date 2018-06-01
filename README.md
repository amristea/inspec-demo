# Inspec Demo
This is the lab document for a demo on inspec used during my meetups.

## Introduction

In this lab we’re going to run through a few scenarios with inspec to learn how to use it, create new profiles, run remediation and ultimately integrate into a CI/CD pipeline.

## Lab 1. Set up your environment 

What you have before you is a vagrant box with the tools required to run through the labs. The method of delivery will be "show and tell" but I will allow time for everyone to follow what I’m doing and very much encourage you to run through it together with me.

Note: To be able to run these exercises you will need virtualbox and vagrant on your host machine. If you haven’t installed them, please do so now.

Software Download Links (for your laptop):
* https://www.vagrantup.com/downloads.html
* https://www.virtualbox.org/wiki/Downloads
* https://downloads.chef.io/chefdk (optional, it will be installed in the vagrant box)
* https://downloads.chef.io/inspec (optional, it will be installed in the vagrant box)
* https://git-scm.com/downloads (optional, it will be installed in the vagrant box)

Commands:

```
git clone https://github.com/amristea/inspec-demo.git   # or just download directly and unzip)
cd inspec-demo
vagrant up
vagrant ssh
```

What the box offers:
-	Oracle Linux 7.4 (RHEL/CentOS family)
-	Installed Chef Development Kit (includes Inspec)
-	Jenkins server
-	Chef solo set up to be able to run recipes
-	Dependencies such as git, vim, openjdk

## Lab 2. Create your first inspec test

```
vim test.rb
describe service(‘jenkins’) do
    it { should be_running }
    it { should be_enabled }
end

describe port(8080) do
    it { should be_listening }
end
```

Run your first test!
**inspec exec test.rb**

Check out more inspec resource definitions: https://www.inspec.io/docs/reference/resources 

## Lab 3. Run pre-defined profiles (locally or against targets)

Now that you have your first test running, let’s check an example profile.
```
git clone https://github.com/dev-sec/linux-baseline.git 
inspec exec linux-baseline
```

Note: You can run these profile locally, as it is the default, but also against targets like ssh or docker machines.

Example 1: inspec exec linux-baseline –t docker://<container>
Example 2: inspec exec https://github.com/dev-sec/cis-docker-benchmark.git

## Lab 4. Run a remediation cookbook

```
cd /cookbooks
git clone https://github.com/dev-sec/chef-os-hardening.git  os-hardening
git clone https://github.com/sous-chefs/sysctl.git 
git clone https://github.com/chef-cookbooks/ohai.git

chef-solo -c /etc/chef/solo.rb -o 'recipe[os-hardening]'
inspec exec linux-baseline
```

## Lab 5. Let’s fix these issues!

So, it seems the remediation cookbook did not fix everything after all, but that’s to be expected. Versions change, different hardware/software setups also influence the outcome. It’s up to you whether to fix or ignore those errors.

```
vim /etc/sysctl.conf
net.ipv4.conf.default.rp_filter = 1 net.ipv4.conf.default.rp_filter = 1
sysctl -p

vim /etc/audit/auditd.conf
Fix the entry max_log_file_action = keep_logs
systemctl start auditd

vim -R linux-baseline/controls/os_spec.rb
cat /proc/sys/kernel/random/entropy_avail
```

**inspec exec linux-baseline**


## Lab 6. Create a new profile

```
inspec init my-linux-baseline

vim my-linux-baseline/controls/newbaseline.rb
include_controls 'linux-baseline' do
  skip_control 'os-08'
end

vim my-linux-baseline/inspec.yml
name: my-linux-baseline
title: DevSec Linux Security Baseline Modified
maintainer: DevSec Hardening Framework Team
copyright: DevSec Hardening Framework Team
copyright_email: hello@dev-sec.io
license: Apache-2.0
summary: Test suite for best practice Linux OS hardening
version: 2.2.1
supports:
  - os-family: linux
depends:
  - name: linux-baseline
    url: https://github.com/dev-sec/linux-baseline/archive/master.tar.gz
```

**inspec exec my-linux-baseline**

All green (I hope!)

## Lab 7. Set up Jenkins

cat /var/lib/jenkins/secrets/initialAdminPassword

Log in to Jenkins, download JUNIT plugin and restart when prompted.

Create new job:
Add to Build step - Execute shell:
inspec exec /opt/my-linux-baseline --reporter junit:my-linux-baseline-results.xml

Add to Post Build Actions the JUnit config, pulling from xml (this is located in /var/lib/jenkins/workspace/<project>)

## Conclusion

As you can see there is a multitude of applications for inspec in the land of compliance and having participated in this meetup, it should be easier for you to implement solutions based on it.
Chef.io provides a commercial product called Automate that aggregate results from multiple chef server, has a wide range of compliance profiles and can run on demand scans via ssh. https://docs.chef.io/chef_automate.html 

## Further reading

*	https://www.inspec.io/docs/reference/resources
*	https://msdn.microsoft.com/en-us/magazine/mt808501.aspx
*	https://lollyrock.com/articles/inspec-terraform
*	http://lollyrock.com/articles/inspec-for-docker
