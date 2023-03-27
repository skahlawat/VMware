### SDDC Manager Log Locations

SDDC Manager has different logs for different functions. This is to help you identify what log to look at depending on what you are trying to do in SDDC Manager.

If you are troubleshooting a bringup and still using cloudbuilder, the main log will be _**/var/log/vmware/vcf/bringup/bringup.log**_ or _**bringup-debug.log**_ for more detail.

For example: If you are trying to add a host and something goes wrong, you would look in _**/var/log/vmware/vcf/domainmanager/domainmanager.log**_

The details of the of the logs have done a complete 180 from when I first started working with VCF(Version 2.1). Most of the time nyou can find the issue in the SDDC Manager logs without needing to review the actual problem component itself.

All of the main logs are located under _**/var/log/vmware/vcf/<service>**_

Login as _**vcf**_ and _**su**_ to _**root**_ in order to view.


**Domain Manager**

_**/var/log/vmware/vcf/domainmanager/domainmanager.log**_

-   Adding or removing a Workload Domain
-   Adding or removing a cluster
-   Adding or removing a host
-   VSAN Stretched Cluster Deployment issues
-   NSX-T Edge Cluster Deployment issues
-   vRealize Lifecycle Manager(vRSLCM) deployment issues

  

**LCM**

_**/var/log/vmware/vcf/lcm/lcm.log**_

_**/var/log/vmware/vcf/lcm/lcm-debug.log**_

-   Anything to do with upgrading a component. I.E SDDC Manager, NSX-T etc.
-   Depot connection issues
-   Bundle download issues

  

**Operations Manager**

_**/var/log/vmware/vcf/operationsmanager/operationsmanager.log**_

Anything to do with the following:

-   Certificates
-   Passwords (Rotate, update, etc.)
-   Backups (NSX-T and SDDC)
-   DNS and NTP
-   Licenses

  

Other Directories:

**ceip**: This has the VMware Customer Experience Improvement Program info.

**commonsvcs**: This is kind of a dump all. If you are troubleshooting some API issues the _**vcf-commonsvcs.log**_ might have the info you need.

**pantheon**: This has to do with the SDDC Federation.

**sddc-manager-ui-app**: This will have info dealing with errors having to do with the SDDC Manager UI.

**sddc-support**: This is the default directory for logs when you run the _sos_ command.
