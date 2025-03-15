# ansible-role: dnf-automatic

This role installs, configures and activates `dnf-automatic` via Ansible on hosts which use the dnf package manager. A possible use case is the automatic installation of security updates.

See [https://dnf.readthedocs.org/en/latest/automatic.html](https://dnf.readthedocs.org/en/latest/automatic.html) for more information about `dnf-automatic`.


## Role Variables

The variable names are mostly self-explanatory. Beside the fact that the names are role-name prefixed, the names are identical to the preferences for the `dnf-automatic` configuration file. See [https://dnf.readthedocs.org/en/latest/automatic.html#configuration-file-format](https://dnf.readthedocs.org/en/latest/automatic.html#configuration-file-format) for details.

In particular, the following variables (including their default values) are used:

```yaml
dnf_automatic_apply_updates: true
dnf_automatic_download_updates: true
dnf_automatic_network_online_timeout: 60
dnf_automatic_random_sleep: 0
dnf_automatic_upgrade_type: security
dnf_automatic_emit_via: stdio
dnf_automatic_system_name: "{{ ansible_nodename }}"
dnf_automatic_send_error_messages: false
dnf_automatic_command_format: cat
dnf_automatic_stdin_format: "{body}"
dnf_automatic_email_command_format: "mail -Ssendwait -s {subject} -r {email_from} {email_to}"
dnf_automatic_email_stdin_format: "{body}"
dnf_automatic_email_from: root
dnf_automatic_email_host: localhost
dnf_automatic_email_port: 25
dnf_automatic_email_tls: "no"
dnf_automatic_email_to: root

dnf_automatic_base_overrides: {}
```

This default configuration sets `dnf-automatic` up to automatically download and install only security updates.

Note that the `dnf_automatic_base_overrides` dictionary can be used to override arbitrary preferences from the base dnf configuration file for `dnf-automatic`.

In addition, `dnf_automatic_reboot` can be set to true to perform automatic reboots when installed updates require it:

```yaml
dnf_automatic_reboot: false
dnf_automatic_reboot_dependencies: yum-utils
dnf_automatic_reboot_OnCalendar: "03:00"
dnf_automatic_reboot_AccuracySec: "15s"
dnf_automatic_reboot_Description: "dnf-automatic-reboot"
dnf_automatic_reboot_ExecStart: "/bin/bash -c '/bin/needs-restarting -r || /sbin/reboot'"
```


## Dependencies

No dependencies needed.


## Example Playbook

This example playbook deploys `dnf-automatic` on all hosts but is configured such that all updates are installed automatically, not only security updates.

```yaml
- name: Example playbook
  hosts: all
  remote_user: root
  roles:
  - { role: exploide.dnf-automatic, dnf_automatic_upgrade_type: default }
```

This example playbook deploys `dnf-automatic` to install security updates only, and deploys additional timer to reboot at 4:00 am when required:

```yaml
- name: Example playbook with auto reboot
  hosts: all
  remote_user: root
  roles:
  - { role: exploide.dnf-automatic, dnf_automatic_reboot: true, dnf_automatic_reboot_OnCalendar: "04:00" }
```


## License

MIT
