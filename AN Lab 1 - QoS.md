---
tags: Advanced Networking
---
:::success
# AN Lab 1 - QoS
Name: Ivan Okhotnikov
:::

:::warning
**Introduction**
In this lab, you will learn one of the most important and interesting topics in Advanced Networking:
Quality of Service. Qos is a technology for providing different classes of traffic with different service
priorities. You will learn speed limiting and bandwidth testing tools, Traffic shaping and Traffic Policing,
traffic prioritization, learn how to apply QoS rules, learn about physical QoS metrics and answer a
number of theoretical questions.
:::

## Task 2 - QoS learning & configuring

:::warning
1. Let's start with a little theory. Briefly answer the questions or give one-line description what is it: Сlass of Service (CoS), Type Of Service (ToS), Differentiated Services Code Point (DSCP), Serialization, Packet Marking, Tail Drop, Head Drop, The Leaky bucket algorithm, The Token Bucket Algorithm, Traffic shaping, Traffic policing?
2. Configure your network as you decided earlier. After your network is configured (don't forget to show the main configuration steps in the report), try to set a speed limitation (traffic shaping) between the two hosts.
Hint: It was found that the virtual solution for the Mikrotik router has a "sewn" in the firmware speed limit of not more than one megabit per second, that is, in these conditions, we can only configure the speed limit of not more than one megabit per second. Try to verify this and to get around the restriction (could it have been fixed in the specific version) ? Otherwise, set the traffic limit to no more than one megabit per second.
3. Run a bandwidth testing tool, see what is the max speed you can get and verify your speed limitation. Compare the speed between the different hosts.
Hint: for example, you can use iperf3 tool.
4. While your bandwidth test is still running, try to download a file from one host to the other host and see what is the max speed you can get. If you have more than two hosts on the network, play around with different speed values and show it.
Hint 1: you can use any other scenario than download a file, for example, a VoIP call or streamed media.
Hint 2: for example, you can use iftop tool for speed measurement.
5. Deploy and verify your QoS rules to prioritize the downloading of a file (or any other scenario) over the bandwidth test.
6. What is the difference between the QoS rules to traffic allocation and priority-based QoS? Try to set up each of them and show then them. In which tasks of this lab do you use one or the other? This is mostly theoretical question.
7. Choice and install any tool that you like for bandwidth control/netflow analysis/network control & monitoring. Play around with the tool features and show the different network metrics via GUI.
Hint: for example, you can use ntopng.
Bonus: try to apply and verify any QoS rule/mechanism that you are interested in (this may be your new idea or one of the QoS configuration that you set up in the lab earlier).
8. Try to answer the question: packet drops can occur even in an unloaded network where there is no queue overflow. In what cases and why does this happen?
:::

## Implementation

:::info
> 1. Let's start with a little theory. Briefly answer the questions or give one-line description what is it: Сlass of Service (CoS), Type Of Service (ToS), Differentiated Services Code Point (DSCP), Serialization, Packet Marking, Tail Drop, Head Drop, The Leaky bucket algorithm, The Token Bucket Algorithm, Traffic shaping, Traffic policing?

**Сlass of Service (CoS)** - this is a way to pre-configure traffic by grouping similar traffic types (considered as a class)

**Type Of Service (ToS)** - a byte contained in a traffic packet (figure 1) that contains a list of criteria. These criteria allow you to determine the type of IP packet service to ensure good data transmission quality.

<center>

![](https://i.imgur.com/d1Uv2Vt.png)
Figure 1: Description of ToS
</center>

**Differentiated Services Code Point (DSCP)** - a mechanism that is based on traffic class aggregation. Each packet at the input is classified and its own processing methods are applied to it, depending on the class. Unlike IntServ, it is scalable

**Serialization** - translating data into a sequence of bytes

**Packet Marking** - adding a marking record to the package that helps determine whether the package belongs to a certain class

**Tail Drop** - discarding the end of the queue when the buffer overflows

**Head Drop** - discarding the beginning of the queue when the buffer overflows

**The Leaky Bucket Algorithm** - this is an algorithm that is based on an analogy with a bucket in which something is always poured and there is a constant leak (in our case, bandwidth)

**The Token Bucket Algorithm** - this is an algorithm that allows you to limit the bandwidth of the channel. This is achieved by the fact that each package must pay tokens for passing. The number of tokens depends on the buffer size

**Traffic shaping** - bandwidth limitation, only unlike policing, packets are not deleted, but placed in a queue (until the buffer overflows)

**Traffic policing** - limiting bandwidth by removing redundant packets

:::

:::info
> 2. Configure your network as you decided earlier. After your network is configured (don't forget to show the main configuration steps in the report), try to set a speed limitation (traffic shaping) between the two hosts.
>>Hint: It was found that the virtual solution for the Mikrotik router has a "sewn" in the firmware speed limit of not more than one megabit per second, that is, in these conditions, we can only configure the speed limit of not more than one megabit per second. Try to verify this and to get around the restriction (could it have been fixed in the specific version) ? Otherwise, set the traffic limit to no more than one megabit per second.

<center>

![](https://i.imgur.com/6mKqAOz.png)
Figure 2: My topology
</center>

To limit the connection speed, I added a `queue simple` rule to set the maximum speed and the client to which these rules will be applied next (figure 3)

This is done by the command:
```
queue simple add name=2 max-limit=800K/800K target=10.10.2.2
```

<center>

![](https://i.imgur.com/ItjkykU.png)
Figure 3: My rule set to limit the connection speed of one of the clients
</center>

After setting the rule, you can check the connection speed (figure 4,5)<center>

![](https://i.imgur.com/FlDcfVL.png)
Figure 4: Connection speed before restriction
![](https://i.imgur.com/sUYvZYF.png)
Figure 5: Connection speed after restriction
</center>


About the `1Mbit/s` limit:
Yes, that's exactly it. It's not the firmware version, but the fact that the company decided to introduce a paid license for CHR. Since I'm using the free version, I have a speed limit (figure 6)
<center>

![](https://i.imgur.com/g9lAXGb.png)
Figure 6: A table on the microtik website with information about the speed limit
</center>
:::

:::info
> 3. Run a bandwidth testing tool, see what is the max speed you can get and verify your speed limitation. Compare the speed between the different hosts.
Hint: for example, you can use iperf3 tool.

After checking the speed between hosts, the highest speed was at the beginning (4.77Mbit/s), but it quickly became 1Mbit/s
On average, the speed has become equal to 1.3Mbit/s (figure 7)

After setting the limit to 800 Kbit/s, the speed decreased noticeably (figure 8)
<center>

![](https://i.imgur.com/YLUCY6a.png)
Figure 7: Connection speed before restriction

![](https://i.imgur.com/OmWfRJO.png)
Figure 8: Connection speed after restriction
</center>
:::

:::info
> 4. While your bandwidth test is still running, try to download a file from one host to the other host and see what is the max speed you can get. If you have more than two hosts on the network, play around with different speed values and show it.
>>Hint 1: you can use any other scenario than download a file, for example, a VoIP call or streamed media.
Hint 2: for example, you can use iftop tool for speed measurement.

To test this, first I'm on host `10.10.4.2` I will create a large file weighing `100MB`:
```
fallocate -l 100M big_file.txt
```
Next, on the host (on which I previously set the limit) I have enabled the iftop traffic monitoring program, which I installed in advance:
```
sudo iftop
```
The program is able to show incoming and outgoing connections, connection ports. It also shows traffic metrics (how much has come, gone since the launch of the program)
But the most important thing for me now is to find out the connection speed.

To do this, I started the process of transferring the file from the host on which I created the file (`10.10.4.2`) to the host with a limit of 800 Kbit/s (`10.10.2.2`).
I did it through the scp program, which uses the sftp protocol:
```
scp big_file.txt ubuntu@10.10.2.2:~/
```

The maximum file download speed was exactly what I set in Mikrotik (figure 9)

<center>

![](https://i.imgur.com/j1Eoi40.png)
Figure 9: The `iftop` program window
</center>

:::

:::info
> 5. Deploy and verify your QoS rules to prioritize the downloading of a file (or any other scenario) over the bandwidth test.

Since I will be transferring files using `sftp`, I will mark this traffic using mangle in my router (figure 10):

```
ip firewall mangle add action=mark-packet chain=forward new-packet-mark=ssh passthrough=yes protocol=tcp port=22
```

<center>

![](https://i.imgur.com/ZuoqSzl.png)
Figure 10: Configuration of package labeling using mangle in microtik
</center>

Next, I will set a `queue simple` rule for all packets marked as `ssh`, give them high priority and set the maximum speed to `1Mbit/s` (figure 10)

<center>

![](https://i.imgur.com/BVRjczq.png)
Figure 11: Created the `queue simple` rule
</center>

Now I can check the connection speed between two hosts and when downloading at the same time

Since sftp has the highest priority in my case, it has the highest download speed. iperf can only get the speed that remains after the `sftp traffic` occupies the band (figure 12)
<center>

![](https://i.imgur.com/pyke3lH.png)
Figure 12: Checking the priority of downloading a file and checking the speed when doing both at the same time
</center>
:::

:::info
> 6. What is the difference between the QoS rules to traffic allocation and priority-based QoS? Try to set up each of them and show then them. In which tasks of this lab do you use one or the other? This is mostly theoretical question.

The difference is that based on priorities, traffic is distributed from the highest priority to the lowest. If you do not use prioritization, then the message traffic is distributed randomly.
In task 5 I used prioritization, before this task I did not use prioritization

:::

:::info
> 7. Choice and install any tool that you like for bandwidth control/netflow analysis/network control & monitoring. Play around with the tool features and show the different network metrics via GUI.
Hint: for example, you can use ntopng.


To install ntopng for host `10.10.3.2`

**Since I don't have access to this host, I need to push the port inside:**
```
ip firewall nat add chain=dstnat action=dst-nat to-addresses=10.10.3.2 to-ports=3000 protocol=tcp in-interface=ether1 dst-port=3000 
```

After that, the `3000` port became available to me. By visiting the page `http://192.168.122.13:3000` and after changing the password, I will be taken to the main page of the `ntop web interface` (figure 13)

<center>

![](https://i.imgur.com/BOPhTiM.png)
Figure 13: Home page of the `ntop` web interface
</center>

The main menu is shown above. Each item speaks for itself and it's easy to work out

One of the main information is displayed at the bottom of the page - the download and download speed at the moment, the number of devices and the number of streams.

**I will consider a couple of functions of this interface:**

Clicking on the Flows button takes you to the output page of all flows. Applications/hosts and clients/servers (including active ports) are listed there (figure 14)
It is also impossible not to note the fields of metrics
<center>

![](https://i.imgur.com/C4AfYRP.png)
Figure 14: The `Flows` tab
</center>

By clicking on the `Hosts` button I can consider the hosts connected to me

<center>

![](https://i.imgur.com/sSO1SOm.png)
Figure 15: The `Hosts` tab
</center>

I would also like to note the ability to view information about connected network interfaces - the `Interfaces` button

On the open page, I can get acquainted with the configured IP address, the number of packets received (and their weight), the amount of traffic, the connection speed, the set MTU value and information about the amount of traffic received/sent in a convenient pie chart
<center>

![](https://i.imgur.com/DvIGLqP.png)
Figure 16: The `Interfaces` tab
</center>

---

>Bonus: try to apply and verify any QoS rule/mechanism that you are interested in (this may be your new idea or one of the QoS configuration that you set up in the lab earlier).

I will try to configure the time of the `QoS rules`
To begin with, you need to specify the correct time, because after a reboot it gets lost
I can do this with the command: 
```
system clock set time=hour:min:seconds
```

After that I can start setting up the `QoS time rule`
To do this, I need to specify the time property for the desired rule in queue simple
This property has its own format. `timeFrom-timeTo,day`
For example, I will set the value for this property (figure 17)
<center>

![](https://i.imgur.com/SFx0eqj.png)
Figure 17: A `queue simple` rule containing the running time
</center>

When the working time has not come, there will be an `I` sign in front of the rule, which means that the rule is not valid and does not work
By checking the speed of the Internet connection using `speedtest-cli`, you can make sure that the rule does not work (figure 18)

<center>

![](https://i.imgur.com/jsrhbUQ.png)
Figure 18: Checking the connection speed using `speedtest-cli`
</center>

When the working time corresponds to the current one, the `I` sign disappears and the rule starts working (figure 19,20)

<center>

![](https://i.imgur.com/wRrzLXL.png)
Figure 19: A `queue simple` rule containing the running time

![](https://i.imgur.com/YE6oFRT.png)
Figure 20: Checking the connection speed using `speedtest-cli`
</center>

:::

:::info
> 8. Try to answer the question: packet drops can occur even in an unloaded network where there is no queue overflow. In what cases and why does this happen?

This happens in two cases:
- When the packet is too large for the channel (because otherwise there will be a delay in receiving it)
- When the router is configured incorrectly
:::


## Task 3 - QoS verification & packets analysis
:::warning
1. How can you check if your QoS rules are applied correctly? List and describe the various methods.
2. Try to use Wireshark to see the QoS packets. How does this depend on the number of routers in the network topology?
:::

## Implementation
:::info
> 1. How can you check if your QoS rules are applied correctly? List and describe the various methods.

I can verify that the rule has been applied using the command:
```queue simple print stats```
The output of this command contains information about the traffic that passed according to this rule
<center>

![](https://i.imgur.com/ozw7R6d.png)
Figure 21: Information about the traffic passed according to the QoS rule
</center>

However, as for the correctness of the applied rule, you can check this by downloading a large file.
:::

:::info
> 2. Try to use Wireshark to see the QoS packets. How does this depend on the number of routers in the network topology?


To view `QoS information in wireshark`, you need to add the display of this field to it (figure 22)
<center>

![](https://i.imgur.com/yfU05H7.png)
Figure 22: Configuring wireshark to add a field displaying the QoS value
</center>

To do this, I specify the title and select from the list of types: `IP DSCP Value`

After that, I can correctly see the value (figure 23)
<center>

![](https://i.imgur.com/HZWAWLx.png)
Figure 23: Information about intercepted traffic in 'wireshark' with QoS information
</center>

I can also substitute this value by configuring `mangle` in my router `Mikrotik`
<center>

![](https://i.imgur.com/OaDRpep.png)
Figure 24: `mangle` rule for changing dscp
</center>

>  How does this depend on the number of routers in the network topology?

It depends directly, because routers can change the QoS value (for example, as I said earlier)

:::


## Bonus - Two routers

:::warning
1. Edit your network scheme to use 2 hosts and 2 routers, each one of them has a different subnet, and the 2 hosts should be able to reach each other (direct connection between the routers and static routes is more than enough) and redo task 2.
2. Do you need to write QoS rules for each router? Is there a way to let the other routers know that this packet has more priority over the other packets?
3. Try to deploy your network scheme over IPv6.
:::

## Implementation

:::info
> 1. Edit your network scheme to use 2 hosts and 2 routers, each one of them has a different subnet, and the 2 hosts should be able to reach each other (direct connection between the routers and static routes is more than enough) and redo task 2.

For this task, I have compiled the following topology (figure 24)

<center>

![](https://i.imgur.com/xOfDzc9.png)
Figure 24: My topology
</center>

The next step was to configure addresses and static routes
`Figure 25,26` shows the routes prescribed by static in the routers `R2` and `R3`
These rules are necessary so that I can get from the computer from the subnet `R2` to the computers inside `R3` and vice versa

<center>

![](https://i.imgur.com/nL7jiMr.png)
Figure 25: Route table for `R2`

![](https://i.imgur.com/rEoYLPK.png)
Figure 26: Route table for `R3`
</center>

Next, I check the work with the routing rules (which I configured earlier)(figure 27)

<center>

![](https://i.imgur.com/wG5akVV.png)
Figure 27: Checking the operation of routing rules
</center>

Now I will add a traffic restriction rule to `R2` (figure 28) and check it using the `iperf3` program by contacting the host that is connected to the router `R3` (figure 29)
<center>

![](https://i.imgur.com/mLDL4Kp.png)
Figure 28: `queue simple` rule of `R2`
![](https://i.imgur.com/vFQZ89Y.png)
Figure 29: Output of the iperf3 program from the server and from the client
</center>

It is clearly visible that the rules have been applied. Next I will add a constraint for `R3` (figure 30). I want to check what the speed will be between two hosts connected via 2 different routers. (figure 31)


<center>

![](https://i.imgur.com/UGudfgf.png)
Figure 30: `queue simple` rule of `R3`
![](https://i.imgur.com/TZ1SYZB.png)
Figure 31: Speed limit scheme
</center>

After checking with iperf, the connection speed (as expected) was approximately `200 Kbit/s` (figure 32). This happened because in this case the connection between `Host2` and `R2` is the lowest speed and it will not be possible to rise above it

<center>

![](https://i.imgur.com/sYO1bSn.png)
Figure 32: Speed between `Host1` and `Host2`
</center>

---

### Traffic prioritization

To begin with, I will start marking ssh traffic (since I transfer files via sftp) (figure 33)

<center>

![](https://i.imgur.com/aHapOEq.png)
Figure 33: `mangle` rule for marking of ssh traffic
</center>

Then I will create a new `queue simple` rule specifying priority for ssh traffic only (figure 34)

<center>

![](https://i.imgur.com/7191qfr.png)
Figure 34: `queue simple` rule only ssh traffic with hight priority
</center>

After setting up, I will check the operation of the established rules.
I will start downloading the file to the host `20.20.2.2` from the host `10.15.2.2` and check the speed using the iperf program between `10.15.1.2` and `20.20.2.2` (figure 35)

<center>

![](https://i.imgur.com/NCRg0F7.png)
Figure 35: Checking the established `QoS rules`
</center>

Figure 35 shows that the rules work (since it is clear that the speed when checking with `iperf3` is noticeably reduced)

:::

:::info
> 2. Do you need to write QoS rules for each router? Is there a way to let the other routers know that this packet has more priority over the other packets?

As in the case above (when I set the speed limit rule for `Host2` (figure 31)) I made sure that the speed would not exceed the minimum set maximum. Even if the input is `1Gb/s`, but the maximum speed to the host is limited to `200 Kbit/s`, it will not be possible to get a speed higher than `200 Kbit/s`
:::

:::info
> 3. Try to deploy your network scheme over IPv6.

I failed to complete this task because I ran into a problem. It consists in the fact that all hosts ping each other perfectly, but it is not possible to establish a connection using ssh/iperf via `ipv6`.

Below are the configuration steps:


To begin with, I assigned ipv6 addresses to interfaces and specified static routes (figure 36,37)
<center>

![](https://i.imgur.com/2ZGvdq2.png)
Figure 36: Static routes `ipv6` for `R2`

![](https://i.imgur.com/htPoIYK.png)
Figure 37: Static `ipv6` routes for `R3`
</center>

For better convenience of perception, I put ipv6 addresses under the hosts in the topology (figure 38)

<center>

![](https://i.imgur.com/c0Ob8rf.png)
Figure 38: My topology
</center>

It's time to move on to the problem itself (figure 39)
I tried to run `iperf3` as well, but the problem remains the same. It waits for connection and crashes on timeout

<center>

![](https://i.imgur.com/2TToFUJ.png)
Figure 39: Checking the connection using the `ping` and `ssh` commands
</center>

By capturing the traffic that goes to the destination machine, you can see that the traffic is transmitted to the machine. However, it is not perceived by the machine itself (for the sake of the test, I also checked `disable firewall`, but without success)

<center>

![](https://i.imgur.com/PBEMKNi.png)
Figure 40: Captured traffic by the `wireshark` program
</center>

I believe that the `QoS` setting for `ipv6` is not much different from `ipv4` and the actions taken to configure it will be the same as in `ipv4`
:::


## References:
1. [Manual:IP/Firewall/Mangle](https://wiki.mikrotik.com/wiki/Manual:IP/Firewall/Mangle)
2. [Simple method to organize branch-VPN failover between MikroTik and other-vendor router](https://mum.mikrotik.com//presentations/KZ16/presentation_3424_1465542527.pdf)
5. [QoS troubleshooting with Wireshark](https://erwinbierens.com/qos-troubleshooting-wireshark/)
6. [CHR Licensing](https://wiki.mikrotik.com/wiki/Manual:CHR#CHR_Licensing)