# Network-automation
This is design for my project
Access switch:

conf t
vlan 10
name IT
vlan 11
name sales
vlan 12
name server


int range fa0/0 - 1
switchport trunk encapsulation dot1q
switchport mode trunk

int fa0/3
switchport mode access
switchport access vlan 10

int fa0/4
switchport mode access
switchport access vlan 11

int fa0/5
switchport mode access
switchport access vlan 12


Distributed or core switch:
Core1:
conf t
hostname core1
vlan 10
name IT
vlan 11
name sales
vlan 12
name server
ip routing
int range f0/2 - 6
switchport trunk encapsulation dot1q
switchport mode trunk



interface vlan10
ip address 192.168.10.1 255.255.255.0
no sh
ip ospf 1 area 0

standby 10 ip 192.168.10.3 
standby 10 priority 200
standby 10 preempt

interface vlan11
ip address 192.168.11.1 255.255.255.0
no sh
ip ospf 1 area 0

standby 11 ip 192.168.11.3 
standby 11 priority 200
standby 11 preempt

interface vlan12
ip address 192.168.12.1 255.255.255.0
no sh
ip ospf 1 area 0

standby 12 ip 192.168.12.3 
standby 12 priority 200
standby 12 preempt

int fa0/1
no sw
ip add 10.10.10.2 255.255.255.252
no sh
ip ospf 1 area 0


ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.11.1 192.168.11.10
ip dhcp excluded-address 192.168.12.1 192.168.12.10

ip dhcp pool ITvlan
default-router 192.168.10.3
network 192.168.10.0 255.255.255.0
dns-server 8.8.8.8

ip dhcp pool Salesvlan
default-router 192.168.11.3
network 192.168.11.0 255.255.255.0
dns-server 8.8.8.8

ip dhcp pool servervlan
default-router 192.168.12.3
network 192.168.12.0 255.255.255.0
dns-server 8.8.8.8

Core2:
conf t
hostname core2
vlan 10
name IT
vlan 11
name sales
vlan 12
name server
ip routing
int range f0/2 - 6
switchport trunk encapsulation dot1q
switchport mode trunk



interface vlan10
ip address 192.168.10.1 255.255.255.0
no sh
ip ospf 1 area 0

standby 10 ip 192.168.10.3 


interface vlan11
ip address 192.168.11.1 255.255.255.0
no sh
ip ospf 1 area 0

standby 11 ip 192.168.11.3 

interface vlan12
ip address 192.168.12.1 255.255.255.0
no sh
standby 12 ip 192.168.12.3 
ip ospf 1 area 0

int fa0/1
no sw
ip add 12.10.10.2 255.255.255.252
no sh
ip ospf 1 area 0

ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.11.1 192.168.11.10
ip dhcp excluded-address 192.168.12.1 192.168.12.10
ip dhcp pool ITvlan
default-router 192.168.10.3
network 192.168.10.0 255.255.255.0
dns-server 8.8.8.8

ip dhcp pool Salesvlan
default-router 192.168.11.3
network 192.168.11.0 255.255.255.0
dns-server 8.8.8.8

ip dhcp pool servervlan
default-router 192.168.12.3
network 192.168.12.0 255.255.255.0
dns-server 8.8.8.8


R0
conf t
hostname mainrouter
int gi0/0/0
ip add 10.10.10.1 255.255.255.252
no sh
ip ospf 1 area 0



int gi0/0/1
ip add 200.1.1.1 255.255.255.252
no sh


access-list 1 permit 192.168.10.0 0.0.0.255
access-list 1 permit 192.168.11.0 0.0.0.255

ip nat inside source list 1 interface GigabitEthernet0/0/1 overload


int gi0/0/0
ip nat inside

int gi0/0/1
ip nat outside



ip route 0.0.0.0 0.0.0.0 200.1.1.2

router ospf 1
default-route information originate

ISP
en
conf t
hostname ISP
int gi0/0/0
ip add 200.1.1.2 255.255.255.252
no sh

int lo0
ip add 8.8.8.8 255.255.255.255























