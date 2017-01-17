Fail2ban
=========

[![Build Status](https://travis-ci.org/jebovic/ansible-fail2ban.svg?branch=master)](https://travis-ci.org/jebovic/ansible-fail2ban) [![Ansible Galaxy](https://img.shields.io/badge/galaxy-jebovic.fail2ban-blue.svg?style=flat)](https://galaxy.ansible.com/jebovic/fail2ban)

Install and configure fail2ban, you can add your jails with yaml list variables

This role is a part of my [OPS project](https://github.com/jebovic/ops), follow this link to see it in action. OPS provides a lot of stuff, like a vagrant file for development VMs, playbooks for roles orchestration, inventory files, examples for roles configuration, ansible configuration file, and many more.

Compatibility
-------------

Tested and approved on :

* Debian jessie (8+)
* Ubuntu Trusty (14.04 LTS)
* Ubuntu Xenial (16.04 LTS)

Role Variables
--------------

```yaml
# fail2ban basic configuration
fail2ban_root_path: /etc/fail2ban
fail2ban_loglevel: 3
fail2ban_logtarget: /var/log/fail2ban.log
fail2ban_socket: /var/run/fail2ban/fail2ban.sock
fail2ban_pidfile: /var/run/fail2ban/fail2ban.pid

# fail2ban jail configurations
fail2ban_jails:
  DEFAULT:
    ignoreip: 127.0.0.1/8
    ignorecommand:
    bantime : 600
    findtime: 600
    maxretry: 3
    backend: auto
    usedns: warn
    destemail: root@localhost
    sendername: Fail2Ban
    sender: fail2ban@localhost
    banaction: iptables-multiport
    mta: sendmail
    protocol: tcp
    chain: INPUT
    action_: |
                %(banaction)s[name=%(__name__)s, port="%(port)s", protocol="%(protocol)s", chain="%(chain)s"]
    action_mw: |
                %(banaction)s[name=%(__name__)s, port="%(port)s", protocol="%(protocol)s", chain="%(chain)s"]
                %(mta)s-whois[name=%(__name__)s, dest="%(destemail)s", protocol="%(protocol)s", chain="%(chain)s", sendername="%(sendername)s"]
    action_mwl: |
                %(banaction)s[name=%(__name__)s, port="%(port)s", protocol="%(protocol)s", chain="%(chain)s"]
                %(mta)s-whois-lines[name=%(__name__)s, dest="%(destemail)s", logpath=%(logpath)s, chain="%(chain)s", sendername="%(sendername)s"]
    action: |
                %(action_)s
```

Example Playbook
----------------

```yaml
- hosts: servers
  roles:
     - { role: jebovic.fail2ban }
```

Example : config
----------------

```yaml
# fail2ban custom jails
fail2ban_jails:
  DEFAULT:
    ignoreip: 127.0.0.1/8
    ignorecommand:
    bantime : 600
    findtime: 600
    maxretry: 3
    backend: auto
    usedns: warn
    destemail: root@localhost
    sendername: Fail2Ban
    sender: fail2ban@localhost
    banaction: iptables-multiport
    mta: sendmail
    protocol: tcp
    chain: INPUT
    action_: |
                %(banaction)s[name=%(__name__)s, port="%(port)s", protocol="%(protocol)s", chain="%(chain)s"]
    action_mw: |
                %(banaction)s[name=%(__name__)s, port="%(port)s", protocol="%(protocol)s", chain="%(chain)s"]
                %(mta)s-whois[name=%(__name__)s, dest="%(destemail)s", protocol="%(protocol)s", chain="%(chain)s", sendername="%(sendername)s"]
    action_mwl: |
                %(banaction)s[name=%(__name__)s, port="%(port)s", protocol="%(protocol)s", chain="%(chain)s"]
                %(mta)s-whois-lines[name=%(__name__)s, dest="%(destemail)s", logpath=%(logpath)s, chain="%(chain)s", sendername="%(sendername)s"]
    action: |
                %(action_)s
  ssh:
    enabled: true
    port: ssh
    filter: sshd
    logpath: /var/log/auth.log
    maxretry: 6

```

Tags
----

* fail2ban_config : only update config and restart service

License
-------

MIT

Author Information
------------------

Jérémy Baumgarth https://github.com/jebovic
