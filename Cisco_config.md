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
 `̀ ̀
switch# configure terminal
switch(config)# interface vlan 1
switch(config-if)# ip address 192.168.1.2 255.255.255.0
switch(config-if)# exit```

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
```switch(config)# hostname SG500
SG500 (config)# ip default-gateway 192.168.1.1
SG500 (config)# ip name-server 192.168.1.1
SG500 (config)# ip routing # enable ip routing`̀ ` 

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
