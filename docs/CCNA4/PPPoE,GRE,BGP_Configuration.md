### PPPoE Configuration

ISP Router (based on Lab 3.2.2.7):

```
username <Username, eg.Cust1> password <Password>
ip local pool PPPoEPOOL <begin-ipv4-addr> <end-ipv4-addr>

interface virtual-template 1
  ip address <IPv4-addr> <net-mask>
  mtu 1492
  peer default ip address pool PPPoEPOOL
  ppp authentication chap callin

bba-group pppoe global
  virtual-template 1
  exit

int g0/1
  pppoe enable group global
  no shut

```

Customer Router:

```
interface dialer 2
  encapsulation ppp
  ip address negotiated

  ! Authenticate inbound only:
  ppp chap hostname <Username on ISP Router, eg.Cust1>
  ppp chap password <Password on ISP Router>

  mtu 1492
  dialer pool <dial-pool-num>

  no shutdown

interface GigabitEthernet0/1
  no ip address
  pppoe enable
  pppoe-client dial-pool-number <dial-pool-num>
  no shutdown
```

Note that The PPPoE header adds an additional 8 bytes to each segment. To prevent TCP sessions from being dropped, the maximum segment size (MSS) needs to be adjusted to its optimum value on the physical interface.

```
interface <intf-id>
  ip tcp adjust-mss 1452
```

### PPPoE Verification

```
show interface dialer <number>
show ip route
show pppoe session

debug ppp negotiation
debug ppp authentication
```

### GRE Configuration

To implement a GRE tunnel, the network administrator must first learn the IP addresses of the endpoints. After that, there are five steps to configuring a GRE tunnel:

1. Create a tunnel interface using the `interface tunnel <number>` command.
2. Configure an IP address for the tunnel interface. This is normally a private IP address.
3. Specify the tunnel source IP address.
4. Specify the tunnel destination IP address.
5. (Optional) Specify GRE tunnel mode as the tunnel interface mode. GRE tunnel mode is the default tunnel interface mode for Cisco IOS software.


```
interface Tunnel 0
  tunnel mode gre ip
  ip address <ipv4-addr> <netmask>
  tunnel source <ipv4-addr>
  tunnel destination <ipv4-addr>
```

### Verify GRE

```
show ip interface brief | include Tunnel
show interface Tunnel <num>
```

### BGP Configuration

```
router bgp <as-number>
  neighbor <ip-address> remote-as <as-number>
  network <network-address> [mask <network-mask>]
```

To advertise a default route:

```
  network 0.0.0.0
```

Note that above method is not the best way to advertise a default route in eBGP but the better ways are beyond the scope of CCNA4.

### Verify BGP

```
show ip route
show ip bgp
show ip bgp summary
```
