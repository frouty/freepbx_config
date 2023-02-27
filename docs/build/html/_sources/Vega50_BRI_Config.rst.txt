========================
Configuration du VEGA 50
========================


Vega 50 Europa 
--------------

Firmware

Vega 50 BRI configuration
-------------------------

https://wiki.freepbx.org/pages/viewpage.action?pageId=60522602
Cette page ne donne la solution pour configurer le SIP trunk

https://wiki.freepbx.org/pages/viewpage.action?pageId=60915768
Avec la solution pour configurer le SIP trunk.

- 1 trouver l'IP du VEGA
- 2 se connecter au WebGUI (admin / admin au départ) 
- 3 Quick config / continue
- 4 change login password, le mettre dans le gestionnaire de mots de passe. 
- 5 Onglet basic config :

        - Country FR
        - Timezone : +11
        - hostname on choisit ce que l'on veut.
        - Le mettre en static ip decocher obtain ip settings using dhcp.
-6 onglet VoIP: 
    - Registration mode : Gateway
    - SIP domain : ipfreepbx server.
    - Registration and authentification ID:  On utilise le nom du Trunk
    - Authentification password : On met celui definit dans le truind ou on les note on aura besoin lors de la configuration du trunk sur le freepbx.
    
     
Les onglets en haut a cote de basic config vont dependre du type de port que l'on a sur le vega. 

Si on fait un changement le submit button passe en rouge. 

 System Status :
    - Registrar and proxy sont set sur l'ip du freepbx.
    - SIP registrations : show 
    je vois que
    
    SIP REG
-------------------------------------------------------------------------------
SIP Profile 1 - registration expiry = 600s
-------------------------------------------------------------------------------
SIP REG USER 1
--- address   - vega50@10.66.0.2
--- auth user - vega50
--- contact   - <sip:vega50@10.66.0.3>
--- state     - unregistered   (user 1)
--- TTL       - 0 seconds
SIP REG USER 2
--- address   - 02@10.66.0.2
--- auth user - 
--- contact   - <sip:02@10.66.0.3>
--- state     - unregistered   (user 2)
--- TTL       - 0 seconds 

Ce n'est pas registred. 

J'ai vu https://wiki.freepbx.org/pages/viewpage.action?pageId=125436078 ou il utilise le port 5160. E
t j'ai vu que dans Settings / Asterisk sip sesettings / SIP legacy settings /  Advanced port 5160.
Ca n'a rien changé. 

Je vais essayer pjsip

https://community.freepbx.org/t/how-to-connect-freepbx-13-pjsip-sangoma-vega/31319
https://wiki.freepbx.org/pages/viewpage.action?pageId=125436194
 
Et ca a marché.

Rien de différent pour la configuration du vega.

Connectivity / Add trunk / Add SIP (chan_pjsip) trunk

- Trunk name j'ai mis le meme que celui mis dans la config du vega.
- Hide CallerID No
- Outbound CallerID = 2000 parce qu'il faut mettre quelque chose.
- ... je laisse tout en valeur par defaut

- Dialed Number manipulation rules : je fais rien

Onglet pjsip settings:
General 
- username vega50 le meme que celui Registration and Authentication ID du vega.
- secret celui mis en place dans le vega.
- authentification Outbound 
- Registration : send 
- language code default
- SIP server : IPVega
- SIP server Port : 5060
- Context : from-pstn
-Tranport : 0.0.0.0-udp
Onglet Advanced je laisse tout par défaut. 
Onglet codecs tout par defaut.

Dans le vega Status REgistred user 0 
