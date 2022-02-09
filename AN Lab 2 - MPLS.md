---
tags: Advanced Networking
---
:::success
# AN Lab 2 - MPLS
Name: Ivan Okhotnikov
:::

:::warning
**Introduction**
In this lab, you will learn the MPLS Protocol, which performs packet switching via labels. You will answer
a number of theoretical questions, learn how to enable MPLS switching with authentication, learn MPLS
tables, learn deeper about MPLS packets and finally learn how to configure L2VPN (VPLS)
:::


## Task 1 - Prepare your network topology
:::warning
1. In the GNS3 project, select and install a virtual routing solution that you would like to use: for example, Mikrotik, Pfsense, vyos.
2. Prepare a simple network consisting of at least three routers and two hosts (for example, a bus topology with dynamic routing). Each one of them has a different subnet. Your network has to have routing protocols configured. That's why you can use your OSPF lab project from INR course.
:::

## Implementation

:::info
> 1. In the GNS3 project, select and install a virtual routing solution that you would like to use: for example, Mikrotik, Pfsense, vyos.

I chose mikrotik
:::


:::info
> 2. Prepare a simple network consisting of at least three routers and two hosts (for example, a bus topology with dynamic routing). Each one of them has a different subnet. Your network has to have routing protocols configured. That's why you can use your OSPF lab project from INR course.

<center>

![](https://i.imgur.com/iawudo8.png)
Figure 1: My topology
</center>

Below is a table of settings for my routers.

### **R1 Router**
<center>

![](https://i.imgur.com/NqAQEex.png)
Figure 2: Table of installed IP addresses
</center>
<center>

![](https://i.imgur.com/KTghBhU.png)
Figure 3: Setting up OSPF
</center>
<center>

![](https://i.imgur.com/rBLGns4.png)
Figure 4: Table of assigned OSPF networks
</center>

---

### **R2 Router**
<center>

![](https://i.imgur.com/HslpGSE.png)
Figure 5: Table of installed IP addresses
</center>

<center>

![](https://i.imgur.com/yC9uVUR.png)
Figure 6: Setting up OSPF
</center>

<center>

![](https://i.imgur.com/VR0OVyU.png)
Figure 7: Table of assigned OSPF networks
</center>

---

### **R3 Router**
<center>

![](https://i.imgur.com/qWy3MJa.png)
Figure 8: Table of installed IP addresses
</center>

<center>

![](https://i.imgur.com/puF80BO.png)
Figure 9: Setting up OSPF
</center>

<center>

![](https://i.imgur.com/nT0LmF3.png)
Figure 10: Table of assigned OSPF networks
</center>
:::

## Task 2 - MPLS learning & configuring
:::warning
1. Briefly answer the questions or give one-line description what is it: LSP, VPLS, PHP, LDP, MPLS L2VPN, CE-router, PE-router, LSR-router?
2. Configure MPLS domain on your OSPF network, first without authentication.
Hint: it is assumed that OSPF has already been configured previously.
3. Enable authentication (what kind of authentication did you use)? Make sure that you can ping and trace all your network.
:::
## Implementation
:::info
> 1. Briefly answer the questions or give one-line description what is it: LSP, VPLS, PHP, LDP, MPLS L2VPN, CE-router, PE-router, LSR-router?

**LSP** (Label Switched Path) - the path in the MPLS network that was set by the criteria in the FEC (Forwarding equivalence class)
**VPLS** (Virtual Private LAN Service) - a method for providing multipoint communication based on IP or MPLS, allowing physically separated nodes to share a broadcast domain
**PHP** (Penultimate Hop Popping) - a function that allows you to delete the last label before the transfer of `ce-roure`. It is the latter, because if there is a stack of labels (for example, `QoS`), I would not like to reset everything.
**LDP** (Label Distribution Protocol) - a protocol that allows labels to be exchanged with other routers.
**MPLS** (Multiprotocol Label Switching) - a protocol that allows routing via labels
**L2VPN** (Layer 2 Virtual Private Networks) - a method for creating a virtual private network above the second layer of the `OSI model`
**CE-router** (Custom Edge router) - the client has an edge router. It removes the label in the ip packet when transmitting the packet to the client.
**PE-router** (Provider Edge router) - the router connected to the ce router. Usually passes label - `0` when transmitting information to the ce-router
**LSR-router** (Label Switch Router) - not an edge router in MPLS implementing the LSP protocol. Allows you to set a label for packages.
:::

:::info
> 2. Configure MPLS domain on your OSPF network, first without authentication.
Hint: it is assumed that OSPF has already been configured previously.

To configure, I performed the following steps for the routers:
1. Enabled MPLS LDP and specified the `loopback` address of the interface 
as `transport-address` and `lsr-id`
3. Added serviced LDP interfaces

I will provide information about the settings themselves below as screenshots (figure 11-16)

### **R1 Router**
<center>

![](https://i.imgur.com/y5uYpmO.png)
Figure 11: Information about setting up LDP
![](https://i.imgur.com/L1ttp0k.png)
Figure 12: Table of assigned LDP interfaces
</center>

---

### **R2 Router**
<center>

![](https://i.imgur.com/FEVrGdT.png)
Figure 13: Information about setting up LDP
</center>

<center>

![](https://i.imgur.com/zSuSuI0.png)
Figure 14: Table of assigned LDP interfaces
</center>

---

### **R3 Router**
<center>

![](https://i.imgur.com/UoWN5H2.png)
Figure 15: Information about setting up LDP
</center>

<center>

![](https://i.imgur.com/VzehpHk.png)
Figure 16: Table of assigned LDP interfaces
</center>

---

### **Check configuration**

To check that everything is configured correctly, I will check the presence of neighbors at R1 (figure 17)
<center>

![](https://i.imgur.com/8VnUis5.png)
Figure 17: Checking the correct MPLS configuration
</center>



:::

:::info
> 3. Enable authentication (what kind of authentication did you use)? Make sure that you can ping and trace all your network.

I decided to use two available authorization methods at once `simple` and `md5`. Between `R3` and `R1` I set up `Simple`, and between `R1` and `R2` - `MD5`

### **R1 Router**
<center>

![](https://i.imgur.com/IBZZ99l.png)
Figure 18: OSPF table with authentication indication
</center>

---

### **R2 Router**
<center>

![](https://i.imgur.com/t5duHqs.png)
Figure 19: OSPF table with authentication indication
</center>

---

### **R3 Router**
<center>

![](https://i.imgur.com/pFdryMW.png)
Figure 20: OSPF table with authentication indication
</center>



I can implement authentication using `md5` or `simple` specifying the key value I need

To check, I will execute the ping command from `10.11.2.2` to `10.10.2.2` (figure 21)
<center>

![](https://i.imgur.com/1hvURFv.png)
Figure 21: Checking the availability of host `10.10.2.2` from host `10.11.2.2`
</center>



:::


## Task 3 - Verification
:::warning
1. Show your LDP neighbors.
Hint: in the case if you have some problems... think about whether there are enough routers and subnets for MPLS to pass the route?
2. Show your local LDP bindings and remote LDP peer labels.
3. Show your MPLS labels.
4. Show your forwarding table.
5. Show your network path from one customer edge to the other customer edge.
Hint: you can use traceroute command.
:::

## Implementation

:::info
> 1. Show your LDP neighbors.
> 
I can see the neighbors using the command `mpls ldp neighbor print`
The results for each router are shown below

### **R1 Router**

<center>

![](https://i.imgur.com/ZmIuJZU.png)
Figure 22: Output result of neighbors `mpls ldp`
</center>

---

### **R2 Router**
<center>

![](https://i.imgur.com/Nz7eTgk.png)
Figure 23: Output result of neighbors `mpls ldp`
</center>

---

### **R3 Router**
<center>

![](https://i.imgur.com/OwdRNQV.png)
Figure 24: Output result of neighbors `mpls ldp`
</center>


:::

:::info
> 2. Show your local LDP bindings and remote LDP peer labels.

I can view such information using the 
`mpls local-bindings print` and `mpls remote-bindings print` commands, respectively
Below I will output the result of these commands for **`R1`**
### **Local bindings**
<center>

![](https://i.imgur.com/gOvcIgL.png)
Figure 25: Information about `local-bindings`
</center>

### **Remote bindings**
<center>

![](https://i.imgur.com/HZcwzox.png)
Figure 26: Information about `remote-bindings`
</center>


:::

:::info
> 3. Show your MPLS labels.

I can see my labels using local-bindings.
I can also see them using Wireshark (I'll tell you how to configure it to view labels in a separate column in the traffic capture section)
on figure 27, the MPLS label is clearly visible in the package contents.

<center>

![](https://i.imgur.com/1WLwVrE.png)
Figure 27: the `Wireshark' window
</center>


:::

:::info
> 4. Show your forwarding table.

I can find out with the command:
`mpls forwarding-table print`
Below I will give the output result for routers **`R1`**, **`R2`**, **`R3`**


### **R1 Router**

<center>

![](https://i.imgur.com/jPrM5uW.png)
Figure 28: Output of the forwarding table
</center>

---

### **R2 Router**
<center>

![](https://i.imgur.com/riNdxEM.png)
Figure 29: Output of the forwarding table
</center>

---

### **R3 Router**
<center>

![](https://i.imgur.com/8thzKjL.png)
Figure 30: Output of the forwarding table
</center>

:::

:::info
> 5. Show your network path from one customer edge to the other customer edge.


<center>

![](https://i.imgur.com/iawudo8.png)
Figure 31: My topology
</center>

To do this, I used the `traceroute` utility, which I executed for `10.11.2.2` to find out the path to `10.10.2.2` (figure 32)

<center>

![](https://i.imgur.com/O9qKy4o.png)
Figure 32: Output of the `traceroute` program
</center>


:::


## Task 4 - MPLS packets analysis
:::warning
1. Can you use Wireshark to see the MPLS packets?
2. Look deeper into the MPLS packets: can you identify MAC address, ICMP, Ethernet header or something else useful?
:::

## Implementation
:::info
> 1. Can you use Wireshark to see the MPLS packets?

Yes I can see LDP protocol packages as well as MPLS in the interior of other packages


<center>

![](https://i.imgur.com/t7J6pC0.png)
Figure 30: `Wireshark` program window
</center>

To view the `MPLS-label`, I added another column. I will show its configuration on figure 31
<center>

![](https://i.imgur.com/kqvwRgA.png)
Figure 31: Setting up `Wireshark`
</center>


:::

:::info
> 2. Look deeper into the MPLS packets: can you identify MAC address, ICMP, Ethernet header or something else useful?

Since MPLS adds its information inside a regular package, I can see all this data (figure 32)
<center>

![](https://i.imgur.com/jcSPFIK.png)
Figure 32: MPLS information
</center>

Also, based on the fact that it works on the basis of LDP, I can see these packages (figure 33)
There I can see the 'lsr-id', transport address and message type.

<center>
    
![](https://i.imgur.com/1OiPvjo.png)
Figure 33: Contents of the LDP package
</center>

:::

## Task 5 - VPLS
:::warning
1. Configure VPLS between the 2 hosts edges.
Hint: you should connect the hosts connected to the router in one subnet to the router from the second subnet without changing the topology physically. To do this try to configure VPLS that runs on L2.
2. Show your LDP neighbors again, what has been changed?
3. Find a way to prove that the two customers can communicate at OSI layer
4. Is it required to disable PHP? Explain your answer.
:::

## Implementation
:::info
> 1. Configure VPLS between the 2 hosts edges.


<center>

![](https://i.imgur.com/9Oz1xhE.png)
Figure 34: My topology
</center>


To configure it, I have done the following steps:
1. Created vpls interface for **`R2`,** and **`R3`**, where he pointed out the name `remote-peer` (each other) and `vpls-id` (this must match) (figure 34,36)
2. Created bridgeport between the VPLS interface and the interface to which the clients will connect (figure 35,37)


### **R2 Router**
<center>

![](https://i.imgur.com/1aTjJtS.png)
Figure 35: Information about the `VPLS` interface
</center>

<center>

![](https://i.imgur.com/3Aw69QF.png)
Figure 36: Created a bundle of the `VPLS` interface and the `ether` physical interface in the loopback bridge
</center>


---

### **R3 Router**
<center>

![](https://i.imgur.com/glXz0yw.png)
Figure 37: Information about the `VPLS` interface
</center>


<center>

![](https://i.imgur.com/gcsK4ef.png)
Figure 38: Created a bundle of the `VPLS` interface and the `ether` physical interface in the loopback bridge
</center>


:::

:::info
> 2. Show your LDP neighbors again, what has been changed?

For **`R2`** and **`R3`**, a neighborhood signed as `VPLS` appeared
(figure 38,39)
### **R2 Router**
<center>

![](https://i.imgur.com/OcyL8EH.png)
Figure 39: MPLS neighbors
</center>

---

### **R3 Router**
<center>

![](https://i.imgur.com/PLN4E3M.png)
Figure 40: MPLS neighbors
</center>


:::

:::info
> 3. Find a way to prove that the two customers can communicate at OSI layer

Since they are on layer 2, I can connect two clients on the IPv4 layer
I have previously set up a bridge between the VPLS and the output interface.
To do this, I can set the ip address to clients from the same subnet and check the connection using the ping command

<center>

![](https://i.imgur.com/Yuot66i.png)
Figure 41: OSI Level 2 Verification
</center>
:::

:::info
> 4. Is it required to disable PHP? Explain your answer.

It is not necessary to disable it.
PHP (Penultimate Hop Popping) simply removes the MPLS label from the PE router (which is in front of the CE router). The CE router receives a packet that has already been cleared from the MPLS label.
Therefore, the work of PHP does not matter for VPLS

:::

## Bonus - VPWS
:::warning
There are two approaches to build L2VPN: Point-to-Point (VPWS) or Point-to-Multipoint (VPLS). You
studied VPLS a little earlier. Now you may learn the concept of PW (Pseudowire) and VPWS (Virtual
Private Wire Service technology). VPWS is an L2VPN technology that transmits layer 2 services,
simulating the main characteristics and functions of services such as ATM, Ethernet and some others.
1. Rebuild your topology to get a pseudowire P2P that emulates OSI layer
2. Repeat the steps from Task 5, but now with VPWS.
:::


## Implements
:::info
>1. Rebuild your topology to get a pseudowire P2P that emulates OSI layer
<center>
    
![](https://i.imgur.com/ZZFzods.png)
Figure 42: My new topology
</center>

I decided to configure pptp between two routers **`CE1`** and **`CE4`**

**`CE1`** will act as a pptp server, and **`CE4`** will act as a client

### **CE1 Router**

For the server, I created the `pptp-server` interface, enabled it and specified a user who can connect to it (figure 43, 44)


<center>
    
![](https://i.imgur.com/5JFFYzD.png)
Figure 43: Configuring the pptp server interface
</center>

<center>
    
![](https://i.imgur.com/f5Hw4oC.png)
Figure 44: List of created users for the pptp-server interface
</center>

I will also create a pool of addresses issued to clients (figure 44)

<center>

![](https://i.imgur.com/IIpzKLC.png)
Figure 45: Output of the `ip pool` command
</center>

Now it's time to configure the user I previously specified
I will create it in the `ppp profile` (figure 46) and give it a password
I also pointed out to him the pool of ip addresses - `remote-pool`, which I created earlier and assigned the client an ip address `remote-address` = `55.55.1.2`
<center>
    
![](https://i.imgur.com/xsaeDUr.png)
Figure 46: List of 'ppp' profiles
</center>

<center>
    
![](https://i.imgur.com/bepe0fL.png)
Figure 47: Output of the `ppp secret` command
</center>

Next I'll move on to configuring the client

---

### **CE4 Router**

To begin with, I will create a client interface (figure 48)
In it I specified the address **`CE1`**, the username and password to connect
<center>
    
![](https://i.imgur.com/4pp4CER.png)
Figure 48: Information about the client interface
</center>

The setup is complete. The client should now connect to the server. I can check this using the command `interface pptp-client monitor 0`
It is clearly visible that the client received exactly the ip address that I specified on the server (figuer 49)
<center>
    
![](https://i.imgur.com/kaZPsf9.png)
Figure 49: Checking the pptp connection status
</center>

After a successful pptpd connection, the assigned ip address will also appear, which belongs to the pptp interface (figure 50)
<center>
    
![](https://i.imgur.com/No010gK.png)
Figure 50: List of addresses
</center>

The ping command to the server also works (figure 51)

<center>

![](https://i.imgur.com/zBvKqSP.png)
Figure 51: `ping` to the pptp server using its internal pptp address
</center>


:::

:::info
>2. Repeat the steps from Task 5, but now with VPWS.

Checking the MPLS neighbors did not show anything new (figure 51)

<center>
    
![](https://i.imgur.com/NtOf8zg.png)
Figure 51: List of MPLS neighbors
</center>

PHP still has no effect.

:::

## References

2. [Setup MPLS on mikrotik](https://blog.eldernode.com/setup-mpls-on-mikrotik/)
2. [Basic MPLS](https://habr.com/ru/post/246425/)
2. [MPLS and VPLS on mikrotik](https://habr.com/ru/post/169103/)
2. [PPTP on mikrotik](https://lantorg.com/article/nastrojka-vpn-cherez-mikrotik-pptp-i-pppoe)