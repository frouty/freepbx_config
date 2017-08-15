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

The TP-Link Archer C7 has eth0 = WAN, and eth1 = LAN (the 4 switch ports). Port 0 of the switch = eth1 (labelled CPU in Luci), Port 6 = eth0. Port 1 is labelled WAN in Luci.

## section interfaces
Les sections interfaces sont des containers pouir les adresses IP, aliases, routes, nom physique des interfaces and regles firewall. 
```
config 'interface' 'wan'
        option 'proto' 'dhcp'
        option 'ifname' 'eth0.1'
```
'wan' est le nom logique de l'interface. Parfois suivant le protocole le system ajoute un prefix automatiquement.  Pas plus de 9 caracteres.  
Les options sont différentes suivant les protocoles : https://wiki.openwrt.org/doc/uci/network
