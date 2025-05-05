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
  - [Ansible Automation Platform - Containerized Setup](#ansible-automation-platform---containerized-setup)
  - [Removing instance from cluster](#removing-instance-from-cluster)
  - [Installing Ansible Automation Platfom](#installing-ansible-automation-platfom)
    - [Enable RHEL and AAP Subscriptions](#enable-rhel-and-aap-subscriptions)
    - [Prepare for Ansible Automation Platform Installation](#prepare-for-ansible-automation-platform-installation)
    - [Get Registry Credential](#get-registry-credential)
    - [Configure Ansible Automation Platform Installation Inventory](#configure-ansible-automation-platform-installation-inventory)
    - [Run Setup Script](#run-setup-script)
  - [Disaster Recovery for the Ansible Automation Platform](#disaster-recovery-for-the-ansible-automation-platform)
  - [Ansible Tower HA and DR](#ansible-tower-ha-and-dr)
    - [Verify Streaming Replication\*\*](#verify-streaming-replication)
    - [Ansible Tower HA Failover](#ansible-tower-ha-failover)
    - [Database tips](#database-tips)
  - [Ansible Tower with database SSL connection enabled](#ansible-tower-with-database-ssl-connection-enabled)
  - [Troubleshooting](#troubleshooting)
  - [References](#references)
- [Ansible on Clouds](#ansible-on-clouds)
- [Red Hat Ansible Automation Platform Hardening Guide](#red-hat-ansible-automation-platform-hardening-guide)
- [Migrating, Updating and Upgrading AAP](#migrating-updating-and-upgrading-aap)
- [Ansible Automation Hub](#ansible-automation-hub)
- [Ansible Playbook References](#ansible-playbook-references)
- [Tools for Ansible](#tools-for-ansible)
- [Ansible for Network Automation](#ansible-for-network-automation)
- [Ansible and Python Modules](#ansible-and-python-modules)
  - [Ansible and Python 3](#ansible-and-python-3)
- [Ansible Blog/Articles to Follow](#ansible-blogarticles-to-follow)
- [Ansible Interview Questions](#ansible-interview-questions)
- [Ansible Modules](#ansible-modules)
- [Other References](#other-references)
- [Ansible for IBM Power](#ansible-for-ibm-power)
- [Ansible for Arista Networks](#ansible-for-arista-networks)
- [Ansible for CIS Hardening/CIS Check](#ansible-for-cis-hardeningcis-check)
- [AD/LDAP Integration - AAP 2.5](#adldap-integration---aap-25)
  - [Authentication mapping](#authentication-mapping)
- [AD/LDAP Integration - AAP 2.4](#adldap-integration---aap-24)
- [LDAP Troubleshooting and testing](#ldap-troubleshooting-and-testing)
- [Best practices](#best-practices)
- [Utilities and References](#utilities-and-references)


## Ansible Automation Platform (Formerly Ansible Tower)

### Ansible Automation Platform - Containerized Setup

```shell
$ export ANSIBLE_COLLECTIONS_PATH=/home/devops/ansible-automation-platform-containerized-setup-bundle-2.4-1-x86_64/collections/

$ ansible-playbook -i inventory ansible.containerized_installer.install
```

To uninstall a containerized deployment, execute the uninstall.yml playbook.


```shell
$ ansible-playbook -i inventory ansible.containerized_installer.uninstall
```

This will stop all systemd units and containers and then delete all resources used by the containerized installer such as:

- config and data directories/files
- systemd unit files
- podman containers and images
- RPM packages

References:
 - [Containerized Ansible Automation Platform Installation Guide](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.4/html-single/containerized_ansible_automation_platform_installation_guide/index)


### Removing instance from cluster

```shell
bash-4.4$ awx-manage deprovision_instance --hostname aap-rhel-92-2.lab.local
Instance Removed
Successfully deprovisioned aap-rhel-92-2.lab.local
(changed: True)
```

### Installing Ansible Automation Platfom

*Note : The Ansible Automation Platform installer only supports Red Hat Enterprise Linux and CentOS.*

#### Enable RHEL and AAP Subscriptions

Make sure you subscribed to Red Hat and added RHEL Subscription.

```shell
# subscription-manager register
# subscription-manager attach --pool=<pool_id of RHEL subscription>
# subscription-manager list --consumed
```

Now, search for Ansible Automation Platform subscription and attach the pool ID.

```shell
# subscription-manager list --available --all | grep "Ansible Automation Platform" -B 3 -A 6
# subscription-manager attach --pool=<pool_id>
# subscription-manager list --consumed
```

Update required repo for AAP 2.1

```shell
# subscription-manager repos \
  --disable=ansible-automation-platform-2.0-early-access-for-rhel-8-x86_64-rpms

# subscription-manager repos \
  --enable=ansible-automation-platform-2.1-for-rhel-8-x86_64-rpms
```

#### Prepare for Ansible Automation Platform Installation

1. Download the latest Ansible Automation Platform Installation Program

Download Options:

- [access.redhat.com/downloads](https://access.redhat.com/downloads/content/480)(Note, you must have a Red Hat customer account to access the downloads)
- [developers.redhat.com](https://developers.redhat.com/products/ansible/download?source=sso)
- Old versions of Ansible Tower: [setup](https://releases.ansible.com/ansible-tower/setup/)
- Old versions of Ansible Tower: [bundle](https://releases.ansible.com/ansible-tower/setup-bundle/)
- Old versions of Ansible Tower: [OpenShift installer](https://releases.ansible.com/ansible-tower/setup_openshift/) - For migration activities.
- [AAP Nightly Builds](http://nightlies.testing.ansible.com/ansible-tower_nightlies_m8u16fz56qr6q7/released/2.4/setup-bundle/x86_64/)
- [aap-containerized-nightlies](http://nightlies.testing.ansible.com/aap-containerized-installer_o145Sdsti5fcoUJi/)

1. Extract the content

```shell
$ tar xvzf ansible-tower-setup-latest.tar.gz
$ cd ansible-tower-setup-<tower_version>
```

#### Get Registry Credential

You need access to Red Hat Container Registry to fetch the continer images for automation controller and execution environment.
You can create a [service account in Red Hat registry](https://access.redhat.com/RegistryAuthentication#creating-registry-service-accounts-6) for the same and use the credential in AAP installation inventory.

```shell
# Execution Environment Configuration
# Credentials for container registry to pull execution environment images from,
# comment out registry_username if authentication is not required
registry_url='registry.redhat.io'
registry_username='YOUR_SERICE_ACCOUNT_USERNAME'
registry_password='YOUR_SERICE_ACCOUNT_PASSWORD'
```

Read more [Registry Service Account Management Application](https://access.redhat.com/RegistryAuthentication)

#### Configure Ansible Automation Platform Installation Inventory



#### Run Setup Script

```shell
# ./setup.sh
```

You can ignore `ignore_preflight_errors` if you are testing or preparing home labs.

```shell
# ./setup.sh ignore_preflight_errors=true
```

Root access to the remote machines is required, you need to use any of the below methods

```shell
$ ansible_user=root ansible_ssh_pass=”your_password_here” inventory host or group variables
# or
$ ansible_user=root ansible_ssh_private_key_file=”path_to_your_keyfile.pem” inventory host or group variables
# or
$ ANSIBLE_BECOME_METHOD=’sudo’ ANSIBLE_BECOME=True ./setup.sh
```

### Disaster Recovery for the Ansible Automation Platform

Please note DR and High Availability (“HA”) are not currently supported. Current Ansible Automation Platform customers are needing to configure their DR instances to accurately reflect their node usage and need guidance based on changes due to manifest files. This can be for cold, warm, or hot DR/HA. This article regards how manifests are handled in DR and HA environments.

Refer to the [documentation](https://access.redhat.com/articles/6967148).


### Ansible Tower HA and DR

_(TODO)_
#### Verify Streaming Replication**

**On primary database node**

swtich to `postgres` user.

```shell
[ansible@AAP-DB-1 ~]$ sudo su - postgres
```

Then,

```shell
[postgres@AAP-DB-1 ~]$ psql -c 'select application_name, state, sync_priority, sync_state from pg_stat_replication;'
 application_name |   state   | sync_priority | sync_state
------------------+-----------+---------------+------------
 awx              | streaming |             1 | sync
(1 row)
```

or

```shell
[postgres@AAP-DB-1 ~]$ psql -c 'select client_addr, state, sent_lsn, write_lsn,flush_lsn, replay_lsn from pg_stat_replication;'
  client_addr  |   state   | sent_lsn  | write_lsn | flush_lsn | replay_lsn
---------------+-----------+-----------+-----------+-----------+------------
 192.168.56.32 | streaming | 0/402AF38 | 0/402AF38 | 0/402AF38 | 0/402AF38
(1 row)

[postgres@AAP-DB-1 ~]$ psql -c 'select * from pg_stat_replication;'
  pid  | usesysid |  usename   | application_name |  client_addr  | client_hostname | client_port |
backend_start         | backend_xmin |   state   | sent_lsn  | write_lsn | flush_lsn | replay_lsn |    write
_lag    |    flush_lag    |   replay_lag    | sync_priority | sync_state
-------+----------+------------+------------------+---------------+-----------------+-------------+---------
----------------------+--------------+-----------+-----------+-----------+-----------+------------+---------
--------+-----------------+-----------------+---------------+------------
 46392 |    23055 | replicator | awx              | 192.168.56.32 |                 |       37668 | 2022-02-
03 07:23:25.296914+00 |              | streaming | 0/402B4E0 | 0/402B4E0 | 0/402B4E0 | 0/402B4E0  | 00:00:00
.002516 | 00:00:00.006299 | 00:00:00.007456 |             1 | sync
(1 row)
```

**On secondary/standby database node**

Switch to `postgres` user

```shell
[ansible@AAP-DB-2 ~]$ sudo su - postgres
Last login: Thu Feb  3 07:59:07 UTC 2022 on pts/1
[postgres@AAP-DB-2 ~]$
```

Then,

```shell
[postgres@AAP-DB-2 ~]$ psql -c 'select * from pg_stat_wal_receiver;'
  pid  |  status   | receive_start_lsn | receive_start_tli | received_lsn | received_tli |      last_msg_send_time
       |     last_msg_receipt_time     | latest_end_lsn |        latest_end_time        | slot_name |

                      conninfo

-------+-----------+-------------------+-------------------+--------------+--------------+------------------------
-------+-------------------------------+----------------+-------------------------------+-----------+-------------
------------------------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------------------
------------------------------------------------------------------
 51587 | streaming | 0/4000000         |                 1 | 0/4031110    |            1 | 2022-02-03 08:15:14.493
483+00 | 2022-02-03 08:15:14.493939+00 | 0/4031110      | 2022-02-03 08:15:14.493483+00 |           | user=replica
tor passfile=/var/lib/pgsql/.pgpass channel_binding=prefer dbname=replication host=192.168.56.31 port=5432 applica
tion_name=awx fallback_application_name=walreceiver sslmode=prefer sslcompression=0 ssl_min_protocol_version=TLSv1
.2 gssencmode=prefer krbsrvname=postgres target_session_attrs=any
(1 row)
```

or,

```shell
[postgres@AAP-DB-2 ~]$ psql -c 'SELECT pg_last_xact_replay_timestamp();'
 pg_last_xact_replay_timestamp
-------------------------------
 2022-02-03 08:15:54.592737+00
(1 row)
```

#### Ansible Tower HA Failover

Check current Tower database configurations.

```shell
[root@AnsibleController1x-1 ~]# cat /etc/tower/conf.d/postgres.py
# Ansible Tower database settings.

DATABASES = {
   'default': {
       'ATOMIC_REQUESTS': True,
       'ENGINE': 'awx.main.db.profiled_pg',
       'NAME': 'awx',
       'USER': 'awx',
       'PASSWORD': """aapadmin""",
       'HOST': 'aap-db-1.lab.local',
       'PORT': '5432',
       'OPTIONS': { 'sslmode': 'prefer',
                    'sslrootcert': '/etc/pki/tls/certs/ca-bundle.crt',
       },
   }
}
```

- [redhat-cop/automate-tower-ha-dr](https://github.com/redhat-cop/automate-tower-ha-dr)
- [Ansible Tower High Availability and Disaster Recovery](https://www.redhat.com/en/blog/ansible-tower-high-availability-and-disaster-recovery) - April 8, 2019 / Marc Petrivelli, Red Hat Blog

#### Database tips

**Check Data path**

```shell
[postgres@AAP-DB-2 ~]$ pg_ctl status
pg_ctl: server is running (PID: 51581)
/usr/bin/postgres "-D" "/var/lib/pgsql/data"

[postgres@AAP-DB-2 ~]$ psql -c "SHOW data_directory;"
   data_directory
---------------------
 /var/lib/pgsql/data
(1 row)
```



### Ansible Tower with database SSL connection enabled

**Verify SSL Certificate-Key pair**

If all the three match, the SSL certificate matches the Private Key.

```shell
# SSL certificate
$ openssl x509 –noout –modulus –in <file>.crt | openssl md5

# RSA private key
$ openssl rsa –noout –modulus –in <file>.key | openssl md5

# CSR
$ openssl req -noout -modulus -in <file>.csr | openssl md5
```

**SSL Certificate with Passphrase**

- [After Replacing Self-Signed Certificate with Passphrase in Ansible Tower, How Do I Correct Issues with Nginx Service Failing to Start?](https://access.redhat.com/solutions/3520121)
- [Using ssl_password_file for nginx](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_password_file)


### Troubleshooting

- [Resolve error occurred while running Ansible Tower installation](https://bakingclouds.com/resolve-error-occurred-while-running-ansible-tower-installation/)
- [Why Ansile Tower Setup Is Failing At 'Migrate the Tower database schema' Task With Errors 'Server does not support SSL' / 'certificate verify failed' / 'no pg_hba.conf entry for host' When Connecting To PostgreSQL Database With SSL Enabled ?](https://access.redhat.com/solutions/5044701)
- [How to install a CA certificate on Red Hat Enterprise Linux 7 and later](https://access.redhat.com/solutions/5868401)
- [How Can I Bypass "noexec" Permission Issue On "/tmp" and "/var/tmp" During Ansible Tower Installation?s](https://access.redhat.com/solutions/4308791)
- [Verify SSL is enabled on PGSQL](https://support.cloud.engineyard.com/hc/en-us/articles/115000612114-PostgreSQL-SSL-Support)
- [Why is Ansible Tower Restore Process Failing With Error `no pg_hba.conf entry for host x.x.x.x`?](https://access.redhat.com/solutions/5501731)


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

## Ansible on Clouds

- [Ansible Automation Platform - Cloud Deployments](https://docs.redhat.com/en/documentation/ansible_on_clouds/2.4)
- [Ansible on Azure Articles](https://access.redhat.com/articles/6983528)

## Red Hat Ansible Automation Platform Hardening Guide

- [Red Hat Ansible Automation Platform hardening guide](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html-single/red_hat_ansible_automation_platform_hardening_guide/index)(Doc)
- [Red Hat Ansible Automation Platform Hardening Guide](https://access.redhat.com/articles/7025278)

## Migrating, Updating and Upgrading AAP

- [On-Prem Ansible Automation Platform Patching Procedure - OS Packages Update & AAP Packages Update](https://access.redhat.com/solutions/7034370)
- [Frequently Asked Questions (FAQs) on OS Patching for Ansible Automation Platform 2.3 and later](https://access.redhat.com/articles/7050473)
- [Migrating Red Hat Ansible Automation Platform to Ansible Automation Platform Operator](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/deploying_the_red_hat_ansible_automation_platform_operator_on_openshift_container_platform/aap-migration)


## Ansible Automation Hub

[Documentation](https://docs.ansible.com/ansible/devel/reference_appendices/automationhub.html)

- Installing [Ansible Galaxy NG](https://github.com/ansible/galaxy_ng/wiki/End-User-Installation)
- [INSTALLING PRIVATE AUTOMATION HUB](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/1.2/html/installing_private_automation_hub/index)

## Ansible Playbook References

- [Ansible Examples](https://github.com/ginigangadharan/ansible-examples){:target="_blank"}
(*Forked from [@ansible](https://github.com/ansible/ansible-examples)*)
- [Ansible for DevOps](https://github.com/ginigangadharan/ansible-for-devops){:target="_blank"}
(*Forked from [geerlingguy](https://github.com/geerlingguy/ansible-for-devops)*)
- [Ansible NXOS Samples](https://github.com/ginigangadharan/Ansible-NXOS){:target="_blank"} (Network Automation)

## Tools for Ansible

- [Ansible Automation Platform Inventory File Generator](https://access.redhat.com/labsinfo/aapifg){:target="_blank"}
- [Ansible Automation Platform Upgrade Assistant](https://access.redhat.com/labs/aapua/){:target="_blank"}
- [Ansible Silo](https://groupon.github.io/ansible-silo){:target="_blank"}
- [ansible-risk-insight](https://github.com/ansible/ansible-risk-insight){:target="_blank"}: Ansible Risk Insight (ARI) is the tool to evaluate the quality and risk of the ansible content.

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
# subscription-manager repos --enable rhel-server-rhscl-7-rpms
# yum install python27-python-pip
```

Switch to a normal user and check the pip
```
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

```
[ansible@vm-ans-02 ~]$ ansible --version |grep python
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  python version = 2.7.5 (default, Sep 26 2019, 13:23:47) [GCC 4.8.5 20150623 (Red Hat 4.8.5-39)]
```


## Ansible Blog/Articles to Follow
- [MyDailyTutorials](http://www.mydailytutorials.com/category/tutorials/ansible/){:target="_blank"}
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

## Other References

- [Red Hat Ansible Tower Life Cycle](https://access.redhat.com/support/policy/updates/ansible-tower)
- [Red Hat Ansible Automation Platform Workshops](https://github.com/ansible/workshops)
- [DEEP DIVE INTO ANSIBLE NETWORK RESOURCE MODULE](https://www.ansible.com/deep-dive-into-ansible-network-resource-module)

## Ansible for IBM Power

- [IBM Power Systems AIX collection](https://github.com/IBM/ansible-power-aix)

## Ansible for Arista Networks

- [Ansible Modules for Arista CloudVision Platform](https://github.com/aristanetworks/ansible-cvp)

## Ansible for CIS Hardening/CIS Check
- [DevSec Hardening Framework](https://github.com/dev-sec) / [devops + security - Server Hardening Automation](https://dev-sec.io/)

## AD/LDAP Integration - AAP 2.5

(In-Progress)

Example LDAP Configuration for Ansible Automation Platform

- **Name**: `ActiveDirectorySandboxDev`
- **Type**: `LDAP`
- **LDAP Server URI**: `ldap://192.168.57.101:389`
- **LDAP Bind DN**: `cn=ansible_bind,cn=users,dc=sandbox,dc=dev`
- **LDAP Bind Password**: `your-bind-user-password`
- **LDAP Group Type**: `ActiveDirectoryGroupType`
- **LDAP User DN Template**: Optional

NOTE: This LDAP User DN Template configuration has not tested successfully.

```shell
sAMAccountName=%(user)s,OU=AAP,DC=sandbox,DC=dev
```

For openLDAP style it would be of below format

```shell
uid=%(user)s,cn=users,cn=accounts,dc=sandbox,dc=dev
```

- **LDAP Start TLS**: Off/On
- **LDAP Require Group** (optional): `CN=aap-users,OU=AAP,DC=sandbox,DC=dev`
- **LDAP User Search**:

```json
[
  "cn=users,dc=sandbox,dc=dev",
  "SCOPE_SUBTREE",
  "(sAMAccountName=%(user)s)"
]
```

- **LDAP Group Search**:

```json
[
  "cn=users,dc=sandbox,dc=dev",
  "SCOPE_SUBTREE",
  "(objectClass=group)"
]
```

- **LDAP User Attribute Map**:

```json
{
  "email": "mail",
  "last_name": "sn",
  "first_name": "givenName"
}
```

- **LDAP Group Type Parameters**:

```json
{
  "name_attr": "cn"
}
```

### Authentication mapping

Examples:

**Superuser:**

- Trigger: Groups
- Groups: `cn=aap-admins,cn=users,dc=sandbox,dc=dev`

**NW-Org-Admin:**

- Trigger: Groups
- Groups: `cn=network-admins,cn=users,dc=sandbox,dc=dev`

**NW-Org-Member:**

- Trigger: Groups
- Groups: `cn=network-team,cn=users,dc=sandbox,dc=dev`

**NW-Team-Admins:**

- Trigger: Groups
- Groups: `cn=network-admins,cn=users,dc=sandbox,dc=dev`
- Team: Network-Admins
- Organization: Network Dept
- Role: Team Admin

**NW-Org-Member:**

- Trigger: Groups
- Groups: `cn=network-team,cn=users,dc=sandbox,dc=dev`
- Team: Network-Team
- Organization: Network Dept
- Role: Team Member


## AD/LDAP Integration - AAP 2.4

Example LDAP Configuration for Ansible Automation Platform

- **Name**: `ActiveDirectoryExampleCom`
- **Type**: `LDAP`
- **LDAP Server URI**: `ldap://192.168.57.101:389`
- **LDAP Bind DN**: `CN=ansible_bind,CN=users,DC=sandbox,DC=dev`
- **LDAP Bind Password**: your-bind-user-password
- **LDAP Group Type**: `MemberDNGroupType`
- **LDAP User DN Template**:

NOTE: This LDAP User DN Template configuration has not tested successfully.

```shell
sAMAccountName=%(user)s,OU=AAP,DC=sandbox,DC=dev
```

For openLDAP style it would be of below format

```shell
uid=%(user)s,cn=users,cn=accounts,DC=sandbox,DC=dev
```

- **LDAP Start TLS**: Off/On
- **LDAP Require Group** (optional): `CN=aap-users,OU=AAP,DC=sandbox,DC=dev`
- **LDAP User Search**:

```json
[
  "DC=sandbox,DC=dev",
  "SCOPE_SUBTREE",
  "(sAMAccountName=%(user)s)"
]
```

- **LDAP Group Search**:

```json
[
  "DC=sandbox,DC=dev",
  "SCOPE_SUBTREE",
  "(objectClass=group)"
]
```

- **LDAP User Attribute Map**:

```json
{
  "email": "mail",
  "first_name": "givenName",
  "last_name": "sn"
}
```

- **LDAP Group Type Parameters**:

```json
{
  "member_attr": "member",
  "name_attr": "cn"
}
```

**LDAP Organization Map**

```json
{
  "Network-Ops": {
    "admins": "CN=network_admins,OU=AAP,DC=sandbox,DC=dev",
    "remove_admins": false,
    "remove_users": false,
    "users": true
  },
  "XYZCorp-CaC": {
    "admins": "CN=ansible_admins,OU=AAP,DC=sandbox,DC=dev",
    "remove_admins": false,
    "remove_users": false,
    "users": true
  }
}
```

**LDAP Team Map**

```json
{
  "cac-admins": {
    "organization": "XYZCorp-CaC",
    "remove": true,
    "users": "cn=ansible_admins,ou=AAP,DC=sandbox,DC=dev"
  },
  "cac-operators": {
    "organization": "XYZCorp-CaC",
    "remove": true,
    "users": "cn=ansible_operators,ou=AAP,DC=sandbox,DC=dev"
  },
  "network-admins": {
    "organization": "Network-Ops",
    "remove": true,
    "users": "cn=network_admins,ou=AAP,DC=sandbox,DC=dev"
  },
  "network-operators": {
    "organization": "Network-Ops",
    "remove": true,
    "users": "cn=network_operators,ou=AAP,DC=sandbox,DC=dev"
  }
}
```

## LDAP Troubleshooting and testing

```shell
# install ldap client if doe
$ sudo yum install openldap-clients -y

# install nc
$ sudo yum install nmap-ncat -y
```

**Ensure you can reach the LDAP server and port:**

```shell
$ nc -zv 192.168.57.101 389
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Connected to 192.168.57.101:389.
Ncat: 0 bytes sent, 0 bytes received in 0.06 seconds.
```

**Test LDAP Bind Credentials**

Verify if the ansible_bind account can bind to LDAP:

```shell
$ ldapwhoami -x -H ldap://192.168.57.101:389 -D "CN=ansible_bind,CN=users,DC=sandbox,DC=dev" -w 'Welcome123'
```
Or use environment variables for quick access
```shell
export LDAP_SERVER=192.168.57.101
export LDAP_PORT=389
export LDAP_BIND_USER=ansible_bind
export LDAP_BIND_PASSWORD='Welcome123'

ldapwhoami -x \
  -H ldap://${LDAP_SERVER}:${LDAP_PORT} \
  -D "CN=${LDAP_BIND_USER},CN=Users,DC=sandbox,DC=dev" \
  -w "${LDAP_BIND_PASSWORD}"
```

**Test LDAP User Search**

Validate the user search query:

```shell
$ ldapsearch -x -H ldap://192.168.57.101:389 \
-D "CN=ansible_bind,CN=users,DC=sandbox,DC=dev" \
-w 'Welcome123' \
-b "DC=sandbox,DC=dev" \
"(sAMAccountName=john)"
```

**Test LDAP Group Search**

Validate the group search query:

```shell
$ ldapsearch -x -H ldap://192.168.57.101:389 \
-D "CN=ansible_bind,CN=users,DC=sandbox,DC=dev" \
-w 'Welcome123' \
-b "DC=sandbox,DC=dev" \
"(objectClass=network-team)"
```

**Verify LDAP User Attribute Mapping**

Check if email, givenName, and sn attributes are available for a user:

```shell
$ ldapsearch -x -H ldap://192.168.57.101:389 \
-D "CN=ansible_bind,CN=users,DC=sandbox,DC=dev" \
-w 'Welcome123' \
-b "DC=sandbox,DC=dev" \
"(sAMAccountName=john)" mail givenName sn
```

**Verify Group Membership**

If you want to validate if a user belongs to a specific group:

```shell
$ ldapsearch -x -H ldap://192.168.57.101:389 \
-D "CN=ansible_bind,CN=users,DC=sandbox,DC=dev" \
-w 'Welcome123' \
-b "DC=sandbox,DC=dev" \
"(member=CN=user1,CN=users,DC=sandbox,DC=dev)"
```

- Replace `user1` with an actual LDAP username.

**Verify TLS Configuration (if enabled)**

If LDAP Start TLS is On, test the TLS connection:

```shell
$ ldapsearch -x -H ldap://192.168.57.101:389 \
-Z \
-D "CN=ansible_bind,CN=users,DC=sandbox,DC=dev" \
-w 'Welcome123' \
-b "DC=sandbox,DC=dev" \
"(sAMAccountName=user1)"
```

- `-Z` enables StartTLS.

**Test from the Automation Controller Container (if applicable)**

If Ansible Automation Platform is running in a container:

```shell
$ podman exec -it <container_name> bash \
ldapsearch -x -H ldap://192.168.57.101:389 \
-D "CN=ansible_bind,CN=users,DC=sandbox,DC=dev" \
-w 'Welcome123' \
-b "DC=sandbox,DC=dev" \
"(sAMAccountName=john)"
```

- Replace `<container_name>` with the appropriate container name.

```shell
$ ldapsearch -x  -H ldap://192.168.57.101:389 -D "CN=ansible_bind,CN=Users,DC=sandbox,DC=dev" -b "DC=sandbox,DC=dev" -w yourbindpassword

# search for a specific user
$ ldapsearch -x  -H ldap://192.168.57.101:389 -D "CN=ansible_bind,CN=Users,DC=sandbox,DC=dev" -w yourbindpassword -b "cn=devops,cn=Users,DC=sandbox,DC=dev"
```

## Best practices

- [Performance tuning for automation controller](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_administration_guide/assembly-controller-improving-performance)

## Utilities and References

- [Janus](https://github.com/ansible-middleware/janus) - The project ansible-middleware/janus holds a set of playbooks used to transform upstream collection (such as ansible-middleware/wildfly) to downstream collection (redhat/eap) using [FQCN migration|https://github.com/ansible-collections/community.fqcn_migration]. Please refers to this project for more information.
- [community.fqcn_migration](https://github.com/ansible-collections/community.fqcn_migration) - This project, called fqcn_migration, is a set of Ansible roles designed to rename a collection and even changed its namespace.
- [How to Stop/Start/Restart & Disable/Enable Containerized Ansible Automation Platform 2.5 using systemd?](https://access.redhat.com/solutions/7090499)