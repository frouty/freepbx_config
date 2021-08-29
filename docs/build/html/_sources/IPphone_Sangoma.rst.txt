IPPhone Sangoma
===============

http://wiki.freepbx.org/display/PHON

Comment reset factory sans connaitre le mot de passe
----------------------------------------------------

Menu button -> * key 3 fois -> down arrow pour 10 s. Le téléphone reboot

Le login / password par default apres un reset factory est : admin / admin

Reset factory connaissant le mote de passe 
------------------------------------------
- s500
  - Web GUI
    -Management -> Upgrade -> reset to factory

- s700 
  - sur le téléphone 
  - Menu / Settings / Advanced  Settings / Password que l'on trouve dans le WebGui du Freepbx : Settings / EPM / Global settings / Phone admin password
  - Phone Settings / Reset Factory
  
- Polycom 
  - TODO
  
Comment trouver l'IP d'un phone Sangoma sur l'ipphone
-----------------------------------------------------
menu -> Status -> information

Comment trouver/modifier  login/password pour se connecter en WebGUI ou sur l'ecran de l'ipphone?
----------------------------------------------------------------------------------------

On peut modifier de password dans : ? 

Setting / Endpoint Management / Global settings.

C'est vrai une fois que l'ipphone c'est auto-provisionné sur le freepbx.


Comment provisionner un IPphone sur le freepbx
----------------------------------------------

Les 3 facons de configurer un phone Sangoma
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- 1  redirection service (zero-touch auto-provisioning)
- 2  DHCP option 66
- 3  hard setting provisioning server 


redirection service (zero-touch auto-provisioning)
*************************************************

Dans le web GUI on verifie

* EndPointManagement -> Global Setting 
* Admin -> USer management -> Onglet Groups -> on verifie que le user est bien dans le group "All Users"
* Edit -> onglet **Phone Apps** -> Allow Access -> Yes
* Users -> Edit button -> Onglet General -> verifier que les permission sont sur Inherit from the group.


Pour utiliser le redirection service il faut l'enregistrer sur le portal de sangoma.
https://portal.sangoma.com cloud service > sangoma phones > register phone tab

Je n'arrive pas. Claim adress mac du phone de hello cab. Me dit qu'il y a un probleme contacter le support.


DHCP option 66
**************

ll faut que le serveur dhcp du routeur support le dhcp option 66

Jamais fait comme cela


The hard way
************
C'est comme cela que je fais  chez moi.

from the GUI of the phone
login/password : admin/admin
Management -> Auto Provision - upgrade mode - config server path - autoprovision Now click

upgrade mode = HTTP
firmware server path : http://username:password@IPFREEPBX:port 
avec username / password / port que l'on trouve dans  System Admin / Provisionning protocole ou dans Endpoint Management / Global Setting c'est mieux.


Connecter un sangoma phone à freepbx  
************************************

http://wiki.freepbx.org/display/PHON/Connecting+Sangoma+Phone+to+FreePBX+or+PBXact+Indepth
- 1 s'assurer que les provision protocoles HTTP et HTTPS sont OK : system admin / Provisionning protocols.
- 2 s'assurer que le serveur PnP marche  
System Admin / PnP configuration / PnP server status enable.  
Pnp server configuration : automatic.
- 3 configurer les bons protocoles http dans les template : Settings / EPM / Brand / Sangoma 
- 4 S'assurer que l'IP phone est bien reset factory. 
- 5 brancher l'IP phone. Accepter le PnP message.
- 6 et voila.  

Moi j'ai du en plus rentrer dans le WebGui de l'ipphone est configurer Management / auto provisionning voir the hard way.

