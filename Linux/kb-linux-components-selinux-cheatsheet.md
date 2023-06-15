# KB Linux SELinux cheat sheet

- [KB Linux SELinux cheat sheet](#kb-linux-selinux-cheat-sheet)
  - [SELinux packages](#selinux-packages)
  - [SELinux States and Modes](#selinux-states-and-modes)
    - [SELinux modes](#selinux-modes)
      - [Analysing and troubleshooting SELinux messages](#analysing-and-troubleshooting-selinux-messages)
      - [Changing to permissive mode](#changing-to-permissive-mode)
        - [Prerequisites](#prerequisites)
        - [Procedure](#procedure)
      - [Changing to enforcing mode](#changing-to-enforcing-mode)
      - [Enabling SELinux on systems that previously had it disabled](#enabling-selinux-on-systems-that-previously-had-it-disabled)
  - [Troubleshooting problems related to SELinux](#troubleshooting-problems-related-to-selinux)
    - [Identifying SELinux denials](#identifying-selinux-denials)
  - [Sources](#sources)

## SELinux packages

- Verify which SELinux packages are installed on your system
  - `sudo rpm -aq | grep selinux`
- Make sure the following packages are installed
  - `sudo yum install policycoreutils policycoreutils-python setools setools-console setroubleshoot`

- `policycoreutils` and `policyoreutils-python` contain several management tools to administer your SELinux environment and policies.
- `setools` provides command line tools for working with SELinux policies. Some of these tools include, `sediff` which you can use to view differences between policies, `seinfo` a tool to view information about the components that make up SELinux policies, and `sesearch` used to search through your SELinux policies. `setools-console` consists of `sediff`, `seinfo`, and `sesearch`. You can issue the `--help` option after any of the listed tools in order to view more information about each one.
- `setroubleshoot` suite of tools help you determine why a script or file may be blocked by SELinux.

## SELinux States and Modes

- To disable SELinux, update your SELinux configuration file using the text editor of your choice. Set the SELINUX directive to disabled as shown in the example.

```bash
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

Verify the status and state of SELinux by using `sestatus`.

```bash
[root@linuxhost ~]# sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   permissive
Mode from config file:          permissive
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Memory protection checking:     actual (secure)
Max kernel policy version:      33
```

### SELinux modes

When SELinux is enabled, it can run in either enforcing or permissive modes.

- In enforcing mode, SELinux enforces its policies on your system and denies access based on those policies. Use the following command to view SELinux policy modules currently loaded into memory:
  - `sudo semodule -l`
- Permissive mode does not enforce any of your SELinux policies, instead, it logs any actions that would have been denied to your /var/log/audit/audit.log file.
- You can check which mode your system is running by issuing the following command:
  - `sudo getenforce`
- To place SELinux in permissive mode, use the following command:
  - `sudo setenforce 0`
  >Please note that Changes made with `setenforce` do not persist across reboots
- Permissive mode is useful when configuring your system, because you and your system’s components can interact with your files, scripts, and programs without restriction. However, you can use audit logs and system messages to understand what would be restricted in enforcing mode. This will help you better construct the necessary policies for your system’s user’s and programs.
- You can set individual domains to permissive mode while the system runs in enforcing mode. For example, to make the httpd_t domain permissive:
  - `# semanage permissive -a httpd_t`
  >Note that permissive domains are a powerful tool that can compromise security of your system. It is recommended to use permissive domains with caution, for example, when debugging a specific scenario.

#### Analysing and troubleshooting SELinux messages

Use the sealert utility to generate a report from your audit log. The log will include information about what SELinux is preventing and how to allow the action, if desired.

- `sudo sealert -a /var/log/audit/audit.log`

Below is an example of this output

```bash
--------------------------------------------------------------------------------

SELinux is preventing zabbix_agentd from getattr access on the file /proc/kcore.

*****  Plugin catchall (100. confidence) suggests   **************************

If you believe that zabbix_agentd should be allowed getattr access on the kcore file by default.
Then you should report this as a bug.
You can generate a local policy module to allow this access.
Do
allow this access for now by executing:
# ausearch -c 'zabbix_agentd' --raw | audit2allow -M my-zabbixagentd
# semodule -X 300 -i my-zabbixagentd.pp


Additional Information:
Source Context                system_u:system_r:zabbix_agent_t:s0
Target Context                system_u:object_r:proc_kcore_t:s0
Target Objects                /proc/kcore [ file ]
Source                        zabbix_agentd
Source Path                   zabbix_agentd
Port                          <Unknown>
Host                          <Unknown>
Source RPM Packages
Target RPM Packages
SELinux Policy RPM            selinux-policy-targeted-36.10-1.fc36.noarch
Local Policy RPM              zabbix-selinux-5.0.21-1.fc36.noarch
Selinux Enabled               True
Policy Type                   targeted
Enforcing Mode                Permissive
Host Name                     pbl-ldt-p-11.pblm.local
Platform                      Linux pbl-ldt-p-11.pblm.local
                              5.18.5-200.fc36.x86_64 #1 SMP PREEMPT_DYNAMIC Thu
                              Jun 16 14:51:11 UTC 2022 x86_64 x86_64
Alert Count                   3
First Seen                    2022-06-17 11:45:40 CEST
Last Seen                     2022-06-17 12:48:16 CEST
Local ID                      d2b7c127-75a8-4311-99cd-d263867a0aee

Raw Audit Messages
type=AVC msg=audit(1655462896.76:453): avc:  denied  { getattr } for  pid=1300 comm="zabbix_agentd" path="/proc/kcore" dev="proc" ino=4026532030 scontext=system_u:system_r:zabbix_agent_t:s0 tcontext=system_u:object_r:proc_kcore_t:s0 tclass=file permissive=1


Hash: zabbix_agentd,zabbix_agent_t,proc_kcore_t,file,getattr
```

The report also provides the commands to correct this behaviour if you believe this should be the case.

#### Changing to permissive mode

Use the following procedure to permanently change SELinux mode to permissive. When SELinux is running in permissive mode, SELinux policy is not enforced. The system remains operational and SELinux does not deny any operations but only logs AVC messages, which can be then used for troubleshooting, debugging, and SELinux policy improvements. Each AVC is logged only once in this case.

##### Prerequisites

- The selinux-policy-targeted, libselinux-utils, and policycoreutils packages are installed on your system.
- The selinux=0 or enforcing=0 kernel parameters are not used.

##### Procedure

- Open the /etc/selinux/config file in a text editor of your choice, for example:
  - `vi /etc/selinux/config`
- Configure the SELINUX=permissive option:

```bash
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#       enforcing - SELinux security policy is enforced.
#       permissive - SELinux prints warnings instead of enforcing.
#       disabled - No SELinux policy is loaded.
SELINUX=permissive
# SELINUXTYPE= can take one of these two values:
#       targeted - Targeted processes are protected,
#       mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

- Restart the system
  - `reboot`
- After the system restarts, confirm that the getenforce command returns Permissive

```bash
$ getenforce
Permissive
```

#### Changing to enforcing mode

- After changing to enforcing mode, SELinux may deny some actions because of incorrect or missing SELinux policy rules. To view what actions SELinux denies, enter the following command as root:
  - `ausearch -m AVC,USER_AVC,SELINUX_ERR,USER_SELINUX_ERR -ts today`
- Alternatively, with the `setroubleshoot-server` package installed, enter:
  - `grep "SELinux is preventing" /var/log/messages`
- If SELinux is active and the Audit daemon (auditd) is not running on your system, then search for certain SELinux messages in the output of the dmesg command:
  - `dmesg | grep -i -e type=1300 -e type=1400`

#### Enabling SELinux on systems that previously had it disabled

To avoid problems, such as systems unable to boot or process failures, follow this procedure when enabling SELinux on systems that previously had it disabled.

>When systems run SELinux in permissive mode, users and processes might label various file-system objects incorrectly. File-system objects created while SELinux is disabled are not labeled at all. This behavior causes problems when changing to enforcing mode because SELinux relies on correct labels of file-system objects.
>
>To prevent incorrectly labeled and unlabeled files from causing problems, SELinux automatically relabels file systems when changing from the disabled state to permissive or enforcing mode. 
>
>Before rebooting the system for relabeling, make sure the system will boot in permissive mode, for example by using the enforcing=0 kernel option. This prevents the system from failing to boot in case the system contains unlabeled files required by systemd before launching the selinux-autorelabel service.

- Enable SELinux in permissive mode, see [Changing to permissive mode](#changing-to-enforcing-mode)
- Restart your system
- Check for SELinux denial messages.For more information, see [Identifying SELinux denials](#identifying-selinux-denials).
- Ensure that files are relabeled upon the next reboot:
  - `fixfiles -F onboot`
  - This creates the `/.autorelabel` file containing the `-F` option.

## Troubleshooting problems related to SELinux

### Identifying SELinux denials

Follow only the necessary steps from this procedure; in most cases, you need to perform just step 1.

1. When your scenario is blocked by SELinux, the `/var/log/audit/audit.log` file is the first place to check for more information about a denial. To query Audit logs, use the `ausearch` tool. Because the SELinux decisions, such as allowing or disallowing access, are cached and this cache is known as the Access Vector Cache (AVC), use the `AVC` and `USER_AVC` values for the message type parameter, for example:
   1. `ausearch -m AVC,USER_AVC,SELINUX_ERR,USER_SELINUX_ERR -ts recent`
If there are no matches, check if the Audit daemon is running. If it does not, repeat the denied scenario after you start `auditd` and check the Audit log again.
2. In case `auditd` is running, but there are no matches in the output of ausearch, check messages provided by the `systemd` Journal:
   1. `journalctl -t setroubleshoot`
3. If SELinux is active and the Audit daemon is not running on your system, then search for certain SELinux messages in the output of the `dmesg` command:
   1. `dmesg | grep -i -e type=1300 -e type=1400`
4. Even after the previous three checks, it is still possible that you have not found anything. In this case, AVC denials can be silenced because of `dontaudit` rules.To temporarily disable `dontaudit` rules, allowing all denials to be logged:
   1. `semodule -DB`
After re-running your denied scenario and finding denial messages using the previous steps, the following command enables dontaudit rules in the policy again:
   1. `semodule -B`
5. If you apply all four previous steps, and the problem still remains unidentified, consider if SELinux really blocks your scenario:
   1. Switch to permissive mode:
      1. `setenforce 0`
   2. Repear your scenario

If the problem still occurs, something different than SELinux is blocking your scenario.

## Sources

- [A Beginner's Guide to SELinux on CentOS 8](https://www.linode.com/docs/guides/a-beginners-guide-to-selinux-on-centos-8/)
- [Red Hat: Getting Started with SELinux | RedHat.com](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/using_selinux/getting-started-with-selinux_using-selinux)
- [Troubleshooting problems related to SELinux | RedHat.com](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/using_selinux/troubleshooting-problems-related-to-selinux_using-selinux#identifying-selinux-denials_troubleshooting-problems-related-to-selinux)
- [How to troubleshoot SELinux policy violations | RedHat.com](https://www.redhat.com/sysadmin/diagnose-selinux-violations)