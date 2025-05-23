---
layout: postpage
title: Ansible - Learning Docs & References
categories: [ Ansible, Automation ]
image: "assets/images/2020/ansible-automation.png"
tags: [ aap, cloud, automation, containers, kubernetes, ansible automation platform, red hat ansible]
permalink: ansible
featured: false
hidden: false
titleshort: Ansible
---

## Ansible for Absolute Beginners

- [Ansible for Absolute Beginners - 30 Days of Ansible](techbeatly.com/ansible-course){:target="_blank"} (YouTube)
- [Ansible Real Life Scenarios](techbeatly.com/ansible-real-life){:target="_blank"} (YouTube)
- **[Automation with Ansible – All You Want to Learn](https://www.techbeatly.com/ansible/){:target="_blank"}**
- **[techbeatly.com](https://www.techbeatly.com/ansible/){:target="_blank"}**.
- [ANSIBLE LEARNFEST GUIDE](https://ansible-learnfest.github.io/)
  - Track 1 “Ansible Automation Platform Administrator”
  - Track 2 “Automation Content Developer”
- [Ansible - Frequently Asked Questions](ansible-faq)

**Table of Contents**

- [Ansible for Absolute Beginners](#ansible-for-absolute-beginners)
- [Ansible Automation Platform (Formerly Ansible Tower)](#ansible-automation-platform-formerly-ansible-tower)
  - [References](#references)
- [Ansible Playbook References](#ansible-playbook-references)
- [Ansible for Network Automation](#ansible-for-network-automation)
- [Ansible and Python Modules](#ansible-and-python-modules)
  - [Ansible and Python 3](#ansible-and-python-3)
- [Ansible Blog/Articles to Follow](#ansible-blogarticles-to-follow)
- [Ansible Interview Questions](#ansible-interview-questions)
- [Ansible Modules](#ansible-modules)
- [Ansible for IBM Power](#ansible-for-ibm-power)
- [Ansible for Arista Networks](#ansible-for-arista-networks)
- [Ansible for CIS Hardening/CIS Check](#ansible-for-cis-hardeningcis-check)
- [Utilities and References](#utilities-and-references)


## Ansible Automation Platform (Formerly Ansible Tower)



### References

- [Explore Ansible Automation Platform](https://www.ansible.com/products/ansible-training) - Hands on Lab/Practices via **instruqt**.
- [Red Hat Ansible Automation Platform installation guide - 2.](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.1/html-single/red_hat_ansible_automation_platform_installation_guide/index)
- [Red Hat Ansible Automation Platform installation guide - 2.0](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.0-ea/html-single/red_hat_ansible_automation_platform_installation_guide/index?lb_target=production)(2.0-EA)
- [Ansible Automation Platform 2.0 Early Access Homepage](https://access.redhat.com/articles/6145072)
- [What’s New in Ansible Automation Controller 4.0](https://access.redhat.com/articles/6184841)
- [What Happens to an Ansible Tower Installation After the Subscription/License has Expired?](https://access.redhat.com/solutions/3389421)



- [Red Hat Ansible Automation Platform Life Cycle](https://access.redhat.com/support/policy/updates/ansible-automation-platform)
- [Red Hat Ansible Tower Life Cycle](https://access.redhat.com/support/policy/updates/ansible-tower) - Details about Ansible Tower and Ansible Automation Platform Life Cycle, End of Life, End of Support etc.
- [Ansible Tower Installation and Reference Guide](https://docs.ansible.com/ansible-tower/latest/pdf/AnsibleTowerInstallationandReferenceGuide.pdf)
- [Preparing for the Ansible Automation Platform Installation](https://docs.ansible.com/ansible-tower/latest/html/quickinstall/prepare.html)
- [Ansible Automation Platform Quick Installation Guide v3.8.0](https://docs.ansible.com/ansible-tower/latest/html/quickinstall/index.html)
- [Installing Ansible Automation Platform](https://docs.ansible.com/ansible-tower/latest/html/quickinstall/install_script.html)
- [Ansible Tower Administration Guide](https://docs.ansible.com/ansible-tower/3.8.3/pdf/AnsibleTowerAdministrationGuide.pdf) (3.8.3) *[PDF]*
- [Which Ports and Services Should I Monitor for Ansible Tower?](https://access.redhat.com/solutions/3720511)
- [Ansible Automation Platform Installation and Reference Guide v3.8.0](https://docs.ansible.com/ansible-tower/latest/html/installandreference/index.html)
- [Tower - Previous versions](https://docs.ansible.com/automation-tower-prior-versions.html)
- [Guidelines, Troubleshooting, and Recommended Configurations for Ansible Tower](https://access.redhat.com/articles/3344101)

## Ansible Playbook References

- [Ansible Examples](https://github.com/ginigangadharan/ansible-examples){:target="_blank"}
(*Forked from [@ansible](https://github.com/ansible/ansible-examples)*)
- [Ansible for DevOps](https://github.com/ginigangadharan/ansible-for-devops){:target="_blank"}
(*Forked from [geerlingguy](https://github.com/geerlingguy/ansible-for-devops)*)
- [Ansible NXOS Samples](https://github.com/ginigangadharan/Ansible-NXOS){:target="_blank"} (Network Automation)


## Ansible for Network Automation

- [Network Device Authentication with Ansible 2.3](https://www.ansible.com/blog/network-device-authentication-with-ansible-2-3){:target="_blank"}


## Ansible and Python Modules

- [How to set up and use Python virtual environments for Ansible](https://www.redhat.com/sysadmin/python-venv-ansible)
- [Running in a virtualenv](https://docs.ansible.com/ansible/latest/reference_appendices/faq.html#running-in-a-virtualenv)
- [How to install pip on Red Hat Enterprise Linux?](https://access.redhat.com/solutions/1519803)


```shell
pip install --target=/your/pyinstalldir loremipsum
pip install fortiosapi --target=/var/lib/awx/venv/ansible/lib/python2.7/site-packages

pip list --target=/var/lib/awx/venv/ansible/lib/python2.7/site-packages
```

```shell
$ sudo subscription-manager repos --enable rhel-server-rhscl-7-rpms
$ sudo yum install python27-python-pip
```

Switch to a normal user and check the pip

```shell
$ scl enable python27 bash
$ which pip
$ pip -V
```

**For Existing Tower**

```
# source /var/lib/awx/venv/ansible/bin/activate
# umask 0022
# pip install --upgrade pywinrm
# deactivate
```

### Ansible and Python 3

- [Python 3 Support](https://docs.ansible.com/ansible/latest/reference_appendices/python_3_support.html)
- [How Can I Use Virtual Environment with Python3 on RHEL 7 for Ansible Tower?](https://access.redhat.com/solutions/4371201)
- [How do I use Python 3 in Ansible Tower?](https://access.redhat.com/solutions/3569661)
- [Ansible and Python 3](https://docs.ansible.com/ansible/latest/dev_guide/developing_python_3.html#ansible-and-python-3) (docs.ansible.com/)

Ansible 2.5 and above work with Python 3.

```shell
[ansible@vm-ans-02 ~]$ ansible --version |grep python
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  python version = 2.7.5 (default, Sep 26 2019, 13:23:47) [GCC 4.8.5 20150623 (Red Hat 4.8.5-39)]
```

## Ansible Blog/Articles to Follow

- [Ansible Blog](https://www.ansible.com/blog){:target="_blank"}
- [Ansible & Cisco](https://blogs.cisco.com/tag/ansible){:target="_blank"}
- [ansiblejunky.com](https://www.ansiblejunky.com/){:target="_blank"}
- [ansible linting - Abhijeet Kamble](https://medium.com/faun/linting-your-ansible-playbooks-and-make-a-continuous-integration-ci-solution-bcf8b4ea4c03){:target="_blank"}

## Ansible Interview Questions

- [Question Bank 1](https://career.guru99.com/ansible-interview-questions-answers/){:target="_blank"}
- [Question Bank 2](https://www.edureka.co/blog/interview-questions/ansible-interview-questions/){:target="_blank"}
- [Question Bank 3](https://mindmajix.com/ansible-interview-questions){:target="_blank"}

## Ansible Modules

- [Foreman Ansible Modules](https://github.com/theforeman/foreman-ansible-modules)


## Ansible for IBM Power

- [IBM Power Systems AIX collection](https://github.com/IBM/ansible-power-aix)

## Ansible for Arista Networks

- [Ansible Modules for Arista CloudVision Platform](https://github.com/aristanetworks/ansible-cvp)

## Ansible for CIS Hardening/CIS Check
- [DevSec Hardening Framework](https://github.com/dev-sec) / [devops + security - Server Hardening Automation](https://dev-sec.io/)


## Utilities and References

- [Janus](https://github.com/ansible-middleware/janus) - The project ansible-middleware/janus holds a set of playbooks used to transform upstream collection (such as ansible-middleware/wildfly) to downstream collection (redhat/eap) using [FQCN migration|https://github.com/ansible-collections/community.fqcn_migration]. Please refers to this project for more information.
- [community.fqcn_migration](https://github.com/ansible-collections/community.fqcn_migration) - This project, called fqcn_migration, is a set of Ansible roles designed to rename a collection and even changed its namespace.
- [How to Stop/Start/Restart & Disable/Enable Containerized Ansible Automation Platform 2.5 using systemd?](https://access.redhat.com/solutions/7090499)