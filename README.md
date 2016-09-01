# freepx_config

Amportal commands
====
http://wiki.freepbx.org/display/L1/amportal+commands

linux command qui controle freepbx
on y arrive avec un ssh sur la machine ou est installé freepbx/asterix.

Liste de commande
-----

1 `amportal restart` pour relancer asterisk et autre process nécessaire à asterisk. On utilise cette commande et pas un /etc/init.d asterisk.
2 amportal stop

3 **amportal chown** change toutes les permissions vers l'utilisateur apache.

4 **amportal a r** reload de tout le dialplan et de tous les modules. Equivalent à cliquer sur Apply Changes dans le web GUI

5 **amportal a u phpsessionID** permet de déverouiller le GUI login si on oublie le password. Comment obtenir le phpsessionID: ouverture de la page de login avec htttp://ipduserver/admin --> ctrl a --> en bas a gauche : phpsessionID

6 **amportal a m** log dans la database mysql

7 **amportal a s** resync the credential (?)

8 UCP
  * amportal admin|a ucp `<option>`
    * enableall|en active UCP login pour tous les USER MANAGER: **amportal a ucp enableall**
    * unlock|u unlock session in UCP: **amportal a ucp u**

Comment configurer son serveur de mail
====
todo

Comment configurer les notifications par mail
====

Admin>System Admin>Notification Settings


Creer une extension
=====
Application -> Extension
