# VLAN Settings for H3C Router and UBNT UAP

#### Background
* There're many devices in local network.
  * Hikvision recorder and cameras.
  * UBNT UAP-AC-Lite APs.
    * One SSID for employees.
    * One SSID for guests.
  * Wireless Printers.
  * PCs.
* Use H3C Router(ER5200G2) as gateway.

#### Requirements
* Hikvision recorder and cameras should **NOT** access other devices.
* Devices of SSID for guests should **NOT** access other devices.

#### Solution
Use VLAN.

* Configure port-based VLAN on H3C router.
* Configure tag-based VLAN on UBNT Unifi Controller for UBNT UAP-AC-Lite APs.

#### Steps
1. VLAN Plan
   * VLAN 1(default created)
     * IP: `192.168.1.1`.
     * DHCP pool IP range: `192.168.1.2` ~ `192.168.1.254`.
     * for PCs, Wireless Printers, Devices of SSID for employees.

   * VLAN 2
     * IP: `192.168.2.1`.
     * DHCP pool IP range: `192.168.2.2` ~ `192.168.2.254`.
     * for Devices of SSID for guests.
     * UBNT UAP-AC-Lite will provide VLAN settings(tag: VLAN -> 2).

   * VLAN 3
     * IP: `192.168.3.1`
     * DHCP pool IP range: `192.168.3.2` ~ `192.168.3.254`
     * for Hikvision recorder and cameras.

2. Configure port-based VLAN on H3C router
   * Create and configure VLAN 2
     * Goto `192.168.1.1` to login admin page of H3C router.
     * Goto "Interface Setings" -> "VLAN Settings".
       * Click "Create" to create a new VLAN.
       * VLAN ID: `2`.
       * IP: `192.168.2.1`.
       * Subnet Mask: `255.255.255.0`.
     * Goto "Interface Settings" -> "VLAN Settings" -> "Trunk Settings" Tab
       * Set the LAN ID binding to the VLAN2(e.g. LAN 2).
         * PVID: `1`.
         * Permitted VLAN: `1,2`.

     * Goto "Interface Settings" -> "DHCP Settings".
       * Click "Create" to create a new DHCP pool.
         * Name: "VLAN2"
         * DHCP pool IP range: `192.168.2.2` ~ `192.168.2.254`.

   * Create and configure VLAN 3
     * Goto "Interface Setings" -> "VLAN Settings".
       * Click "Create" to create a new VLAN.
       * VLAN ID: `3`.
       * IP: `192.168.3.1`.
       * Subnet Mask: `255.255.255.0`.
     * Goto "Interface Settings" -> "VLAN Settings" -> "Trunk Settings" Tab
       * Set the LAN ID binding to the VLAN3(e.g. LAN 3).
       * PVID: `1`.
       * Permitted VLAN: `1,3`.

     * Goto "Interface Settings" -> "DHCP Settings".
       * Click "Create" to create a new DHCP pool.
         * Name: "VLAN3"
         * DHCP pool IP range: `192.168.3.2` ~ `192.168.3.254`.

   * Firewall Settings
     * Goto "Security" -> "Firewall" -> "Outgoing Policy" Tab.
     * Add a new firewall:
       * Source Interface: `VLAN2`.
       * Source IP: `IP range`: `192.168.2.2 - 192.168.2.254`
       * Destination IP range: `192.168.1.2 - 192.168.1.254`
       * Description: `Deny access from VLAN2 -> VLAN1`

     * Add a new firewall:
       * Source Interface: `VLAN2`.
       * Source IP: `IP range`: `192.168.2.2 - 192.168.2.254`
       * Destination IP range: `192.168.3.2 - 192.168.3.254`
       * Description: `Deny access from VLAN2 -> VLAN3`

     * Add a new firewall:
       * Source Interface: `VLAN3`.
       * Source IP: `IP range`: `192.168.3.2 - 192.168.3.254`
       * Destination IP range: `192.168.1.2 - 192.168.1.254`
       * Description: `Deny access from VLAN3 -> VLAN1`

     * Add a new firewall:
       * Source Interface: `VLAN3`.
       * Source IP: `IP range`: `192.168.3.2 - 192.168.3.254`
       * Destination IP range: `192.168.2.2 - 192.168.2.254`
       * Description: `Deny access from VLAN3 -> VLAN2`

3. Configure tag-based VLAN on UBNT Unifi Controller for UBNT UAP-AC-Lite APs(wireless network)
   * Make sure the PC installed Unifi Controller is in the native or untagged VLAN(e.g. H3C ER routers use VLAN 1 by default).
   * Open "Unifi Controller" and goto "https://localhost:8443" to login admin page of Unifi Controller.
   * Click "Settings" -> "Wireless Network" -> Select the wireless network for guests.
   * Click "Advanced" -> Set VLAN to `2`(this will add VLAN tag: 2 for the packets in the network).
