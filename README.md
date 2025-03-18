# mikrotik-sd-wan configuration

## Features

- support multi wan 
- Easy config, simple to use.
- support load balancer
- support fail over
- script check when link down

## Requirements

- Router Mikrotik OS6 or 7  hardware , x86 , CHR
- 2 Wan Internet


## 1. Prepare Mikrotik Devices Recommended OS7

- Make sure your Mikrotik Router has a RouterOS version that supports SD-WAN settings

- (it is recommended to use newer versions that support additional features such as Load Balancing, Routing, Firewall, etc.)


## 2. Set up multiple internet connections

- Let’s say we have two internet connections (ISP1 and ISP2)

- Go to Interfaces and set up both internet connections (e.g. ether1 for ISP1 and ether2 for ISP2)

- Set the transmission capacity (Interface) correctly


## 3. Set IP Address (Example)

- Set the IP address of each interface used to connect to each ISP:

## (terminal)
```
/ip address add address=192.168.1.1/24 interface=ether1
/ip address add address=192.168.2.1/24 interface=ether2
```




