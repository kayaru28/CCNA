## MAC ADDRESS
* (config)#`mac address-table static <mac-address> vlan <vlan> interface <interface-id>`
* (config)#`mac address-table aging-time <time> vlan <vlan>`
* #`show mac address-table [dynamic|static]`
* #`show mac address-table aging-time`



## VLAN
Trunk Protocol = 802.1Q / ISL

### access port

> For steps
1. (config)#`vlan 1,2,10-20,40`
1. (config-vlan)#`name <vlan-name>`
1. (config-if)#`switchport mode access`
1. (config-if)#`switchport access vlan 2`

* #`show vlan {[brief] | [id <vlan-id>]} [name <vlan-name>]`(*switch)
* #`show interfaces fastethernet 0/3 switchport`
* #`show mac address-table`

### trunk port
> word=DTP
* (config-if)#`switchport mode {dynamic desirable | dynamic auto}`

> Three steps at switch
1. (config-if)#`switchport trunk encapsulation {isl | dot1q | negotiate}`
1. (config-if)#`switchport mode trunk `
1. (config-if)#`switchport nonegotiate`

* #`show interfaces [<interface-id>] trunk`
* #`show interfaces [<interface-id>] switchport`
* #`show vlan {[brief] | [id <vlan-id>]} [name <vlan-name>]`(*switch)
* #`show interfaces vlan <vlan-name>`
* #`show run`
* (config-if)#`switchport trunk native vlan <vlan-id>`
* (config-if)#`switchport trunk allowed vlan {add|all|except|remobe} <vlan-id>`  

> Three steps at router
* (config)#`interface fastethernet 0/0.100`
* (config-subif)#`encapsulation dot1q <vlan-id> [native]`
* (config-subif)#`ip address <ip-address> <subnet-mask>`
* #`show ip interface [brief]`
* #`show ip route`
* #`show vlans [<vlan-id>]`
* #`show vlan-switch [brief]`  

> Two steps at L3switch
1. (config)#`ip routing`
1. (config)#`interface vlan <vlan-id>`
1. (config-f)#`ip address <ip-address> <subnet-mask>`

### other
> word=VTP
* (config)#`vtp domain <domain-name>`
* (config)#`vtp mode {server|client|transparent|off}`
* (config)#`vtp password <password>`
* (config)#`vtp pruning`
* (config)#`vtp version {1|2|3}`
* #`show vtp status`
* #`show vtp password`
* (config)#`vtp mode transparent`
* (config)#`vtp mode off`
* #`delete vlan.dat`
* (config)#`vlan <vlan-id>`
* (config-vlan)#`shutdown`
* (config)#`shutdown vlan <vlan-id>`

## STP

### CSP
> BPDU
* MaxAge 20s
* Hellow 2s
* Configuration BPDU = normal root to no root
* TCA BPDU = return for TCN BPDU
* TC BPDU
* TCN BPDU

> For steps
1. select Root Bridge
   * mimum bridge id = priority + MAC address
   * priority 32768 [0,65535] 0 or a*4096
1. select Root Port
   * minimum root path cost at a non root bridge
   * cost 10Gbps=2 1Gbps=4 100Mbps=19 10Mbps=100
   * →bridge id of sender → port id of sender
1. select Designated Port
   * minimum root path cost at a segment
   * →bridge id of sender → port id of sender
1. determin Non Designated Port

> status
1. blocking 20s(Max Age)
1. listening 15s
1. learning 15s
1. forwarding
1. disable

### PVST+
> bridge ID
* 4bit  for priority
* 12bit for vlan iD
* 48bit for mac address

* (config)#`spanning-tree mode pvst`
* (config)#`no spanning-tree vlan <vlan-id>`
* (config)#`spanning-tree vlan <vlan-id>`
* #`show spanning-tree [vlan <vlan-id>][detail]`
* #`show spanning-tree root`
* (config)#`spanning-tree vlan <vlan-id> priority <priority>`
* (config)#`spanning-tree vlan <vlan-id> root primary`
* (config)#`spanning-tree vlan <vlan-id> root secondary`
* (config-if)#`spanning-tree portfast`
* (config)#`spanning-tree portfast default`
* #`show spanning-tree summary`
* (config)#`spanning-tree portfast bpduguard default`
* (config)#`spanning-tree bpduguard enable`
* (config)#`spanning-tree bpduguard disable`

### RSTP
> for ports
1. Root
1. Designated
1. Alternate
1. Backup


> Three status
1. discarding
1. learnign
1. forwarding

* (config)#`spanning-tree mode rapid-pvst`

## EtherChannel
* max = 8

### PAgP
* (config-if-group)#`channel-group <group-num> mode {desirable [non-silent]|auto [non-silent]}`

### LACP
* (config-if-group)#`channel-group <group-num> mode {active|passive}`

### common
> L2
1. (config)#`interface range fastethernet 0/1 - 2`
1. (config-if-range)#`switchport mode access`
1. (config-if-range)#`switchport access vlan 10`
1. (config-if-range)#`channel-group 3 mode on`

> L3
1. (config)#`interface port-channel <group-num>`
1. (config-if)#`no switchport`
1. (config-if)#`ip address <ip-address> <subnet-mask>`
1. (config)#`interface range fastethernet 0/1 - 2`
1. (config-if-range)#`no switch-port`
1. (config-if-range)#`channel-group <group-num> mode {on|desirable|auto|active|passive}`

* (config)#`port-channel load-balance <method>`
* method = src-/dst-/src-dst- + mac/ip/port
* #`show etherchannel [summary]`
* #`show interfaces etherchannel`
* #`show etherchannel port-channel`
* #`show etherchannel load-balance`
* #`show interfaces status`

## FHRP
### HSRP
* virtual mac 0000.0c07.acXX
* priority > ip address
* Hellow 3s 224.0.0.2
* Hold 10s

> Three steps
1. (config-if)#`standby [<group-num>] ip [<ip-address>]` group-num 0 
1. (config-if)#`standby [<group-num>] priority <priority>` priority 100 [0,255]
1. (config-if)#`standby [<group-num>] preempt`

* (config-if)#`standby [<group-num>] preempt {delay minimum {second}}`
* #`show standby [<interface-type>] [<group-numvwe>] [brief]`
* #`debug standby`
* (config-if)#`standby [<group-num>] authentication <word>`
* (config-if)#`standby [<group-num>] authentication md5 key-string <word>`
* (config-if)#`standby [<group-num>] track <interface-type> [<decrement>]`

### VRRP
* virtual mac 0000.5e00.01XX

### GLBP
* virtual mac 0007.b400.XXYY
* AVF/AVG
* Hellow 3s 224.0.0.102
* Hold 10s

> For Steps
1. (config-if)#`glbp <group-num> ip [<ip-address>]`
1. (config-if)#`glbp <group-num> priority <priority>`
1. (config-if)#`glbp <group-num> preempt`
1. (config-if)#`glbp <group-num> load-balancing [host-dependent|roud-robin|weighted]`

* #`show glbp [<interface-type>] [<group-numvwe>] [brief]`

## routing
### static
* (config)#`ip route <address> <mask> {<next-hop>|<interface>} {<AD>} [permanent]`

### rip
* multi 224.0.0.9
* (config)#`router rip`
* (config-router)#`network 172.16.0.0`
* (config-router)#`network 192.168.1.0`
* (config-router)#`version 2`
* (config-router)#`no auto-summary`
* (config-router)#`passive-interface FastEthernet0/0`
* (config-router)#`maximum-paths 6`
* (config-router)#`default-information originate`
* #`show ip route`
* #`show ip protocols`
* #`debug ip rip`
* (config-router)#`neighbor <ip-address(dest)>`

### EIGRP
* multi 224.0.0.10
* Successor = minimum FD
* Feasible Successor　= AD < Successor's FD
* DUAL
* metric = 256 * [(K1*band)+(k2*band)/(256-load)+(K3*delay)] * [K5 * (reliability+4)] 1 0 1 0 0 

> Two steps
1. (config)#`router eigrp <as-num>`
1. (config-router)#`network <network-num> [<wildcard-mask>]`

* #`show ip protocols`
* #`show ip eigrp neighbors`
* #`show ip route [eigrp]`
* #`show ip eigrp interfaces [detail]`
* #`show ip eigrp topology [all-links]`
* #`show ip eigrp traffic`
* #`debug ip eigrp`
* #`debug eigrp packets`
* (config-router)#`passibe-interface {<interface>|defalt}`
* #`show ip protocols`
* (config-router)#`maximum-paths <value>`
* (config-router)#`variance <multiplier>` * multi of FD
* (config-router)#`auto-summary`
* (config-router)#`metric weights 0 0 1 0 1 0`

> Five steps`
1. (config)#`key chain <name-of-chain>`
1. (config-keychain)#`key <key-id>` 0~2147483647
1. (config-keychain-key)#`key-string <text>`
1. (config-keychain-key)#`accept-lifetime <start-time> {<end-time>|inginite|duration <seconds>}`
1. (config-keychain-key)#`send-lifetime <start-time> {<end-time>|inginite|duration <seconds>}`
1. (config-if)#`ip authentication mode eigrp <as-number> md5`
1. (config-if)#`ip authentication key-chain eigrp <as-number> <name-of-chain>`

* (config-router)#`shutdown`

> EIGRPv6
1. (config)#`ipv6 unicast-routing`
1. (config)#`ipv6 router eigrp <as-num>`
1. (config-if)#`ipv6 eigrp <as-number>`
1. (config-router)#`eigrp router-id <x.x.x.x>`

* #`show ipv6 protocols`
* #`show ipv6 eigrp neighbors`
* #`show ipv6 route [eigrp]`
* #`show ipv6 eigrp interfaces [detail]`
* #`show ipv6 eigrp topology [all-links]`
* #`show ipv6 eigrp traffic`

### OSPF
* multi 224.0.0.5/224.0.0.6
* BMA  Hello 30s / Dead 120s
* PtoP Hello 10s / Dead 40s
* NBMA Hello 10s / Dead 40s
* Hello/DBD/LSR/LSU/LSAck
* priority 1 [0,255]
* area [0,4294967295(2^32-1)]

1. (config)#`router ospf <process-id>`
1. (config-router)#`network <address> <wildcard-mask> area <area-id>`
1. (config-router)#`passive-interface fastethernet 0`
1. (config-router)#`default-information originate [always]`
1. (config-if)#`ip ospf <process-id> area <area-id>`
1. (config-if)#`ip ospf cost 100`

* #`show ip protocols`
* #`show ip ospf`
* #`show ip ospf neighbor`
* #`show ip ospf interface [brief]`
* #`show ip route`
* #`show ip ospf database`
* (config-router)#`router-id <x.x.x.x>`
* (config-router)#`maximum-paths <values>`
* (config-if)#`ip ospf priority <priority>`
* (config-if)#`ip ospf hello-interval <seconds>`
* (config-if)#`ip ospf dead-interval <seconds>`
* #`clear ip ospf process`

> Two steps authentication if
1. (config-if)#`ip ospf authentication [message-digest | null]`
1. (config-if)#`ip ospf authentication-key <password>`
1. (config-if)#`ip ospf message-digest-key <key-id> md5 <password>`

> Two steps authentication area
1. (config-router)#`area <area-id> authentication [message-digest]`
1. (config-if)#`ip ospf authentication-key <password>`
1. (config-if)#`ip ospf message-digest-key <key-id> md5 <password>`

> OSPFv3
* FF02::5,FF02::6

1. (config)#`ipv6 router ospf <process-id>`
1. (config-rtr)#`router-id <x.x.x.x>`
1. (config-if)#`ipv6 ospf <process-id> area <area-id>`

* #`show ipv6 protocols`
* #`show ipv6 ospf`
* #`show ipv6 ospf neighbor`
* #`show ipv6 ospf interface [brief]`
* #`show ipv6 route [ospf]`
* #`show ipv6 ospf database`

> NBMA
* (config-router)#`neighbor <ip-address>`

### BGP
> For messages
* OPEN
* UPDATE
* KEEPALIVE hellw 60s holddown 180s
* NOTIFICATION

> Two status
* Idle → Connect → Active
* Idle → Connect → Open Sent → Open Confirm → Established

* (config)#`show ip bgp neighbors`
* (config)#`show ip bgp summary`
* (config)#`show ip bgp`
* (config)#`show ip route`

* Path attribute = max Cisco WEIGHT > max LOCAL_PREF > Local Route > min AS_PATH > min ORIGIN > min MED

1. (config)#`router bgp <as-number>` as-number=1~65535
1. (config-router)#`neighbor <ip-address> remote-as <as-number>`
1. (config-router)#`network <ip-address> mask <subnet mask>`

* (config)#`router bgp <as-number>`
* (config-router)#`timers bgp <keepalive-time(s)> <holdtime(s)>`
* (config-router)#`neighbor <ip-address> timer <keepalive-time(s)> <holdtime(s)>`

* (config-router)#`neighbor 10.2.2.2 update-source loopback 0`
* (config-router)#`neighbor 10.2.2.2 ebgp-multihop 2`

* (config)#`route-map map-tag permit | deny [ seq-number ]`
* (config-route-map)#`match condition`
* (config-route-map)#`set origin [ egp as-number | igp | incomplete ]`

## WAN
****
## Serial
* #`show controllers [<interface-id>]`
* (config-if)#`clock rate <speed(bps)>`
* (config-if)#`bandwidth <BW(kbps)>`
* (config)#``

### HDLC
* (config-if)#`encapsulation hdlc`

### PPP
* LCP / NCP
* PAP / CHAP
* (config-if)#`encapsulation PPP`

> Tree steps for auth
1. (config)#`hostname <hostname>`
1. (config)#`username <username(dst)> password <password>`
1. (config-if)#`ppp authentication {chap|pap|chap pap|pap chap}`

* #`debug ppp authentication`

### flame relay
* (config-if)#`encapsulation frame-relay [cisco|ietf]`
* (config-if)#`frame-relay lmi-type {cisco|ansi|q333a}`
* (config-if)#`[no] frame-relay inverse-arp <protocol> <dlci>`
* (config-if)#`frame-relay map <protocol> <address> <dlci> {broadcast] [ietf]`
* (config-if)#`no ip split-horizon [eigrp <as-number>]`

1. (config)#`interface <interface>.<subinterface> {point-to-point|multipoint}`
1. (config-subif)#`frame-relay interface-dlci <dlci-number>`

* #`show interfaces`
* #`show frame-relay lmi`
* #`show frame-relay pvc`
* #`show frame-relay map`
* #`show frame-relay lmi`
* #`show ip route`

### VPN
> IPsec
* AH / ESP / ESP+AH
* DES / 3DES / AES
* MD5 / SHA
* DH1 / DH2 / DH5

> GRE
>> Five steps
* (config)#`interface tunnel <number>`
* (config-if)#`ip address <ip-address> <subnet-mask>`
* (config-if)#`tunnel source {<ip-address>|<interface>}`
* (config-if)#`tunnel destination <ip-address>`
* (config-if)#`tunnel mode grp ip`

## Others

### PPPoE
* (config)#`interface fastethernet 0/0`
* (config-if)#`pppoe-client dial-pool-number 10`
* (config)#`interface Dialer1`
* (config-if)#`mtu 1492`
* (config-if)#`ip address negotiated`
* (config-if)#`encapsulation ppp`
* (config-if)#`dialer pool 10`

### routing algorithm
* OSPF  = SPF \ Dijkstra's algorithm 
* RIP   = Bellman–Ford algorithm
* EIGRP = Diffusing Update Algorithm (DUAL)

### priority
* Root Bridge = minimum priority
* Root Port = minimum root path cost / source bridge id / source port id
* Desg Port = minimum root path cost / source bridge id / source port id

### IEEE list
* IEEE802.3  Ethernet CSMA/CD
* IEEE802.11 wireless LAN CSMA/CA
* IEEE802.1Q trunk
* IEEE802.1d STP
* IEEE802.1w RSTP

### SPAN
* (config)#`monitor session 1 source interface <interface-id>`
* (config)#`monitor session 1 destination interface <interface-id>`

### QoS
* IntSer(RSVP) / DiffServ Best Effort

#### DiffServ

1. Clasification
    * by ip-address / port / CoS / DSCP / IP Preference
1. Marking
    * switch = L2 or L3 marking
    * router = L3 marking
   > L2 marking
    * by CoS of 3bit in dot1Q tag
   > L3 marking
    * by IP Precedence of 3bit or DSCP of 6 bit in ToS field
1. Queuing
    * FIFO
    * PQ(Prioirty Queuing)
    * WFQ(Weighted Fair Queuing)
    * CBWFQ(Class Based WFQ)
    * LLQ (Low Latency Queuing) = PQ & CBWFQ
1. Port Base
    * (config)#`mls qos`
    * (config)#`interface gigabitethernet1/0/4`
    * (config-if)#`mls qos cos 5`
    * (config-if)#`srr-queue bandwidth limit 20`
    * (config-if)#`priority-queue out`
    * (config-if)#`exit`
1. MQC Base
    * (config)#`access-list 101 permit ip host 172.16.1.100 any`
    * (config)#`access-list 102 permit ip host 172.16.1.200 any`
    * (config)#`class-map match-all C-VOICE`
    * (config-cmap)#`match access-group 101`
    * (config)#`class-map match-all C-KANRI`
    * (config-cmap)#`match access-group 102`
    * (config)#`policy-map P-MARK`
    * (config-pmap)#`class C-VOICE`
    * (config-pmap-c)#`set dscp ef`
    * (config-pmap)#`class C-KANRI`
    * (config-pmap-c)#`set dscp af31`
    * (config)#`interface FastEthernet 0/1`
    * (config-if)#`service-policy input P-MARK`

    * (config)#`class-map match-all C-EF`
    * (config-cmap)#`match dscp ef`
    * (config)#`class-map match-all C-AF31`
    * (config-cmap)#`match dscp af31`
    * (config)#`policy-map P-CHILD`
    * (config-pmap)#`class C-EF`
    * (config-pmap-c)#`priority percent 25`
    * (config-pmap)#`class C-AF31`
    * (config-pmap-c)#`bandwidth percent 10`
    * (config-pmap)#`class class-default`
    * (config-pmap-c)#`fair-queue`

    * (config)#`access-list 111 permit ip any 172.16.1.0 0.0.0.255`
    * (config)#`access-list 112 permit ip any 172.16.2.0 0.0.0.255`

    * (config)#`class-map match-all C-CE2`
    * (config-cmap)#`match access-group 111`
    * (config)#`class-map match-all C-CE3`
    * (config-cmap)#`match access-group 112`

    * (config)#`policy-map P-PARENT`
    * (config-pmap)#`class C-CE2`
    * (config-pmap-c)#`shape average 15000000`
    * (config-pmap-c)#`service-policy P-CHILD`
    * (config-pmap)#`class C-CE3`
    * (config-pmap-c)#`shape average 15000000`
    * (config-pmap-c)#`service-policy P-CHILD`

    * (config)#`interface FastEthernet 0/0`
    * (config-if)#`service-policy output P-PARENT`

### NetFlow version 5
> key field
* source ip
* destination ip
* source port
* destination port
* layer 3 protocol
* ToS（Type of Service）
* input interface
