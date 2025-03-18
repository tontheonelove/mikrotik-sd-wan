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

## 4. Set Default Gateway

- Set Default Gateway for each route:

## (terminal)
```
/ip route add gateway=192.168.1.254
/ip route add gateway=192.168.2.254 distance=2
```
- Setting different distance will make Mikrotik choose the best route based on Distance value (1 will have highest priority).

## 5. Set up Load Balancing

- To distribute the connection load between two ISPs, use the PCC (Per Connection Classifier) ​​settings to perform load balancing:

## (terminal)  for os6

```
/ip firewall mangle add chain=prerouting in-interface=ether1 action=mark-connection new-connection-mark=ISP1_conn
/ip firewall mangle add chain=prerouting in-interface=ether2 action=mark-connection new-connection-mark=ISP2_conn
/ip firewall mangle add chain=prerouting connection-mark=ISP1_conn action=mark-routing new-routing-mark=to_ISP1
/ip firewall mangle add chain=prerouting connection-mark=ISP2_conn action=mark-routing new-routing-mark=to_ISP2

```
## (terminal)  for os7
```
/routing table
add disabled=no fib name=ISP1_conn
add disabled=no fib name=ISP2_conn
```
```
/ip firewall mangle add chain=prerouting in-interface=ether1 action=mark-connection new-connection-mark=ISP1_conn
/ip firewall mangle add chain=prerouting in-interface=ether2 action=mark-connection new-connection-mark=ISP2_conn
/ip firewall mangle add chain=prerouting connection-mark=ISP1_conn action=mark-routing new-routing-mark=to_ISP1
/ip firewall mangle add chain=prerouting connection-mark=ISP2_conn action=mark-routing new-routing-mark=to_ISP2
```


- Then add a new route for each connection:

## (terminal)
```
/ip route add gateway=192.168.1.254 routing-mark=to_ISP1
/ip route add gateway=192.168.2.254 routing-mark=to_ISP2
```

## 6. Set Failover
- To enable the system to failover if one connection fails, use Netwatch to check the status of the Internet connection: (example)
- 
## (terminal)
```
/tool ​​netwatch add host=8.8.8.8 interval=30s up-script="/ip route enable [find gateway=192.168.1.254]" down-script="/ip route disable [find gateway=192.168.1.254]"
```
- In case 192.168.1.254 (ISP1) cannot connect, it will automatically disable that route and use another route.

## 7. Check and Test
- Test the connection from both ISPs
- Check the route chosen at each time
- Test Failover by shutting down the connection of one ISP and see if the Mikrotik can switch to another ISP
## 8. Set up monitoring and logging
-Finally, set up monitoring and logging to allow for quick troubleshooting if problems arise in the future, such as:
-Use System Logs to record the status of the connection and various activities
-Set up SNMP or The Dude (Mikrotik's network management tool) to check the status of the connection

