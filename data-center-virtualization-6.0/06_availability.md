<!---
https://github.com/forbesguthrie/vReferenceCards
Reference card for Data Center Virtualization 6.0
06_availability.md
-->

# Availability
## Maximums
**Maximums (per HA cluster)**: Hosts = 32 VMs = 3000  
**Maximums (FT)**: Disks per VM =16 vCPUs per VM=1 RAM per VM = 64GB VMs
per host = 4  
## Firewall
**Firewall Ports**: <u>HA</u> interhost 2050-2250, 8042-8045 TCP/UDP, <u>FT</u>
interhost 8100, 8200 TCP/UDP

**HA**: Single master, multiple slaves. If master fails, is shut down,
or removed from cluster then an election occurs. All hosts not in
standby, maintenance mode or disconnected participate in election. VMs
on disconnected hosts are not protected. <u>Master responsibilities</u>: •
monitors slaves • monitors VMs • restarts VMs on best host • reports
cluster HA health to vCenter. <u>Slaves</u>: • monitors runtime state •
reports state to master. Network heartbeat every second between master &
slaves. Hosts can use all VMkernel networks for heartbeat except vMotion
network (unless vMotion is on only available VMkernel network). If host
stops responding to heartbeat, liveness checks datastore heartbeat &
pings to management address. Host deemed Failed if both tests negative,
if it passes liveness check it is Network Isolated (host cannot ping
cluster isolation address) or Network Partitioned. Master monitors
Network Isolated/Partitioned VMs and restarts them if they power off.
<u>Host Isolation Response</u>: Action taken when Powered-on VMs are on
isolated host. Options: • power off • leave powered on (default) • shut
down (requires VMware tools). Cluster selects 2 datastores for heartbeat
(*das.heartbeatdsperhost* can increase to 5). Datastores used for
datastore heartbeat must be mounted by at least 2 hosts.

<u>HA Admission Control</u>: Can VMs power on when they violate availability
constraints at HA failover. Actions that change a reservation must
satisfy admission control. Secondary FT VMs added to calculation.
<u>Control policies</u>: • Host Failures Cluster Tolerates - adds Advanced
Runtime Info box showing slot size, total, used, available slots, total
VMs on, hosts, good hosts • % of Cluster Resources (up to 50%) • Specify
Failover Hosts (multiple allowed) - leaves host(s) unused until failure.
Policy Factors: • resource fragmentation • flexibility • VM diversity.
<u>Slot size</u>: represents VM CPU & memory resources needed for any powered
on VM. Distorted by large VM reservations. Avoided with
*das.slotCpuInMHz* or *das.slotMemInMB*.

**FT**: Uses anti-affinity rules to keep primary & secondary apart,
checked when primary powers on. VM-VM affinity applies to primary only,
VM-Host affinity applies to primary & secondary. <u>Requires</u>: HA & host
monitoring, host certificate checking (on by default), dedicated logging
NIC (not IPv6), compatible CPUs with same extensions & power mgt
features, Hardware Virtualization (HV), thick disks on shared storage
(VMDKs or vRDMs), supported guest OS, min 3 hosts in cluster.  
<u>Not supported</u>: snapshots, Storage vMotion, Linked Clones, hotplugging,
MSCS, VM backups, SMP, physical RDMs, PVSCSI, NPIV, VMDirectPath, 3D
video, EPT/RVI is automatically disabled. DRS only if cluster has EVC.
SiteSurvey can identify configuration issues. vLockstep Interval
typically needs \< 500ms.  
**vCenter Server Heartbeat**: vCenter replication & clustering, monitors
services including MSSQL, VUM & View Composer. Automated or Manual
failover/failback. Uses Active/Passive nodes with heartbeat.  
**MSCS**: • 2003 SP2 & 2008 (Failover Clustering) • 32 & 64bit • only 2
nodes clusters.  
<u>Not supported</u>: DRS on VMs, vMotion, FT, NPIV, VMW\_PSP\_RR,
FCoE/iSCSI/NFS based disks. SW iSCSI initiator in guest is supported.

| VMDK & RDM support       | VMDK                | Virtual RDM | Physical RDM             |
| :----------------------- | :-----------------: | :---------: | :----------------------: | 
| Cluster in a box (CIB)   | <u>Yes</u> (zeroed) | Yes         | No (not supported)       |
Cluster across boxes (CAB) | No                  | Only 2003   | <u>Yes</u> (recommended) |
Physical & VM (n+1)        | No                  | No          | <u>Yes</u>               |
Snapshots                  | Yes                 | Yes         | No                       |
SCSI target software       | No                  | No          | Yes                      |
Configure all RDMs before configuring VM's network settings or
initializing LUNs within windows. Add RDMs to 2nd SCSI controller i.e.
SCSI(1:x). Set SCSI bus sharing: • CIB = Virtual • CAB or N+1 = Physical  
**NLB, Exchange CCR & DAG**: does not use shared quorum disk, above
restrictions not applicable.  
**SQL Mirroring**: not considered clustering. Fully supported by VMware
with no restrictions.

## Links
HA Deep Dive
http://www.yellow-bricks.com/vmware-high-availability-deepdiv/  
HA and FT Error Messages http://kb.vmware.com/kb/1033634  
CPUs & guest OSes that support FT http://kb.vmware.com/kb/1008027  
MSCS Supported configurations (vSphere 4)
http://kb.vmware.com/kb/1037959  
MSCS Support on ESX/ESXi http://kb.vmware.com/kb/1004617  

