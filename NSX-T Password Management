#vmware/nsx 

[NSX-T Authentication and Authorization](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.2/administration/GUID-1CAC911E-A224-4521-A9A2-0D668B4BFB61.html)

### Manage Local Users using 'admin' account

- The `admin` user can manage passwords, change the name of the admin and other users, and add, delete, or deactivate users. Any user account change is audited.
- By default, user passwords expire after 90 days. You can change or deactivate the password expiration for each user.

```bash
# login as admin user
set user [TAB][TAB]                               To see a list of existing user names
get user admin password-expiration                To get password expiration information
set user admin password-expiration 120            To set the password expiration time in days
set user admin password-expiration-warning 14     To change the default number of days for Warning
clear user admin password-expiration              To deactivate password expiration

set user <username> password                      To change user password, you must know the current password
set user admin username admin_new                 To change the name of the user

```

### Authentication Policy Settings

You can view or set the minimum password length with the following commands: 
```
get auth-policy minimum-password-length
set auth-policy minimum-password-length <password-length>
```

The following commands apply to logging in to the NSX Manager UI, or making an API call: 
```
get auth-policy api lockout-period
get auth-policy api lockout-reset-period
get auth-policy api max-auth-failures
set auth-policy api lockout-period <lockout-period>
set auth-policy api lockout-reset-period <lockout-reset-period>
set auth-policy api max-auth-failures <auth-failures>
```

The following commands apply to logging in to the CLI on an NSX Manager or an NSX Edge node: 
```
get auth-policy cli lockout-period
get auth-policy cli max-auth-failures
set auth-policy cli lockout-period <lockout-period>
set auth-policy cli max-auth-failures <auth-failures>
```

> By default, after five consecutive failed attempts to log in to the NSX Manager UI, the administrator account is locked for 15 minutes.

```
set auth-policy api lockout-period 0                      To turn off account lockout for the UI/API
set auth-policy cli lockout-period 0                      To turn off account lockout for the CLI
```


### Resetting Expired Passwords

The admin user can reset the expired passwords for their own, as well as the audit and Enterprise Admin user, using the CLI. The admin user can reset the expired passwords for all other local users, but not themselves in the UI.
 
When the admin resets any local user's expired password using the CLI and UI, the password expiration date does not reset. To extend the password expiration date to a new date or to the default 90 days, the admin must change it using the CLI or API. Another quick way for the admin to reset a local user's password expiration date is to deactivate and then activate the user.

```bash
set user <username> password
```


### Resetting the Passwords of an Appliance

The following procedure applies to **NSX Manager**, **NSX Edge**, and **Cloud Service Manager** appliances.

> [!info]
> If you have an NSX Manager cluster, resetting the password for any of the local users on one automatically resets the password for the other NSX Managers in the cluster. The synchronization of the password can take a few minutes.
> 
>If you have renamed the local user, use the new name in the following procedures.
>
>When you reboot an appliance, the GRUB boot menu does not appear by default. Before you perform this procedure, you must configure GRUB so that the GRUB boot menu displays.

### If you know the `root` password

1.  Log in to the appliance as `root`.
2.  To stop the server:
    1.  For NSX Edge, run the command     `/etc/init.d/nsx-edge-api-server stop`
    2.  For a Cloud Service Manager, skip this step. 
    3.  Otherwise, run the command          `/etc/init.d/nsx-mp-api-server stop`

```
	passwd admin                To reset the password for 'admin'
	passwd audit                To reset the password for 'audit'
	passwd guestusername        To reset the password for 'guest'
```

1.  Run the command     `touch /var/vmware/nsx/reset_cluster_credentials`

2.  To restart the server: 
    1.  For NSX Edge, run the command      `/etc/init.d/nsx-edge-api-server start` 
    2.  Otherwise, run the command            `/etc/init.d/nsx-mp-api-server start`


### Reset procedure if you have forgotten the `root` password

1.  Connect to the console of the appliance.
2.  Reboot the system.
3.  When the GRUB boot menu appears, press the left SHIFT or ESC key quickly. If you wait too long and the boot sequence does not pause, you must reboot the system again.
4.  Press `e` to edit the menu.

>	Choose the top Ubuntu line then enter the user name `root` and the GRUB password for `root` (not the same as the appliance's user root). The default password is `NSX@VM!WaR10`

1.  Press `e` to edit the selected option.
2.  Search for the line starting with `linux` and add  `systemd.wants=PasswordRecovery.service`  to the end of the line.
3.  Press  `Ctrl-X`  to boot.
4.  When the log messages stop, enter the new password for root.
5.  Enter the password again.

> 	The boot process continues.

1.  After the reboot, you can verify the password change by logging in as root with the new password.


