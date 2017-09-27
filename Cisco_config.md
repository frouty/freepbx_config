# Cisco
sg500-28P    
## Default  
login :  cisco  
passwd : cisco

## emulator
https://www.cisco.com/assets/sol/sb/SG500_Emulators/SG500_Emulator_v1-2-9/config/home_SG_500-52P_1_2_9.htm
## firmware
https://software.cisco.com/download/release.html?mdfid=284099563&softwareid=282463181&release=1.2.0.97
## recuperer le passwd
pas possible à faire à distance : http, ssh  
Il faut avoir un acces au switch.   
- Step 1. Connect the switch to the computer using a standard 9-pin serial cable sur le port rj45 marqué `console` 
- connectique RJ45 sur le switch 
- connectique DB9 sur le PC
- utiliser secureCRT, Tera term, putty
- https://sbkb.cisco.com/CiscoSB/ukp.aspx?pid=3&vw=1&articleid=4984
- Set
  - 115000 baud
  - 8 data bits
  - no parity
  - 1 stop bit
  - no flow control
- reboot the switch
- Apparition du logo 
- taper escape ou return  
- choisir [3] Password Recovery Procedure : press 3. Reset the password to null
- 'current password will be ignored'
- Enter
- Exit to exit. This start the autoboot sequence
- le switch ne fait rien mais attendre qu'il boot.
- Switch>enable
- Switch> copy startup-config running-config pour eviter de surcharger la configuration existante du switch.
- Enter global configuration mode : Switch#>config
- Create a new username and password combination (username can be whatever, password too) : 
    - Switch (config)#>username cisco password P@$$w0rd privilege 15
    - Switch (config)#>exit
    - Switch (config)#>write
    - Switch (config)#>reload to reboot and 
- Save the password into the startup config
    - Switch#>copy run start

That will set a new password in the running (and startup config) so that you will be able to access the switch after a reboot.

## Changer de passwd
- débrancher tous les cables
- appuyer sur reset avec un trombone en facade avant du switch  
- et voilà

## time
Time setting / System time : Main Clock Source (SNTP Servers):	 Enable
SNTP unicast 
SNTP Client Unicast: Enable
Uncicast SNTP Server Table : Add adress domain name server.



# Port Configuration on the Sx500 Series Stackable Switches
https://supportforums.cisco.com/t5/small-business-switches/port-configuration-on-the-sx500-series-stackable-switches/ta-p/3146252

# Installation and Setup of Cisco SG500-52P - 500 Series Stackable Managed Switches
http://www.firewall.cx/cisco-technical-knowledgebase/cisco-switches/885-cisco-switches-sg500-52p.html
- show system mode
- set system mode router  ----> cela efface toute configuration comme le mote de passe aussi.
- show system mode
- retrouver l'adresse ip du switch
- http:/ipduswitch (cisco/cisco)
- changement de password.
- passer en IP static
- IP configuration / IPv4 Interface
- check the box to the VLAN 1 interface et delete it
- click add new interface pour VLAN 1 
- 1 configurer les vlans
- 2 configurer les vlans interfaces pour les adress IP.
- `configure terminal`
```
#interface vlan 1
#ip address 192.168.1.2 255.255.255.0
#exit
```
- on fait la meme chose pour le VLAN2 Voice VLAN et VLAN8 Guest VLAN
```
#vlan 2
#interface vlan 2
# name Voice-VLAN
# ip address 192.168.10.2 255.255.255.0  ----> 192.168.10.2 - 192.168.10.254
# exit
#vlan 5
#interface vlan 5
# name Guest-VLAN
# ip address 192.168.50.2 255.255.255.0
# exit
```
```
# hostname SG500
# ip default-gateway 192.168.1.1
# ip name-server 192.168.1.1 for dns resolution.
# ip routing
```
- Sauvegarder la configuration

`#copy running-config startup-config`

Le switch crée automatiquement le routage pour les device du VLAN quand ils sont pluggés au switch. Pour le voir aller dans IP configuration / IPv4 Routes. Tant qu'il n'y a pas de device branché il n'y a pas de route.

Pour qu'un VLAN puisse accéder à l'internet il faut créer une route static.
```
Navigate to IP Configuration > IPv4 Routes click Add and then create a route with the following information:
Destination Prefix; 0.0.0.0
Mask: Prefix Length: 0
Route Type: Remote
Next Hop Router IP Address: The address to your firewall -> mine is 10.20.30.1
Metric: 1
```

# Vlans
port vlan peuvent être:  
- tagged = trunk
- untagged = access

Le trunk c'est pour faire passer le traffic de plusieurs vlan et l'access c'est pour un seul vlan.  
En pratique le trunk c'est pour relier des switch et l'access c'est pour se relier à des end devices.  


Les trunks sont plus compliqués à configurer. Les deux terminaisons du link doivent etre configurés de la meme facon:  
-Encapsulation
-Allowed vlan's
-Native Vlan.

SG500 est un switch layer 3 on peut créer de multiples vlan et router entre eux = interVLAN routing.   
Mais par default il est layer 2. Il faut passer en layer 3 = router mode . Et quand on passe en layer 2 on perd toute configuration

## Obtenir des infos sur le switch
### Comment savoir si le switch est en layer 2 ou layer 3 

Status and statistic / system Summary  
- system informations:
  - System operational mode : pour savoir si on est layer 2 ou layer 3
  
  `show system mode`
  `show run`
### Router show command
`show running-config`  
`show ip interface brief`  
`show ip route`  
`show arp`  
`show cdp neighbors`  

`show mac address-table`  
`show vlan brief`

`sh ip int vlan 1`
`sh ip int vlan 40`

## comment passer en layer 3 
- 1 brancher le cable.
- 2 configurations:
  - 115200 baud rate
  - 8 data bits
  - No parity.
  - 1 Stop bit
  - No flow control.
  
  - `show system mode`
  - `set system mode router`

## Passage en L3
Administration / System mode and stack management / System mode / L3 mode  
apply and reboot  
toujours en L2  
Il fallait autoriser les popup bloqués par firefox.  OK
success  
apply and reboot  
déconnection du pc
reconnection avec ifup (n'a pas marché avec wicd)  
on a tout perdu dans la config du cisco notamment du passwd.
new passwd  

## Creation des vlan
### Creation d'un VLAN 1 
```
switch# configure terminal
switch(config)# interface vlan 1
switch(config-if)# ip address 192.168.1.2 255.255.255.0
switch(config-if)# exit
```

### Creation d'un VLAN 2 pour Voice VLAN
```
switch(config)# vlan 2
switch(config)# interface vlan 2
switch(config-if)# name Voice-VLAN
switch(config-if)# ip address 192.168.10.2 255.255.255.0
switch(config-if)# exit
```
### Creation d'un VLAN 5 pour les guests
```
switch(config)# vlan 5
switch(config)# interface vlan 5
switch(config-if)# name Guest-VLAN
switch(config-if)# ip address 192.168.50.2 255.255.255.0
switch(config-if)# exit
```
### set the hostname of the switch
```
switch(config)# hostname SG500
SG500 (config)# ip default-gateway 192.168.1.1
SG500 (config)# ip name-server 192.168.1.1
SG500 (config)# ip routing # enable ip routing
```

VLAN 2 (VLAN ID equals 2), our Voice VLAN, and configured all but one port to carry VLAN 2 traffic as Tagged. When configuring a VLAN as Tagged traffic, the port automatically becomes a trunk port  
When configuring a VLAN to Untagged it then becomes the Native VLAN for that port. 

Finally, Port GE1 is forbidden to carry VLAN2 traffic. The reason for this is that we plan to connect our Internet router on port GE1 and there is no reason for our Voice VLAN traffic to exist on that port, for security reasons of course.

prot to vlan et port Vlan membership ca permet de configurer les vlans. C'est un peu la meme chose. Mas port to vlan est plus pratique.

### Configuring Voice VLAN
Vlan management / Voice Vlan / properties / 
- voice vlan ID : 2
- desable Dynamic Voice Vlan

Note: If problems are experienced with the IP Phones registering to CallManager or CallManager Express, make sure to Enable the Dynamic Voice VLAN feature and setting it to Enable Auto Voice VLAN.

### Copy/Save configuration
Les configuration du switch sont volatiles. Pour les rendre persistentes au prochain démarrage il faut faire 
- Source File Name : Running configuration
- Destination file name : Startup configuration.
- Apply

### configuration backup restore
https://sbkb.cisco.com/CiscoSB/GetArticle.aspx?docid=c590f1ecbe8a4b80a57ea955a3407534_Download_Backup_Configuration_File_on_the_Sx500_Series_Stack.xml&pid=2&converted=0

Administration / File management / Download/backup configuration/log.

- Running configuration la configuration courante
- Startup memory la config qui est enregistrée dans le bootup memory
- Backup configuration un backup
- Mirror configuration
- Flash log


File Management > Download/Backup Configuration/Log from the main menu. Here, select HTTP method and Backup action. Finally select Running or Startup Configuration depending on your requirements and Apply

### Verify
`show ip route`
`show ip interface brief`


#Configuration des devices branchés sur le switch Cisco.
Est ce qu'on peut utiliser le DHCP?
- Créer un pool DHCP sur le switch L3, qui va assigner aux end devices, qui sont sur différents subnets, une adresse IP.
-configure
-dhcp pool vlan-10
-network 10.0.0.0 255.255.255.0 ---> on met le subnet du vlan.
-default-routeur on met l'adresse IP de l'interface du VLAN.
-`show running-config`
- et on met les end device en dhcp. 


Sinon le parametre gateway du end device est l'adresse IP de l'interface VLAN. 

Si j'ai bien compris on peut ping depuis un device sur un VLAN vers un autre device d'un autre VLAN sans rien configurer de particulier.


## inter vlan routing

https://www.cisco.com/c/en/us/support/docs/lan-switching/inter-vlan-routing/41860-howto-L3-intervlanrouting.html

Quand on crée des vlan on segmente le réseau. On diminue le broadcasting et on améliore la sécurité.
- un réseau pour les services critiques:
  - serveur de fichier
  - serveur d'impression
Mais il faut qu'un user d'un vlan puisse utilise un service d'un autre vlan.

- 1 IP route enable
  - IP configuration / Management and IP interfaces / IPv4 interface
  - Double check IPv4 interface table
- 2 Verifier la VLAN configuration
  -Si un port rejoint un VLAN le port peut etre ACCESS ou TRUNK
  -Si un port rejoint plusieurs VLAN / Port peut etre mis à TRUNK ou GENERAL

# How to inter-VLAN routing with Cisco SG series SMB Layer 3 switches
- 1 on branche le switch sur le routeur principal. Il obtient une adresse DHCP. On cherche qu'elle est cette adress IP.
- 2 on utilise un navigateur pour aller sur cette IP. 
- 3 Changer en mode 3. Administration / System settings / System mode 3 / apply. Toutes la configuration est effacée et le switch reboot. 
- 4 nouvelle adresse DHCP a retrouver. Mise en place d'une adresse static. IP Configuration / IPv4 Interface.
Si j'ai bien compris il faut delete le VLAN 1 interface. et Add et donner une adresse static dans le subnet du routeur. 
 
- 5 Creation des VLAN. VLAN Management / Create VLAN / Add / Apply.
- 6 Assign à chaque VLAN une IP. IP configuration/IPv4 interface/Add/Set interface to VLAN et selectionner VLAN ID. IP address Type Static/. Le switch crée le routing pour le VLAN qu'une fois qu'il y a un device branché sur le VLAN. on peut le vérifier dans IP configuration / IPv4 Routes.  

Le VLAN 1 est créé par défaut. Il répond au serveur DHCP. Mais il a aussi comme IP par défaut: 192.168.1.254
```
switch# configure terminal
switch(config)# interface vlan 1
switch(config-if)# ip address 192.168.1.2 255.255.255.0
switch(config-if)# exit
```
Apres on configure les autres VLAN
```
switch(config)# vlan 2
switch(config)# interface vlan 2
switch(config-if)# name Voice-VLAN
switch(config-if)# ip address 192.168.10.2 255.255.255.0
switch(config-if)# exit
switch(config)# vlan 5
switch(config)# interface vlan 5
switch(config-if)# name Guest-VLAN
switch(config-if)# ip address 192.168.50.2 255.255.255.0
switch(config-if)# exit
```
```
My test vlans are as follows:
VLAN 1 - Management - 192.168.1.2
VLAN 2 - Data - 172.16.2.1
VLAN 3 - Telephony - 172.16.3.1
VLAN 4 - SecureWireless - 172.16.4.1
VLAN 5 - GuestWifi - 172.16.5.1
```
Sur le switch il faudra une default route : `ip route 0.0.0.0 0.0.0.0 192.168.1.1`
Sur le routeur il faudra des routes pour les network sur le switch SG500:
`ip route 172.16.2.0 255.255.255.0 192.168.1.2`
`ip route 172.16.3.0 255.255.255.0 192.168.1.2`
`ip route 172.16.4.0 255.255.255.0 192.168.1.2`
`ip route 172.16.5.0 255.255.255.0 192.168.1.2`

# VLAN routing solutions
On peut brancher un cable pour chaque sur le switch et brancher sur  router.
On peut utiliser un seul lien qui transporte de multiples VLAN. Mais le routeur doit etre capable de tagger 
On peut utiliser un switch layer 3


# Tagged/untagged
Le vlan c'est une séparation logique des réseaux. Sans vlan il faut un switch pour chaque broadcast domain.  
Si on a plus d'un vlan sur un port (trunk port) il faut un moyen de savoir quel paquet appartient à quel vlan de l'autre coté. On fait cela avec un tag. On VLAN tag le paquet ou VLAN header. C'est le 802.1Q qui contient un VLAN-ID et d'autres trucs.   


The above answers are quite technical. Think of it this way:

In fact VLANs and tagging is nothing more than a logical separation of networks in contrast to a physical one. Now what does that mean?

If there were no VLANs you would need one switch for each broadcast domain. Imagine the cabling involved and also the potential number of NICs required at the hosts. So first, VLANs allow you to have multiple independent layer 2 constructs within the same switch.

Since now you can have multiple networks on each link/port you have to somehow be able to distinguish which packet belongs to which network. That's why they are tagged. If a port carries more than one VLAN it's also usually called a trunk. (for n>1 VLANs, at least n-1 VLANs have to be tagged and there can be one untagged VLAN, the native VLAN)

Generally you have to distinguish packets at port ingress (incoming "from the cable") and egress (outgoing "into the cable"):

Ingress

    ingress untagged: this is where the native vlan of the port comes in. If the switch has multiple VLANs configured, you have to tell the switch to which VLAN an incoming untagged packet belongs ;

    ingress tagged: well, if it comes in tagged, then it's tagged, and you can't do much about it. If the switch doesn't know about tagging or about that precise VLAN, it will reject it, sometimes you have to activate some kind of ingress-filter though. You can also force a port to accept untagged or tagged packets only.

Egress

    egress untagged: for each port you can select one VLAN whose outgoing packets on that port are not tagged (e.g. because the host doesn't support it, or only one VLAN is required for example for a PC, printer, etc.) ;

    egress tagged: You have to tell the switch which VLANs to make available on the port and if more than one, all but one have to be tagged anyway.

What happens inside the switch

A switch has an FDB (Forwarding DataBase) which

    in a switch that is not VLAN capable (sometimes called "unmanaged" or "dumb", ...): associates a host (MAC address) to a port: the FDB is a table comprised of tuples of two elements: (MAC, port)

    in a switch that is VLAN capable (sometimes called "managed" or "smart", ...): associates (VLAN, MAC) tuples to a port: the FDB is a table comprised of tuples of three elements: (MAC, port, VLAN).

    The only restriction here is that one MAC address cannot appear in the same VLAN twice, even if on different ports (essentially the VLAN in VLAN-capable switches replaces the notion of port in non-VLAN-capable switches). In other words:
    There can be multiple VLANs per port (which is why there need to be tags at some point).
    There can be multiple VLANs per port and per MAC: the same MAC address can appear in different VLANs and on the same port (although I wouldn't recommend that for sanity purposes).
    The same MAC address still cannot appear on the same VLAN but on different ports (different hosts having the same MAC address in the same layer 2 network).
    
    
tagged port = trunk port     
Un device connecté à un tagged port doit comprendre les VLAN. 
Si on veut accéder à un device à partir de VLAN multiples, on a plusieurs options:
      - Utiliser un device avec des capacités de routage (router ou layer 3 switch)
      - Utiliser plusieurs interfaces dans le device, une pour chaque VLAN.  Et on connecte chaque interface à un untagged port sur son VLAN correspondant.

On groupe les ports du switch dans des VLAN. Chaque VLAN a un ID. Chaque port du switch peut etre `Tagged`, `Untagged`,`Excluded`. 
Les paquets tagged ne sont compris que par les device qui acceptent les VLAN. 
Si le port est untagged. il ne mettra pas de tag au paquet sortant et enlevera le tag du paquet entrant.  
Excluded. Les paquet avec ce VLAN-ID ne sera jamais envoyé.

Prenons 3 VLAN: 
VLAN 1 has IP net 192.168.1.0/24
VLAN 2 has IP net 192.168.2.0/24
VLAN 3 has IP net 192.168.3.0/24

Ces 3 VLAN sont des reseaux séparés qui partagent le meme switch. Comment est ce qu'on connecte un device de stockage?  
-1 On met en place de intervlan routing. On creé un VLAN 4 et on met le storage device dans ce réseau. On le connecte à un untagged access port. Et mettre en place des regles de routage pour que les autres VLAN peuvent joindre le VLAN 4. 
-2 On ne veut/peut pas mettre en place. On connecte le device de storage à un tagged port membre du VLAN 1-3 et créer 3 interfaces sur le device une pour chaque VLAN. 
## IEEE 802.1Q
Est le standard qui supporte le VLAN avec tagging.
### Multiple Spanning Tree Protocol
C'est une révision de ce protocole.

# ACL (access control list) et access control entry (ACE)
un ACL contient le host qu'un device peut accesder ou pas.
C'est une liste de source d'address IP qui utilise le layer 3 pour permettre ou pas l'accés.   
Un ACE contient le critere de l'access rule. L'ACE s'applique à l'ACL. 
https://sbkb.cisco.com/CiscoSB/ukp.aspx?login=1&pid=2&app=search&vw=1&articleid=3025&donelr=1  
On crée d'abord un ACL puis on crée un ACE que l'on rattache à un ACL ou plusieurs ACL. 


# Save de la configuration avant de passer en L3:

# Cisco traceroute
traceroute 8.8.8.8
192.168.1.1
[....]

traceroute 192.168.1.11
192.168.1.11

# File management / Download Backup configuration
ne marche pas sur chrome

# IP configuration
IPv4 interface table. Je vois que le VLAN 1 a : IP adress type : dhcp , IP address: 192.168.1.48 qui est l'adresse pour l'administration. Je l'attends à voir les autres ip pour les différents ports.

IPv4 Routes est assez clair. 0.0.0.0 route type: remote next hop 192.168.1.1 qui est l'ip du router.
192.168.1.0/24 local next hop: 0.0.0.0

ARP table:
Je n'ai que trois entrée 192.168.1 et 11  (mon PC) et 52 (?). Je l'attendais à plus d'entrées. car d'autres PC sont allumés en ce moment.

# IP configuration du VLAN 2
IP configuration / IPv4 interface / add / interface : VLAN 2 / IP address type : static IP adress 192.168.2.1 network mask 255.255.255.0 / apply / close.

La qu'est ce que j'ai fait.
Est ce que l'ip du port 22 qui est associé au VLAN 2 est à 192.168.2.1?  
Est ce que les clients qui vont se brancher sur les ports du VLAN 2 vont etre servis par la dhcp avec du 192.168.2.0?  

# Connection un switch Layer 3 vers internet
pc1 192.168.20.102 ---- switch L3  192.168.1.1 ----- 192.168.1.2 Router ----- internet

## Layer 3 switch
-1 Créer les vlans
-2 Assigner les ports
-3 Créer les interfaces
-4 enable layer 3 function to switchport connected to the router.
-5 enable and configure IP routing
	- 
	-6 enable une route static
## for the router
pas adapté à ma configuration.



# configuring a gateway of last resort using IP command.

https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/16448-default.html

Firstly, One-To-One NAT is mapping multiple public IPs to multiple private IPs. The common NAT that most of you folks do at home, is to map your public IP address and a port number to a private IP address and a port number. The reason you use port numbers is because you only have 1 public IP address and using different ports are a good way to share that 1 public IP with a host of services you want to run (which obviously run on different ports). When you have a couple of public IPs, you can afford to map the entire public IP to an entire private IP including all its ports. So for example, If I had public IP addresses, eg: 136.130.20.11 – 136.130.20.15 (5 public IPs) I could map each one of those addresses to machines on my private IP range:

136.130.20.11 -> 172.28.1.11
136.130.20.12 -> 172.28.2.12
136.130.20.13 -> 172.28.3.13
136.130.20.14 -> 172.28.9.14
136.130.20.15 -> 172.28.9.15




# Configurer un switch avec une IP.

On peut assigner une IP static avec `ip address ip_address network_mask`  
Dynamic :`ip address dhcp`

## Sur quel type d'interface peut-on assigner une IP?
## Quelle est la différence entre un switch port et une interface.
- switch port n'a pas d'intelligence, il performe uniquement du L2 en forwardant les frames basés sur la MAC address. C'est comme un bridge, forwardant d'une layer à une même layer. Pas d'assignation possible d'une IP virtuelle. Il n'y a qu'une adress MAC.
- interface a sa propre intelligence, il encapsule les frames depuis L2 vers les laayers supérieurs en paquet. Une interface est multilayer et a une adresse MAC et peut avoir une adresse IP virtuelle L3.

Comme les switch L2 ne peuvent avoir d'IP virtuelle sur aucun des ports, Cisco a introduit le concept de SVI (Switch Virtual Interface), sur lequel on peut assigner une IP.

Sur switch L3 on peut utiliser les SVI et lui assigner une IP ou on peut assigner directement une IP à une interface.

Mais un switch L3 peut avoir une switch port ou une interface. Comment tranformer un switch port vers une interface:  
`switchport` ou `noswitchport` 

```
SW(config)#interface f0/1
SW(config-if)#no switchport
SW(config-if)#ip address ip_address network_mask
```

# SSH ENABLE  
ssh ne fonctionne pas.  
Pour le mettre en route.
security / TCP/UDP service / ssh enable.

# CLI

SW1> c'est le user mode  pour passer en administrateur on fait:
SW1> enable  
SW1# priviledge mode  
SW1# disable  
SW1# Erase  
SW1# erase startup-config  
SW1# delete flash:vlan.dat  
SW1# reload  

SW1# configure terminal
SW1(config)#  
SW1(config)#hostname NEWNAME  
NEWNAME(config)# end  

NEWNAME# show ip route  

## Comment définir une IP address pour le switch
il faut le faire pour pouvoir le manager à distance. C'est une management IP address
SW1# config t  
SW1(config)# interface VLAN 1  
SW1(config-if)# ip address 192.168.1.254 255.255.255.0  

## default gateway  
SW1(config-if)# exit  
SW1(config)#ip default-gateway ip-adress-du-router-local (qui est dans le meme subnet que le switch)  
SW1(config)#exit  
SW1# copy running-config startup-config  
SW1#reload  

## confirmation
SW1# show startup-config  
SW1# show running-config  
sW1# show version  
SW1# show interfaces  
SW1# show interfaces fa0/22  

## MAC address table (layer 2)
SW1# show mac address-table dynamic

## 
SW1# on peut faire du ping.

# routing protocoles
-EIGRP  
-RIPV2  
-OSPF  

# static routing

Si on ping une IP et que cela ne marche pas il faut faire un :

SW1# show ip route

On peut ajouter une route static

SW1# conf t  
SW1(config)#  ip route ip_subnet_you_want_to_go subnetmask nominterface(fa0/0) ou IP
SW1#(config)# end  
SW1# show ip table  
SW1# ping ip_you_want_to_go. Ca ne marche pas.  

Attention il faut etre sur que le ping puisse revenir.

SW1# ip route 0.0.0.0 0.0.0.0 ip. Cela veut dire que quoi que ce soit je l'envois sur ip.  
SW1# show ip route on voit une asterisk. Gateway of last resort.


SW1#show running-config

# configuration de l'ip par port


 IP Configuration - IPV$ address - ADD - Interface = port.




##
Tous les pc peuvent se pinguer : inter-vlan marche
Le pc du vlan 1 peut sortir sur internet par le router qui est dans le vlan1
Les pc du VLAN2 ne peuvent aller sur internet.
Cela veut dire qu'il manque une route static dans le router back vers chaque VLAN.


VLAN 1 (192.168.9.254) qui est connecté au router(192.168.9.29)
Dans le switch ip default-gateway 192.168.2.29

Les interfaces VLAN du switch savent passer le traffic WAN vers 192.168.2.254 et VLAN 1 sait que la passerelle c'est 192.168.2.29. Le probleme c'est que 192.168.2.29 ne sait pas comment atteindre 192.168.20.0/24 192.168.60.0/24 ....
https://supportforums.cisco.com/t5/small-business-switches/sg500-vlan-configuration/td-p/2243839

show inventory gigabitethernet 2/1/49

essayer `sh int status`

https://www.packet6.com/getting-started-with-ciscos-500-series-smb-switches/

`sh vlan brief` on the switch. This will show you the vlans on the switch and which ports are allocated into which vlan. You should then see, as Glen said, that port allocated to vlan 1.
Un port ne peut etre unallocated il est par defaut VLAN1

`show ip interface brief`


```
switch(config)# interface fa0/1
switch(config-if)# switchport access vlan 9
switch(config-if)# switchport mode access


switch# show run

switch# sh vlan br
```

Voir page 1223 du cli guide


# CLI commnands
## user level
prompt = nom du switch suivi par #.
## Priviliged mode
Un utilisateur de niveau 7 à 15 se logue automatiquement en priviliged
`enable` `disable`
## Global config mode
pour configurer au niveau system  
seul les utilisateurs 7 à 15 peuvent y accéder  
`configure`  
prompt = nom du switch suivi par (config)#  
go back : `exit` `end` `CTRL Z`  
## Sous mode de global config
- 1 Interface
  - port
  - VLAN
  - Port channel (tunnel)

- 2 Line Interface
  - `line` pour entrer dans ce mode
  - configure management connections for console, telnet, ssh
- 3 VLAN database
  - `vlan database`
- 4 Management access list
  - `management access-list`
- 5 Port Channel
   - `interface port-channel`
- 6 QoS
  - `qos`
- 7 MAC access List
  - `mac access-list`
- Exit : `exit`
  

# interface naming convention

Type d'interface:
- Gigabit Ethernet gi ou GE
- TenGigabit Ethernet port : te ou xg
- LAG (port channel) po
- VLAN
- Tunnel tu

Unit number. Qd il y a pas de stack c'est toujours 1

Slot number 1 toujours

SW(config)#interface gi1/1/1  
Sw(config-if)#

# ip route
## exemple
- 1 router les paquets pour le subnet 172.3.0.0 to a router à 172.31.6.6
	- SW(congig)# ip route 172.3.0.0 255.255.255.0.0 172.31.6.6
	- SW(congig)# ip route 172.3.0.0/16 172.31.6.6
- 2 rejeter les paquets pour le subnet194.1.1.0
	- SW(config)# ip route 194.1.1.0 255.255.255.0 reject-route
- 3 effacer toutes les routes statiques pour 194.1.1.0
	- SW(config)# no ip route 194.1.1.0/24
- 4 effacer une statique route du subnet 194.1.1.0 vers 1.1.1.1
	- SW(config)# no ip route 194.1.1.0/24 1.1.1.1

# Switchport mode
## exemple
SW(coonfig)#interface gi1/1/1  
SW(config-if)#switchport mode access  
SW(config-if)#switchport access vlan 2  

- If a switchport will only be on 1 van, use access mode, not trunk mode.  
switchport mode access  
switchport access vlan X

# switchport trunk allowed vlan
# switchport trunk native vlan
# switchport general allowed vlan



commands ||
-------  | ------|
sh run | | 
show ip interface [interface_id :gi1/1/22]||
show ip interface vlan 1||
show arp||
show ip dhcp client interface ||
show ip protocols [summary] |for debugging routing| 
show ip route||
show ip route summary||
interface ip ip-address | ip int|
copy running-config startup-config||
ip default-gateway 192.168.0.1 | |

# Comment obtenir une nouvelle adress ip sur un client?
`sudo dhclient -r`  
Je ne sais pas si cela marche


