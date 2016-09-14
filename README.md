# freepx_config

http://kb.digium.com/


Comment configurer le firewall?
====
http://wiki.freepbx.org/display/FPG/Firewall+Command+Line

fwconsole firewall --help 

fwconsole firewall disable  
puis en GUI recherche -> firewall -> enable --> re-run wizard

Comment mettre à jour l'achat de modules commerciaux
====
Admin -> Sys admin -> Activation -> Update activation (en bas à droite)


Comment mettre à jour les modules?
====
Admin -> Module Admin

Comment installer en ligne de commande un module 
====
fwconsole ma --edge upgrade framework  
Le plus dur va etre pour trouver le nom du module.



Amportal commands
====
http://wiki.freepbx.org/display/L1/amportal+commands

linux command qui controle freepbx
on y arrive avec un ssh sur la machine ou est installé freepbx/asterix.

Liste de commande
-----

1 **amportal restart** pour relancer asterisk et autre process nécessaire à asterisk. On utilise cette commande et pas un /etc/init.d asterisk.
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

    /etc/asterisk/sip.conf : Configuration globale d'Asterisk. toutes les modifications sur ce fichiers doivent être faites par le web GUI
    /etc/asterisk/users.conf : Configuration des utilisateurs
    /etc/asterisk/extensions.conf : Configuration du Dialplan. Toutes les modifications sur ce fichiers doivent être faites par le web GUI

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
-> Extension Mapping -> Add Extension (dans le sous menu de Settings->EndPoint Manager)
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

Reset factory s500
---
Web GUI
Management -> Upgrade -> reset to factory

Connecter un sangoma phone à freepbx  
---
http://wiki.freepbx.org/display/PHON/Connecting+Sangoma+Phone+to+FreePBX+or+PBXact+Indepth

3 facons de configurer un phone Sangoma
---

1  redirection service (zero-touch auto-provisioning)
2  DHCP option 66
3  hard setting provisioning server 

redirection service (zero-touch auto-provisioning)
----
Dans le web GUI on verifie
* EndPointManagement -> Global Setting 
* Admin -> USer management -> Onglet Groups -> on verifie que le user est bien dans le group "All Users"
* Edit -> onglet **Phone Apps** -> Allow Access -> Yes
* Users -> Edit button -> Onglet General -> verifier que les permission sont sur Inherit from the group.

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
Management -> Auto Provision - upgrade mode - config server path - autoprovision Now click


Comment trouver l'IP d'un phone Sangoma
---
menu -> Status -> information

Comment rebooter un phone Sangoma
----
Menu button -> * key 3 fois -> down arrow pour 10 s. Le téléphone reboot


provisioning success
----
Settings - Endpoint manager - Brands - sangoma - Template - sangoma_default - firmware management
et provision server Protocole : TFTP 

Et configuration sur le phone de l'adresse tftp://ip_du_freepbx


Phone Polycom 
===
LOgin / Password :

reset factory : loading software cancel -> hold 1;3;5 keys -> demande un password : 456 et c'est fait. Non pas toujours
mais avec la mac address à la place de 456 c'est OK
Reboot-> 0;1;3 ne marche pas  
1;5;9 ne marche pas

Dans le menu du phone: home - settings - advanced -- provisioning server j'ai rentré l'adresse du server freepbx, j'ai laissé user et login. Et c'est bon.


Quels sont les services que l'on peut utiliser pour tester son phone SIP
====
Admin -> Feature code

Clock *60  
echo test *43

configuration des FXO
====

http://documentation.xivo.io/en/stable/administration/hardware/hardware.html

http://www.voip-info.org/wiki/view/DAHDI



/proc/dahdi 
dahdi_hardware
asterisk -rvd

The userspace tools to control DAHDI spans/channels:-
dahdi_cfg
The DAHDI Configurator, which parses system.conf
dahdi_genconf
Generates /etc/dahdi/system.conf, so it's better that you don't hand edit system.conf. Uses /etc/dahdi/genconf_parameters to define it's actions.
dahdi_hardware
Displays listing of DAHDI hardware detected
dahdi_monitor
Monitors signal level on analog channel allows you to record audio from it
Usage: dahdi_monitor <channel num> -v -m -o -p -l limit -f FILE -s FILE -r FILE1 -t FILE2 -F FILE -S FILE -R FILE1 -T FILE2
example :- dahdi_monitor 1 -vv
note: extremly usefull, but otherwise not mentioned, that the raw format output is 8Khz 16bit signed. Use sox to convert to a wav. sox -r 8000 -s -w rx.raw rx.wav
dahdi_scan
Generates a list of things DAHDI channels, with some details
dahdi_test
Measures accuracy of the FXO/FXS board software digital signal processing
dahdi_tool
A nice tool to see what your boards are doing.

 Sample installation

After compiling and installing of dahdi and asterisk, you have to perform some further steps to use your hardware.
This example will show you a few steps how to get asterisk and two Digium cards enabled:

1.) Detect your hardware
  # (this will generate /etc/dahdi/system.conf and  /etc/asterisk/dahdi-channels.conf)
 asterisk:~# dahdi_genconf
2.) Read systm.conf file and configure the kernel
 asterisk:~# dahdi_cfg -v
3.) Restart dahdi to unload and reload all modules and drivers
  asterisk:~#  /etc/init.d/dahdi restart
4.) Point file chan_dahdi.conf to /etc/asterisk/dahdi-channels.conf

  # open chan_dahdi.conf and include it under the section [channels]
  #
  # NOTE: You can edit and configure /etc/asterisk/dahdi-channels.conf at any time 
  # to set up your specific options there.
  ...
  [channels]
  ...
  #include /etc/asterisk/dahdi-channels.conf
  ...
5.) Restart asterisk
  asterisk:~#  /etc/init.d/asterisk restart
5.a) Verify your current system status. You should get some output like this:
  asterisk*CLI> dahdi show status
  Description                              Alarms     IRQ        bpviol     CRC4      
  Wildcard TDM410P Board 1                 OK         0          0          0         
  Wildcard TDM800P Board 2                 OK         0          0          0    

5.b) Verify your configured channels
  asterisk*CLI> dahdi show channels
    Chan Extension  Context         Language   MOH Interpret       
  pseudo            default                    default             
       1            from-pstn       de         default             
       2            from-pstn       de         default             
       3            from-pstn       de         default             
       4            from-pstn       de         default             
       5            from-pstn       de         default             
       6            from-pstn       de         default             
       7            from-pstn       de         default             
       8            from-pstn       de         default             
       9            from-pstn       de         default             
      10            from-pstn       de         default             
      11            from-pstn       de         default             
      12            from-pstn       de         default             
  asterisk*CLI> 
 




DADHI
----
connectivity - DADHI Config - DADHI write disable disclaimer  [ENABLE]
Analog Hardware : type : FXO ports Ports : 1.2.3.4

Appeler un autre systeme SIP, un autre systeme analogique par une carte FXO il faut un trunk http://wiki.freepbx.org/display/FPG/Trunks+Module 

Connectivity -> Trunk 

J'ai un dadhi trunk. Mais je n'arrive pas a appeler l'exterieur

Connectivity - Outbound routes : on y définit les regles qui s'appliquent en fonction du numéro composé.  
Quelques exemples de regles: http://wiki.freepbx.org/display/FPG/Outbound+Routes+Configuration+Examples
Le module outbound route: http://wiki.freepbx.org/display/FPG/Outbound+Routes+Module

Configuration des appels entrants sur le freepbx server
====

Fax 
===
voir module User Management 

BLF
===
Busy Lamp Field c'est une LED sur un IP phone qui te dit si une autre extension connecté au meme PBX est occupé ou non. 

Modules commerciaux
=====
Call recording Reports : http://wiki.freepbx.org/display/FCM/Call+Recording+Reports

<<<<<<< HEAD
Trunk
===
J'ai 4 ports FXO. Comment attrribuer ces ports.

Connectivity -> Trunk (tout en bas) Je peux choisir les ports que j'ai déjà configuré dans Connectivity --> DADHI Config en leur donnant des numéro de group Mais je n'ai pas que le numéro de group comme choix j'ai aussi : analog 1 analog 2 analog 3

Je peux dans leur donner un groupe/
Add a DADHI trunk
Me demande de configurer l'outbound callerID je ne comprends pas. Ce n'est pas défini dans le premier trunk. TODO

Création des trunks puis Créations des outbound route qui vont utiliser ces trunks.

Outbound Route
----
on définit:
* le trunk qui va etre utilisé. On peut définir plusieurs trunk. il faut donc définir le trunk avant l'outbound route.
* le CID 
* le dial pattern qui va etre utilisé par cette route.

On peut utiliser ce dial pattern pour forcer l'utilisation d'une route. Par example pour forcer l'utilisation de la ligne de fax on peut imaginer de taper 000numeroducorrespondant. Mais comment on fait pour modifier ce dial pattern pour que seul le numéro du correspondant soit envoyé sur l'OPT?
http://wiki.freepbx.org/display/FPG/Trunk+Sample+Configurations

X   matches any digit from 0-9
Z   matches any digit from 1-9
N   matches any digit from 2-9
[1237-9] matches any digit or letter in the brackets (in this example, 1,2,3,7,8,9)
.   wildcard, matches one or more characters (not allowed before a | or +)

Exemples 
----

Dialed Number Manipulation Rules:

(49)+0|Z                           : this will strip the 0 from long distance Germany calls and add insted cuntry code 49

(49711)+|N                         : this will add country code 49 and area code 711 to local calls

()+00|Z                            : this will just strip the double 0 for international calls

(18882472425) + 411 |  empty        : re-routes 411 calls to 1-888-247-2425.

(1212) + empty | XXXXXXX            : adds 1 + 212 to the beginning of any number that is seven digits long

(1) + empty | XXXXXXXXXX            : adds 1 + to the beginning of any ten digit number.  



Dans Connectivity - Inbound Route je ne peux pas configurer le port FXO je ne comprends pas pourquoi.
Dans connectivity - outbound route je peux configurer le port FXO par l'intermediaire du parametre : Trunk Sequence for Matched Routes

Since the PBX routes all inbound calls based on the DID or number dialed, we need to map each analog port or channel to a fake number so we can match that number to an inbound route number and route your calls.

Connectivity - DADHI channel DID - add DADHI DID - channel : g1 (mais je ne suis pas sur) 
Channel : 
The DAHDI Channel number to map to a DID. For example, If you have a 4-port card, your channels would be ports 1-4.

Connectivity -DAHDI channel DID pour définir un DID par port FXO (1, 2 , 3 , 4). Puis j'utilise ce DID dans la configuration des Connectivity - Inbound Route - Set destination.

On définit la detection de fax dans : applications - extension. Mais cela n'a pas marché. 
Dans Connectivity -DHADI config - global setting - fax detection --> Yes et on essaye à nouveau

Inbound route - set destination - fax destination user 2 ne marche pas non plus.

Si je mets dans Connectivity -Inbound Route - set destination - Fax recipient user2
=======


Quelles sont les applications qui sont supportés par les IP phones. Liste
=====
http://wiki.freepbx.org/display/FPG/Phone+Apps-Supported+Devices
>>>>>>> master
