Configuration du VPN server pour pouvoir enregistrer un IPphone en VPN
======================================================================
https://wiki.freepbx.org/display/FPG/System+Admin+-+VPN+Server
 
- 1 Admin / system admin / VPN server
- 2 Admin / User management pour assigner le client crée en 1
- 3 Settings / Endpoint Manager / Extension mapping

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
   Yes
    Je n'ai pas trouvé où est configuré cette adresse. Après on voit dans System Admin / VPN server / Onglet Settings **Sever Remote address** ip public externe en chiffre. Je laisse comme cela pour l'instant.

**Client Remote address**
    DYNDNS FQDN. Est ce qu'il faut mettre le port? J'ai du port forwarding sur ce port dans le main router.
    Je n'ai pas mis le port. 
    
**Assigned Address**
    Address assignée au client. j'en choisis une. On choisit dans la liste. 
    
Submit

On vient juste de le créer il est "not connected".

Maintenant il faut linker ce client à un user. Ce qui va permettre à ce user de downloader la config VPN depuis l'UCP (jamais fait)(quel usage?), de lier ce user dans l'EPM pour que le sangoma phone utilise le VPN. 

Setting the user for the VPN client
-----------------------------------

Admin / User management / Edit the user / Onglet VPN /  

**Autocreate and link**
    Yes Cela crée automatiqueement un autre client pour le VPN server.
    Apres dans une autre config j'ai mis et j'ai ajouté le client que j'ai créée dans Admin / System Admin / VPN server

**Define additionnal VPN Clients**
   Me donne la liste des clients crée dans System Admin / VPN server / Client
    Je mets celui que je viens de creer manuellement. 

Submit / Apply Config. 

On peut aller voir en CLI dans /etc/openvpn/clients les fichiers de conf. 

On voit  que dans System Admin / VPN server / Onglet Settings **Sever Remote address** est apparu.

On voit que des routes sont apparues: 
    - 10.8.0.0 avec enabled à No
    - 10.66.0.0 enabled à No 
    - Mais enable à No (!?)
    
Maintenant on va lier dans l'EPM

Setting Extension
-----------------

Settings  / EPM / Extension Mapping / edit l'extension qui nous interesse / tout en bas je vois VPN client/ Je choisis celui que j'ai configuré manuellement. 

Le téléphone est branché sur le reseau local. 

Save , Rebuild config and Update device.

Le boot se passe sans probleme. Je vois qu'il y a du provisionning avec des adresses du VPN et pas du reseau local. 
"VPN activated" s'affiche sur l'écran du tel. 
Je vais le tester en remote  

CLI > ifconfig . je vois une interface tun0ta
J'ai des messages d'erreur dans /var/log/messages concernant le client0 qui correspond à celui que j'ai créer manuellement. Je le détruit.

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

