
SDDC Manager has different logs for different functions. This is to help you identify what log to look at depending on what you are trying to do in SDDC Manager.

If you are troubleshooting a bringup and still using cloudbuilder, the main log will be `/var/log/vmware/vcf/bringup/bringup.log` or `bringup-debug.log` for more detail.

For example: If you are trying to add a host and something goes wrong, you would look in `/var/log/vmware/vcf/domainmanager/domainmanager.log`

The details of the of the logs have done a complete 180 from when I first started working with VCF(Version 2.1). Most of the time nyou can find the issue in the SDDC Manager logs without needing to review the actual problem component itself.

All of the main logs are located under  `/var/log/vmware/vcf/<service>`

Login as `vcf` and `su` to `root` in order to view.

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
-   Anything to do with upgrading a component. I.E SDDC Manager, NSX-T etc.
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

**ceip**: This has the VMware Customer Experience Improvement Program info.
**commonsvcs**: This is kind of a dump all. If you are troubleshooting some API issues the _**vcf-commonsvcs.log**_ might have the info you need.
**pantheon**: This has to do with the SDDC Federation.
**sddc-manager-ui-app**: This will have info dealing with errors having to do with the SDDC Manager UI.
**sddc-support**: This is the default directory for logs when you run the _sos_ command.
