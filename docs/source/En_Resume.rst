============================
Config du Freepbx en resume.
============================

Configuration des mails
-----------------------

Config du VEGA donc de la ligne BRI
-----------------------------------

1 configuration du vega avec le webGUI du vega

2 Configuration d'un sip (chan_pjsip) (chan_sip je n'ai pas reussi à le faire marcher) qui relie  le vega au freepbx

4 outbound route : on y  fait un mapping entre une numerotation et d'autres criteres et un trunk de sortie de l'appel.

5 inbound route : on y fait un mapping entre un DID (destination id) et une destination comme une extension par exemple.
le DID semble etre tout simplement le numero de telephone. Peut etre que les 4 derniers chiffres. On peut mettre ANY, c'était comme cela en 13 mais je ne comprends pas tres bien.
Config des lignes analogiques 
-----------------------------

C'est la config des carte FXO

1 DAHDI config / Analog FXO port j'ai juste changer Group
2 DAHDI Channel - DID : mapping entre le n° de port FXO est un DID. On le fait ici car la ligne ne transmet pas cette information.
2 DAHDI Trunk mappe au FXO par le groupe C'est là que l'on voit le role de Group  defini ci dessus
3 Inbound route avec un mapping DID- destination

Au 27/08/2021 : 

 
- La ligne  des orthoptistes est sur le port (channel) FXO 1.  groupe 0 qui se configure dans Connectivity / DAHDI  config
- La ligne 281608 est sur le port (channel)  FXO  2.  groupe 0 qui se configure dans Connectivity / DAHDI  config

On le trouve dans Connectivity / DAHDI Channel DID 

Mais pour le savoir physiquement il faut regarder les cables. Il faut les marquer.

Creation des users 
------------------

Admin / User Management

Creation des extensions
-----------------------
