---
layout: post
title: "Tomato Router as standalone DHCP and local DNS server"
date: 2016-10-16
---
## New WiFi Router
My venerable WRT54GL with Tomato firmware, finally was about to bite the dust. Its 54Mbps 802.11g speed was becoming inadequate for modern bloat. The Wifi signal could barely reach my den. Its time to upgrade. Got a TP-Link Archer-C9 for a discount price at Frys and my home network reached modern speeds. But wait a minute... I cant ping my media server by name, from my Debian laptop, anymore. I need to use the IP address. So whats going on? My new Router doesnt have a local dns server and quick searches indicate that its a common issue. So what are my options now?

1. Get DD-WRT. Unfortunately its a work in progress for C9 and Iam not ready to flash mine yet
2. Run a local DNS server on machine on the LAN. It needs to handle DHCP as well. Dont want to keep a machine running 24/7 for a piddly dns service
3. Re-purpose my WRT54GL. Would be Brilliant if it could work. Especially since I was already using it as a switch to add a few LAN ports.

## Tomato as Switch, DHCP and DNS server
Lots of info on how to re-purpose an old wifi router as a switch. I found this [HowToGeek](http://www.howtogeek.com/174419/how-to-reuse-your-old-wi-fi-router-as-a-network-switch/) very useful. To task the switch with DHCP and DNS duties as well, only a few more steps are needed. The one gotcha that took me a while to figure out was to configure the DHCP server to send out the main router (my Archer C9) as the gateway in the DHCP offer packets. Tomato makes this painfully easy. No messing around with DNSMasq confs although its not too difficult either [DNSMasq](http://www.thekelleys.org.uk/dnsmasq/docs/dnsmasq.conf.example).

1. Basic->Network [ScreenShot]({{ site.url }}/assets/images/BasicNetwork.png)
  * WAN/Internet Type : Disabled
  * Router IP Address : 192.68.0.2
  * Subnet Mask : 255.255.255.0
  * Default Gateway : 192.168.0.1 (my Archer C9)
  * Static DNS : 8.8.8.8 (or from your ISP)
  * DHCP Server: Checked
  * IP Address Range : 192.168.0.100 - 192.168.0.255
  * Wireless/ Enable Wireless : Not Checked
2. Advanced->DHCP/DNS [ScreenShot]({{ site.url }}/assets/images/AdvancedDhcp.png)
  * Use internal DNS : Checked
  * Use received DNS with user-entered DNS : Checked
  * Use user-entered gateway if WAN is disabled : Checked
  
## Final Steps
Go to main router (Archer C9) and disable DHCP server. Reboot both router and switch. Voila, now all DHCP configs are handed out by Tomato and it knows the Name->Address mapping. So I can go back to using names (Eg: ping kzin) in my LAN. I can use Tomato's web interface to add static names and all the cool features that Iam so used to. I now also have the option of enabling Wifi on the Tomato to serve older clients.

