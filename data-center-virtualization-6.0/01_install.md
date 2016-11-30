<!---
https://github.com/forbesguthrie/vReferenceCards
Reference card for Data Center Virtualization 6.0
01_install.md
-->

# ESXi Install
**HW requirements**: HCL compliant (see link below), 64bit x86 CPUs
(minimum of 2 cores), 2GB RAM, 5GB disk, password 6-64 characters.  
Set HW clock (BIOS) to UTC. VMFS only created on first disk. IDE/ATA
drives not supported for VMFS.  
ESXi Installable starts in evaluation mode (60 days) on first power-on
even if host is licensed. If no DHCP at install, link local IP used
169.254.x.x/16. Disconnect Fibre Channel connections prior to
installation.  

## Firewall
**FW Port** **Source** **Destination** **Protocol** **Description**  
22 SSH client ESXi TCP SSH server  
53 ESXi DNS server UDP DNS requests  
80 Clients ESXi TCP Redirects to HTTPS (443)  
123 ESXi NTP source UDP NTP (time) client  
427 ESXi CIM servers UDP CIM SLPv2 client to find server  
443 Clients, vCenter ESXi TCP HTTPS access  
902 ESXi ESXi TCP/UDP Migrate & provision  
902 Client ESXi UDP Access to VM console  
902 ESXi vCenter TCP/UDP Heartbeat  
5900-5964 ESXi ESXi TCP RFB for management tools-VNC  
5988 CIM server ESXi TCP CIM transactions over HTTP  
5989 vCenter/ESXi ESXi/vCenter TCP CIM XML over HTTPS  
8000 ESXi ESXi TCP vMotion requests  
<u>Possible extras</u>: 68(DHCP),161/162(SNMP),514(syslog), 1234/1235(HBR) &
HA,FT,NFS,iSCSI traffic  

| FW Port   | Source           | Destination  | Protocol | Description |
|-----------|------------------|--------------|----------|-------------|
| 22        | 22               | ESXi         | TCP      | SSH server  |
| 53        |                  |              |          |             |
| 80        |                  |              |          |             |
| 123       |                  |              |          |             |
| 427       |                  |              |          |             |
| 443       | Clients/ vCenter |              |          |             |
| 902       |                  |              | TCP/UDP  |             |
| 902       |                  |              |          |             |
| 902       |                  |              |          |             |
| 5900-5964 |                  |              |          |             |
| 5988      |                  |              |          |             |
| 5989      |                  | ESXi/vCenter |          |             |
| 8000      |                  |              |          |             |

**ESXi Partitions**: • 2 boot banks • 4GB VFAT scratch (system swap & vm-support info) – not required but uses ramdisk if not present, or can
use remote NFS partition • locker • 110MB diagnostic for core dumps (can
redirect to ESXi Dump Collector) • VMFS5 on each disk’s free space.  
Fresh install has GPT, upgraded keeps MBR style.  
Not supported: • ESXi Installable & Embedded on same host • Booting
multiple servers from 1 image.  

**Sources**: • <u>Boot</u> – CD, USB, PXE boot, remote access mounted ISO
(iLO, DRAC, RSA, etc) • <u>Script</u> – CD, USB, NFS, HTTP(S), FTP. Specify
location in *kernelopts* line of <file>boot.cfg</file>, or `ks=` boot option (`Shift` +
`O`) . ESXi5 cannot PXE boot from one image, then install different image.
Boot image is always the installed image. Default ks file at
<file>/etc/vmware/weasel/ks.cfg</file> (password is *mypassword*).  

**Destination**: – SATA (considered remote – no scratch), SAS, SCSI
disk, flash drive, FC or SW iSCSI (target IQN set in iBFT BIOS) SAN LUN,
PXE stateless (Auto Deploy).  

**Image Builder**: PowerCLI tool (server component & cmdlets) to create
images (Image Profile) with customized updates & drivers. Deploy image
as install CD or via Auto Deploy server. VIBs must pass dependency check
& meet acceptance level – VMwareCertified, VMwareAccepted,
PartnerSupported, CommunitySupported. `-AcceptanceLevel` parameter changes
level. Include <file>vmware-fdm</file> VIB if host will be in HA cluster. Clone a
published profile to create custom profile.  

**Auto Deploy** (stateless): loads ESXi images across network into
host’s memory every boot. Can set configuration via Host Profile (see
vCenter section). Server as Windows install or VCSA. 1-to-1 Auto Deploy
registered to vCenter. Hosts need BIOS firmware (not UEFI). Host DHCP
reservations recommended. VLAN tagged (trunked) boot NICs not
recommended. Multiple hosts rebooting can cause boot storm for Auto
Deploy server. Hosts require Dump Collector. Redirect logs to syslog
server or NFS datastore. PowerCLI Bulk Licensing useful for Auto Deploy.
Rebooted host stays in maintenance mode if vDS is used & vCenter is
unavailable. Can use VIBs, Images Profiles & Software Depots (online -
HTTP or offline - ZIP file) during install.  

<u>First boot</u> • set DHCP for IP and point to TFTP server for gPXE, add
rules to rules set • identify Image Profile • (optional) rule for Host
Profile • apply Active Rule Set.  

<u>Re-provisioning</u> (subsequent reboots) can change answer file, use
different image or host profile.  

<u>Components</u>: • Auto deploy server – manages state information, serves
images & host profiles • Rules engine – manages rules & rule sets •
Image Profile - matches sets of VIBs to host • Host Profile - machine
specific information • Answer File - host specific information the user
provided during first boot (only accessed via Host Profiles UI).  

<u>Rules engine</u>: • Rules – assigns Image Profiles, Host Profiles,
location within vCenter hierarchy, identifies host via MAC address,
SMBIOS asset tag, BIOS UUID, or IP address • Active Rule Set – maps
matching rules to hosts when image is requested • Working Rule Set -
test rules before making active.  

<u>Deployment Information</u>: • Image state - profile created by Image
Builder PowerCLI tool. Contains executable software • Configuration
state - from Host Profile • Dynamic state - runtime information in
memory, lost during reboot • VM state - VM auto-start info, managed by
vCenter but locally stored if HA is enabled • User Input – host profile
set to require user supplied host specific information, stored in answer
file.  
<mark>
**Deleted - probably not relevent for 6.0**  
**Post install**: test cables in correct VMNICs: ```watch -n 1 `esxcli network nic list```  
**Upgrade** from ESX/ESXi 4.x: • vCenter Upgrade Manager (needs 350MB free in <file>/boot</file>) • Interactive upgrade from CD or USB drive • Scripted upgrade. 5.0 upgrades to 5.x can also use • Auto Deploy (if used for 5.0 install) • `esxcli`.
</mark>

## Links
Installing ESXi 5.0 Best Practices http://kb.vmware.com/kb/2005099  
Upgrading to ESXi 5.0 Best Practices http://kb.vmware.com/kb/2005102  
Resolution Path – Troubleshooting Installs http://communities.vmware.com/docs/DOC-15789  
Hardware Compatibility Guide (“*HCL*”) http://www.vmware.com/go/hcl  
Troubleshooting vSphere Auto Deploy http://kb.vmware.com/kb/2000988



