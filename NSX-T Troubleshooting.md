#vmware/nsx/routing 


>[!Info]
> - Troubleshooting Routing Issues
>	- VM  ➤  DFW  ➤  T1-DR  ➤  Physical (Overlay to Edge)  ➤  T1-SR  ➤  Edge Firewall  ➤  T0-DR  ➤  T0-SR  ➤  Edge Firewall  ➤  Upstream Router
>	- Check L2 before L3
>	- Use Traceflow as starting point
>		- Traceflow from VM to outside destination i.e. 8.8.8.8
>		- Traceflow from VM to VM for internal routing issues
>	- Routing Tables - DR (forwarding) and SR (route)
>		- Check Routing Table on Transport Nodes using `nsxcli`
>		- Check Routing Table on T0 SR
>	- Check Firewall, NAT and other dependencies
>	- ALWAYS check the return path
>
> - Troubleshooting Overlay Issues
>	- Transport Node does not have a valid TEP IP
>	- Edge VM does not have a valid TEP IP
>	- Jumbo MTU required on TEP VLAN (1600+, 1700+ preferred)
>	- Jumbo MTU not enabled on physical network or Edge VM vDS
>
> - BFD (Bi-Directional Forwarding Detection) 
>	- Network protocol used to detect faults between two network endpoints connected by a link
>	- Often used between routers to ensure fast failover
>	- Normal routing protocols can take some time to failover; BFD allows sub-second failover
>	- NSX-T uses BFD to ensure host-to-host ( TN ⇆ TN | TN ⇆ Edge | Edge  ⇆ Edge ) communication is up (`vmk10` = TEP IP (nsx-overlay))
>	- BFD Tunnels only available if Transport node has active workloads (VMs)
>	- Could be used for BGP between Edge SR and upstream Routers.

❯ 

### NSX Manager
---

##### Setup Troubleshooting

```bash
st en                              Enter 'root' privileged Mode
set cli-timeout 0                  Disable CLI timeout

list                               Retrive all available commands
get services                       Get the status of all the services and its state in NSX-T Manager
get service ssh                    Get SSH service status
start service ssh                  Start the SSH service
/etc/init.d/ssh restart            Restart the SSH service
set service ssh start-on-boot      Set the SSH service to autostart when the VM is powered on
clear service ssh start-on-boot     Disable/remove the service to start on boot

get cluster status [verbose]       Query Management Cluster Status               
get cluster config                 View the cluster configuration and which node is running which cluster component
detach node <node-id>              Detach a NSX Manager node from Cluster
get nodes                          List the transport nodes registered with NSX Manager
get managers                       Query the Managers Connection Status

get transport-nodes status         List all ESXi hosts to get the transport Node UUIDS
get transport-node <uuid> status   List the transport Node Status
get transport-node <uuid> vtep     List the Transport Node vtep information
get transport-node <uuid> vifs     Lists the VIF UUID of a VM connected to Segment on a Transport Node


# Join NSX Manager to Cluster
get cluster config | find Id:      # Retrive Cluster ID
get certificate api thumbprint     # Retrieve NSX Manager Certificate Thumbprint
join NSX-Manager-ip-address cluster-id <cluster-id> thumbprint <thumbprint> username <username> password <password>

set service manager logging-level debug      Set logging level on NSX Manager

# Configure a remote syslog server
set logging-server <hostname-or-ip-address[:port]> proto <protocol> level <level>

get log-file policy.log <follow>       Read the Policy Manager log
get log-file syslog <follow>           Read the syslog log

# Setup packet capture
start capture interface <interface-name> [file <filename>] [count <packet-count>] [expression <expression>]

```



##### Logical Routing
```bash
get logical-router                                      View the list of logical routers
get logical-router <uuid>                               View the information about a logical router
get logical-router <uuid> interfaces                    View the list of logical router interfaces
get logical-router <uuid> interface <interface-id>      View the logical router interface information
get logical-router <uuid> route                         View the Routers on a logical router
get transport-node status             List the NSX Edge nodes registered with NSX Manager and their associated controller
```


##### User Account Administration
```bash
set user <username> [password <password> [old-password <old-password>]      Change local user password
set auth-policy minimum-password-length <password-length>                   Set Password length
set auth-policy api lockout-period <lockout-period>                         Set UI and API Authentication Policy
set auth-policy api lockout-reset-period <lockout-reset-period>             Set UI and API Authentication Policy
set auth-policy api max-auth-failures <auth-failures>                       Set UI and API Authentication Policy
set auth-policy cli lockout-period lockout-period <lockout-period>          Set CLI authentication policy
```

❯❯

### NSX Edges
---

##### Setup Troubleshooting
```bash
st en                                     Enter root privileged Mode
get service ssh                           Verify SSH service status
start service ssh                         Start the SSH service
set service ssh start-on-boot             Set the SSH service to autostart when the VM is powered on
get service ssh                           Verify that the SSH service is running and Start on boot is set to True
get configuration                         View the Edge configuration
get node-uuid                             Display the Edge node UUID
get interfaces                            View the Edge interfaces
get managers                              Query the connection to the NSX Managers
get host-switches                         View all the host switches information
get tunnel-ports                          View the tunnel port information

# Setup packet capture
set capture session <session-number> interface <port-uuid> direction <direction>
# Remove captured session information
del capture session 1
```

##### Logical Routing
```bash
get vteps                                  View the VTEPs
get logical-routers                        View the logical routers
get logical-router <uuid>                  View the logical router information
get logical-routers stats                  View the logical router statistics
get logical-router <uuid> interfaces       View the logical router interfaces
get logical-router <uuid> neighbor         View the logical router neighbour
get logical-router interfaces stats        View the logical router interfaces statistics
get logical-router                         View the logical router bgp neighbour

# To enter into the VRF construct
vrf <VRF-ID-of-Tier-0-SR>

# View the bgp neighbor of a Tier-0 SR
(tier0_sr)> get bgp neighbor
(tier0_sr)>	get bgp neighbor summary

# View the interfaces on a Tier-0 SR
(tier0_sr)> get interfaces

# View the forwarding table
(tier0_sr)>	get forwarding

# View the Routers
(tier0_sr)> get route

# View the BFG configuration
(tier0_sr)> get bfd-config

# View BGP IPv4 route info
(tier0_sr)> get bgp ipv4

# View the Tier-1 or Tier-0 distributed router routing information
(tier[0|1]_dr)> get forwarding
```

##### Firewall
```bash
get firewall interfaces                      View the list of Firewall Interfaces
get firewall <interface_id> ruleset rule     View the Firewall Ruleset and Rules
```


##### Load Balancer
```bash
Display load balancers configuration	get load-balancers
Display load balancer pool config	get load-balancer <lb-uuid> pools
Display load balancer virtual servers configuration	get load-balancer <lb-uuid> virtual-servers
Display specific load balancer virtual servers info	get load-balancer <lb-uuid> virtual-server Virtual_Server_ID
Display load balancer status	get load-balancer <lb-uuid> status
Display load balancer virtual servers stats	get load-balancer <lb-uuid> virtual-servers stats
Display load balancer stats	get load-balancer <lb-uuid> stats
```

##### DHCP
```bash
Retrieve the DHCP server information	get dhcp servers
View the configured DHCP pools	get dhcp ip-pools
List the leased IP addresses	get dhcp leases
```

##### VPN
```bash
Verify L2VPN session is active, identify the peers, and ensure that the tunnel status is up	get ipsecvpn session active
Verify that the sessions are up	get ipsecvpn session status
Check whether the ipsecvpn session is up between the local and remote peer	get ipsecvpn session summary
Get the l2vpn session, tunnel, and IPSEC session numbers, and check that the status is UI	get l2vpn sessions
Get statistical information of the local and remote peers, whether the status is UP, count of packets received, bytes received (RX), packets transmitted (TX), and packets dropped, malformed, or loops	get l2vpn session stats
Get the session configuration information	get l2vpn session config
```

❯❯❯

### ESXi Transport Nodes
---

##### Setup Troubleshooting
```bash
# List the VIBs loaded on ESXi:
esxcli software vib list | grep -e nsx -e vsip

# List all the NSX-T modules currently loaded in the system
esxcli system module list | grep nsx

# Check the User World Agents (UWA):	 
/etc/init.d/nsx-mpa          status | start | stop | restart
/etc/init.d/nsx-proxy        status | start | stop | restart
/etc/init.d/nsx-opsagent     status | start | stop | restart
/etc/init.d/nsxa             status | start | stop | restart

# Check UWA Connection
esxcli network ip connection list | grep 1235            # Port 1235 to Controllers
esxcli network ip connection list | grep 5671            # Port 5671 to NSX Manager

esxcli network nic list                                  # List Physical NICs/vmnic
esxcli network nic get -n vmnic3                         # Physical NIC details
esxcli network ip interface ipv4 get                     # List vmk NICs with IP addresses/MAC/MTU etc. (vmk10=TEP, vmk50=containers)

esxcli network ip interface list                         # Details of each vmk NIC, including vDS information
esxcli network ip interface list --netstack=vxlan        # Details of netstack IP Stack created on ESXi
vmkping ++netstack=vxlan <host-IP> -s <packet-size>      # Ping from a VXLAN TCP/IP Stack
esxcli network ip route ipv4 list -N vxlan               # View routing table of VXLAN-dedicated TCP/IP stack
esxcli network ip neighbor list -N vxlan                 # View ARP table of VXLAN dedicated TCP/IP stack

# List Connectivity to NSX-T Managers and Controllers (Control plane)
> nsxcli
get managers
get controllers

# SYSLOG Setup	 
esxcli network firewall ruleset set -r syslog -e true
esxcli system syslog config set --loghost=<hostname-or-ip-address[:port]>
esxcli system syslog reload
```


##### Logical Routing
```bash
# login as 'root' and use 'nsxcli'
> nsxcli
get logical-routers
get logical-router <uuid>
get logical-router <uuid> forwarding
get logical-router <uuid> forwarding <prefix>
get logical-router <uuid> forwarding ipv4
get logical-router <uuid> forwarding ipv6
get logical-router <uuid> igmp membership
get logical-router <uuid> igmp membership <ip-address>
get logical-router <uuid> interface <uuid>
get logical-router <uuid> interface <uuid> policy-table-ref
get logical-router <uuid> interface <uuid> stats
get logical-router <uuid> interface <uuid> stats ipv4
get logical-router <uuid> interface <uuid> stats ipv6
get logical-router <uuid> interfaces
get logical-router <uuid> multicast load-balancing <ip-address>
get logical-router <uuid> neighbor <ip-or-mac-address>
get logical-router <uuid> neighbors
get logical-router-policy-table <policy-table-id>
get logical-router-policy-tables

Example:
# Distributed Router (DR) Information
get logical-routers
get logical-router ee30d2ea-90bc-4c83-978f-99decdbf3eaa forwarding
get logical-router ee30d2ea-90bc-4c83-978f-99decdbf3eaa interface 11fe0dd60-39e1-4675-9978-7788653549da

```

##### Firewall
```bash
View the Firewall Status	get firewall status
View the Firewall Rules applied at the VIF with Address Sets	get firewall <vif_uuid> addrsets
 	get firewall <vif_uuid> profile
List all the VMs dvFilter Names	summarize-dvfilter
List all the VMs dvFilter Names associated with a VM and limit the response to 16 lines	Summarize-dvfilter | grep -A16 <SERVER_NAME>
List the Firewall Rules applied on DvFilter	vsipioctl getrules -f <filtername>
View the Firewall Configuration for a given dvFilter name	vsipioctl getfwconfig -f <dvfilter-name>
View the DVSPort ID and MAC address associated with a VM	nsxdp-cli -c get ports | egrep -A1 "<VM_NAME|MAC"
View the number of packets dropped on a host switch	nsxdp-cli swsec get stats -dvs <Overlay_N-VDS> --dport <DVSPort-UUID>
```


❯❯❯❯

### References
---

[NSX-T Command line Reference](https://vdc-download.vmware.com/vmwb-repository/dcr-public/cc42e3c1-eb34-4567-a916-147e79798957/8264605c-a5e1-49a8-b603-cc78621eeeab/cli.html)
[NSX-T Command Line Cheat Sheet](https://www.simongreaves.co.uk/nsx-t-command-line-cheat-sheet/)

