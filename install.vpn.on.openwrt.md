# TPlink Archer C7 V2.0 (V2.0 c'est marqué sur l'etiquette de la boite)


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


Mais avec :
  
- pkitool --initca                      ## equivalent to the 'build-ca' script
- pkitool --server my-server            ## equivalent to the 'build-key-server' script
- pkitool          my-client            ## equivalent to the 'build-key' script (*not build-key-pkcs12)
- openssl dhparam -out dh2048.pem 2048  ## equivalent to the 'build-dh' script



On cherche s'il y a une interface tun avec ifconfig -a
On regarde la table de routage avec netsat -nr. On doit avoir et c'est essentiel une route vers le serveur VPN (xxx.xxx.XXX.XXX), une route 
On fait un traceroute pour voir si on va vers le server vpn.

# Quand est ce qu'on donne l'adresse du serveur vpn?
Dans le fichier de configuration du client /etc/config/openvpn : 
- option remote SERVER_IP_ADRESS 1194  
- option remote 'pw.openvpn.ipredator.se 1194'


YOu will need to open port 1194 (TCP) to be forwarding through the firewall to your OpenVPN server. Cela veut dire qu'il faut faire quoi?

# Comment obtenir l'adresse IP du serveur openVPN?
You can easily find out your OpenVPN server IP address. The syntax is as follows to get tun0 ip address on Unix or Linux:
`ifconfig tun0` sur le device qui fait tourner le serveur

Or use Linux specific command:
`ip -a show tun0`

# Comment obtenir l'adresse public
dig TXT +short o-o.myaddr.l.google.com @ns1.google.com

# Comment voir si tout c'est bien passé:
Afficher les log : `logread -f` en console ssh

- If everything went fine, the last log line from OpenVPN should contain Initialization Sequence Completed. There are some warnings and errors ... ignore them.

- `ps | grep openvpn`

- s'assurer que le lien est up avec `ifconfig tun0`, `ifconfig | grep tun` `ifconfig -a`
	- on doit avoir une ligne du genre : ` inet addr:10.8.0.6  P-t-P:10.8.0.5  Mask:255.255.255.255`

- s'assurer que cela fonctionne : `ping 10.8.0.1 -c 2`

- On regarde la table de routage avec netsat -nr. On doit avoir et c'est essentiel une route vers le serveur VPN (xxx.xxx.XXX.XXX)
 
- On fait un traceroute  10.8.0.1 pour voir si on va vers le server vpn. (?)


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

###  A sauvegarder dans : /etc/openvp. 


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