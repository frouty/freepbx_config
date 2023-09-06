Configuration du VPN server pour pouvoir enregistrer un IPphone en VPN
======================================================================
https://wiki.freepbx.org/display/FPG/System+Admin+-+VPN+Server

Setting Up a VPN Server
-----------------------

Admin / System Admin / VPN Server / onglet Setting 

**Enabled** 
    Yes. Lance le server et s'assure qu'il redemarre au reboot de freepbx
**Server Range**
    Ceux sont les ip address qui seront assignées aux clients à la connection. 
**Redirect Gateway**
    No dans la plupart des cas. 
    
Puis setting up a client pour se connecter au serveur VPN

Setting Up a client
-------------------


Onglet Clients / + ADD 

**Enabled**
    Yes
**Description**
    Ce que l'on veut.
    
Pour  bloquer la connection d'un client :
   -  mettre Enabled sur No, ou en 
   -  Effacer le client.

**Use DDNS**
    Yes

**Use Server Remote Address**
    Je n'ai pas trouvé où est configuré cette adresse. Après on voit dans System Admin / VPN server / Onglet Settings **Sever Remote address** ip public externe en chiffre. Je laisse comme cela pour l'instant.

**Client Remote address**
    DYNDNS FQDN. Est ce qu'il faut mettre le port? J'ai du port forwarding sur ce port dans le main router.
    
**Assigned Address**
    Address assignée au client. j'en choisis une. 
    
Submit

On vient juste de le créer il est "not connected".

Maintenant il faut linker ce client à un user. Ce qui va permettre à ce user de downloader la config VPN depuis l'UCP (jamais fait)(quel usage?), de lier ce user dans l'EPM pour que le sangoma phone utilise le VPN. 

Setting the use for the VPN client
----------------------------------

Admin / User management / Edit the user / Onglet VPN /  

**Autocreate and link**
    Yes Cela crée automatiqueement un autre client pour le VPN server.

**Define additionnal VPN Clients**
    Je mets celui que je viens de creer manuellement. 

Submit / Apply Config. 

On peut aller voir en CLI dans /etc/openvpn/clients les fichiers de conf. 

On voit  que dans System Admin / VPN server / Onglet Settings **Sever Remote address** est apparu.

On voit que des routes sont apparues: 
    - 10.8.0.0 avec enabled à No
    - 10.66.0.0 enabled à No 
    
Maintenant on va lier dans l'EPM

Setting Extension
-----------------

Settings  / EPM / Extension Mapping / edit l'extension qui nous interesse / tout en bas je vois VPN client/ Je choisis celui que j'ai configuré manuellement. 

Save , Rebuild config and Update device.

CLI > ifconfig . je vois une interface tun0ta
J'ai des messages d'erreur dans /var/log/messages concernant le client0 qui correspond à celui que j'ai créer manuellement. Je le détruit.

Je verrais ce qui s'est passé sur le téléphone IP

Comment connaitre la status de Openvpn
======================================
systemctl status openvpn@sysadmin_server1.service

VPN 23/03/2020 qui marche
=========================
system admin / VPN server / Onglet settings / 

**server range** 
   10.8.0.0 / 255.255.255.0
**server port**
   1194
**server remote address**
    ipaddress du routeur principal en chiffres. Et maintenant j'ai l'address domain de mon dddns.
**Redirect gateway** :
    no
**Routes** : 

.. list-table:: Routes
    :widths: 25 25 25 50
    :headers-rows: 1
    
    * - IP Address
      - Netmask
      - Enable
    * - 10.66.0.0
      - 255.255.255.0
      - Yes
      
+------------+---------------+--------+
| IP address | Netmask       | Enable |
+============+===============+========+
| 10.8.0.0   | 255.255.255.0 | No     |
+------------+---------------+--------+
| 10.66.0.0  | 255.255.255.0 | No     |
+------------+---------------+--------+




system admin / VPN server /Onglet client 

+-------+-------------+------------+-----------+
| ID 17 | Description | cllient IP | Connected |
+=======+=============+============+===========+
10.8.0.3 | secoph -10 | 26032020 9:26:03 |
++++++++++++++++++++++++++++++++++++++++++
si je fais edit :

**enabled:**
    Yes 
**description**
    secoph - 10

**use ddns**
     Yes 
**use server remote address :**
    Yes 
**client remote address :**
    vide ou IP public du routeur freepbx et address ddns. 
**assigned address :**
    none ou 10.* .*.n

VPN Config Freepbx 15
=====================

