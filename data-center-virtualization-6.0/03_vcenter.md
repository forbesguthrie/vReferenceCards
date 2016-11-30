<!---
https://github.com/forbesguthrie/vReferenceCards
Reference card for Data Center Virtualization 6.0
03_vcenter.md
-->

# vCenter
**Maximums** (per vCenter): Hosts = 1,000 VMs = 15,000 (VCSA=10,000)
Running VMs = 10,000  Web Clients = 180 Windows Clients = 100 MAC addresses = 65,536 Datastore
clusters = 256  
**Maximums** (per datacenter): Hosts = 500  
**Maximums** (Linked mode): vCenters = 10 VMs = 50,000 Running VMs =
30,000 Hosts = 3,000

**HW**: <u>Min</u> - 2 CPU cores, 4GB RAM, 4GB disk space • <u>Medium</u> ≤50
hosts/500 VMs - 2 cores, 4GB RAM • <u>Large</u> ≤300 hosts/3000 VMs - 4
cores, 8GB RAM • <u>Extra large</u> ≤1000 hosts/10000 VMs - 8 cores, 16GB RAM  
**SW**: • 64bit Win (2003 SP2/R2 SP1, 2008 SP2/R2) • 64bit DSN (*SQL
Native* driver) • hostname ≤15 characters  
**Databases**: • SQL 2008 Express (≤5 hosts/50 VMs) • SQL 2005 SP3 • SQL
2008 SP1 or R2• Oracle 10g R2 or 11g R1 • IBM DB2 9.5 fix pack 5 or 9.7
fix pack 2. VUM only supports Oracle & MS SQL.  
User needs DBO rights. Default of max 50 simultaneous DB connections. MS
SQL don't use master DB.  

<mark>Missing sections: VCVA & DVD tools</mark>  

**FW Port** **Source Destination Protocol Description**  
80 Clients vCenter TCP Redirect to HTTPS  
389 vCenter Other vCenters TCP Linked Mode LDAP  
443 Clients vCenter TCP vSphere Client access  
443 vCenter ESXi TCP vCenter agent  
902 ESXi vCenter UDP Heartbeat  
902 vCenter ESXi UDP Host management, heartbeat  
903 Clients vCenter TCP VM Console  
Possible extras: 25(SMTP), 53(DNS), 80/443/623(DPM), 88(AD), 161/162(SNMP), 636(Linked vCenters), 1433 (MSSQL), 1521(Oracle), 5988/5989(CIM), 6500/8000(Dump Collector), 8000(vMotion), 8080/8443/60099(webservices), 9443 (Web Client),10109/10111/10443(Inventory service),51915(Auth proxy)

**Logs**: DB upgrade: <file>%TEMP%\\VCDatabaseUpgrade.log</file>  
vCenter agent: <file>/var/log/vmware/vpx/vpxa.log</file>  
vCenter (Win XP, 2000, 2003): <file>%ALLUSERSPROFILE%\\Application Data\\VMware\\VMware VirtualCenter\\Logs\\</file>  
vCenter (Win 7, 2008): <file>%ALLUSERSPROFILE%\\VMware\\VMware
VirtualCenter\\Logs\\</file>
(see KB in links below for description of different log files)  
VCVA logs <file>/var/log/vmware/vpx</file>  
Windows Client Install <file>%TEMP%\\vmmsi.log</file>  
Windows Client Service <file>%USERPROFILE%\\Local Settings\\Application
Data\\vpx\\viclient-x.log</file> (x=0-9)  
Guest customization - Win: <file>%WINDIR\\temp\\vmware-imc</file> -  Linux: <file>/var/log/vmwareimc/toolsDeployPkg.log</file>
  
**Default roles** (<u>System roles</u> - permanent, cannot edit privileges,
ESXi & vCenter. <u>Sample roles</u> - just vCenter):  
No access System - Default except users in Admin Group. Cannot view or change.  
Read only System - View state & details except console tab.  
Administrator System - All privileges. Default for members of the Admin Group, & AD ESX Admins.  
VM power user Sample - Interact with, change VM HW settings, snapshots &
schedule tasks.  
VM user Sample - Interact with, insert media & power ops. Cannot change
VM HW settings.  
Resource pool admin Sample - Create, modify child pools & assign VMs,
but not RP itself.  
Datastore consumer Sample - Allows space consumption of the datastore.  
Network consumer Sample - Allows hosts or VMs to be assigned to network.  

**Permissions**: pair user/group with role & associate with object.
<u>Role</u> - predefined set of privileges. Users initially granted *No
Access* role on new objects including datastores/networks. Logged in
users removed from domain keep permissions until next validation period
(default 24 hrs). <u>Tasks</u> - activities that don't complete immediately.
All roles allow schedule tasks by default. Can schedule tasks if user
has permission when task created. vCenter Local Admins have
Administrator role by default. Propagation is per permission, not
universal. Child permissions override those propagated. User permissions
override Group ones.  
Use *No Access* role to mask areas from users. Moving objects needs
permission on object, source & destination parent.  

**Licensing**: Essential Essential+ Standard Enterprise Enterprise+  
vRAM (per socket license) ------------------ 32GB ------------------
64GB 96GB  
vCPU ---------------------------- 8 way ------------------------ 32 way  
vpxa, Thin pro, VUM, VADP **Yes Yes Yes Yes Yes**  
vMotion, HA, vDR **Yes Yes Yes Yes**  
SLES (SUSE Linux Ent Server) for VMware **Yes Yes Yes**  
DRS, DPM, Storage vMotion, FT, VAAI, Hot add, Linked mode, |  
3<sup>rd</sup> party MPP, Orchestrator, vShield Zones, Serial port concentrator
|\> **Yes Yes**  
DVS, NIOC, SIOC, Host Profiles, Auto Deploy, Policy-driven Storage,
Storage DRS **Yes**

<u>vRAM</u> - Memory configured on all powered-on VMs. Consumed vRAM capped
at 96GB per VM. Only Essential & Essential+ has hard vRAM limit. CPU
licenses from same license level are pooled across linked mode vCenters.
Keys in vCenter not deployed add to entitlement. Add vRAM by adding
licenses or upgrading existing. Consumed vRAM is 12 month average. Can
create reports & alerts for consumed/entitled vRAM. Key assigned to host
via vCenter is persistent. <u>vSphere Hypervisor</u> - free, no connection to
vCenter , ≤32GB vRAM, only servers ≤32GB physical RAM, limited/read-only
vCLI & PowerCLI support, no SNMP support.  
<u>vSphere Desktop</u> - for VDI, functionality of Enterprise+ & unlimited
vRAM. Per powered-on desktops.  
<u>Expiring licenses</u>: vCenter - hosts are disconnected. ESXi - VMs
continue to run, cannot power-on new VMs.

**Statistics**: CPU, memory, datastore, disk, storage adapters/paths,
network, power, DRS, HA, mgt agents, system & VM ops. <u>Collection
Intervals</u> (time period stats archived in DB) frequency/retention is 5
mins - 1 day, 30 mins - 1 week, 2 hrs - 1 month, 1 day - 1 year.
<u>Real-time stats</u> (just performance charts) flat file on hosts & vCenter
memory (not in DB), frequency/retention is 20 secs - 30 mins, only
powered-on hosts & VMs. <u>Collection level</u> 1-4 for each interval, most
counters is 4 (default 1). <u>Reports</u> & <u>Maps</u> updated every 30 mins.
VMware Tools adds perfmon objects to Windows guests.

**Alarms**: notifications of selected events, conditions & states.
Composed of Trigger & Action. <u>Triggers</u>: condition, state or event.
<u>Actions</u>: responses to triggered alarms. Can disable action without
disabling alarm, but effects actions on all alarms. Disable for selected
object, child continues. Default alarms not preconfigured with actions.
Acknowledging alarm stops action, but alarm still visible. Reduce alarms
with tolerance range & trigger frequency (default 5 mins). Disconnect
hosts to suspend monitoring.

**Linked mode**: joins VCs. Global data: IP & ports, certificates.
licensing, user roles. Uses ADAM (AD App Mode) to store & sync data.
Instances can run under different domain accounts. Installed by domain
user who is admin on both machines. Requirements: DNS, 2-way trust if
different domains, time sync, DNS name matches hostname. Roles are
replicated, assignments of roles are not.

**Server settings**: Licensing (vCenter & 3.x hosts), Statistics
(intervals & DB size), Runtime Settings (unique ID, managed IP, name),
AD (timeouts, query limit, validation period), Mail, SNMP receivers,
Ports - http(s), client timeouts, Logging detail, DB connections
(default 50), DB retention, SSL host verification, Advanced Settings

**Host Profiles**: Policy to centrally configure & check compliance of
hosts. Set at host or cluster level. *Reference host* - host which
created profile. Exported profile format <file>.vpf</file>. When profile is detached,
settings persist on host/cluster. A*nswer File* contains host specific
input required by Auto Deploy - 1 per host. Host must be in Maintenance
Mode to apply profile, Auto Deploy hosts need reboot.

**Authentication Proxy**: No AD credentials on ESXi, just domain name &
proxy IP. Installer creates AD account prefixed with *CAM*. Authenticate
proxy to ESXi by importing SSL certificate, or push via Host Profiles.

**Web Client server**: Alternative to Windows Client. Cross-platform &
cross-browser (Adobe Flex plugin). Connects to vCenter (not to hosts
directly), register Client server first. Subset of Windows Client
functionality - Monitoring & VM deployment, no
host/cluster/datastore/network configuration

Admin tool https://localhost:9443/admin-app Web client
https://<*servername*>:9443/vsphere-client

**Guest Customization**: Guest OS must be on SCSI node 0:0. Requires
Perl in Linux guests. Windows guest Admin password must be blank for
customization to change it.

**vService**: Service dependency for vApps or VMs. vService Manager
monitors health: • Red - issue needs fixed in solution (the extension) •
Yellow - vService Manager is repairing • Green – OK

Resolution Path Troubleshooting Licensing http://communities.vmware.com/docs/DOC-16082  

Collecting diagnostic information for vCenter http://kb.vmware.com/kb/1011641  

Location of vCenter Server log files http://kb.vmware.com/kb/1021804  

Installing vCenter Server 5.0 best practices
http://kb.vmware.com/kb/2003790  

Upgrading to vCenter Server 5.0 best practices
http://kb.vmware.com/kb/2003866  

Sysprep file locations and versions http://kb.vmware.com/kb/1005593    

Firewall Ports http://kb.vmware.com/kb/1012382  

vCenter client shortcuts
http://www.jume.nl/articles/vmware/143-vcenter-client-shortcuts  

vSphere 5 Licensing, Pricing & Packaging http://www.vmware.com/files/pdf/vsphere\_pricing.pdf  

