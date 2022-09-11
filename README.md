# ha-mikrotik 
HA Scripting code for Mikrotik routers - v7

# Status: September 10th 2022
**Reviving this project as a fork and starting with v7.5** - Download at your own risk.  Nothing has been tested yet. 

# Concept
Using a dedicated interface, VRRP, scripts, and backups, we can make a pair of Mikrotik routers highly available. 
Configuration and files are actively synchronized to the standby and the standby remains ready to takeover when the VRRP heartbeat fails.
In my case, my 

# Video demonstrating/explaining ha-mikrotik by The Network Berg (2019 version)
[Mikrotik - REAL HA Configuration](https://www.youtube.com/watch?v=GEef9P8wwxs)

# Installing
1. Source a pair of matching routers (or at least matching number of ports).  In theory this could work with different Tik routers, with 5 or 10 ports, but you'll need to conform to the port standards I'm suggesting. 
2. Install RouterOS v7.5 and make sure the Routerboard firmware is up date.
3. Ensure you have serial connections to both devices (or emergency network port on each)
***4. Reset both routers using the command: (this is being reviewed)
`/file remove [find]; /system reset-configuration keep-users=no no-defaults=yes skip-backup=yes`
5. Connect an ethernet cable between ether8 and ether8. (this and below is being reviewed)
6. On one router, configure a basic network interface on ether1 with an IP address of your choosing. Just enough to be able to copy a file.
7. Upload HA_init.rsc and import it:
`/import HA_init.rsc`
8. Install HA (note to replace the fields of macA, macB, and password. I suggest a sha1 hex hash for the password.
`$HAInstall interface="ether8" macA="[MAC_OF_A_ETHER8]" macB="[MAC_OF_B_ETHER_8]" password="[A RANDOM PASSWORD OF YOUR CHOOSING]"`
9. Follow the instructions given by $HAInstall to bootstrap the secondary. I use the MAC telnet that is suggested at the top but any other method is sufficient.
10. Once router B is bootstrapped, it will reboot itself into a basic networking mode. It needs to be pushed the current configuration.
`$HASyncStandby`
11. B will now reboot and when it returns, it should be in standby mode. A should be the active router. You can now reboot A and B will takeover.

# Upgrading ha-mikrotik
1. Download a new release of ha-mikrotik
2. Upload HA_init.rsc to the active and import it:
`/import HA_init.rsc`
3. Run `$HAPushStandby` on the active, this should push the new code and reboot the standby.
4. Wait for the standby to come back, login and make sure everythig looks good. (/log print).
5. Run `$HASyncStandby` on the active, there should be no changes (unless something else changed on the active inbetween).
6. **THIS WILL REBOOT THE ACTIVE** Run `$HASwitchRole` on the active.
7. Your active is now the previous standby and both are upgraded once the standby boots.

# Rebuilding a hardware failed standby
Rebuilding failed hardware is similar to a new installation except that we don't need to reset both and don't need to bring in a new HA_init, assuming both RouterOS are compatible.

Install a compatible version of RouterOS on the new hardware and factory reset the configuration. Connect your new hardware the same exact way the old one was. We assume you have used the default of ether8 for the $haInterface.

**If A is active, run from A:**
1. `$HAInstall interface=$haInterface macA=$haMacMe macB="[NEW_MAC_OF_B_ETHER8]" password=$haPassword`
2. Follow on screen instructions just like original install.
3. Once the standby comes back, `$HASyncStandby`.
4. Done.

**If B is active, run from B:**
1. `$HAInstall interface=$haInterface macB=$haMacMe macA="[NEW_MAC_OF_A_ETHER8]" password=$haPassword`
2. Follow on screen instructions just like original install.
3. Once the standby comes back, `$HASyncStandby`.
4. Done.
