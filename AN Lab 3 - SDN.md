---
tags: Advanced Networking
---
:::success
# AN Lab 3 - SDN
Name: Ivan Okhotnikov
:::


I decided to reproduce part of the lab work related to the `VLAN`, its task was to use the `VLAN` to allocate two subnets and isolate clients.
One subnet for `HR`, the other for `HR_Manager` and `IT_Department`

I started by building a topology

<center>

![](https://i.imgur.com/ADuYZ2d.png)
Figure 1: My topology
</center>

So far, the SDN manager is a clean Ubuntu image, so I'll start installing the `Faucet`:

I will update the current dependencies and install the necessary programs for further work:

```
sudo apt-get update
sudo apt-get install curl gnupg apt-transport-https lsb-release -y
```

I will add a new package to the list of dependencies and add its public key `gpg`


```
echo "deb https://packagecloud.io/faucetsdn/faucet/$(lsb_release -si | awk '{print tolower($0)}')/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/faucet.list

curl -L https://packagecloud.io/faucetsdn/faucet/gpgkey | sudo apt-key add -
```
I will update the dependencies again so that the faucet package is available for installation
```
sudo apt-get update
```

It remains only to start the `faucet` installation process

```
sudo apt-get install faucet-all-in-one
```

While the faucet is being installed, I can start setting up managed Open vSwitch switches

Their setup is nothing unusual.
It is necessary to set them:
- Personal identifiers in the system (this is necessary, since during further configuration of the SDN network it will be necessary to use them)
- The address of the controller and the bridge that is responsible for external connections (by default, br0 covers all interfaces). It is important to specify two addresses (different ports) for the controller, since the faucet communicates with switches using its two ports
- I will disable in-band management, since it is enabled by default and can lead to a shot in the foot if the network grows (disable-in-band=true)
- I will also allow dropping packets in case of a controller failure (fail_mode=secure)

Yes, I could transfer control to ovs in case of a failure, but the developers advise enabling secure mode

```
ovs-vsctl set bridge br0 other-config:datapath-id=0000000000000001 \
-- set bridge br0 other-config:disable-in-band=true \
-- set bridge br0 fail_mode=secure \
-- set-controller br0 tcp:192.168.122.14:6653 tcp:192.168.122.14:6654


ovs-vsctl set bridge br0 other-config:datapath-id=0000000000000002 \
-- set bridge br0 other-config:disable-in-band=true \
-- set bridge br0 fail_mode=secure \
-- set-controller br0 tcp:192.168.122.14:6653 tcp:192.168.122.14:6654
```


Now it's time for an interesting thing - to set up a network using a `Faucet`

`Faucet` configures the network based on the configuration file that lies in `/etc/faucet/faucet.yaml`, so I will proceed to the formation of the configuration file


```
vlans:
    hr:
        vid: 100
        faucet_vips: ["10.20.2.1/24"]
    managment:
        vid: 200
        faucet_vips: ["10.20.3.1/24"]
routers:
    router-1:
        vlans: [hr, managment]
dps:
    openvswitch-1:
        dp_id: 0x1
        hardware: "Open vSwitch"
        stack:
            priority: 1
        interfaces:
            2:
                description: "hr network namespace"
                native_vlan: hr
            3:
                description: "hr_manager network namespace"
                native_vlan: managment
            4:
                description: "OpenvSwitch-1 stack link to openvswitch-2"
                stack:
                    dp: openvswitch-2
                    port: 3

    openvswitch-2:
        dp_id: 0x2
        hardware: "Open vSwitch"
        interfaces:
            2:
                description: "id_department network namespace"
                native_vlan: managment
            3:
                description: "openvswitch-2 stack link to openvswitch-1"
                stack:
                   dp: openvswitch-1
                   port: 4
```

I'll start in order:

```
vlans:
    hr:
        vid: 100
        faucet_vips: ["10.20.2.1/24"]
    managment:
        vid: 200
        faucet_vips: ["10.20.3.1/24"]
routers:
    router-1:
        vlans: [hr, managment]
```

First, I specified vlans with their identifiers and a pool of ip addresses.
Since I have `HR_MANAGER` and `IT_Department` connected to different routers, it will be necessary to put vlans in `routers`

Next, I painted `dps`. This is how controlled switches are designated.
I will tell you more on the basis of `openvswitch-1`

```
    openvswitch-1:
        dp_id: 0x1
        hardware: "Open vSwitch"
        stack:
            priority: 1
        interfaces:
            2:
                description: "hr network namespace"
                native_vlan: hr
            3:
                description: "hr_manager network namespace"
                native_vlan: managment
            4:
                description: "OpenvSwitch-1 stack link to openvswitch-2"
                stack:
                    dp: openvswitch-2
                    port: 3
```

I specified its identifier (the one I indicated earlier on the switch itself), said what kind of switch it was and set it the first priority.
Next comes the work with the interfaces that I set the settings for. For the `2nd` and `3rd` interfaces, I set the vlan, but for the `4th` I indicated that it is a link to the second switch and connects to its `3rd` port

Before applying these settings, it is necessary that there are no errors. This is done using the command:

```
check_faucet_config /etc/faucet/faucet.yaml 
```

If `JSON` with the configuration came in the response, then the configuration file does not contain errors and it can be applied (figure 2)
<center>
    
![](https://i.imgur.com/ouXAICl.png)
Figure 2: Checking the validity of the configuration file
</center>

To apply the settings, you need to restart the `faucet` service
```
sudo service faucet reload
```

But you can't do without logs either, they can be found in the directory: `/var/log/faucet` (figure 3)

```
tail -n 30 /var/log/faucet/faucet.log
```

The logs contain all information about connections, the application of rules, and so on. This is indispensable when looking for problems when the network does not work as intended
<center>

![](https://i.imgur.com/Ml1zlMF.png)
Figure 3: Contents of the `faucet` logs
</center>

To make sure that the switches have successfully connected to the controller, you can run the command: `ovs-vsctl show` on any switch (figure 4)
<center>
    
![](https://i.imgur.com/ldODWuN.png)
Figure 4: Checking the connection of the switch to the controller
</center>

It's time to move on to the tests.

I will `ping` from `IT_Department` to `HR_Manager` (figure 5)

<center>
    
![](https://i.imgur.com/dB732qK.png)
Figure 5: `ping` on the same subnet
</center>

And now from `HR` to `HR_Manager` (figure 6)

<center>

![](https://i.imgur.com/3YFY2y0.png)
Figure 6: ping to `HR_Manager` from `HR`
</center>

It is clearly visible that `HR` has access only to its gateway.

It would also be good to look at the traffic that goes between the nodes (to make sure that everything works via vlan) (figure 7)

<center>
    
![](https://i.imgur.com/XQ3aW37.png)
Figure 7: Captured traffic between two switches
</center>


In conclusion, I would like to add. Even if `Faucet` does not have a built-in beautiful web interface for topology visualization (for example, like `OpenDaylight`), but it has very good documentation that allows you to quickly understand how to work with it.


## References: 

1. [Official documentation](https://docs.faucet.nz/)