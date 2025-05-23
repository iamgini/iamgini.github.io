---
layout: postpage
title: Ansible Automation Platform - Docs & References
categories: [ Ansible, Automation, AAP ]
image: "assets/images/2020/ansible-automation.png"
tags: [ aap, cloud, automation, containers, kubernetes, ansible automation platform, red hat ansible]
permalink: aap
featured: false
hidden: false
titleshort: AAP
---

- [1. Ansible Automation Platform](#1-ansible-automation-platform)
  - [1.1. Enable RHEL and AAP Subscriptions](#11-enable-rhel-and-aap-subscriptions)
  - [1.2. Ansible Automation Platform - Containerized Setup](#12-ansible-automation-platform---containerized-setup)
  - [1.3. Removing instance from cluster](#13-removing-instance-from-cluster)
- [2. AAP SSL Certificate Renewal](#2-aap-ssl-certificate-renewal)
  - [2.1. Renewing the self-signed SSL certificate](#21-renewing-the-self-signed-ssl-certificate)


## 1. Ansible Automation Platform

(Formerly Ansible Tower)

### 1.1. Enable RHEL and AAP Subscriptions

Make sure you subscribed to Red Hat and added RHEL Subscription.

```shell
$ sudo subscription-manager register
$ sudo subscription-manager attach --pool=<pool_id of RHEL subscription>
$ sudo subscription-manager list --consumed
```

Now, search for Ansible Automation Platform subscription and attach the pool ID.

```shell
$ sudo subscription-manager list --available --all | grep "Ansible Automation Platform" -B 3 -A 6
$ sudo subscription-manager attach --pool=<pool_id>
$ sudo subscription-manager list --consumed
```

Update required repo for AAP 2.1

```shell
$ sudo subscription-manager repos \
  --disable=ansible-automation-platform-2.0-early-access-for-rhel-8-x86_64-rpms

$ sudo subscription-manager repos \
  --enable=ansible-automation-platform-2.1-for-rhel-8-x86_64-rpms
```

### 1.2. Ansible Automation Platform - Containerized Setup

```shell
$ export ANSIBLE_COLLECTIONS_PATH=/home/devops/ansible-automation-platform-containerized-setup-bundle-2.4-1-x86_64/collections/

$ ansible-playbook -i inventory ansible.containerized_installer.install
```

To uninstall a containerized deployment, execute the uninstall.yml playbook.


```shell
$ ansible-playbook -i inventory ansible.containerized_installer.uninstall
```

Reference: [Ansible Automation Platform containerized installation (2.5)](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.5/html/containerized_installation/)

### 1.3. Removing instance from cluster

```shell
bash-4.4$ awx-manage deprovision_instance --hostname aap-rhel-92-2.lab.local
Instance Removed
Successfully deprovisioned aap-rhel-92-2.lab.local
(changed: True)
```

## 2. AAP SSL Certificate Renewal

### 2.1. Renewing the self-signed SSL certificate

The following steps regenerate a new SSL certificate for both automation controller and automation hub.

Procedure

Add aap_service_regen_cert=true to the inventory file in the [all:vars] section:

[all:vars]
aap_service_regen_cert=true

Run the installer.