# Ansible Role: Supervisor

An Ansible Role that installs [Supervisor](http://supervisord.org/) on Linux.

[![Ansible Role: TTRSS](https://img.shields.io/ansible/role/ID?style=flat-square)](https://galaxy.ansible.com/thorian93/ansible_role_ttrss)
[![Ansible Role: TTRSS](https://img.shields.io/ansible/quality/ID?style=flat-square)](https://galaxy.ansible.com/thorian93/ansible_role_ttrss)
[![Ansible Role: TTRSS](https://img.shields.io/ansible/role/d/ID?style=flat-square)](https://galaxy.ansible.com/thorian93/ansible_role_ttrss)

**I forked this role to use the OS package manager for the installation. Thanks @geerlingguy for the initial role!**

## Requirements

No special requirements; note that this role requires root access, so either run it in a playbook with a global `become: yes`, or invoke the role in your playbook like:

    - hosts: foobar
      roles:
        - role: thorian93.ansible_role_supervisor
          become: yes

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

    supervisor_started: true
    supervisor_enabled: true

Choose whether to use an init script or systemd unit configuration to start Supervisor when it's installed and/or after a system boot.

    supervisor_config_path: /etc/supervisor

The path where Supervisor configuration should be stored.

    supervisor_programs:
      - name: 'foo'
        command: /bin/cat
        state: present
    
      - name: 'apache'
        command: apache2ctl -DFOREGROUND
        state: present
        configuration: |
          autostart=true
          autorestart=true
          startretries=1
          startsecs=1
          redirect_stderr=true
          stderr_logfile=/var/log/apache-err.log
          stdout_logfile=/var/log/apache-out.log
          user=root
          killasgroup=true
          stopasgroup=true

`supervisor_programs` is an empty list by default; you can define a list of `programs` to be managed by Supervisor. If you set `state` to `present`, then a configuration file for the program (named `[program-name-here].conf`) will be added to the `conf.d` path included by the global Supervisor configuration. You can also manage program-level configuration on your own, outside this role, if you need more flexibility.

    supervisor_nodaemon: false

Set to `true` if you need to run Supervisor in the foreground.

    supervisor_log_dir: /var/log/supervisor

The location where Supervisor logs will be stored.

    supervisor_user: root
    supervisor_password: 'my_secret_password'

The user under which `supervisord` will be run, and the password to be used when connecting to Supervisor's HTTP server (either for `supervisorctl` access, or when viewing the administrative UI).

    supervisor_unix_http_server_password_protect: true
    supervisor_inet_http_server_password_protect: true

Password protection can be turned off for Unix HTTP and Inet HTTP by setting these variables to `false`, This would disable password protection for `supervisorctl` as well.

    supervisor_unix_http_server_enable: true
    supervisor_unix_http_server_socket_path: /var/run/supervisor.sock

Whether to enable the UNIX socket-based HTTP server, and the socket file to use if enabled.

> **Note**: By default, this role enables an HTTP server over a UNIX socket that can be accessed locally using the `_user` and `_password` defined earlier. Make sure you set a secure `supervisor_password` to prevent unauthorized access! (Or, if you don't need to HTTP server nor need to use `supervisorctl`, you should disable the UNIX http server by setting this variable to `false`).

    supervisor_inet_http_server_enable: false
    supervisor_inet_http_server_port: '*:9001'

Whether to enable the TCP-based HTTP server, and the interface and port on which the server should listen if enabled.

## Dependencies

None.

## OS Compatibility
This role ensures that it is not used against unsupported or untested operating systems by checking, if the right distribution name and major version number are present in a dedicated variable named like `<role-name>_stable_os`. You can find the variable in the role's default variable file at `defaults/main.yml`:

    role_stable_os:
      - Debian 10
      - Ubuntu 18
      - CentOS 7
      - Fedora 30

If the combination of distribution and major version number do not match the target system, the role will fail. To allow the role to work add the distribution name and major version name to that variable and you are good to go. But please test the new combination first!

Kudos to [HarryHarcourt](https://github.com/HarryHarcourt) for this idea!

## Example Playbook

    - hosts: all
      roles:
        - thorian93.ansible_role_supervisor

If you need to use `supervisorctl`, you can either use [Ansible's built-in `supervisorctl` module](http://docs.ansible.com/ansible/supervisorctl_module.html) for management, or run it like so (accounting for the variable path to the configuration directory):

    supervisorctl -c /etc/supervisor/supervisord.conf -u root -p [password] status all

## License

MIT / BSD

## Author Information

This role was created in 2017 by [Jeff Geerling](https://www.jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/).

This role was modified in 2019 by [Thorian93](http://thorian93.de/).