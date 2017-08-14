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

