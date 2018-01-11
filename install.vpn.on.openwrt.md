# Tplink Archer C7 V2.0 (V2.0 c'est marqué sur l'etiquette de la boite)


metre dans /etc/profile:   
export PS1='\[\033[35;1m\]\u\[\033[0m\]@\[\033[31;1m\]\h \[\033[32;1m\]$PWD\[\033[0m\] [\[\033[35m\]\#\[\033[0m\]]\[\033[31m\]\$\[\033[0m\] '
c'est plus jolie.

switch port

- 0  eth1
- 1  wan
- 2  lan1
- 3  lan2
- 4  lan3
- 5  lan4 
- 6  eth0

le port wan (je pense que c'est le connecteur rj45 bleu). Il est connecté à eth0

https://wiki.openwrt.org/doc/howto/secure.access


Dans la configuration du routeur a aucun moment je n'ai besoin de donner le ipv4 gateway adresse ni l'adresse du dns server. Je ne comprends pas pourquoi.

# interet du vpn:
Guest network access can easily be granted because you do not need to care about the things your guests are using your Internet for. 

https://blog.ipredator.se/howto/openwrt/configuring-openvpn-on-openwrt.html

Cela veut dire qu'il faudra payer un abonnement à un server VPN extérieur.


# pour connaitre les paquets disponibles 
`opkg search openvpn`

# pour voir les log :
En console ssh:
`logread -f` 
C'est bien mais on ne peut plus utiliser la console. Donc c'est bien pour faire des manipulations dans le webGUI.

Dans le web GUI : Status / System Log  
Mais cela n'est pas dynamique il faut rafraichir.  

# UCI CLI
Qd on lance une commande UCI il faut faire ensuite

> uci commit \<le nom du module\>

uci commit openvpn pe.

Si pas de commit pas de modification dans le fichier de configuration.

# Config du serveur VPN :

https://wiki.openwrt.org/doc/howto/vpn.openvpn  

Afficher les logs: `Status /system log.`

# Création des clefs
Le fichier de conf de easy-rsa est dans /etc/easy-rsa/vars.
./clean-all pour tout effacer

Seuls les .key files doivent etre gardé confidentiel.
.crt et .csr peuvent etre transférer par une voie insecure (comme les mails)
On ne doit jamais devoir copier un .key d'un computer à un autre.

-
- 1 Build own root certificat authority / key
   - ./build-ca
   - CA : certificat authority.
   - crée ca.crt et ca.key dans le KEY_DIR definit dans var.
- 2 Buid intermediate certificat authority / key. Optionel
  - ./Build-inter inter
- 3 Build a diffie-helmann parameters necessaire pour la partie server terminale d'une connection SSL/TLS
  - ./build-dh. Au deuxieme essais cette commande a pris un temps fou. Je n'ai pas pu en voir la fin.
ca n'a pas marché avec les commandes ci dessus.
- 4
  - ./build-key-server server
- 5 ./build-key-pkcs12 client1

Mais avec :
  
- pkitool --initca                      ## equivalent to the 'build-ca' script
- pkitool --server my-server            ## equivalent to the 'build-key-server' script
- pkitool          my-client            ## equivalent to the 'build-key' script (*not build-key-pkcs12)
- openssl dhparam -out dh2048.pem 2048  ## equivalent to the 'build-dh' script

`cp ca.crt ca.key dh1024.pem server.crt server.key /etc/openvpn/`

On cherche s'il y a une interface tun avec ifconfig -a

On regarde la table de routage avec netsat -nr. On doit avoir et c'est essentiel une route vers le serveur VPN (xxx.xxx.XXX.XXX), une route 
On fait un traceroute pour voir si on va vers le server vpn.

# pkitool --server my-openvpn_server  

choisir un nom my-openvpn_server court et ayant du sens car on va le réutiliser dans d'autres fichiers de config et s'affiche dans le webGUI.

nouveaux fichiers dans /etc/easy-rsa/keys  
  01.pem   
                index.txt.attr  
         my_openvpn_server.key  
ca.crt                 index.txt.old          serial
ca.key                 my_openvpn_server.crt  serial.old
index.txt              my_openvpn_server.csr


# pkitool my-openvpn-client
Choisir un nom court et ayant du sens...


j'ai des nouveaux fichiers my-openvpn-client;*  

Je vois que cela utilise des parametres qui sont dans `/etc/easy-rsa/vars`, que l'on doit pouvoir customiser.

# openssl  dhparam -out dh2048.pem 2048
C'est long ...  
Trop long ... on peut diner.
apres tout ce temps je ne vois pas le fichier dh2048.pem dans /etc/easy-rsa/keys  


# Copie  des certificats
cp /etc/easy-rsa/keys/ca.crt /etc/easy-rsa/keys/my-openvpn-server.* /etc/easy-rsa/keys/dh2048.pem /etc/openvpn

On copie le ca.crt et tous ce qui concerne le serveur et le dh2048.pem  

Si on change le chemin il faudra adapter au fichier de config de openvpn.

# distribution des certificats sur le client openvpn
On fait cela comme on veut. clef usb, mail....etc...   

depuis le ssh du router TPLINk: scp /etc/easy-rsa/keys/ca.crt /etc/easy-rsa/keys/my-openvpn-client.* lfs@ipduclient:/home/lof/TPLINK.  
Mais en fait les mettre dans le /etc/openvpn du client.


On diffuse le ca.crt et tout ce qui concerne le client. On a un .csr .crt .key  
 Il sont dans mon bitbucket maintenant.

 
# distribution des certificats sur le client openvpn
On fait cela comme on veut. clef usb, mail....etc...   

depuis le ssh du router TPLINk: scp /etc/easy-rsa/keys/ca.crt /etc/easy-rsa/keys/my-openvpn-client.* lfs@ipduclient:/home/lof/TPLINK.  
Mais en fait les mettre dans le /etc/openvpn du client.


On diffuse le ca.crt et tout ce qui concerne le client. On a un .csr .crt .key  
 Il sont dans mon bitbucket maintenant.

# Configuration du reseau sur le openwrt router (je ne sais pas si j'ai fait comme cela)

- 1 Create the VPN interface (named vpn0):
```
uci set network.vpn0=interface
uci set network.vpn0.ifname=tun0
uci set network.vpn0.proto=none
uci set network.vpn0.auto=1
```

- 2 Allow incoming client connections by opening the server port (default 1194) in our firewall:
```
uci set firewall.Allow_OpenVPN_Inbound=rule
uci set firewall.Allow_OpenVPN_Inbound.target=ACCEPT
uci set firewall.Allow_OpenVPN_Inbound.src=*
uci set firewall.Allow_OpenVPN_Inbound.proto=udp
uci set firewall.Allow_OpenVPN_Inbound.dest_port=1194
```


- 3 Create firewall zone (named vpn) for the new vpn0 network. By default, it will allow both incoming and outgoing connections being created within the VPN tunnel. Edit the defaults as required. This does not (yet) allow clients to access the LAN or WAN networks, but allows clients to communicate with services on the router and may allow connections between VPN clients if your OpenVPN server configuration allows:
```
uci set firewall.vpn=zone
uci set firewall.vpn.name=vpn
uci set firewall.vpn.network=vpn0
uci set firewall.vpn.input=ACCEPT
uci set firewall.vpn.forward=REJECT
uci set firewall.vpn.output=ACCEPT
uci set firewall.vpn.masq=1
```

- 4 (Optional) If you plan to allow clients to connect to computers within your LAN, you'll need to allow traffic to be forwarded between the vpn firewall zone and the lan firewall zone:
```
uci set firewall.vpn_forwarding_lan_in=forwarding
uci set firewall.vpn_forwarding_lan_in.src=vpn
uci set firewall.vpn_forwarding_lan_in.dest=lan
```

- 5 And you'll probably want to allow your LAN computers to be able to initiate connections with the clients, too.
```
uci set firewall.vpn_forwarding_lan_out=forwarding
uci set firewall.vpn_forwarding_lan_out.src=lan
uci set firewall.vpn_forwarding_lan_out.dest=vpn
```

- 6 (Optional) Similarly, if you plan to allow clients to connect the internet (WAN) through the tunnel, you must allow traffic to be forwarded between the vpn firewall zone and the wan firewall zone:
```
uci set firewall.vpn_forwarding_wan=forwarding
uci set firewall.vpn_forwarding_wan.src=vpn
uci set firewall.vpn_forwarding_wan.dest=wan
```

- 7 Commit the changes:
```
uci commit network
/etc/init.d/network reload
uci commit firewall
/etc/init.d/firewall reload
```

# configuration de OpenVPN 
en tun server

```
echo > /etc/config/openvpn # clear the openvpn uci config
uci set openvpn.myvpn=openvpn
uci set openvpn.myvpn.enabled=1
uci set openvpn.myvpn.verb=3
uci set openvpn.myvpn.port=1194
uci set openvpn.myvpn.proto=udp
uci set openvpn.myvpn.dev=tun
uci set openvpn.myvpn.server='10.8.0.0 255.255.255.0'
uci set openvpn.myvpn.keepalive='10 120'
uci set openvpn.myvpn.ca=/etc/openvpn/ca.crt 
uci set openvpn.myvpn.cert=/etc/openvpn/my-server.crt  # hackme
uci set openvpn.myvpn.key=/etc/openvpn/my-server.key # hackme
uci set openvpn.myvpn.dh=/etc/openvpn/dh2048.pem
uci commit openvpn
```

# demarrage du server
```
/etc/init.d/openvpn enable
/etc/init.d/openvpn start
```

Parfois rien ne se passe. J'ai du faire un reboot pour voir dans services  / openvpn une instance de nom `myvpn` qui correspond au nom dans le fichier /etc/config/openvpn.

cat /tmp/openvpn.log:
```
Tue Jan  2 23:02:33 2018 OpenVPN 2.3.6 mips-openwrt-linux-gnu [SSL (OpenSSL)] [LZO] [EPOLL] [MH] [IPv6] built on Jul 25 2015
Tue Jan  2 23:02:33 2018 library versions: OpenSSL 1.0.2g  1 Mar 2016, LZO 2.08
Tue Jan  2 23:02:33 2018 Diffie-Hellman initialized with 2048 bit key
Tue Jan  2 23:02:33 2018 Socket Buffers: R=[163840->131072] S=[163840->131072]
Tue Jan  2 23:02:33 2018 TUN/TAP device tun0 opened
Tue Jan  2 23:02:33 2018 TUN/TAP TX queue length set to 100
Tue Jan  2 23:02:33 2018 do_ifconfig, tt->ipv6=0, tt->did_ifconfig_ipv6_setup=0
Tue Jan  2 23:02:33 2018 /sbin/ifconfig tun0 10.8.0.1 pointopoint 10.8.0.2 mtu 1500
Tue Jan  2 23:02:33 2018 /sbin/route add -net 10.8.0.0 netmask 255.255.255.0 gw 10.8.0.2
Tue Jan  2 23:02:33 2018 UDPv4 link local (bound): [undef]
Tue Jan  2 23:02:33 2018 UDPv4 link remote: [undef]
Tue Jan  2 23:02:33 2018 MULTI: multi_init called, r=256 v=256
Tue Jan  2 23:02:33 2018 IFCONFIG POOL: base=10.8.0.4 size=62, ipv6=0
Tue Jan  2 23:02:33 2018 Initialization Sequence Completed
```

et je vois apparaitre une nouvelle interface en faisant ifconfig sur le router:

tun0 avec comme ip 10.8.0.1 qui est configurée dans le /etc/config/openvpn.

`/etc/init.d/openvpn enable or disable` se retrouve dans le webgui system / startup 


Mais plus de connection extranet. Je reboot le reouter pas de connection internet. Le serveur openerp a l'air d'etre ok. 
Je le stop et disable dans startup. Je le reboot. et a nouveau pas de connection internet. donc je reboot le modem adsl.
J'essaie de redémarrer le serveur openvpn pour voir si c'est lui qui fait tomber la connection internet. 
- 1 enable dans startup 
- 2 start dans service.
et c'est bon j'ai la connection extranet.

IP 103.17.47.187




Quand est ce qu'on donne l'adresse du serveur vpn? dans le fichier de configuration du client /etc/config/openvpn : option remote SERVER_IP_ADRESS 1194
option remote `pw.openvpn.ipredator.se 1194`

You will need to open port 1194 (TCP) to be forwarding through the firewall to your OpenVPN server.  

You can easily find out your OpenVPN server IP address. The syntax is as follows to get tun0 ip address on Unix or Linux:
`ifconfig tun0`

OR use Linux specific command:
ip a show tun0

dig TXT +short o-o.myaddr.l.google.com @ns1.google.com

## clean-all 
me dit que si je fais ./clean-all alors j'efface les directory /etc/easy-rsa/keys  
mais ./clean-all me dit que la commande n'existe pas.  

mais je me retrouve avec un repertoire keys vide. 


# comment tester le server openvpn
- If you don't mind reconfiguring your network, you could also unplug the modem, plug a computer in its place, set the router WAN to a static IP (192.168.64.1) and the computer on 192.168.64.2, and try connecting to the VPN using the .1 IP.
- En utilisant une connection 3G
- En changeant l'option remote pour qu'elle pointe vers l'adresse IP privée du serveur openvpn.
```
client
dev tap
proto udp
# remote yourddns.dyndns.org 5712
remote 192.168.1.160 5712
```

# Le client sur une debian:
cd /etc/openvpn  
touch client.conf  
y mettre
```
dev tun
proto udp

verb 3

ca /etc/openvpn/ca.crt
cert /etc/openvpn/my-openvpn-client.crt
key /etc/openvpn/my-openvpn-client.key

client 
remote-cert-tls server
remote kuendu.ddns.net 1194
```

On démarre le client avec la commande `service openvpn stop service openvpn start`  
Pas besoin de mettre le chemin du fichier de config le script init va chercher automatiquement les .conf dans /etc/openvp.  

Ou est le fichier de log?

Dans le script /etc/init.d/openvpn on des lignes comme:
log_daemon_msg  
log_progress_msg  
log_end_msg  
log_action_msg  
log_success_msg  
log_failure_msg  
log_warning_msg  

Il semblerait que les msg s'affiche en console.

On trouve le log dans /etc/openvpn/openvpn.log.

- 1 `service openvpn stop service openvpn start`  
rien en console  
ps aux | grep openvpn rien
rien ne bouge dans etc/openvpn/openvpn.log.
dans /var/log rien juste dans syslog started openvpn service.
- 2 `openvpn --config /etc/openvpn/openvpn.log`  en user ca ne marche pas 
rien dans le log. 
j'ai un process avec ps aux | grep openvpn
- 3  `openvpn --config /etc/openvpn/openvpn.log`  (en root)
-4  si je rm le openvpn.log il n'est pas recrée lors de service openvpn start et aussi /etC/init.d/openvpn 




# Quand est ce qu'on donne l'adresse du serveur vpn?
Dans le fichier de configuration du client /etc/config/openvpn : 
- option remote SERVER_IP_ADRESS_PUBLIC 1194  
- option remote 'pw.openvpn.ipredator.se 1194'


YOu will need to open port 1194 (TCP) to be forwarding through the firewall to your OpenVPN server. Cela veut dire qu'il faut faire quoi?

# Comment obtenir l'adresse IP du serveur openVPN?
You can easily find out your OpenVPN server IP address. The syntax is as follows to get tun0 ip address on Unix or Linux:
`ifconfig tun0` sur le device qui fait tourner le serveur

Or use Linux specific command:
`ip -a show tun0` . Ne marche pas.

# Comment obtenir l'adresse public
dig TXT +short o-o.myaddr.l.google.com @ns1.google.com

# Comment voir si tout c'est bien passé:
Afficher les log : `logread -f` en console ssh. Mais on ne peut plus faire de commande en ssh. Il faut une deuxieme console avec un ssh. 
~~~
/etc/init.d/openvpn enable
/etc/init.d/openvpn start
~~~
- If everything went fine, the last log line from OpenVPN should contain Initialization Sequence Completed. There are some warnings and errors ... ignore them.

- `ps | grep openvpn`

- s'assurer que le lien est up avec `ifconfig tun0`, `ifconfig | grep tun` `ifconfig -a`
	- on doit avoir une ligne du genre : ` inet addr:10.8.0.6  P-t-P:10.8.0.5  Mask:255.255.255.255`

- s'assurer que cela fonctionne : `ping 10.8.0.1 -c 2`

- On regarde la table de routage avec netsat -nr. On doit avoir et c'est essentiel une route vers le serveur VPN (xxx.xxx.XXX.XXX)
 
- On fait un traceroute  10.8.0.1 pour voir si on va vers le server vpn. (depuis le client la machine qui heberge le vpn serveur, une machine du LAN du server VPN?)



# comment tester le server openvpn depuis le LAN
- If you don't mind reconfiguring your network, you could also unplug the modem, plug a computer in its place, set the router WAN to a static IP (192.168.64.1) and the computer on 192.168.64.2, and try connecting to the VPN using the .1 IP.
- En utilisant une connection 3G
- En changeant l'option remote pour qu'elle pointe vers l'adresse IP privée du serveur openvpn.

~~~client
dev tap
proto udp
# remote yourddns.dyndns.org 5712
remote 192.168.1.160 5712
~~~~
Si votre openvpn est votre routeur ce sera l'adresse du routeur 192.168.1.1.

# configuration du client

## Quels sont les fichiers à sauvegarder 
- ca.crt
- my.client.crt
- my.client.crs
- my.client.key

###  A sauvegarder dans : /etc/openvp. (de la machine client)


##  Création du fichier de configuration du client:

~~~
dev tun
proto udp

log openvpn.log
verb 3

ca /etc/openvpn/ca.crt
cert /etc/openvpn/my-client.crt
key /etc/openvpn/my-client.key

client
remote-cert-tls server
remote SERVER_IP_ADDRESS 1194
~~~
`SERVER_IP_ADDRESS` on mettra un dyndns fqdm et une regle de NAT sur le firewall pour rediriger vers le serveur.  
Et donc qu'elle est l'adresse du serveur?

openvpn client.conf


## depuis le client
`ifconfig` à la recherche d'un tun  
`ping SERVER_IP_ADDRESS`  
`ping 10.8.0.1` <-- ip address que l'on trouve en faisant un ifconfig tun0  
`traceroute 10.8.0.1` # je ne sais d'ou sort cette IP. Elle n'existe nulle part ailleurs dans les fichiers de config.   
Si on fait un `traceroute 8.8.8.8` sur une adresse IP public on devrait voir que le traffic utilise l'adress gateway par défaut du client.   

Il n'y a pas de tun dans le openwrt qui heberge le vpn server.

## depuis openwrt qui héberge le vpn server.
- `ifconfig tun0`

# Port forwarding

Je ne sais pas du tout s'il faut faire du port forwarding pour openvpn.

On crée une *forward rule*. Je comprends les choses comme cela. Les paquets ip envoyés depuis le remote client arrive sur l'interface WAN du routeur du serveur openvpn car c'est ce que j'ai mis dans le fichier de configuration du serveur. C'est paquet doivent etre redirigé vers le serveur openrp.  
##  Port forwarding 
permet de forwarder le traffic externe vers un hote interne ou un service.  
fichier de configuration : /etc/config/firewall


# Dans le cas d'odoo:
- *Source zone* : wan
- *Protocol* : TCP +UDP
- *external port* 8069
- *internal ip address*: adress ip du serveur dans le LAN
- *internal* port (optional): 8069
Et dans l'onglet Advanced setting
- *Redirection type*: DNAT
- *Destination zone*: LAN
- *intended destination address* : any
- *Source MAC address* : any
- *Source port* : 0-65536 
- *Enable nat loopback*

~~~
config 'redirect'
	option '_name' 'Odoo'
	option 'src' 'wan'
	option 'proto' 'tcpudp'
	option 'src_dport' '8069'
	option 'dest_ip' '192.168.1.110'
	option 'target' 'DNAT'
	option 'dest' 'lan'
	option 'dest_port' '8069'
~~~
~~~
firewall.@redirect[1]._name=Odoo
firewall.@redirect[1].src=wan
firewall.@redirect[1].proto=tcpudp
firewall.@redirect[1].src_dport=8069
firewall.@redirect[1].dest_ip=192.168.1.110
firewall.@redirect[1].target=DNAT
firewall.@redirect[1].dest=lan
firewall.@redirect[1].dest_port=8069
firewall.@redirect[2]=redirect
~~~

# server configuration file
- crée un VPN en utilisant une interface virtuelle TUN pour le routage. 
- ecoute sur le port UDP 1194 pour les connection client.
- distribue des adresses virtuelles aux clients qui se connectent en utilisant le subnet 10.8.0.0/24

# Comment inclure plusieurs machines soit sur le subnet du client soit sur le subnet du serveur?

Il peut etre souhaitable que les clients puisse joindre plusieurs machines dans le subnet du serveur, plutot que seulement le server.
Le LAN du serveur a comme subnet 10.66.0.0/24  
Le pool IP Address du VPN est 10.8.0.0/24 comme indiqué dans la directive *server* du fichier de conf  

-1 il faut informer le client que le subnet 10.66.0.0/24 est accessible par le VPN. Cela se fait par la directive dans le server.conf coté server donc:
	- push "route 10.66.0.0 255.255.255.0"
-2 il faut mettre en place une route sur le server LAN gateway pour router le subnet du client (10.8.0.0/24) vers le serveur openvpn. C'est seulement nécessaire si le server openvpn et le LAN gateway sont sur des machines différantes.   

# Comment inclure de multiples machines dans le subnet du client en utilisant un routed VPN (TUN)
Si le client VPN est une gateway pour un LAN local et que vous voulez que les machines sur le LAN du client puisse router au travers du VPN.  
subnet IP client LAN : 192.168.4.0/24  
 
# configuration du serveur sur une machine qui n'est pas le routeur
fichier /etc/openvpn/server.conf
- directive `local`: to make it listen to an IP adress. C'est l'adress IP vers qui est forwardé UDP port 1194 par le firewall.
- Pour que les remote clients quand ils se connectent sur le server, puissent atteindre autre choses que les que le serveur lui meme il faut activer le ip forwarding avec
- `echo 1 > /proc/sys/net/ipv4/ip_forward`
- ou editer /etc/systcl.conf : `net.ipv4.ip_forward = 1`

On doit pouvoir pinguer n'importe qu'elle machine sur le LAN du server à partir du client. et pouvoir pinguer le client de n'importe qu'elle machine sur le LAN du server

Depuis le client traceroute 192.168.10.65 (ip address LAN du réseau du serveur)
ping 192.168.10.65

# Comment revoir sa configuration
`uci show openvpn`  
`cat /etc/config/openvpn`  
`cat /var/etc/openvpn-myvpn.conf`  
![GitHub Logo](icono/schema.png)


# /etc/init.d/openvpn stop
- /sbin/route del -net 10.8.0.0 netmask 255.255.255.0
- closing TUN/TAP ifconfig tun0 0.0.0.0
- vpn0 connectivity loss and down.
- interface vpn0 is disable.
- dans le webGUI on ne voit pas grand chose juste network / interface / VPN0 tout est à zero. Et Services myvpn started :no

# /etc/init.d/openvpn start
- odhcpd[933]: DHCPV6 REBIND IA_NA from 000100011fcb37e7b827eb8af1d3 on br-lan: ok 
- odhcpd[933]: DHCPV6 CONFIRM IA_NA from 00042a085bb2bdc05a56c90932812284df12 on br-lan: 
- odhcpd[933]: DHCPV6 SOLICIT IA_NA from 00042a085bb2bdc05a56c90932812284df12 on br-lan: ok
- dnsmasq[1403]: read /etc/hosts - 1 addresses
- dnsmasq[1403]: read /tmp/hosts/odhcpd - 0 addresses
- dnsmasq[1403]: read /tmp/hosts/dhcp - 1 addresses
- dnsmasq-dhcp[1403]: read /etc/ethers - 0 addresses
- odhcpd[933]: DHCPV6 REQUEST IA_NA from 00042a085bb2bdc05a56c90932812284df12 on br-lan: ok 8 
- dnsmasq[1403]: read /etc/hosts - 1 addresses
- dnsmasq[1403]: read /tmp/hosts/odhcpd - 1 addresses
- dnsmasq[1403]: read /tmp/hosts/dhcp - 1 addresses
- dnsmasq-dhcp[1403]: read /etc/ethers - 0 addresses
- openvpn(myvpn)[2968]: OpenVPN 2.3.6 mips-openwrt-linux-gnu [SSL (OpenSSL)] [LZO] [EPOLL] [MH] [IPv6] built on Jul 25 2015
- openvpn(myvpn)[2968]: library versions: OpenSSL 1.0.2g  1 Mar 2016, LZO 2.08
- openvpn(myvpn)[2968]: Diffie-Hellman initialized with 2048 bit key
- openvpn(myvpn)[2968]: Socket Buffers: R=[163840->131072] S=[163840->131072]
- netifd: Interface 'vpn0' is enabled

# Mise en route du client openvpn.
 - avant la mise en route : `ifconfig` ne donne pas d'interface tun
 - le fichier de config
 
 ```
 ##############################################
# Sample client-side OpenVPN 2.0 config file #
# for connecting to multi-client server.     #
#                                            #
# This configuration can be used by multiple #
# clients, however each client should have   #
# its own cert and key files.                #
#                                            #
# On Windows, you might want to rename this  #
# file so it has a .ovpn extension           #
##############################################

# Specify that we are a client and that we
# will be pulling certain config file directives
# from the server.
client

# Use the same setting as you are using on
# the server.
# On most systems, the VPN will not function
# unless you partially or fully disable
# the firewall for the TUN/TAP interface.
;dev tap
dev tun

# Windows needs the TAP-Win32 adapter name
# from the Network Connections panel
# if you have more than one.  On XP SP2,
# you may need to disable the firewall
# for the TAP adapter.
;dev-node MyTap

# Are we connecting to a TCP or
# UDP server?  Use the same setting as
# on the server.
;proto tcp
proto udp

# The hostname/IP and port of the server.
# You can have multiple remote entries
# to load balance between the servers.
remote 103.17.45.190 1194
;remote my-server-2 1194

# Choose a random host from the remote
# list for load-balancing.  Otherwise
# try hosts in the order specified.
;remote-random

# Keep trying indefinitely to resolve the
# host name of the OpenVPN server.  Very useful
# on machines which are not permanently connected
# to the internet such as laptops.
resolv-retry infinite

# Most clients don't need to bind to
# a specific local port number.
nobind

# Downgrade privileges after initialization (non-Windows only)
;user nobody
;group nogroup

# Try to preserve some state across restarts.
persist-key
persist-tun

# If you are connecting through an
# HTTP proxy to reach the actual OpenVPN
# server, put the proxy server/IP and
# port number here.  See the man page
# if your proxy server requires
# authentication.
;http-proxy-retry # retry on connection failures
;http-proxy [proxy server] [proxy port #]

# Wireless networks often produce a lot
# of duplicate packets.  Set this flag
# to silence duplicate packet warnings.
;mute-replay-warnings

# SSL/TLS parms.
# See the server config file for more
# description.  It's best to use
# a separate .crt/.key file pair
# for each client.  A single ca
# file can be used for all clients.
ca ca.crt
cert my.client.crt
key my.client.key

# Verify server certificate by checking
# that the certicate has the nsCertType
# field set to "server".  This is an
# important precaution to protect against
# a potential attack discussed here:
#  http://openvpn.net/howto.html#mitm
#
# To use this feature, you will need to generate
# your server certificates with the nsCertType
# field set to "server".  The build-key-server
# script in the easy-rsa folder will do this.
ns-cert-type server

# If a tls-auth key is used on the server
# then every client must also have the key.
;tls-auth ta.key 1

# Select a cryptographic cipher.
# If the cipher option is used on the server
# then you must also specify it here.
;cipher x

# Enable compression on the VPN link.
# Don't enable this unless it is also
# enabled in the server config file.
;comp-lzo

# Set log file verbosity.
verb 3

# Silence repeating messages
;mute 20
```

- dans le client, sous root (a tester sur un user) : `cd /etc/openvpn``openvpn client.conf`
- s'affiche : 
```
OpenVPN 2.3.4 x86_64-pc-linux-gnu [SSL (OpenSSL)] [LZO] [EPOLL] [PKCS11] [MH] [IPv6] built on Nov 12 2015
 library versions: OpenSSL 1.0.1t  3 May 2016, LZO 2.08
 WARNING: file 'my.client.key' is group or others accessible
 Socket Buffers: R=[212992->131072] S=[212992->131072]
 UDPv4 link local: [undef]
 UDPv4 link remote: [AF_INET]103.17.45.190:1194
 TLS: Initial packet from [AF_INET]103.17.45.190:1194, sid=66d68e07 4b481f65
 VERIFY OK: depth=1, C=US, ST=CA, L=SanFrancisco, O=Fort-Funston, OU=MyOrganizationalUnit, CN=Fort-Funston CA, name=EasyRSA, emailAddress=me@myhost.mydomain
 VERIFY OK: nsCertType=SERVER
 VERIFY OK: depth=0, C=US, ST=CA, L=SanFrancisco, O=Fort-Funston, OU=MyOrganizationalUnit, CN=my.server, name=EasyRSA, emailAddress=me@myhost.mydomain
 Data Channel Encrypt: Cipher 'BF-CBC' initialized with 128 bit key
 Data Channel Encrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
 Data Channel Decrypt: Cipher 'BF-CBC' initialized with 128 bit key
 Data Channel Decrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
 Control Channel: TLSv1, cipher TLSv1/SSLv3 DHE-RSA-AES256-SHA, 2048 bit RSA
 [my.server] Peer Connection Initiated with [AF_INET]103.17.45.190:1194
 SENT CONTROL [my.server]: 'PUSH_REQUEST' (status=1)
 PUSH: Received control message: 'PUSH_REPLY,route 10.8.0.1,topology net30,ping 10,ping-restart 120,ifconfig 10.8.0.6 10.8.0.5'
 OPTIONS IMPORT: timers and/or timeouts modified
 OPTIONS IMPORT: --ifconfig/up options modified
 OPTIONS IMPORT: route options modified
 ROUTE_GATEWAY 192.168.1.1/255.255.255.0 IFACE=eth1 HWADDR=50:e5:49:3e:4b:7e
 TUN/TAP device tun0 opened
 TUN/TAP TX queue length set to 100
 do_ifconfig, tt->ipv6=0, tt->did_ifconfig_ipv6_setup=0
 /sbin/ip link set dev tun0 up mtu 1500
 /sbin/ip addr add dev tun0 local 10.8.0.6 peer 10.8.0.5
 /sbin/ip route add 10.8.0.1/32 via 10.8.0.5
 Initialization Sequence Completed
 [my.server] Inactivity timeout (--ping-restart), restarting
 SIGUSR1[soft,ping-restart] received, process restarting
 Restart pause, 2 second(s)
 Socket Buffers: R=[212992->131072] S=[212992->131072]
 UDPv4 link local: [undef]
 UDPv4 link remote: [AF_INET]103.17.45.190:1194
 TLS: Initial packet from [AF_INET]103.17.45.190:1194, sid=12d067d5 ad235fd0
 VERIFY OK: depth=1, C=US, ST=CA, L=SanFrancisco, O=Fort-Funston, OU=MyOrganizationalUnit, CN=Fort-Funston CA, name=EasyRSA, emailAddress=me@myhost.mydomain
 VERIFY OK: nsCertType=SERVER
 VERIFY OK: depth=0, C=US, ST=CA, L=SanFrancisco, O=Fort-Funston, OU=MyOrganizationalUnit, CN=my.server, name=EasyRSA, emailAddress=me@myhost.mydomain
 Data Channel Encrypt: Cipher 'BF-CBC' initialized with 128 bit key
 Data Channel Encrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
 Data Channel Decrypt: Cipher 'BF-CBC' initialized with 128 bit key
 Data Channel Decrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
 Control Channel: TLSv1, cipher TLSv1/SSLv3 DHE-RSA-AES256-SHA, 2048 bit RSA
 [my.server] Peer Connection Initiated with [AF_INET]103.17.45.190:1194
 SENT CONTROL [my.server]: 'PUSH_REQUEST' (status=1)
 PUSH: Received control message: 'PUSH_REPLY,route 10.8.0.1,topology net30,ping 10,ping-restart 120,ifconfig 10.8.0.6 10.8.0.5'
 OPTIONS IMPORT: timers and/or timeouts modified
 OPTIONS IMPORT: --ifconfig/up options modified
 OPTIONS IMPORT: route options modified
 Preserving previous TUN/TAP instance: tun0
 Initialization Sequence Completed
```
- traceroute 10.8.0.1
```
Je dois voir passer le traffic par la gateway classique.
traceroute to 10.8.0.1 (10.8.0.1), 30 hops max, 60 byte packets
 1  10.8.0.1 (10.8.0.1)  32.545 ms  34.459 ms  36.583 ms
 ```
 
# Comment arréter le client VPN 
 
 killall -SIGINT openvpn  

```
# stop the service    
$ sudo /etc/init.d/openvpn stop

# find the process if for some reason it keeps running
$ lsof -i | grep openvpn

# kill the proccess(s) by its PID
$ kill -9 <PID>

# if necessary restart the service again
$ sudo /etc/init.d/openvpn start
sudo update-rc.d openvpn disable
```
Or edit the config file in /etc/default/openvpn

sudo nano /etc/default/openvpn

And uncomment the line:

#AUTOSTART="none"

So it looks like:

AUTOSTART="none"

Then you'll have to run:

sudo service openvpn start < vpn-name > to manually start the VPN.

sudo service openvpn stop < vpn-name > to manually stop the VPN.

< vpn-name > is the config file name without .conf extension

located in /etc/openvpn and without the < >


# Comment ping les device du LAN du server.
https://serverfault.com/questions/662500/openvpn-access-to-lan-behind-client-and-vice-versa


# Eviter les conflits d'adresse IP
Je change le subnet IP du serveur --> 10.66.0.0/24

- 1 je crée une interface pour ce subnet dans ma machine pour ce subnet.
- 2 je change l'ip subnet dans openwrt. je change juste network / LAN / General Setup / IPv4 address / Save and apply 
- 3 j'utilise ma nouvelle interface configuration dans ma machine.
- 4 ping dans les deux sens OK
- 5 ssh openwrt ne marche pas
- 6 reboot openwrt
- 7 ssh openwrt marche à nouveau

- 8 uci add_list openvpn.myvpn.push='route 10.66.0.0 255.255.255.0'
- 9 on ne voit pas les changements faits par cette commande dans cat /var/etc/openvpn-myvpn.conf, /etc/config/openvpn/
- 10 uci show openvpn on voit la commande.
- 11 finalement je mets cette option dans le fichier de config /etc/config/openvpn
- 12 je mets d'autres options sur le dhcp notamment.

# ajout d'une new forward rule
- nom OpenVPN
- dans firewall / Traffic Rule
- essayer de l'enlever quand cela marchera.

Depuis le client j'arrive à pinguer 10.8.0.5 et 10.8.5.6

Je vois que dans openerp j'ai

/tmp/ipp.txt:
```
my.client,10.8.0.4
```
/tmp/openvpn-status.log:
```
OpenVPN CLIENT LIST
Updated,Wed Sep  6 19:55:10 2017
Common Name,Real Address,Bytes Received,Bytes Sent,Connected Since
my.client,103.17.47.187:58313,226079,227911,Wed Sep  6 10:44:56 2017
ROUTING TABLE
Virtual Address,Common Name,Real Address,Last Ref
10.8.0.6,my.client,103.17.47.187:58313,Wed Sep  6 10:44:58 2017
GLOBAL STATS
Max bcast/mcast queue length,0
END
```
cette adresse IP 103.17.47.167 est bien celle de la potiniere.

Mais je n'arrive à pinguer une machine sur le LAN du serveur.
Je vois dans /etc/config/openvpn
```
config openvpn 'myvpn'
        option enabled '1'
        option verb '3'
        option port '1194'
        option proto 'udp'
        option dev 'tun'
        option server '10.8.0.0 255.255.255.0'
        option keepalive '10 120'
        option ca '/etc/openvpn/ca.crt'
        option dh '/etc/openvpn/dh2048.pem'
        option cert '/etc/openvpn/my.server.crt'
        option key '/etc/openvpn/my.server.key'
        option push 'dhcp-option DOMAIN 10.66.0.1'
        option status '/tmp/openvpn-status.log'
        option ifconfig_pool_persist '/tmp/ipp.txt'
        option log '/tmp/openvpn.log'
```
Je vois qu'il n'y a pas de push de la route vers le LAN du routeur/vpn openwrt.  
donc je fais un push : uci add_list 

- uci show openvpn:
```
openvpn.myvpn=openvpn
openvpn.myvpn.enable='1'
openvpn.myvpn.verb='3'
openvpn.myvpn.port='1194'
openvpn.myvpn.proto='udp'
openvpn.myvpn.dev='tun'
openvpn.myvpn.server='10.8.0.0 255.255.255.0'
openvpn.myvpn.keepalive='10 120'
openvpn.myvpn.ca='/etc/openvpn/ca.crt'
openvpn.myvpn.dh='/etc/openvpn/dh2048.pem'
openvpn.myvpn.cert='/etc/openvpn/my.server.crt'
openvpn.myvpn.key='/etc/openvpn/my.server.key'
openvpn.myvpn.log='/tmp/openvpn.log'
openvpn.myvpn.ifconfig_pool_persit='/tmp/ipp.txt'
openvpn.myvpn.status='/tmp/openvpn-status.log'
openvpn.myvpn.push='route 10.66.0.0 255.255.255.0' 'dhcp-option DNS 10.66.0.1'
```
- #openwrt:/etc/init.d/openvpn stop
- #openwrt:/etc/init.d/openvpn start
- marche pas
- reboot openwrt
- cat /tmp/openvpn.log

```
Thu Sep  7 04:16:16 2017 OpenVPN 2.3.6 mips-openwrt-linux-gnu [SSL (OpenSSL)] [LZO] [EPOLL] [MH] [IPv6] built on Jul 25 2015
Thu Sep  7 04:16:16 2017 library versions: OpenSSL 1.0.2g  1 Mar 2016, LZO 2.08
Thu Sep  7 04:16:16 2017 Diffie-Hellman initialized with 2048 bit key
Thu Sep  7 04:16:17 2017 Socket Buffers: R=[163840->131072] S=[163840->131072]
Thu Sep  7 04:16:17 2017 TUN/TAP device tun0 opened
Thu Sep  7 04:16:17 2017 TUN/TAP TX queue length set to 100
Thu Sep  7 04:16:17 2017 do_ifconfig, tt->ipv6=0, tt->did_ifconfig_ipv6_setup=0
Thu Sep  7 04:16:17 2017 /sbin/ifconfig tun0 10.8.0.1 pointopoint 10.8.0.2 mtu 1500
Thu Sep  7 04:16:17 2017 /sbin/route add -net 10.8.0.0 netmask 255.255.255.0 gw 10.8.0.2
Thu Sep  7 04:16:17 2017 UDPv4 link local (bound): [undef]
Thu Sep  7 04:16:17 2017 UDPv4 link remote: [undef]
Thu Sep  7 04:16:17 2017 MULTI: multi_init called, r=256 v=256
Thu Sep  7 04:16:17 2017 IFCONFIG POOL: base=10.8.0.4 size=62, ipv6=0
Thu Sep  7 04:16:17 2017 Initialization Sequence Completed
Thu Sep  7 13:15:20 2017 103.17.47.187:44186 TLS: Initial packet from [AF_INET]103.17.47.187:44186, sid=428bc8c9 7a66ea45
Thu Sep  7 13:15:21 2017 103.17.47.187:44186 VERIFY OK: depth=1, C=US, ST=CA, L=SanFrancisco, O=Fort-Funston, OU=MyOrganizationalUnit, CN=Fort-Funston CA, name=EasyRSA, emailAddress=me@myhost.mydomain
Thu Sep  7 13:15:21 2017 103.17.47.187:44186 VERIFY OK: depth=0, C=US, ST=CA, L=SanFrancisco, O=Fort-Funston, OU=MyOrganizationalUnit, CN=my.client, name=EasyRSA, emailAddress=me@myhost.mydomain
Thu Sep  7 13:15:21 2017 103.17.47.187:44186 Data Channel Encrypt: Cipher 'BF-CBC' initialized with 128 bit key
Thu Sep  7 13:15:21 2017 103.17.47.187:44186 Data Channel Encrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
Thu Sep  7 13:15:21 2017 103.17.47.187:44186 Data Channel Decrypt: Cipher 'BF-CBC' initialized with 128 bit key
Thu Sep  7 13:15:21 2017 103.17.47.187:44186 Data Channel Decrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
Thu Sep  7 13:15:21 2017 103.17.47.187:44186 Control Channel: TLSv1, cipher TLSv1/SSLv3 DHE-RSA-AES256-SHA, 2048 bit RSA
Thu Sep  7 13:15:21 2017 103.17.47.187:44186 [my.client] Peer Connection Initiated with [AF_INET]103.17.47.187:44186
Thu Sep  7 13:15:21 2017 my.client/103.17.47.187:44186 MULTI_sva: pool returned IPv4=10.8.0.6, IPv6=(Not enabled)
Thu Sep  7 13:15:21 2017 my.client/103.17.47.187:44186 MULTI: Learn: 10.8.0.6 -> my.client/103.17.47.187:44186
Thu Sep  7 13:15:21 2017 my.client/103.17.47.187:44186 MULTI: primary virtual IP for my.client/103.17.47.187:44186: 10.8.0.6
Thu Sep  7 13:15:24 2017 my.client/103.17.47.187:44186 PUSH: Received control message: 'PUSH_REQUEST'
Thu Sep  7 13:15:24 2017 my.client/103.17.47.187:44186 send_push_reply(): safe_cap=940
Thu Sep  7 13:15:24 2017 my.client/103.17.47.187:44186 SENT CONTROL [my.client]: 'PUSH_REPLY,route 10.66.0.0 255.255.255.0,dhcp-option DNS 10.66.0.1,route 10.8.0.1,topology net30,ping 10,ping-restart 120,ifconfig 10.8.0.6 10.8.0.5' (status=1)
```
- et logread -f

~~~ 
Thu Sep  7 13:33:21 2017 daemon.notice netifd: Network device 'tun0' link is down
Thu Sep  7 13:33:21 2017 daemon.notice netifd: Interface 'vpn0' has link connectivity loss
Thu Sep  7 13:33:21 2017 daemon.notice netifd: Interface 'vpn0' is now down
Thu Sep  7 13:33:21 2017 daemon.notice netifd: Interface 'vpn0' is disabled
Thu Sep  7 13:33:27 2017 daemon.notice netifd: Interface 'vpn0' is enabled
Thu Sep  7 13:33:27 2017 daemon.notice netifd: Network device 'tun0' link is up
Thu Sep  7 13:33:27 2017 daemon.notice netifd: Interface 'vpn0' has link connectivity 
Thu Sep  7 13:33:27 2017 daemon.notice netifd: Interface 'vpn0' is setting up now
Thu Sep  7 13:33:27 2017 daemon.notice netifd: Interface 'vpn0' is now up
Thu Sep  7 13:33:27 2017 user.notice firewall: Reloading firewall due to ifup of vpn0 (tun0)
Thu Sep  7 13:34:12 2017 daemon.info hostapd: wlan1: STA 78:02:f8:58:69:2a IEEE 802.11: authenticated
Thu Sep  7 13:34:12 2017 daemon.info hostapd: wlan1: STA 78:02:f8:58:69:2a IEEE 802.11: associated (aid 1)
Thu Sep  7 13:34:12 2017 daemon.info hostapd: wlan1: STA 78:02:f8:58:69:2a WPA: pairwise key handshake completed (RSN)
Thu Sep  7 13:34:13 2017 daemon.info dnsmasq-dhcp[1403]: DHCPDISCOVER(br-lan) 78:02:f8:58:69:2a 
Thu Sep  7 13:34:13 2017 daemon.info dnsmasq-dhcp[1403]: DHCPOFFER(br-lan) 10.66.0.136 78:02:f8:58:69:2a 
Thu Sep  7 13:34:13 2017 daemon.info dnsmasq-dhcp[1403]: DHCPREQUEST(br-lan) 10.66.0.136 78:02:f8:58:69:2a 
Thu Sep  7 13:34:13 2017 daemon.info dnsmasq-dhcp[1403]: DHCPACK(br-lan) 10.66.0.136 78:02:f8:58:69:2a 
Thu Sep  7 13:34:34 2017 daemon.warn odhcpd[934]: DHCPV6 SOLICIT IA_NA from 00010001211e7d78b827ebdc7cee on br-lan: ok fd32:5c36:1a94::6e7/128 

~~~
- pas de `Iniatialisation sequence completed` ce n'est pas bon signe.

- au niveau client j'ai:

```
[...]
(--ping-restart), restarting
Thu Sep  7 15:56:21 2017 SIGUSR1[soft,ping-restart] received, process
restarting
Thu Sep  7 15:56:21 2017 Restart pause, 2 second(s)
Thu Sep  7 15:56:23 2017 Socket Buffers: R=[212992->131072]
S=[212992->131072]
Thu Sep  7 15:56:23 2017 UDPv4 link local: [undef]
Thu Sep  7 15:56:23 2017 UDPv4 link remote: [AF_INET]103.17.45.190:1194     -----> IP public openwrt VPN server
Thu Sep  7 15:56:23 2017 TLS: Initial packet from
Thu Sep  7 15:56:25 2017 [my.server] Peer Connection Initiated with
[AF_INET]103.17.45.190:1194         ----> c'est l'ip public de l'openwrt avec le server VPN
Thu Sep  7 15:56:27 2017 SENT CONTROL [my.server]: 'PUSH_REQUEST' (status=1)
Thu Sep  7 15:56:27 2017 PUSH: Received control message:
'PUSH_REPLY,route 10.66.0.0 255.255.255.0,dhcp-option DNS    ----> des informations sur le subnet de openwrt server VPN
10.66.0.1,route 10.8.0.1,topology net30,ping 10,ping-restart
120,ifconfig 10.8.0.6 10.8.0.5'
Thu Sep  7 15:56:27 2017 OPTIONS IMPORT: timers and/or timeouts modified
Thu Sep  7 15:56:27 2017 OPTIONS IMPORT: --ifconfig/up options modified
Thu Sep  7 15:56:27 2017 OPTIONS IMPORT: route options modified
Thu Sep  7 15:56:27 2017 OPTIONS IMPORT: --ip-win32 and/or --dhcp-option
options modified
Thu Sep  7 15:56:27 2017 Preserving previous TUN/TAP instance: tun0
```

- sur le client que donne netstat -nr avant et apres lancement du client VPN.

- que donne un traceroute de 10.8.0.6 et 10.8.0.5

- sur le routeur sans le vpn, netstat -nr donne:

```
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
202.22.235.254  *               255.255.255.255 UH    0      0        0 pppoe-wan
192.168.1.0     *               255.255.255.0   U     0      0        0 br-lan
default         202.22.235.254  0.0.0.0         UG    0      0        0 pppoe-wan

```

- 202.22.235.254 c'est une adresse mls.  
- traceroute 202.22.235.254 pas de réponse interessante.
- sur le router avec VPN: netstat -nr

## openwrt openvpn on
- netstat -nr

```
0.0.0.0         202.22.235.254  0.0.0.0         UG        0 0          0 pppoe-wan
10.8.0.0        10.8.0.2        255.255.255.0   UG        0 0          0 tun0
10.8.0.2        0.0.0.0         255.255.255.255 UH        0 0          0 tun0
10.66.0.0       0.0.0.0         255.255.255.0   U         0 0          0 br-lan
202.22.235.254  0.0.0.0         255.255.255.255 UH        0 0          0 pppoe-wan
```
## openwrt openvpn off
```
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         202.22.235.254  0.0.0.0         UG        0 0          0 pppoe-wan
10.66.0.0       0.0.0.0         255.255.255.0   U         0 0          0 br-lan
202.22.235.254  0.0.0.0         255.255.255.255 UH        0 0          0 pppoe-wan
```

## openvpn sur openwrt
- /etc/init.d/openvpn start
- logread -f

```
 daemon.notice netifd: Interface 'vpn0' is enabled
 daemon.notice netifd: Network device 'tun0' link is up
 daemon.notice netifd: Interface 'vpn0' has link connectivity 
 daemon.notice netifd: Interface 'vpn0' is setting up now
 daemon.notice netifd: Interface 'vpn0' is now up
 user.notice firewall: Reloading firewall due to ifup of vpn0 (tun0)
 daemon.warn odhcpd[934]: DHCPV6 SOLICIT IA_NA from 00010001211e7d78b827ebdc7cee on br-lan: ok fd32:5c36:1a94::6e7/128 
```
- cat /tmp/openvpn.log 

```
OpenVPN 2.3.6 mips-openwrt-linux-gnu [SSL (OpenSSL)] [LZO] [EPOLL] [MH] [IPv6] built on Jul 25 2015
library versions: OpenSSL 1.0.2g  1 Mar 2016, LZO 2.08
Diffie-Hellman initialized with 2048 bit key
 Socket Buffers: R=[163840->131072] S=[163840->131072]
 TUN/TAP device tun0 opened
 TUN/TAP TX queue length set to 100
 do_ifconfig, tt->ipv6=0, tt->did_ifconfig_ipv6_setup=0
 /sbin/ifconfig tun0 10.8.0.1 pointopoint 10.8.0.2 mtu 1500
 /sbin/route add -net 10.8.0.0 netmask 255.255.255.0 gw 10.8.0.2
 UDPv4 link local (bound): [undef]
 UDPv4 link remote: [undef]
 MULTI: multi_init called, r=256 v=256
 IFCONFIG POOL: base=10.8.0.4 size=62, ipv6=0
Initialization Sequence Completed
 ```
 - je lance le client
 - #cd /etc/openvpn
 - #cat  client.conf

 ```
7 OpenVPN 2.3.4 x86_64-pc-linux-gnu [SSL (OpenSSL)] [LZO] [EPOLL] [PKCS11] [MH] [IPv6] built on Nov 12 2015
 library versions: OpenSSL 1.0.1t  3 May 2016, LZO 2.08
 WARNING: file 'my.client.key' is group or others accessible
 Socket Buffers: R=[212992->131072] S=[212992->131072]
 UDPv4 link local: [undef]
 UDPv4 link remote: [AF_INET]103.17.45.190:1194
 TLS: Initial packet from [AF_INET]103.17.45.190:1194, sid=9bc7245d 140a4d35
 VERIFY OK: depth=1, C=US, ST=CA, L=SanFrancisco, O=Fort-Funston, OU=MyOrganizationalUnit, CN=Fort-Funston CA, name=EasyRSA, emailAddress=me@myhost.mydomain
 VERIFY OK: nsCertType=SERVER
 VERIFY OK: depth=0, C=US, ST=CA, L=SanFrancisco, O=Fort-Funston, OU=MyOrganizationalUnit, CN=my.server, name=EasyRSA, emailAddress=me@myhost.mydomain
 Data Channel Encrypt: Cipher 'BF-CBC' initialized with 128 bit key
 Data Channel Encrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
 Data Channel Decrypt: Cipher 'BF-CBC' initialized with 128 bit key
 Data Channel Decrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
 Control Channel: TLSv1, cipher TLSv1/SSLv3 DHE-RSA-AES256-SHA, 2048 bit RSA
 [my.server] Peer Connection Initiated with [AF_INET]103.17.45.190:1194
 SENT CONTROL [my.server]: 'PUSH_REQUEST' (status=1)
 PUSH: Received control message: 'PUSH_REPLY,route 10.66.0.0 255.255.255.0,dhcp-option DNS 10.66.0.1,route 10.8.0.1,topology net30,ping 10,ping-restart 120,ifconfig 10.8.0.6 10.8.0.5'
 OPTIONS IMPORT: timers and/or timeouts modified
 OPTIONS IMPORT: --ifconfig/up options modified
 OPTIONS IMPORT: route options modified
 OPTIONS IMPORT: --ip-win32 and/or --dhcp-option options modified
 ROUTE_GATEWAY 192.168.1.1/255.255.255.0 IFACE=eth1 HWADDR=50:e5:49:3e:4b:7e
 TUN/TAP device tun0 opened
 TUN/TAP TX queue length set to 100
 do_ifconfig, tt->ipv6=0, tt->did_ifconfig_ipv6_setup=0
 /sbin/ip link set dev tun0 up mtu 1500
 /sbin/ip addr add dev tun0 local 10.8.0.6 peer 10.8.0.5
 /sbin/ip route add 10.66.0.0/24 via 10.8.0.5
 /sbin/ip route add 10.8.0.1/32 via 10.8.0.5
 Initialization Sequence Completed
```
- ifconfig tun0

```
tun0      Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  
          inet adr:10.8.0.6  P-t-P:10.8.0.5  Masque:255.255.255.255
          UP POINTOPOINT RUNNING NOARP MULTICAST  MTU:1500  Metric:1
          RX packets:3 errors:0 dropped:0 overruns:0 frame:0
          TX packets:5 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 lg file transmission:100 
          RX bytes:336 (336.0 B)  TX bytes:332 (332.0 B)
```
- traceroute 10.8.0.6
```
traceroute to 10.8.0.6 (10.8.0.6), 30 hops max, 60 byte packets
 1  10.8.0.6 (10.8.0.6)  0.026 ms  0.007 ms  0.007 ms
root@linuxbox:/home/lfs# traceroute 10.8.0.5
traceroute to 10.8.0.5 (10.8.0.5), 30 hops max, 60 byte packets
 1  10.8.0.1 (10.8.0.1)  33.723 ms  35.927 ms  37.586 ms
 2  10.8.0.1 (10.8.0.1)  39.769 ms  41.726 ms  43.683 ms
```
- puis tout d'un coup ca a marché
- ping 10.66.0.10
- ssh user@10.66.0.10 et boom.
 
Il faut comprendre le VPN comme un switch unmanaged virtuel auquel est connecté le trafic virtuel du VPN et ce traffic est chainé au switch du LAN



It turns out that if you are trying to connect from a non-Windows client, you need to do a couple of extra steps:  

- On Linux

	- Put this line on your client configuration (client.conf or xxxx.ovpnfile): `dhcp-option DNS 11.22.33.44`

	- Call the OpenVPN client in this way: `$ openvpn --script-security 2 --config xxxx.ovpn`

That worked for me.


# Ping freepbx server
Depuis la maison j'arrivais à me connecter en http sur le freepbx server sans difficulté et puis tout d'un coup plus possible. Impossible de pinguer.
mais http sur le server odoo OK, http sur le router principal OK.  
pas de message d'erreur  
rien dans  system admin / detection d'intrusion.  
redemarrage du serveur openvpn sur le routeur principal du cabinet.  
opewrt TPLINK / services / openvpn /stop  

Il semblerait que cela soit arrivé apres avoir mis en service le server VPN sur le freepbx.  
Il a le meme subnet mask que le sever VPN du main router.  
On change le subent mask du vpn 10.8.0.0 vers 10.10.0.0  
Et cela à l'air d'aller mieux.
```
Mon Jan  8 16:04:58 2018 event_wait : Interrupted system call (code=4)
Mon Jan  8 16:04:58 2018 /sbin/route del -net 10.8.0.0 netmask 255.255.255.0
Mon Jan  8 16:04:58 2018 Closing TUN/TAP interface
Mon Jan  8 16:04:58 2018 /sbin/ifconfig tun0 0.0.0.0
Mon Jan  8 16:04:58 2018 SIGTERM[hard,] received, process exiting
Mon Jan  8 16:05:03 2018 OpenVPN 2.3.6 mips-openwrt-linux-gnu [SSL (OpenSSL)] [LZO] [EPOLL] [MH] [IPv6] built on Jul 25 2015
Mon Jan  8 16:05:03 2018 library versions: OpenSSL 1.0.2g  1 Mar 2016, LZO 2.08
Mon Jan  8 16:05:03 2018 Diffie-Hellman initialized with 2048 bit key
Mon Jan  8 16:05:03 2018 Socket Buffers: R=[163840->131072] S=[163840->131072]
Mon Jan  8 16:05:03 2018 TUN/TAP device tun0 opened
Mon Jan  8 16:05:03 2018 TUN/TAP TX queue length set to 100
Mon Jan  8 16:05:03 2018 do_ifconfig, tt->ipv6=0, tt->did_ifconfig_ipv6_setup=0
Mon Jan  8 16:05:03 2018 /sbin/ifconfig tun0 10.8.0.1 pointopoint 10.8.0.2 mtu 1500
Mon Jan  8 16:05:03 2018 /sbin/route add -net 10.8.0.0 netmask 255.255.255.0 gw 10.8.0.2
Mon Jan  8 16:05:03 2018 UDPv4 link local (bound): [undef]
Mon Jan  8 16:05:03 2018 UDPv4 link remote: [undef]
Mon Jan  8 16:05:03 2018 MULTI: multi_init called, r=256 v=256
Mon Jan  8 16:05:03 2018 IFCONFIG POOL: base=10.8.0.4 size=62, ipv6=0
Mon Jan  8 16:05:03 2018 Initialization Sequence Completed
```
Je fais un ifconfig sur openwrt

```
tun0      Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  
          inet addr:10.8.0.1  P-t-P:10.8.0.2  Mask:255.255.255.255
          UP POINTOPOINT RUNNING NOARP MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:100 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```
Je m'attendais à ce que cette interface disparaisse.

je fais un start
ifconfig  
```
tun0      Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  
          inet addr:10.8.0.1  P-t-P:10.8.0.2  Mask:255.255.255.255
          UP POINTOPOINT RUNNING NOARP MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:100 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```
tun0 toujours là
Je remarque que la case enabled est décochée. Je la coche
ca change pas grand chose.
ps | grep openvpn j'ai toujours un processus openvpn qui tourne
/Status / startup / opnevpn stop   ps | grep openvpn et là plus de openvpn en processus plus d'interface tun0
```
Mon Jan  8 16:17:00 2018 TUN/TAP TX queue length set to 100
Mon Jan  8 16:17:00 2018 do_ifconfig, tt->ipv6=0, tt->did_ifconfig_ipv6_setup=0
Mon Jan  8 16:17:00 2018 /sbin/ifconfig tun0 10.8.0.1 pointopoint 10.8.0.2 mtu 1500
Mon Jan  8 16:17:00 2018 /sbin/route add -net 10.8.0.0 netmask 255.255.255.0 gw 10.8.0.2
Mon Jan  8 16:17:00 2018 UDPv4 link local (bound): [undef]
Mon Jan  8 16:17:00 2018 UDPv4 link remote: [undef]
Mon Jan  8 16:17:00 2018 MULTI: multi_init called, r=256 v=256
Mon Jan  8 16:17:00 2018 IFCONFIG POOL: base=10.8.0.4 size=62, ipv6=0
Mon Jan  8 16:17:00 2018 Initialization Sequence Completed
Mon Jan  8 16:17:13 2018 103.17.45.190:1194 TLS: Initial packet from [AF_INET]103.17.45.190:1194, sid=92b538bf 175ffa85
Mon Jan  8 16:17:17 2018 103.17.45.190:1194 VERIFY OK: depth=1, C=US, ST=CA, L=SanFrancisco, O=Fort-Funston, OU=MyOrganizationalUnit, CN=Fort-Funston CA, name=EasyRSA, emailAddress=me@myhost.mydomain
Mon Jan  8 16:17:17 2018 103.17.45.190:1194 VERIFY OK: depth=0, C=US, ST=CA, L=SanFrancisco, O=Fort-Funston, OU=MyOrganizationalUnit, CN=my-openvpn-client, name=EasyRSA, emailAddress=me@myhost.mydomain
Mon Jan  8 16:17:18 2018 103.17.45.190:1194 Data Channel Encrypt: Cipher 'BF-CBC' initialized with 128 bit key
Mon Jan  8 16:17:18 2018 103.17.45.190:1194 Data Channel Encrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
Mon Jan  8 16:17:18 2018 103.17.45.190:1194 Data Channel Decrypt: Cipher 'BF-CBC' initialized with 128 bit key
Mon Jan  8 16:17:18 2018 103.17.45.190:1194 Data Channel Decrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
Mon Jan  8 16:17:18 2018 103.17.45.190:1194 Control Channel: TLSv1, cipher TLSv1/SSLv3 DHE-RSA-AES256-SHA, 2048 bit RSA
Mon Jan  8 16:17:18 2018 103.17.45.190:1194 [my-openvpn-client] Peer Connection Initiated with [AF_INET]103.17.45.190:1194
Mon Jan  8 16:17:18 2018 my-openvpn-client/103.17.45.190:1194 MULTI_sva: pool returned IPv4=10.8.0.6, IPv6=(Not enabled)
Mon Jan  8 16:17:18 2018 my-openvpn-client/103.17.45.190:1194 MULTI: Learn: 10.8.0.6 -> my-openvpn-client/103.17.45.190:1194
Mon Jan  8 16:17:18 2018 my-openvpn-client/103.17.45.190:1194 MULTI: primary virtual IP for my-openvpn-client/103.17.45.190:1194: 10.8.0.6
Mon Jan  8 16:17:20 2018 my-openvpn-client/103.17.45.190:1194 PUSH: Received control message: 'PUSH_REQUEST'
Mon Jan  8 16:17:20 2018 my-openvpn-client/103.17.45.190:1194 send_push_reply(): safe_cap=940
Mon Jan  8 16:17:20 2018 my-openvpn-client/103.17.45.190:1194 SENT CONTROL [my-openvpn-client]: 'PUSH_REPLY,route 10.66.0.0 255.255.255.0,dhcp-option DNS 10.66.0.1,route 10.8.0.1,topology net30,ping 10,ping-restart 120,ifconfig 10.8.0.6 10.8.0.5' (status=1)
```
Le message ne donne pas initialisation sequence completed  
ifconfig tun0  OK  
service / openvpn / myvpn enabled coché started yes (n°) . N° correspond bien au n° de processus obtenu par ps | grep openerp.  
Comme je n'avais pas initialisation sequence completed j'ai fait  system / startup /openvpn restart et là j'ai eu initialisation sequence completed.

# Dans le web gui on peut configurer openvpn
avec des choix : 
- client configuration for a routed multi-client vpn0
- ....

et cela utilise le fichier /etc/config/openvpn_recipes.

# configuration du client
cd /etc/openvpn  
touch client.conf  
nano client.conf  
## pour lancer le client 
### `service openvpn start`
n'a pas besoin de savoir ou le .conf. Utilise tous le .conf de /etc/openvpn  

s'assurer que le lien est up avec `ifconfig tun0`
on doit avoir une ligne du genre : ` inet addr:10.8.0.6  P-t-P:10.8.0.5  Mask:255.255.255.255`
s'assurer que cela fonctionne : `ping 10.8.0.1 -c 2


 

## le fichier de log
dans /etc/openvpn
nom : openvpn.log  
Si je le rm il n'est pas recrée par `service openvpn start` ni par `openvpn --config /etc/openvpn/client.conf`
Un reboot et le fichier est recrée.  il est récrée avec des lignes alors que je n'ai pas lancer openvpn !!!!
ps aux | grep openvpn J'ai bien openvpn qui tourne. pourquoi openvpn demarre au reboot? TODO
## debugging
Je vois que [AF_INET]103.17.45.190:1194 qui est bien l'adresse IP du cabinet. C'est pas sur.

TLS Error: TLS key negotiation failed to occur within 60 seconds  
Grossiere erreur dans le client.conf ce n'est pas kuendu.ddns.net qu'il faut mettre mais goeen.ddns.net.  
`service openvpn restart`  
C'est nettement mieux :

```
Thu Jan  4 06:26:02 2018 WARNING: file '/etc/openvpn/my-openvpn-client.key' is group or others accessible
Thu Jan  4 06:26:02 2018 Socket Buffers: R=[212992->131072] S=[212992->131072]
Thu Jan  4 06:26:02 2018 UDPv4 link local (bound): [undef]
Thu Jan  4 06:26:02 2018 UDPv4 link remote: [AF_INET]103.17.47.187:1194
Thu Jan  4 06:26:02 2018 TLS: Initial packet from [AF_INET]103.17.47.187:1194, sid=04ed319e 76602813
Thu Jan  4 06:26:02 2018 VERIFY OK: depth=1, C=US, ST=CA, L=SanFrancisco, O=Fort-Funston, OU=MyOrganizationalUnit, CN=Fort-Funston CA, name=EasyRSA, emailAddress=me@myhost.mydomain
Thu Jan  4 06:26:02 2018 Validating certificate key usage
Thu Jan  4 06:26:02 2018 ++ Certificate has key usage  00a0, expects 00a0
Thu Jan  4 06:26:02 2018 VERIFY KU OK
Thu Jan  4 06:26:02 2018 Validating certificate extended key usage
Thu Jan  4 06:26:02 2018 ++ Certificate has EKU (str) TLS Web Server Authentication, expects TLS Web Server Authentication
Thu Jan  4 06:26:02 2018 VERIFY EKU OK
Thu Jan  4 06:26:02 2018 VERIFY OK: depth=0, C=US, ST=CA, L=SanFrancisco, O=Fort-Funston, OU=MyOrganizationalUnit, CN=my_openvpn_server, name=EasyRSA, emailAddress=me@myhost.mydomain
Thu Jan  4 06:26:03 2018 Data Channel Encrypt: Cipher 'BF-CBC' initialized with 128 bit key
Thu Jan  4 06:26:03 2018 Data Channel Encrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
Thu Jan  4 06:26:03 2018 Data Channel Decrypt: Cipher 'BF-CBC' initialized with 128 bit key
Thu Jan  4 06:26:03 2018 Data Channel Decrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
Thu Jan  4 06:26:03 2018 Control Channel: TLSv1, cipher TLSv1/SSLv3 DHE-RSA-AES256-SHA, 2048 bit RSA
Thu Jan  4 06:26:03 2018 [my_openvpn_server] Peer Connection Initiated with [AF_INET]103.17.47.187:1194
Thu Jan  4 06:26:06 2018 SENT CONTROL [my_openvpn_server]: 'PUSH_REQUEST' (status=1)
Thu Jan  4 06:26:06 2018 PUSH: Received control message: 'PUSH_REPLY,route 10.66.0.0 255.255.255.0,dhcp-option DNS 10.66.0.1,route 10.8.0.1,topology net30,ping 10,ping-restart 120,ifconfig 10.8.0.6 10.8.0.5'
Thu Jan  4 06:26:06 2018 OPTIONS IMPORT: timers and/or timeouts modified
Thu Jan  4 06:26:06 2018 OPTIONS IMPORT: --ifconfig/up options modified
Thu Jan  4 06:26:06 2018 OPTIONS IMPORT: route options modified
Thu Jan  4 06:26:06 2018 OPTIONS IMPORT: --ip-win32 and/or --dhcp-option options modified
Thu Jan  4 06:26:06 2018 ROUTE_GATEWAY 192.168.1.1/255.255.255.0 IFACE=eth0 HWADDR=00:25:22:16:3b:e5
Thu Jan  4 06:26:06 2018 TUN/TAP device tun0 opened
Thu Jan  4 06:26:06 2018 TUN/TAP TX queue length set to 100
Thu Jan  4 06:26:06 2018 do_ifconfig, tt->ipv6=0, tt->did_ifconfig_ipv6_setup=0
Thu Jan  4 06:26:06 2018 /sbin/ip link set dev tun0 up mtu 1500
Thu Jan  4 06:26:06 2018 /sbin/ip addr add dev tun0 local 10.8.0.6 peer 10.8.0.5
Thu Jan  4 06:26:06 2018 /sbin/ip route add 10.66.0.0/24 via 10.8.0.5
Thu Jan  4 06:26:06 2018 /sbin/ip route add 10.8.0.1/32 via 10.8.0.5
Thu Jan  4 06:26:06 2018 Initialization Sequence Completed
```


# comment on test depuis le client?
## ping IP du server 
OK
## On vérifie la création de l'interface tun
- 1 `cat /etc/openvpn/openvpn.log | grep tun`
- 2 `ifconfig `  
inet addr:10.8.0.6  P-t-P:10.8.0.5  Mask:255.255.255.255  


## traceroute
- `traceroute 10.8.0.1`  
pourquoi 1 ? TODO  
retourne :
traceroute to 10.8.0.1 (10.8.0.1), 30 hops max, 60 byte packets
 1  10.8.0.1 (10.8.0.1)  49.363 ms  51.015 ms  53.385 ms
- `traceroute 8.8.8.8`  
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets  
 1  OpenWrt.lan (192.168.1.1)  12.743 ms  15.067 ms  15.075 ms  
 2  202.22.235.253 (202.22.235.253)  29.926 ms  29.927 ms  31.662 ms  
 3  202.22.224.41 (202.22.224.41)  36.408 ms  36.419 ms  38.701 ms  
 4  202.87.128.65 (202.87.128.65)  38.732 ms  40.234 ms  42.989 ms  
 5  202.87.128.77 (202.87.128.77)  66.858 ms  66.867 ms  69.123 ms  
 6  202.87.128.78 (202.87.128.78)  69.085 ms  41.048 ms  54.076 ms  
 7  108.170.247.33 (108.170.247.33)  55.660 ms 108.170.247.49 (108.170.247.49)  55.667 ms 108.170.247.33 (108.170.247.33)  55.652 ms  
 8  209.85.251.53 (209.85.251.53)  57.124 ms 216.239.41.191 (216.239.41.191)  53.426 ms 216.239.41.83 (216.239.41.83)  53.411 ms  
 9  google-public-dns-a.google.com (8.8.8.8)  59.310 ms  59.315 ms  59.299 ms  
 On voit que l'on passe par le router du client et pas par l'openvpn server.

 - `nmap -sP 10.8.0.0/24` retourne que seul l'hote 10.8.0.6 est up. 
 
 # j'ai ma conncetion avec le server. Mais ensuite.
 Once the VPN is operational in a point-to-point capacity between client and server, it may be desirable to expand the scope of the VPN so that clients can reach multiple machines on the server network, rather than only the server machine itself.  
 For the purpose of this example, we will assume that the server-side LAN uses a subnet of 10.66.0.0/24 and the VPN IP address pool uses 10.8.0.0/24 as cited in the server directive in the OpenVPN server configuration file. 
  First, you must advertise the 10.66.0.0/24 subnet to VPN clients as being accessible through the VPN. This can easily be done with the following server-side config file directive:
`push "route 10.66.0.0 255.255.255.0"`  
Ce qui pour openwrt se traduit en : `uci add_list openvpn.myvpn.push='route 10.66.0.0 255.255.255.0'`  
Cela devrait pouvoir nous permettre de pinguer les ip du reseau du cabinet depuis le client.

# ping 10.66.0.200 bingo
# j'éteins et je ralume le pc et là pas moyen de pinguer sur l'autre réseau local.   
openvpn tourne  
on fait un restart. `service openvpn restart`. Et là j'ai a nouveau la bonne séquence dans le openvpn.log_action_msg
```
Thu Jan  4 21:08:27 2018 do_ifconfig, tt->ipv6=0, tt->did_ifconfig_ipv6_setup=0
Thu Jan  4 21:08:27 2018 /sbin/ip link set dev tun0 up mtu 1500
Thu Jan  4 21:08:27 2018 /sbin/ip addr add dev tun0 local 10.8.0.6 peer 10.8.0.5
Thu Jan  4 21:08:27 2018 /sbin/ip route add 10.66.0.0/24 via 10.8.0.5
Thu Jan  4 21:08:27 2018 /sbin/ip route add 10.8.0.1/32 via 10.8.0.5
Thu Jan  4 21:08:27 2018 Initialization Sequence Completed
```
# configuration du serveur sur une machine qui n'est pas le routeur
fichier /etc/openvpn/server.conf
- directive `local`: to make it listen to an IP adress. C'est l'adress IP vers qui est forwardé UDP port 1194 par le firewall.
- Pour que les remote clients quand ils se connectent sur le server, puissent atteindre autre choses que les que le serveur lui meme il faut activer le ip forwarding avec
- `echo 1 > /proc/sys/net/ipv4/ip_forward`
- ou editer /etc/systcl.conf : `net.ipv4.ip_forward = 1`

On doit pouvoir pinguer n'importe qu'elle machine sur le LAN du server à partir du client. et pouvoir pinguer le client de n'importe qu'elle machine sur le LAN du server

Depuis le client traceroute 192.168.10.65 (ip address LAN du réseau du serveur)
ping 192.168.10.65

# Config du tel
J'installe le tel sur son switch à la maison  
obtient son adress ip depuis le dhcp du main router de la maison. je pense que c'est normal je n'ai pas configuré le client de l'ipphone. 
je le vois apparaitre dans les dhcp lease du routeur.   
http ipduphone 
Network / advanced  / vpn  
active Yes   
upload VPN Config / browse / je prends le /etc/openvpn/client.conf / import 
me dit qu'il veut un ovpn ou un tar. 
J'ai essayé de renomer le client,conf en client.ovpn ne marche pas.

Je ne trouve pas comment configurer l'ip phone avec un server openvpn sur le main router. Donc j'utile la méthode de freepbx avec un server VPN sur le freepx

# Config du server VPN sur le freepbx.

# System admin / VPN server tout en bas du menu déroulant utiliser l'ascenseur
openVPN client  
Enable yes  
description : hello cab VPN client  
use ddns no   
use server remote address no  
Client remote address : goeen.ddns.net  
assigned address no .

mais normalement quand on démarre le server VPN il y a deux onglets que je ne vois plus.

Applications / Extension / Add extension / Add new CHan_SIP extension  
user extension : un chiffre
display name
Link to a default user : Create new user.


Admin / user management / onglet VPN 
define additionnal VPN client : selectionner hello cab VPN client (crée sans system Admin / VPN server )  

J'essaie et je reessaie rien à faire l'account de l'ip phone est toujours `not registered`  

Je me demande si ce n'est pas un probleme d'absence de regle nat.  
Donc je change d'abord le port udp du server openvpn du main router au cabinet.  
Et ensuite je crée un regle de NAT.



## dans openwrt 
- uci set firewall.Allow_OpenVPN_I::ound.dest_port=1194
- editer le fichier /etc/config/openvpn option port '1194' -> option port '1200'
## sur le pc client
nano /etc/openvpn/client.conf remote fqdn du server 1194 ->  remote fqdn du server 1200

## je crées ma regle de NAT en m'inspirant de odoo.
sur le main router du server freepbx  
Network / firewall / Port forwarding
New port forwarding  
Name : FreePBX VPN server  
Protocol : UDP  
Source zone : wan  
Soure mac address : ___
source ip address :  
source port : 
external ip address:   
external port : 1194
internal zone : lan
internal ip address : 10.66.0.2 
internal port : 
External zone : wan  
External port : ....

mais cela ne marche pas.
## source port vs detination port
la communication va de l'ordinateur source port xxxx choose ramdomly to a web server destination port 80 et depuis un web server (source port 80) to your computer destination port xxxx

Les paquets sont de la forme : ` your_IP, server_IP, source_port (xxxxx), destination port (80)`

# ssh dans le freepbx
cd /etc/openvpn  
on retrouve deux fichiers .conf de client.
sysadmin_client0.conf   
sysadmin_client3.conf  
0 et 13 correspond aux id dans la vue : System Admin / VPN server / Clients.  
Je sais que 13 n'est pas ok car remote `85922823.deployments.pbxact.com 1194` 
je ne sais pas pourquoi il est configuré comme cela.
le O -> `remote goeen.ddns.net 1194`. Ca a l'air meilleur. 
EPM / extension mapping / edit extension / VPN client : VPN client for hellocab ip phone
et pas 5-5 qui a été crée je ne sais qd.

J'ai tout repris à zero.  
System admin /server VPN disable server, delete clients.
Delete client dans EPM / extension / vpn client  
Set ip phone to factory default.  
Redemerrage du server dans system admon / VPN Server  enable et creation d'un client.  
EPM / extension add VPN client 
l'ip phone reboot et affiche VPN activated.  
cd /etc/openvpn je n'ai plus qu'un fichier sysadmin_server1.conf  
Pour les clients ils sont dans : /etc/openvpn/clients/  
Sous ccd il deux fichier client12 et client13 qui sont vides.  

# comment tester un server openvpn a distance:

echo "abcd" | netcat -u -v -w2 goeen.ddns.info 1194
NS fwd/rev mismatch: goeen.ddns.info != 103-17-47-187-last22.mls.nc
goeen.ddns.info [103.17.47.187] 1194 (openvpn) open

Mais je pense que c'est la réponse du server openvpn qui tourne  sur le router principal du LAN. Mais là j'ai changé le port en 1200 et j'ai toujours la meme réponse. 



Here is a shell one-liner:

echo -e "\x38\x01\x00\x00\x00\x00\x00\x00\x00" | 
   timeout 10 nc -u openvpnserver.com 1194 | cat -v

if there is an openvpn on the other end the output will be

@$M-^HM--LdM-t|M-^X^@^@^@^@^@@$M-^HM--LdM-t|M-^X^@^@^@^@^@@$M-^HM--LdM-t|M-^X...

otherwise it will just be mute and timeout after 10 seconds or display something different.

NOTE: this works only if tls-auth config option is not active, otherwise the server rejects messages with incorrect HMAC.

# Comment savoir si un port est ouvert?

- nmap -sP -O 127.0.0.1
- netsat -nap / netsat -ntulp (ne donne que TCP UDP). 
You can scan udp ports by using following command  

- nmap -sU -v <hostname or ip>  



Pour savoir si les ports sont exploitables  il faut faire cela sur l'adresse ip externe. Mais je ne sais pas comment on fait.

# pas moyen de connecter l'ip phone sur le remote subnet.
On me suggere de :
You need to set an incoming port as well, please try adding `option src_dport 1194`
moi j'ai :
config redirect
option target 'DNAT’
option src 'wan’
option dest 'lan’
option proto 'udp’
option name 'FreePBX VPN Server’
option dest_ip '10.66.0.2’
option dest_port ‘1194’

J'ai essayé d'ajouter option src_dport 1194
d'enlever option dest_port '1194' et d'ajouter option src_dport 1194. Cela ne change rien.

# SNAT source network adress translation
on va modifier l'addresse IP source des paquets passant par le router.  
SNAT est typiquement utilisé qd un hote interne (privé) doit établir une connection avec un hote externe public. 
Changement de l'adresse privée de source en une adresse publique
# DNAT destination network adress translation
on va mapper une addresse privée avec une adresse publique.  
DNAT change l'adresse de destination des paquets passant par le router.  
On l'utilise typiquement quand un hote (public) externe doite établir une connection avec un hote privé interne. 

# Port source 
c'est le port source du paquet décrivant ou un paquet en réponse sera envoyé. 
Port destination le port de destination du paquet. 
# My Config

My config on what I call 

-the LAN subnet:
Main router TPLINK OpenWrt Chaos Calmer 15.05
Switch Cisco L3
VPN server on the openwrt 10.10.0.0 subnet port 1200
VPN server on the Freepbx server 10.8.0.0 subnet port 1194
Current Asterisk Version: 11.23.0
Freepbx FreePBX 13.0.192.19
Firewall redirect rule on the openwrt main router to LAN subnet IP of freepbx server

-and a REMOTE subnet where I try to install a S700 sangoma ip phone.
from the REMOTE  subnet  I can ping any server in the LAN subnet. 
# Comprendre les tables de routage

```
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
192.168.1.0     *               255.255.255.0   U     1      0        0 eth0
```
Tous les paquets avec une destination 192.168.1.0 à 192.168.1.255 sera envoyé à travers l'interface eth0 sans utiliser de gateway (sauf s'il y a une autre route qui écrase celle là)    

```
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         192.168.1.1     0.0.0.0         UG    0      0        0 eth0
```
Tous les paquets qui ont une destination sans autre route sera envoyé à travers eth0 en utilisant la gateway 192.168.1.1

*Default* ca veut que si il n'existe pas d'autre route alors utilise celle là.  
*Gateway* next hop. 


## ifconfig
```
eth0      Link encap:Ethernet  HWaddr 00:80:C8:F8:4A:51  
          inet addr:192.168.99.35  Bcast:192.168.99.255  Mask:255.255.255.0
```
l'adresse active est donc 192.168.99.35. Ce qui veut dire que tout paquet envoyé par cette ordinateur aura comme adresse source 192.168.99.35. Et tous les paquets recus par cette ordinateur aura comme destination address 192.168.99.35. 

Gateway : 0.0.0.0 veut dire qu'il n'y en a pas. 

## Unicast vs Broadcast
- Unicast
	- HTTP 
	- SMTP 
	- POP3
	- SSH
	- LDAP
- Broadcast. S'adresse à tout le subnet. On peut le calculer avec l'IP address et le mask mais on peut donner une autre adresse. Et cela peut poser des problemes.
	- SMB
	- Samba
	- DHCP
	
Unicast s'oppose à broadcast.

# DHCP / DNS

le server dhcp a pour role de donner:
- IP address 
- subnet mask
- Default gateway
- IP adress du DNS server
- Lease time

une adresse ip a chaque fois qu'une machine en fait la demande.
le server dhcp fournit aussi les serveur DNS.

Le server DNS fait la traduction entre le domain name et l'adresse IP.

Et ARP/RARP (reverse ARP) fait la traduction entre IP adresse et MAC address et vice versa.

sur openwrt : https://wiki.openwrt.org/doc/uci/dhcp

## dnsmasq
dnsmasq est un DNS forwarder et DHCP server
Un DNS forwarder forward les requetes DNS à un server DNS externe.

dig debian.org  
dig debian.org @8.8.8.8  
dig debian.org @10.66.1.0  # IP de openwrt.  

## /etc/resolv.conf
Contient les informations sur les DNS serveur à contacter.
```
# Generated by NetworkManager
search lan lan.
nameserver 10.66.0.1
nameserver fd32:5c36:1a94::1
```

networkmanager surcharge les informations données par dhcp, surcharge resolv.conf.
networkmanager sauvegarde les configuration dans : `/etc/NetworkManager/system-connections`


Dans les bonnes pratiques, on a l'habitude de mettre un adressage dynamique pour tous les PC et de mettre des adresses fixes pour les serveurs et les imprimantes. On n'oubliera pas de réserver des adresses ou des plages pour cela une plage au niveau du DHCP.

Le dhcp se base sur du broadcast. Donc limité au vlan.

Si vous avez un serveur mail, un serveur d'authentification, un intranet, un serveur de gestion d'impression ... => il faut un DNS. Personnes d'autres que vous ne peut savoir où sont vos ressources en internes.
