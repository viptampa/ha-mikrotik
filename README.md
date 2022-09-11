# ha-mikrotik 
HA Scripting code for Mikrotik routers - v7

# Status: September 10th 2022
**Reviving this project as a fork and starting with v7.5** - Download at your own risk.  Nothing has been tested yet. 

# Concept and Design
Using a dedicated interface, VRRP, scripts, and backups, we can make a pair of Mikrotik routers highly available. 
Configuration and files are actively synchronized to the standby and the standby remains ready to takeover when the VRRP heartbeat fails.
In my case, rb5009 

- ether1 - ISP1 
- ether2 - ISP2
- ether3 - HA Link (used for syncing configs)
- ether4 and 5, bond to down stream switch
- ether6 - admin pc
- ether7 - emergency network (oob)
- ether8 - unused
- sfp-sfpplus1 - unused

All ports should be configurable, but need to be same on both sides.  For example, say your ISP comes in on ether8, that's fine. 

DO NOT RENAME YOUR PORTS.  USE COMMENTS



# Video demonstrating/explaining ha-mikrotik by The Network Berg (2019 version)
[Mikrotik - REAL HA Configuration](https://www.youtube.com/watch?v=GEef9P8wwxs)

# Installing (TBC)
1. Source a pair of matching routers (or at least matching number of ports).  In theory this could work with different Tik routers, with 5 or 10 ports, but you'll need to conform to the port standards I'm suggesting. 
2. Install RouterOS v7.5 and make sure the Routerboard firmware is up date.
3. Ensure you have serial connections to both devices (or emergency network port on each)
4. Reset both routers using the command: (this is being reviewed)
`/file remove [find]; /system reset-configuration keep-users=no no-defaults=yes skip-backup=yes`
5. Connect an ethernet cable between HA Link (ether3 in this example). 
6. On router 1 (R1), configure emergency network port. a basic network interface with an IP address of your choosing (dhcp client even). Just enough to be able to copy a file.
7. Profit... (again, this project is being revived from near scratch, will re-use things that make sense and lose things that don't)