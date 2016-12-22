<!---
https://github.com/forbesguthrie/vReferenceCards
Reference card for Data Center Virtualization 6.0
02_hosts.md
-->

# ESXi Hosts
## Maximums
**Maximums (per host)**: Logical CPUs (incl HT) = 320 RAM = 4TB VMs = 512 NUMA nodes = 16   
vCPUs = 4096 vCPUs per core = 32

**Logs**: All logs in <file>/var/log/</file> directory (sym links from <file>/var/run/log</file>).
View host logs via: • DCUI • ESXi Shell • Extract `vm-support` bundle •
http://hostname/host • vCLI `vifs` • vSphere Client connected to host  
<file>auth.log</file> ESXi Shell authentication  
<file>esxupdate.log</file> ESXi patches/updates  
<file>fdm.log</file> HA logs  
<file>hostd.log</file> Host management (VM & host events; Client, vpxa, SDK
connections)  
<file>shell.log</file> ESXi Shell usage (enable/disable & commands  
<file>sysboot.log</file> VMkernel & module startup  
<file>syslog.log</file> Management service initialization, watchdogs, scheduled
tasks, DCUI  
<file>vmkernel.log</file> Core VMkernel logs (devices, storage/network device/driver
events & VM startup)  
<file>vmkwarning.log</file> VMkernel warnings & alerts  
<file>vmksummary.log</file> ESXi startup/shutdown, uptime, VMs running, service usage  
<file>vpxa.log</file> vCenter vpxa agent   

## Shell Commands
`--help` for esxcli namespaces & commands relative to location. `localcli` bypasses hostd  
Startup level for management services (& lists all services): `chkconfig –-list`  
Restart all management services: `/sbin/service.sh restart`  
Restart single service (& start|stop|status available): `/etc/init.d/`<*service*> `restart`  
<u>Common services</u>: • hostd (primary ESXi daemon) • vpxa (vCenter agent) • vmware-fdm (HA)  
Backup host configuration: `vicfg-cfgbackup -s /tmp/`<*hostname*>`.cfgbak`    
(restore `-l`, force restore to different build number `-f`)  
Export detailed configuration file: `esxcfg-info > /tmp/esxcfg-info.txt`   
Gather debugging report: `vm-support -w /tmp`  
List running VMs (before maintenance): `esxcli vm process list`  
Resource usage: `esxtop` (Shell) `resxtop` (vCLI). Customize & save: `W`  
(updates <file>.esxtop50rc</file> file)
List CPU details: `esxcli hardware cpu list`  
Show CPU supported functions: `esxcli hardware cpu global get`  
Show memory and NUMA nodes: `esxcli hardware memory get`  
List free memory allocated to ramdisks: `esxcli system visorfs ramdisk list`   
Show version information for ESXi: `esxcli system version get`  
Show the host's acceptance level: `esxcli software acceptance get`  
Show all the installed VIBs: `esxcli software vib list`  
Detailed information on installed VIBs: `esxcli software vib get`  
Show syslog configuration: `esxcli system syslog config get`  
Show logging config for each log: `esxcli system syslog config logger get`  
Show remote coredump config: `esxcli system coredump network get`  
Lists firewall status & actions: `esxcli network firewall get`  
Lists firewall rulesets: `esxcli network firewall ruleset list`  
Refresh firewall after adding new ruleset: `esxcli network firewall refresh`  
Show description of VMkernel error: `vmkerrcode` <*error_code_number*>  
Lists drivers loaded at startup: `esxcli system module list`  
List advanced options: `esxcli system settings advanced –l`  

**CPU Power management policies**: • <u>Not Supported</u> - no host support
or disabled in BIOS • <u>High Performance</u> - only used when BIOS warning •
<u>Balanced</u> (default) - conservative, shouldn’t affect performance • <u>Low
Power</u> - aggressive power management, can lower performance • </u>Custom</u>  
**Memory**: Host reclaims memory from VM by: • <u>TPS</u> (Transparent Page
Sharing) – “RAM dedupe”, PSHARE in esxtop • <u>Balloon driver</u>
(vmmemctl) - forces guest to use native algorithms (guest swap) •
<u>Memory compression</u> • <u><file>.vswp</file> file</u> (host level swapping). Local or
networked SSD is tagged by VMkernel as optimal swap location to reduce
impact. During contention, host memory allocated based on shares &
working set size (recent activity). Idle memory is taxed progressively
to prevent VM hoarding. Guest swap should be ≥ (vRAM - Reservation) x
65%, or balloon driver can cause guest kernel panic. Memory faults can
be detected & quarantined to reduce chance of a PSOD (hardware
dependent).  
**NUMA** (Non-Uniform Memory Access): CPUs have localized memory. NUMA
scheduler controls VM distribution across host memory to dynamically
optimize CPU load & memory locality for VMs.  
**Firewall**: Define service’s port/protocol ruleset: <file>/etc/vmware/firewall/service_\<*name\>*.xml</file> (then refresh)  
**PAM** (Pluggable Authentication Modules) plugins: <file>/etc/pam.d/vmware-authd</file>. Default password compliance plugin: <file>pam\_passwdqc.so</file>. No restrictions on root password. Defaults for
non-root users: password retries = 3, minimum password length = 8,
shorter passwords if Characters Classes mixed (upper, lower, digits &
other) 1 or 2 CC – min 8, 3 CC – min 7, 4 CC – min 6. First character as
upper or last character as digit not counted.  
**DCUI (Direct Console UI)**: • Configures host defaults • Sets up
administrative access • Troubleshooting. High contrast video mode `F4`.
Can redirect DCUI to serial cable via Client or boot option (`Shift` + `O`).
Restarting Mgt agents effects <file>/etc/init.d</file> processes: hostd
(mgmt-vmware), ntpd (time), sfcbd (CIM broker), slpd (discover/advertise
services), wsman (share mgt info via SOAP), vobd (error reporting) & fdm
(HA agent) if installed. To isolate ESXi host from DRS/HA cluster,
disable management network.  
 Management Network Test: pings DG, primary DNS nameserver, secondary
DNS, resolves hostname.  
**VIBs:** can update image profiles or 3<sup>rd</sup> party extensions. Updates
firewall ruleset & refreshes hostd.  
**Repair mode**: On ESXi Installable CD, overwrites all configuration
data. Serial number lost on repair, but restored when backup
configuration applied. Configuration reset deletes root password,
removes configuration & reboots host. Storage needs reconfigured &
re-register VMs.  
**Recovery Mode**: Invoked during boot with `Shift` + `R`. Reverts to
previous image before last update.  
**SNMP** agent embedded in hostd (disabled by default). Enable via
`vicfg-snmp`. Can send traps & receive polling (GET) requests.  
**Syslog** service is vmsyslogd.  
**Host certificates**: <file>/etc/vmware/ssl/rui.crt</file> (public key) & <file>rui.key</file> (private key).
Recreate: `/sbin/generate-certificates`  
**Lockdown mode**: Forces operations via vCenter. Mode available only
when host connected to vCenter. Enabling/disabling via DCUI wipes host
permissions – set via vCenter. DCUI restricted to root, Shell & SSH
disabled for all users, vSphere client & CIM monitoring only via vCenter
not direct to host. <u>Normal Mode</u>: DCUI, Shell, SSH & CIM access allowed
to root & Admin role users. vSphere Client access based on ESXi
permissions. <u>Total lockdown mode</u>: also disables root access to the
DCUI, if vCenter access is lost you must reinstall ESXi to regain
control. root & vpxuser are only users not assigned No Access role on
hosts by default, but have same rights as the Administrator role.  
## Links
Firewall Ports http://kb.vmware.com/kb/1012382  
Location of ESXi 5.0 log files http://kb.vmware.com/kb/2004201  
Video: Restarting management agents on an ESX/ESXi server http://kb.vmware.com/kb/1003490  
Interpreting esxtop Statistics http://communities.vmware.com/docs/DOC-9279  
Collecting diagnostic info using the vm-support command http://kb.vmware.com/kb/1010705  
Decoding Machine Check Exception output after purple screen http://kb.vmware.com/kb/1005184  

