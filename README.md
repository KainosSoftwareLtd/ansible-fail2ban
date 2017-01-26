Fail2ban
=========

A role that installs and configures fail2ban to practively act as a host based intrusion detection system.

The role is configurable so you can setup your own custom checks but is configured by default with the basics e.g. sshd

Requirements
------------

At the moment this has only been tested with Centos 7 servers.

Role Invocation
---------------

At the moment it is recommended you declare this role in two different complementary ways.

1. Configure role variables with any default you desire and include the role as part of your standard ansible run
2. Setup custom jails for other services by defining this role as a dependency for those services

1. Role variables
-----------------

Simply configure the default role variables as required and execute this module against the node in question.

```
# Default jail settings for all jails (can be overridden by individual jails)
fail2ban_default_jail_config:
  ignoreip: '127.0.0.1/8'
  bantime: 600
  findtime: 600
  maxretry: 5
  banaction: firewallcmd-ipset
# The default jails you want to be setup on all systems
fail2ban_default_jails:
  sshd:
    enabled: true
    ignoreip: '127.0.0.1/8 192.168.0.0/24'
    port: ssh
    filter: sshd
    logpath: '%(sshd_log)s'
    maxretry: 6
  sshd_ddos:
    enabled: true
    ignoreip: '127.0.0.1/8 192.168.0.0/24'
    port: ssh
    filter: sshd-ddos
    logpath: '%(sshd_log)s'
    maxretry: 10
```

As a role dependency
--------------------

For any role specific jails you want to configure it can be done as a role depdendency.

When true, the 'fail2ban_only_configure_jails' variable will only configure jails and will skip many elements of this module such as configuring custom filters etc. The intention is that skips unnecessary tasks when we are only interested in modifying very specific jails thus resulting in a performance improvement.

```
dependencies:
  - role: KainosSoftwareLtd.fail2ban
    fail2ban_only_configure_jails: true
    fail2ban_custom_jails:
      vsftpd:
        enabled: true
        port: 21
        filter: vsftpd
        logpath: '/var/log/transfer.log'
        maxretry: 3
        bantime: 3600
        findtime: 3600
```

License
-------

MIT

Author Information
------------------

This role was created in 2017 by Caoimhin Graham on behalf of [Kainos Software](https://www.kainos.com)
