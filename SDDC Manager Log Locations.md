
SDDC Manager has different logs for different functions. This is to help you identify what log to look at depending on what you are trying to do in SDDC Manager.

All of the main logs are located under  `/var/log/vmware/vcf/<service>`

Login as `vcf` and `su` to `root` in order to view.

**Bringup**
```
/var/log/vmware/vcf/bringup/bringup.log or 
/var/log/vmware/vcf/bringup/bringup-debug.log
```

**Domain Manager**

```
/var/log/vmware/vcf/domainmanager/domainmanager.log
```
-   Adding or removing a Workload Domain
-   Adding or removing a cluster
-   Adding or removing a host
-   VSAN Stretched Cluster Deployment issues
-   NSX-T Edge Cluster Deployment issues
-   vRealize Lifecycle Manager(vRSLCM) deployment issues

**LCM**
```
/var/log/vmware/vcf/lcm/lcm.log
/var/log/vmware/vcf/lcm/lcm-debug.log
```
-   Anything to do with upgrading a component i.e. SDDC Manager, NSX-T etc.
-   Depot connection issues
-   Bundle download issues

  

**Operations Manager**
```
/var/log/vmware/vcf/operationsmanager/operationsmanager.log
```
Anything to do with the following:
-   Certificates
-   Passwords (Rotate, update, etc.)
-   Backups (NSX-T and SDDC)
-   DNS and NTP
-   Licenses
  
###### Other Directories:

Directory | Description
---|---
`ceip` | This has the VMware Customer Experience Improvement Program info.
`commonsvcs` | This is kind of a dump all. If you are troubleshooting some API issues the `vcf-commonsvcs.log` might have the info you need.
`pantheon` | This has to do with the SDDC Federation.
`sddc-manager-ui-app`   | This will have info dealing with errors having to do with the SDDC Manager UI.
`sddc-support` | This is the default directory for logs when you run the _sos_ command.

