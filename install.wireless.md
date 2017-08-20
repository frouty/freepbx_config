# router TP LINK ARCHER C7 V2.0
# install CHAOS CALMER 15.05 r46767
# liste des fichiers déjà installés
`opkg list | grep <nomdupaquet>`  
`opkg info <nomdupaquet>`
# Savoir si nodosplash est on
`/usr/bin/ndsctl status` 
# apres avoir edité /etc/config/network
`/etc/init.d/network reload`
# /etc/config/network
## section global
## section switch
il y a deux format:  
-1 /proc/switch de type:  
```
config 'switch' 'eth0'
        option 'vlan0' '0 1 2 3 5*'
        option 'vlan1' '4 5'
```
-2 swconfig (newer)
```
cconfig 'switch' 'eth0'
        option 'reset' '1'
        option 'enable_vlan' '1'

config 'switch_vlan' 'eth0_1'
        option 'device' 'eth0'
        option 'vlan' '1'
        option 'ports' '0 1 2 3 5t'

config 'switch_vlan' 'eth0_2'
        option 'device' 'eth0'
        option 'vlan' '2'
        option 'ports' '4 5t'onfig 'switch' 'eth0'
        option 'reset' '1'
        option 'enable_vlan' '1'

config 'switch_vlan' 'eth0_1'
        option 'device' 'eth0'
        option 'vlan' '1'
        option 'ports' '0 1 2 3 5t'

config 'switch_vlan' 'eth0_2'
        option 'device' 'eth0'
        option 'vlan' '2'
        option 'ports' '4 5t'
```
Les propriétés communes sont dans la section 'switch'.  
Les propriétés spécifiques à chaque VLAN sont dans les sections 'switch_vlan' et liées à la section 'switch' par l'option 'device'.  

The TP-Link Archer C7 has:
* eth0 = WAN, and
* eth1 = LAN (the 4 switch ports).
* Port 0 of the switch = eth1 (labelled CPU in Luci),
* Port 6 = eth0.
* Port 1 is labelled WAN in Luci.

## section interfaces : /etc/config/network
Les sections interfaces sont des containers pour les adresses IP, aliases, routes, nom physique des interfaces and regles firewall. 
```
config 'interface' 'wan'
        option 'proto' 'dhcp'
        option 'ifname' 'eth0.1'
```
'wan' est le nom logique de l'interface. Parfois suivant le protocole le system ajoute un prefix automatiquement.  Pas plus de 9 caracteres.  
Les options sont différentes suivant les protocoles : https://wiki.openwrt.org/doc/uci/network

On retrouve les infos de :
- network/interfaces
- general network  avec le ula-prefix
- des infos sur le switch avec des vlan que l'on trouve dans le web GUI network/switch

# TP LINK
login : admin  
password : admin  
installation de openwrt  
mise en place d'un nouveau password
login openwrt: root  
password : 
changement de l'adresse IP lan 
désactivation du dhcp sur LAN. 
reboot

https://wiki.openwrt.org/toh/tp-link/archer-c5-c7-wdr7500

https://wiki.openwrt.org/doc/howto/wireless.hotspot.nodogsplash



## wifi 
par defaut n'est pas enable.  
J'ai deux wifi:
-  Qualcomm atheros QCA9880 (5GHz)
- Generic MAC (2.462 Ghz)
Qui ont le meme ssid
Quand je lance un scan il ne se passe rien que le wifi soit actif ou inactif en 5 Ghz
Quand je lance un scan avec le wifi en 2.4Ghz activé j'ai beaucoup de ssid.

### interface configuration
general setup / mode :

- Access point 
 - c'est le master device. maintient la liste des stations associées, security policies, 
- Client
- ad hoc 
 -est utilisé pour créer un reseau wifi sans Access point. 
- pseudo ad hoc
- 802.11s
- monitor:
 - passive only mode. pas de paquet transmit. Tous les paquets entrants sont transmits à l'ordinateur. 
- cleint (WDS)
- Access point (WDS)
C'est quoi tout cela?

general Setup / network . C'est quoi?

## br-lan 
`brctl show`  
Dans un router cela sert de gateway entre deux réseaux. Ce qui permet le routing, et les regles de firewall , pour controler quel trafic va passer d'un réseau à un autre.  
Souvent le WIFI est bridgé avec le lan pour creer automatiquement un bridge entre les deux. 

Dans un bridge on ne configure pas l'IP sur l'interface individuelle connectée au bridge. 

`ls -l /sys/class/net`
## Créer un VLAN
On met dans le VLAN et dans ce VLAN on va mettre un port du swith. Et on va enlever ce meme port des autres VLAN. 
Et il faut aussi mettre le port CPU sinon on ne peut passer de traffic vers le port du switch

Ce nouveau VLAN crée une nouvelle interface (Que l'on voit où?) 
VLAN : Untagged dit au switch de mettre le tag à chaque trame internet qui quitte ce port. 
VLAN : tagged le switch s'attends à ce que la trame soit déjà tagguée.

switch ports :

- 0	eth1
- 1	WAN
- 2	LAN1
- 3	LAN2
- 4	LAN3
- 5	LAN4
- 6	eth0

Si on compare au web GUI: on a:

- CPU qui est dans le vlan 1
- Port 1 qui est dans le vlan 2
- Port 2 qui est dans le vlan 1
- port 3 qui est dans le vlan 1
- Port 4 qui est dans le vlan 1
- Port 5 qui est dans le vlan 1
- Port 6 qui est dans le vlan 2

# Routed access point


* 1 on creé une nouvelle interface dans le /etc/config/network:
	- netmask
    - static
    - ipaddr
```
config 'interface' 'wifi'
       option 'proto'      'static'
       option 'ipaddr'     '192.168.2.1'
       option 'netmask'    '255.255.255.0'

```
Il faut que l'ip soit dans un autre subnet que celle du LAN.

Dans le web gui, on voit apparaitre une nouvelle interface dans network/interface. Mais il dit 'install protocole extension' 'unsupported protocol type'

Au niveau des interfaces si on va dans network/interfaces/physical settings on voit que l'on a :

* eth0 (wan et wan6) qui est définit dans 'network'
```
config interface 'wan'
        option ifname 'eth0'
        option proto 'dhcp'

config interface 'wan6'
        option ifname 'eth0'
        option proto 'dhcpv6'
```

* les vlan interfaces qui sont définit tjs dans network:

```
config switch
	option name 'switch0'
	option reset '1'
	option enable\_vlan '1'
	
config switch\_vlan
	option device 'switch0option vlan '1'
	option ports '0 2 3 4 5'

config switch_vlan
        option device 'switch0'
        option vlan '2'
        option ports '1 6'
```

* eth1 qui est définit tjs dans network
```
config interface 'lan' 
        option ifname 'eth1'
        option force_link '1' 
        option type 'bridge' 
        option proto 'static'
        option ipaddr '192.168.1.254' 
        option netmask '255.255.255.0'
        option ip6assign '60'
```
Je ne trouve pas dans quel fichier on configure le bridge, où on dit avec quelle interface physique c'est bridgé.

* les deux interfaces wireless qui sont définits dans wireless:
		
* 2 il faut modifier wifi-iface pour qu'elle soit liée à la nouvelle interface
```
config wifi-iface
        option device   radio1
        option network  wifi
        option mode     ap
        option ssid     MyRoutedAP
        option encryption none
```

Une fois que l'on a fait cela on voit dans le web gui qu'il n'y a plus le message de "unsupported protocol"

* 3 Si j'ai bien compris on peut attacher un server dhcp à chaque interface. On va donc definir un nouveau dhcp dans /etc/config/dhcp
		
* 4 configuration du firewall dans /etc/config/firewall. On définit une nouvelle zone pour cette interface 'wifi'


```
config zone
        option name       wifi
        list   network    'wifi'
        option input      ACCEPT
        option output     ACCEPT
        option forward    REJECT		
```		
et dans le web gui on voit apparaitre sous network/firewall l'icone WIFI avec REJECT.

Je peux me connecter avec le smartphone. J'ai une adress ip en 192.168.2.x, subnet 255.255.255.0 passerelle 192.168.2.1. Mais je ne peux pas aller sur 192.168.1.1. 

On ajoute un forwarding
#On essaie de passer sur le LAN 
```
config forwarding
	option src wifi
    option dest lan
```

On voit appaitre le forward.

Mais cela ne me permet pas d'accéder à internet

# On essaie autrement.
On active l'interface network/wifi radio0 qui est bridgé avec l'interface nommée LAN. (tout à l'heure on avait crée une nouvelle interface WIFI et on avait lié une interface wireless avec cette interface WIFI).  
On obtient une adress IP en 192.168.1.x. Mais la passerelle est 192.168.1.254. Et je ne peux toujours pas aller sur le net. On trouve ce  paramtetre dans network/interface/LAN/Edit/general setup/IPv4 gateway. Je le change en 192.168.1.1 / save apply 
Et on voit apparaitre la derniere ligne dans /etc/config/network.  
```
config interface 'lan'
	option ifname 'eth1'
	option force_link '1'
	option type 'bridge'
	option proto 'static'
	option ipaddr '192.168.1.254'
	option netmask '255.255.255.0'
	option ip6assign '60'
	option gateway '192.168.1.1'
```

et c'est bon cela marche. 

# NAT
## Source NAT
On altere l'adresse source. On change donc d'où vient la connection. C'est fait en post routing juste avant d'envoyer le paquet. Masquerading est une forme particuliere de SNAT.
## Destination NAT
On altère l'IP de destination. On change où va la connection. C'est toujours fait before routing quand la paquet sort du cable.

- Port forwardging
- load sharing
- transparent proxy

# ARP
Dans la vie courante les niveaux du model OSI que l'on utilise sont les niveaux 2 3 et 7. 
Pour ARP c'est le niveau 2. C'est le niveau sur lequel les switch travaille et cela concerne les adresses MAC. 
OSI Level 3 : IP address
OSI Level 2 : MAC address
Quand une machine envoie un paquet dans le réseaux elle utilise l'adresse MAC de la machine cible. Quand elle envoie un paquet sur les réseaux public elle envoie à la MAC adress du gateway. L'adresse IP est utilisée pour trouver l'adresse MAC dans la table ARP

`ifconfig` or `ip addr ls` `ip a` sont équivalents.

Pour obtenir l'adresse MAC remotely: tcpdump  `tcpdump -lni any arp`

# GUEST WLAN
Quand le wireless est disable ifconfig ne donne pas d'interface wifi.  
On crée une nouvelle interface wifi. on l'associe à un network auquel on donne un nom. 
On a donc crée une nouvelle interface que l'on retrouve dans network / interfaces.  
Il faut configurer cette interface : Edit. On choisit une adress static, un netmask, une gateway.
Il faut créer une zone (dans l'onglet firewall Settings).
Il faut configurer le firewall  : Network / Firewall. guest zone edit. et là je comprends pas.

* 1 on crée une nouvelle interface:
```config interface 'guest'
       option proto 'static'
       option ipaddr '10.0.0.1'
       option netmask '255.255.255.0
'```

* 2 On configure l'interface wifi. 
```config wifi-iface
       option device     '???'
       option mode       'ap'
       option network    'guest'
       option ssid       'guest'
       option encryption 'none'```
       option isolate 1 # parfois non supportée par certains matériels. 

option device : on me la valeur de option 'device' dans la section wifi-device.

* 3 On configure DHCP pour cette interface.
edit /etc/config/dhcp
[..]
  config dhcp 'guest'
    option interface 'guest'
    option start '50'
    option limit '200'
    option leasetime '1h'
  [..]
       
# failsafe mode
- débrancher tous les cables rj45 du tp link archer
- power on et en meme temps wds button. Attendre c'est long que la deuxieme diode en partant de la gauche clignote rapidement c'est long. 
- brancher le cable RJ45 sur le port WAN
- ping 192.168.1.1
- telnet 192.168.1.1
- root@(none):/# 
- root@(none):/# mount_root    
on peut voir les fichiers de config dans /overlay/upper/etc/config. Mais on peut tout détruire pour repartir sur des bases saines avec:
- root@(none):/#mtd -r erase rootfs_data  
Unlocking rootfs_data ...  
Erasing rootfs_data ...  
Rebooting ...  


- est-ce qu'on peut faire firstboot et rien d'autre pour obtenir le meme résultat. Marche pas..
