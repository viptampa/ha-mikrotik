# ha-mikrotik 
HA Scripting code for Mikrotik routers - v7

# Status: September 10th 2022
**Reviving this project as a fork and starting with v7.5** - Download at your own risk.  Nothing has been tested yet. 

# Join me on discord
[Surviving Networking and IT](https://discord.gg/x7jZuM8pM5)


# Concept and Design
Using a dedicated interface, VRRP, scripts, and backups, we can make a pair of Mikrotik routers highly available. 
Configuration and files are actively synchronized to the standby and the standby remains ready to takeover when the VRRP heartbeat fails.
In my case, rb5009 

- ether1 - ISP1 
- ether2 - ISP2
- ether3 - HA Link (used for syncing configs, should be directly connected)
- ether4 - admin pc
- ether5 - not used
- ether6 - emergency network (oob)
- ether7 - Bonding1 - LACP to down stream switch (LegA)
- ether8 - Bonding1 - LACP to down stream switch (LegB)
- sfp-sfpplus1 - unused

All ports should be configurable, but need to be same on both sides.  For example, say your ISP comes in on ether8, that's fine. 

DO NOT RENAME YOUR PORTS.  USE COMMENTS

Concept of how HA is expected to work.  Assuming this is used for SMB where the Tiks will be used at the edge as well as the main router for the network. If the design is different, the HA script will probably have to change.  

Design Notes:
 There will be a primary router and secondary router.   
 - ISP1 will use DHCP.  (your modem will need to have 2 active ports or will need to feed into a switch to split into the routers)
 - ISP2 will assume it is DHCP as well. (same as above) 
 - HA Link use an APIPA (Automatic Private IP Addressing).  R1 ether3 - 169.254.0.1/30 - R2 ether3 - 169.254.0.2/30.  This is an unrouted network and should work fine.  
 - I use ether4 to connect to my pc on the R1, so which ever network you want to use on this.  For this example I'll use 192.168.199.1/24 for this interface. 
 - Emergency network will be 192.168.89.1/24. 
 - ether7-8 in a bond to the downstream switch.  The the lan bridge will have the bond as a port member.   If you are connecting to 2 independent switches (no mlag), a bond is not needed. 
 
   If you're using the R1/R2 to terminate vlans (like I am), then when you create them, they should land on interface LANBridge.  You'll need to create a VRRP for each vlan, and a router member IP.   For example:  guestVlan - vlan 80, has a default gateway ip of 10.69.80.1/32 (this will be the VRRP IP for the vlan), then R1 will have 10.69.80.253/24 and R2 will have 10.69.80.254/24.  As a side note, if you're trunking all of your vlans down to your switch and terminating them on the routers, all you need to do is create the vlan interfaces on the bridge and done. 

 




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