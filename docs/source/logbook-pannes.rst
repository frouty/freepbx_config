==================
Logbook des pannes
==================

Panne du 28/05/2018 
-------------------
a
Apres deux jours de pluie intense.

- Absence d'appel recu sur l'ip phone
- asterisk -rvvvv
- sip show peers donne:  
` Name/username             Host                                    Dyn Forcerport Comedia    ACL Port     Status      Description                      
1/1                       10.66.0.180                              D  No         No          A  5060     OK (8 ms)                                    
2/2                       10.66.0.235                              D  No         No          A  5060     OK (21 ms)                                   
3/3                       10.66.0.154                              D  No         No          A  5060     OK (10 ms)                                   
4/4                       10.66.0.152                              D  No         No          A  5060     OK (10 ms)                                   
5/5                       (Unspecified)                            D  Yes        Yes         A  0        UNKNOWN                                      
vega50/vega50             10.66.0.3                                D  Yes        Yes            5060     Unmonitored                                  
vegaOut/vega50            10.66.0.3                                   Yes        Yes            5060     Unmonitored    ` 


j'ai rebooté le main retour ; le routeur adsl ; redemarré le service openvpn du main routeur (dans le web GUI ) et du freepbx (./init.d/openvpn restart
- sip show peers donne:

`1/1                       10.66.0.180                              D  No         No          A  5060     OK (10 ms)                                   
2/2                       10.66.0.235                              D  No         No          A  5060     OK (23 ms)                                   
3/3                       10.66.0.154                              D  No         No          A  5060     OK (16 ms)                                   
4/4                       10.66.0.152                              D  No         No          A  5060     OK (12 ms)                                   
5/5                       10.8.0.3                                 D  Yes        Yes         A  5060     OK (49 ms)                                   
vega50/vega50             10.66.0.3                                D  Yes        Yes            5060     Unmonitored                                  
vegaOut/vega50            10.66.0.3                                   Yes        Yes            5060     Unmonitored                                  
7 sip peers [Monitored: 5 online, 0 offline Unmonitored: 2 online, 0 offline]
`

Je fais des reboot sur le ip phone en VPN. Est ce que cela suffit.
Je fais un restart sur le service openvpn du freepbx  ` ./etc/init.d/openvpn restart`
Je fais un reboot du main retour openwrt
Mais je n'arrive plus de la maison à me connecter sur le reseau MLP
Le server ODOO n'est plus accessible meme sur l'adresse dynamic dns.
Il n'y avait plus de connetion extranet wan IPv4 sur le main router.
J'ai rebooté le router adsl

Panne du 12/11/2019
-------------------

tous les appels vers le 281600 sont redirigé vers mon téléphone. 
Si j'essaie de faire l'extension 5 l'appel est regirigé vers mon extension.

J'ai essayé de redemarrer le main router, le server freepbx. Je n'ai pas redemarrer le routeur adsl.
Je vois que dans le endpoint manager je ne peux pas choisir de client vpn.

Je vais tester 
OK I figured out what I needed to do for the VPN system to start generating VPN clients properly:

    Disable VPN permission for all groups
    Disable the VPN service under System Admin
    Remove all VPN clients

Run the following commands from the CLI:

rm -rf /etc/openvpn/clients/*
rm -rf /etc/openvpn/client/*
rm -rf /etc/openvpn/ccd/*
rm -rf /etc/openvpn/sysadmin_*
rm -rf /etc/openvpn/ipp.txt
cd /etc/openvpn/easyrsa3
./easyrsa init-pki

At this point you can reenable the VPN and it will recreate the CA and certificates. You can then reset your group permission to auto-assign VPN clients and then you can assign them to phones.

The original bug of creating VPN clients for every single user in the system when dealing with a single user is still a problem though.

Je n'arrive pas a mettre le VPN client dans Endpoint Manager / Mapping Extendion / . Je renouvelle la licence de Endpoint Manager et mise à jour et voila je peux mettre mettre le VPN client.

Il y a un probleme  avec la provisionning configuration. L'IP externe à changé au cabinet. Mais elle n'a pas changé sur le téléphone. Et j'ai dans le téléphone Menu /Settings / advanced settings / autoprovioning / :
- Upgrade mode : http
- firmware server http ::/user:passwd@IP ancienne du server freepbx:83/sangoma/1
- config server : http ::/user:passwd@IP ancienne du server freepbx:83

Est ce que je dois updater ces données manuellement sur le téléphone ou bien il devrait récuperer ces données sur le serveur automatiquement. Ca n'a pas l'air de changer si je fais un reboot du phone, force phone to check config. Du coup dans le téléphone Menu /Settings / advanced settings / Phone settings / Factory reset. Il y a d'autres facon de faire un reset (https://wiki.freepbx.org/display/PHON/Factory+Reset )  C'est assez long. L'ecran devient tres différent. Menu phone/ settings / advanced settings password "admin" / autoprovisioning on a config server : https://rs.sangoma.net/cfg
Dans advanced settings / network / VPN / VPN active Disable.

Je suis avec un ip phone factory resetté. http://ipduphone gui (password admin login admin) Je vais dans le gui pbx et je fais Endpoint Manager / estension Mapping / Edit  / Save, Rebuild Config and update Device : Apply. il ne se passe rien au niveau de l'IP phone.
On essaye force phone to reboot. cela ne fait rien.

Je vais dans le web gui du phone je fais Management / autoprovisionning :
- firmware server path : http://user:passwod@goeen.ddns.net:83/sangoma/1
- Config server path : http://user:passwod@goeen.ddns.net:83
- save
- autoprovisionning cela bouge sur l'ecran du phone. Mais au final je n'ai pas la meme configuration de l'écran du phone que celle que j'attendais. Restart sur le web gui du phone. j'ai pas la meme configuration de l'écran. Enpoint Manager / extension mapping / force phone to check config il ne se passe rien sur le phone, force reboot il ne se passe rien sur le phone.

Je change les ip d'autoprovisionning je passe à IPlocalduserveur. `autoprovisionning` il se passe plein de chose au niveau du téléphone, initialise, checking firmware, c'est long; tail -f /var/log/httpd/access.log donne des infos sur ce qui se passe et c'est cohérent. Et j'ai l'écran de telephone que j'ai configuré dans le endpoint management / template. Et j'ai affiche VPN activated. Mais je ne peux pas appeler. Je vois que le password du phone est passé à ce qui est spécifié dans le gui freepbx et n'est plus `admin`. Je vois que le provisionning à été changé en :
http://user:password@FQDNDynamic:83.
Je rentre à la maison et j'essaie de le connecter. A la maison boot du phone. Qui récupère une adresse ip local de la maison et donc cela ne marche pas. Tail -f /var/log/httpd/access.log ne donne rien. 

Donc je me dis que c'est goeen.ddns.net qui ne va pas. Endpoint manager / Global setting / Ip external : `auto` Save Global. Ip external passe à l'IP public du router. Je rentre cette ip dansle web gui du phone. SaveSet Autoprovision now autoprovision success. Dans le phone j'ai bien la nouvelle adresse. je fais un resart. Reboot please wait. Cela ne marche pas non plus j'ai toujours une adresse ip privée de la maison.

Avec le web gui du tel depuis la maison je mets 10.66.0.2 
Autoprovisionning il ne passe pas grand chose. Restart. Reboot
initialisation. Cela n'a rien changé. toujours une ip local au reseau de la maison.

https://wiki.freepbx.org/display/PHON/VPN+Setup
https://wiki.freepbx.org/display/FPG/System+Admin+-+VPN+Server


Je suis tres avancé dans la configuration du vpn mais le telephone récupère une adress ip locale et pas une ip vpn. 
Dans le fichier de conf du client je vois `remote FQDNDDNS` et je me demande si c'est pas cela le probleme. Je regarde comment sont les fichiers de conf du vpn de la maison qui fonctionne. et j'ai bien `remote FQDNDDNS 1200`. Donc c'est pas cela le probleme.

reboot the phone sur un lan remote. tail -f /var/log/messages. Tout semble OK. 
System admin / VPN Server / le client à un address IP et Connected avec la date de la connection. 

reboot du phone il ne se passe rien dans le freenas# tail -f /var/log/messages
reboot du phone `tail -f /etc/openvpn/sysadmin_server1-status.log` Il ne se passe rien. 
reboot du phone `tail -f /var/log/messages` il ne se passe rien. 
reboot du phone `tail -f /var/log/httpd/accesslog` il se passe des choses. Il semble que l'ipphone récupere des fichiers.