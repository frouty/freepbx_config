# Cisco
sg500-28P    
## Default  
login :  cisco  
passwd : cisco
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

# -
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
  
 ## creation des vlan
 ### Creation d'un VLAN 1 
```
switch# configure terminal
switch(config)# interface vlan 1
switch(config-if)# ip address 192.168.1.2 255.255.255.0
switch(config-if)# exit
```

### Creation d'un VLAN 2 pour Voice VLAN
```switch(config)# vlan 2
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
`̀ ` 

VLAN 2 (VLAN ID equals 2), our Voice VLAN, and configured all but one port to carry VLAN 2 traffic as Tagged. When configuring a VLAN as Tagged traffic, the port automatically becomes a trunk port  
When configuring a VLAN to Untagged it then becomes the Native VLAN for that port. 

Finally, Port GE1 is forbidden to carry VLAN2 traffic. The reason for this is that we plan to connect our Internet router on port GE1 and there is no reason for our Voice VLAN traffic to exist on that port, for security reasons of course.

prot to vlan et port Vlan membership ca permet de configurer les vlans. C'est un peu la meme chose. Mas port to vlan est plus pratique.

### Configuring Voice VLAN
Vlan management / Voice Vlan / properties / 
- voice vlan ID : 2
- desable Dynamic Voice Vlan

Note: If problems are experienced with the IP Phones registering to CallManager or CallManager Express, make sure to Enable the Dynamic Voice VLAN feature and setting it to Enable Auto Voice VLAN.
###Copy/Save configuration
Les configuration du switch sont volatiles. Pour les rendre persistentes au prochain démarrage il faut faire 
- Source File Name : Running configuration
- Destination file name : Startup configuration.
- Apply
### configuration backup
File Management > Download/Backup Configuration/Log from the main menu. Here, select HTTP method and Backup action. Finally select Running or Startup Configuration depending on your requirements and Apply


## inter vlan routing
Quand on crée des vlan on segmente le réseau. On diminue le broadcasting et on améliore la sécurité.
- un réseau pour les services critiques:
  - serveur de fichier
  - serveur d'impression
Mais il faut qu'un user d'un vlan puisse utilise un service d'un autre vlan.
###VLAN routing solutions


# Tagged/untagged
Le vlan c'est une séparation logique des réseaux. Sans vlan il faut un switch pour chaque broadcast domain. 
si on a plus d'un vlan sur un port (trunk port) il faut un moyen de savoir quel paquet appartient à quel vlan de l'autre coté. On fait cela avec un tag. On VLAN tag le paquet ou VLAN header. C'est le 802.1Q qui contient un VLAN-ID et d'autres trucs (qui l'on peut trouver dans le 


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
    Si on veut accesder à un devic à partir de VLAN multiples, on a plusieurs options:
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
