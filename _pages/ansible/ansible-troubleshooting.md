---
layout: post
title: Troubleshooting Ansible Automation Platform
categories: [ ansible ]
image: "assets/images/2020/ansible-automation.png"
tags: [automation]
permalink: /aap-troubleshooting
featured: false
hidden: false
titleshort: Troubleshooting Ansible
---

- [Error with Self Signed SSL Cert on SCM server](#error-with-self-signed-ssl-cert-on-scm-server)
- [Error:module 'enum' has no attribute 'IntFlag'](#errormodule-enum-has-no-attribute-intflag)
- [Error with `nosuid` when read/write files](#error-with-nosuid-when-readwrite-files)
- [Checking container logs](#checking-container-logs)
- [How to enable DEBUG mode for AAP Components](#how-to-enable-debug-mode-for-aap-components)


## Error with Self Signed SSL Cert on SCM server

Error : Peer's certificate issuer has been marked as not trusted by the user

```yaml
{
    "stderr_lines": [
        "fatal: unable to access 'https://$encrypted$:$encrypted$@vm-gitnode-01.lab.local/ansible/network-automation.git/': Peer's certificate issuer has been marked as not trusted by the user."
    ],
    "cmd": "/usr/bin/git clone --origin origin 'https://$encrypted$:$encrypted$@vm-gitnode-01.lab.local/ansible/network-automation.git' /var/lib/awx/projects/_8__network_poc",
    "_ansible_no_log": false,
    "stdout": "Cloning into '/var/lib/awx/projects/_8__network_poc'...\n",
    "changed": false,
    "invocation": {
        "module_args": {
            "force": false,
            "track_submodules": false,
            "reference": null,
            "dest": "/var/lib/awx/projects/_8__network_poc",
            "umask": null,
            "clone": true,
            "gpg_whitelist": [],
            "accept_hostkey": false,
            "update": true,
            "ssh_opts": null,
            "repo": "https://$encrypted$:$encrypted$@vm-gitnode-01.lab.local/ansible/network-automation.git",
            "bare": false,
            "archive": null,
            "refspec": null,
            "executable": null,
            "remote": "origin",
            "recursive": true,
            "separate_git_dir": null,
            "verify_commit": false,
            "depth": null,
            "version": "HEAD",
            "key_file": null
        }
    },
    "stderr": "fatal: unable to access 'https://$encrypted$:$encrypted$@vm-gitnode-01.lab.local/ansible/network-automation.git/': Peer's certificate issuer has been marked as not trusted by the user.\n",
    "rc": 128,
    "_ansible_delegated_vars": {
        "ansible_host": "localhost"
    },
    "stdout_lines": [
        "Cloning into '/var/lib/awx/projects/_8__network_poc'..."
    ],
    "msg": "fatal: unable to access 'https://$encrypted$:$encrypted$@vm-gitnode-01.lab.local/ansible/network-automation.git/': Peer's certificate issuer has been marked as not trusted by the user."
}
```

**Solution**
Resolution
SSL certificate validation can be prevented for Git connections originating from Tower by adding the following settings in the Tower UI at Settings >> Configure Tower >> Jobs in JSON format:

```json
{
  "GIT_SSL_NO_VERIFY": "True"
}
```

[Reference](https://access.redhat.com/solutions/3131461)

Note : For cloning repo, use below methods

```bash
git config --global http.sslverify false
#or
export GIT_SSL_NO_VERIFY=true
```

## Error:module 'enum' has no attribute 'IntFlag'

```yaml
{
    "exception": "Traceback (most recent call last):\n  File \"/var/lib/awx/.ansible/tmp/ansible-tmp-1599103337.21-17-81526385316505/AnsiballZ_fortios_facts.py\", line 102, in <module>\n    _ansiballz_main()\n  File \"/var/lib/awx/.ansible/tmp/ansible-tmp-1599103337.21-17-81526385316505/AnsiballZ_fortios_facts.py\", line 17, in _ansiballz_main\n    import base64\n  File \"/usr/lib64/python3.6/base64.py\", line 9, in <module>\n    import re\n  File \"/usr/lib64/python3.6/re.py\", line 142, in <module>\n    class RegexFlag(enum.IntFlag):\nAttributeError: module 'enum' has no attribute 'IntFlag'\n",
    "_ansible_no_log": false,
    "_ansible_delegated_vars": {
        "ansible_host": "localhost"
    },
    "module_stderr": "Traceback (most recent call last):\n  File \"/var/lib/awx/.ansible/tmp/ansible-tmp-1599103337.21-17-81526385316505/AnsiballZ_fortios_facts.py\", line 102, in <module>\n    _ansiballz_main()\n  File \"/var/lib/awx/.ansible/tmp/ansible-tmp-1599103337.21-17-81526385316505/AnsiballZ_fortios_facts.py\", line 17, in _ansiballz_main\n    import base64\n  File \"/usr/lib64/python3.6/base64.py\", line 9, in <module>\n    import re\n  File \"/usr/lib64/python3.6/re.py\", line 142, in <module>\n    class RegexFlag(enum.IntFlag):\nAttributeError: module 'enum' has no attribute 'IntFlag'\n",
    "changed": false,
    "module_stdout": "",
    "rc": 1,
    "msg": "MODULE FAILURE\nSee stdout/stderr for the exact error"
}
```

**Solution**

https://access.redhat.com/solutions/4282031

## Error with `nosuid` when read/write files

```yaml
sudo: effective uid is not 0, is /usr/bin/sudo on a file system with the 'nosuid' option set or an NFS file system without root privileges?
```

**Solution**

It isn't possible to use Tower with local action to escalate to the root user. It will be necessary to alter your task to connect via SSH and then escalate to root using another user(not AWX). This is done purposefully to avoid security risks associated with our user having root level access to the system.

*NOTE : It is not recommended that sudo access be given to AWX user. You'll need to adjust your playbook to SSH rather than use a local connection.*

Root Cause: The AWX service user is intentionally restricted from sudo operations.

https://access.redhat.com/solutions/3223501

## Checking container logs

```shell
$ journalctl CONTAINER_NAME=<container_name>
$ journalctl CONTAINER_NAME=<container_name> -n 100 --no-pager -f

# or check other podman logs if any
$ podman logs -f automation-gateway
```

true | openssl s_client -connect awesome.corp.root:636 -showcerts -CAfile awesomecorp_bundle.pem

openssl x509 -in <filename.cert> -noout -subject -issuer

bundle cert
cat rootca intermediate | tee bundle.pem

## How to enable DEBUG mode for AAP Components

E.g. Enable DEBUB for authentication containers - in `~/aap/gateway/etc/settings.py`

```shell
.
.
LOGGING['loggers']['ansible_base.authentication'] = {'level': 'DEBUG'}
```
