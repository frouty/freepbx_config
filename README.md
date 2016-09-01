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
Application -> Extension -> Quick Extension Create

Extension
====
**Applications -> Extension**

Normalement chaque IP phone est assigné à une extension. S'il y a plusieurs lines button sur le phone (?) http://wiki.freepbx.org/display/FPG/Extensions+Module

Extension Module marche avec d'autres modules
* Inbound Routes Module, 
* Ring Groups Module, 
* Queues Module, 
* Paging Module,
* Follow Me Module, because each extension can have its own Follow Me options (?).
* Advanced Settings Module. 
  * Device Settings section of the Advanced Settings Module, you can change a number of the default settings that will apply when you create a new extension.
  * Advanced Settings Module can be used to enable Device and User Mode. When Device and User Mode is enabled, the Extensions Module will disappear and be replaced with two separate modules called "Devices" and "Users."

* User Management Module. In the User Management Module, a user may have a "primary linked extension." (?)

DADHI extension c'est quoi?
---
Fichiers de configuration
====

 La configuration d'Asterisk s'articule sur les fichiers de configuration suivants :

    /etc/asterisk/sip.conf : Configuration globale d'Asterisk
    /etc/asterisk/users.conf : Configuration des utilisateurs
    /etc/asterisk/extensions.conf : Configuration du Dialplan

[work]			; Nom du contexte  
exten => _6XXX,1,Dial(SIP/${EXTEN},20)  
exten => _6XXX,2,Hangup()  

Dans ces trois lignes nous allons voir deux choses, les contextes et les extensions. [work] est le contexte c’est une sorte de conteneur dans lequel les utilisateurs faisant partis de ce contexte pourrons communiquer entre eux. Lors de la création de nos deux utilisateurs nous avons spécifié le contexte work.

    exten ⇒ : déclare l’extension (on peut aussi simplement dire numéros)
    _6XXX : Prend les extensions (ou numéros) de 6000 a 6999 le « _ » permet d’utiliser des regex
    1 : Ordre de l’extension
    Dial : application qui va être utilisé
    SIP: Protocol qui va être utilisé
    ${EXTEN} : variable de l’extension composé, si on appelle le 6001 la variable ${EXTEN} prendra comme valeur 6001
    20: temps d’attente avant de passer a l’étape suivante.

Donc la ligne exten ⇒ _6XXX,1,Dial(SIP/${EXTEN},20) se traduit par: Quand on compose le numéro (par exemple) 6001, on appelle le numéro 6001 et si au bout de 20 secondes il n’y a pas de réponses on passe à la ligne du dessous.

La seconde ligne : exten ⇒ _6XXX,2,Hangup() permet de raccrocher si il n’y a pas de réponses au bout des 20 secondes. 

Le numéro à appeler pour joindre un utilisateur est défini dans le fichier /etc/asterisk/users.conf  
Context
====
?
Dans le fichier /etc/asterisk/users.conf il y a un parametre : context = uncontext

On retrouve ce context uncontext dans les extensions.  

Configuration d'une phone IP avec End Point Manager (EPM)
====

Il faut avoir une extension et un user manager account

Settings -> EndPointManager -> Global Settings

* internal address: ipadressipbx
* external IP Address : (?)

-> Add brand  
* internal destination address : will pull the IP address from global settings
* external:  l'address IP ou FQDN si le téléphone n'est pas local.
* firmware version select firmware slot 1
* Provision Server Address c'est la destination address
* Provision Server Protocol HTTP (depend de l'IP phone)
* Save template.  
* Aller à la page Settings->EndPointManager->Brands(name)->Manage(nom du template)
* Cliquer sur le boutton blue du phone que l'on veut configurer.
* line key section : select Type:line. Label and Value disappear. Select Account: Account1
* save model

Mappage d'un IP phone vers une extension
====
-> Extension Mapping -> Add Extension
-> Extension/Name select l'extension et le compte que l'on veut associer au téléphone.
-> select le brand du telephone 
-> entrer sa mac address
-> select le template du phone et le model du phone
-> save and rebuilt is selected
-> use selected click
-> firmware management click
-> brand click
-> drag and drop the latest firmware to slot1
-> submit click
-> wait reload the page ou confirm from submission. Green msg confirmant l'installation du firmware.
-> sur le téléphone:

Comment savoir un téléphone SIP a été enregistré dans le serveur
====
Son numéro d'appel s'affiche sur l'écran.

Phones Sangoma
====
http://wiki.freepbx.org/display/PHON

3 facons de configurer un phone

1  redirection service (zero-touch auto-provisioning)
2  DHCP option 66
3  hard setting provisioning server 

redirection service (zero-touch auto-provisioning)
----
Dans le web GUI on verifie
* EndPointManagement -> Global Setting 
* Group Permissions dans User Management -> **groups** tab -> on verifie que le user est bien dans le group "All Users"
* onglet **Phone Apps** -> Allow Access -> Yes
* Users -> Edit button verifier que les permission sont sur Inherit from the group.

register le phone
----
pour utiliser le redirection service il faut l'enregistrer sur le portal de sangoma.
https://portal.sangoma.com cloud service > sangoma phones > register phone tab

DHCP option 66
----
ll faut que le serveur dhcp du routeur support le dhcp option 66

The hard way
----
from the GUI of the phone
login/password : admin/admin
management -> Auto Provision - upgrade mode - config server path - autoprovision Now click
Fax 
===
voir module User Management 
