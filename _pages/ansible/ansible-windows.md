---
layout: post
title: Ansible for Windows
categories: [ ansible ]
tags: [ansible]
show-avatar: false
permalink: ansible-windows
featured: false
hidden: false
showindex: true
---


Checkout the [Ansible Automation for Windows](https://www.techbeatly.com/ansible-windows/) guides.

- [WinRM vs OpenSSH – Pros \& Cons](#winrm-vs-openssh--pros--cons)
  - [WinRM (Default)](#winrm-default)
    - [✔️ Pros](#️-pros)
    - [❌ Cons](#-cons)
  - [🆕 OpenSSH](#-openssh)
  - [✔️ Pros](#️-pros-1)
      - [❌ Cons](#-cons-1)
  - [💡 Use Case Scenarios](#-use-case-scenarios)
  - [📝 Summary Recommendation for Proposal](#-summary-recommendation-for-proposal)
  - [📋 Proposal Add-on: Migration/Decision Factors](#-proposal-add-on-migrationdecision-factors)
- [CredSSP](#credssp)


## WinRM vs OpenSSH – Pros & Cons

Ansible supports managing Windows hosts using two connection types:
- **WinRM (Windows Remote Management)** – default and traditional method.
- **OpenSSH** – newer method, especially post-Windows 10 / Server 2019.

### WinRM (Default)

#### ✔️ Pros

| Pros | Description |
|------|-------------|
| ✅ Native Support | Built-in to Windows OS; enabled and configured on most enterprise systems. |
| ✅ Full Module Compatibility | All Ansible Windows modules are fully tested with WinRM. |
| ✅ PowerShell-centric | Designed for PowerShell automation and WinRM natively uses it. |
| ✅ Kerberos & Domain Integration | Supports AD/Kerberos for seamless authentication in domain environments. |
| ✅ Mature & Documented | WinRM has been the default for years; tons of docs, blogs, support. |

#### ❌ Cons

| Cons | Description |
|------|-------------|
| ❌ Complex Configuration | Requires setting up listeners, ports (5985/5986), HTTPS, certificates, etc. |
| ❌ Firewall Hassles | Extra firewall rules needed; port 5986 often blocked by security policies. |
| ❌ Slow Performance | Known to be slower than SSH in many operations. |
| ❌ SSL Certificate Setup | Secure mode needs signed certs, which is painful in many orgs. |
| ❌ Occasional Encoding Issues | Especially with special characters or non-English content. |
| ❌ No Agent Forwarding | Unlike SSH, WinRM lacks native support for agent/key forwarding. |

---

### 🆕 OpenSSH

### ✔️ Pros

| Pros | Description |
|------|-------------|
| ✅ Simpler Setup | Just install OpenSSH, open port 22, and you're good to go. |
| ✅ Uses Existing SSH Workflow | Easy integration for teams already using SSH in Linux/macOS. |
| ✅ Better Performance | Usually faster than WinRM in most tasks. |
| ✅ Standard Port | Uses port 22, often already open in mixed environments. |
| ✅ Easier Key Management | Supports SSH keys (no need for certs or complex auth). |
| ✅ Works with SSH Agent | Supports ssh-agent and agent forwarding. |

##### ❌ Cons

| Cons | Description |
|------|-------------|
| ❌ Not Fully Supported | Some Ansible Windows modules may not work or behave differently. |
| ❌ Requires Extra Setup | OpenSSH is not enabled by default on all Windows versions (esp. older). |
| ❌ Limited AD/Kerberos | No built-in support for domain/Kerberos auth like WinRM. |
| ❌ Troubleshooting May Be Harder | Errors are often lower-level and less descriptive. |
| ❌ Inconsistent Shell Experience | Some PowerShell-specific commands may behave oddly under SSH shell. |

---

### 💡 Use Case Scenarios

| Scenario | Recommendation |
|----------|----------------|
| Domain-joined environment | **WinRM** (for Kerberos & seamless integration) |
| Hybrid/mixed Linux-Windows infra | **OpenSSH** (for consistency and ease) |
| Security restrictions on custom ports | **OpenSSH** (uses port 22) |
| Need speed and simplicity | **OpenSSH** |
| Complex legacy systems with existing WinRM setup | **WinRM** |
| AD authentication is mandatory | **WinRM** |

### 📝 Summary Recommendation for Proposal

> **Default Option:** Start with **WinRM** if compatibility and AD integration are top priority, especially for large enterprise or domain-joined machines.
>
> **Alternative Option:** Consider **OpenSSH** if:
> - New environment with fewer legacy dependencies.
> - You're already using SSH keys.
> - You want faster and easier setup.
> - The infrastructure team prefers standard SSH tooling.
>
> **Long-Term Plan:** Evaluate hybrid usage, or standardize on OpenSSH as it becomes more mature and broadly supported.


### 📋 Proposal Add-on: Migration/Decision Factors

If switching between connection methods, consider:
- **Compatibility check** for required modules.
- **Authentication needs** (Kerberos vs SSH keys).
- **Security team policies** (ports, protocols).
- **User familiarity** (PowerShell vs bash-like environments).
- **Automation tools** and their SSH/WinRM compatibility.

## CredSSP

Enable CredSSP

Error:
"credssp: Server did not response with a CredSSP token after step TLS Handshake - actual ''",

ansible_winrm_credssp_disable_tlsv1_2

[Patching windows servers through Ansible](https://medium.com/@gopi.narayanaswamy/patching-windows-servers-through-ansible-32f5f606b6c0)