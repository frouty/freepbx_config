========
Asterisk
========

Comment connaitre sa version d'asterisk
---------------------------------------

asterisk -x "core show version"

CLI
---
on peut utiliser les commandes CLI dans le web GUI.
- Admin
- Asterisk CLI

On peut les utiliser apres un ssh root@freepbxIP

Si on fait un CLI> `core stop now`    
Mais là on ne peut plus se reconnecter à asterisk.   

Faire un ssh puis  
`fwconsole stop`  
`fwconsole restart`  
`fwconsole trunks`

Pour entrer dans les commands line d'asterisk  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- `asterisk -r | asterisk -rvvv`    
- Le prompt change en localhost\*CLI>  
- ? pour avoir toute les commandes  

Quelques commandes utiles
~~~~~~~~~~~~~~~~~~~~~~~~~

- `sip show peers` 
- `sip show peer <extension number>`
- `sip show registry`
- `asterisk -rx`
- `sip show users`

Déboger Asterisk
----------------

- ssh root@FreePBXIP
- asterisk -rvvv

- `sip set debug on`

- `sip set debug 10`

- `sip set debug off`

Logger
------

https://wiki.asterisk.org/wiki/display/AST/Collecting+Debug+Information


Comment annuler les log output
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Comment annuler les log output in CLI asterisk: CLI> logger mute
 
 
Comment avoir des infos sur les autres commandes de logger
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 
 core show help logger
 
Couldn't connect to asterisk
----------------------------

Apres un reboot forcé par interruption du courant j'ai le msg  
*couldn't connect to asterisk*  
je n'ai rien fait j'ai attendu un peu.

Can not connect to Asterisk
~~~~~~~~~~~~~~~~~~~~~~~~~~~

ssh root@IPFREEPBX  
`fwconsole restart`

Debugger asterisk
-----------------

https://support.telosalliance.com/article/nf69bavy6e-how-to-enable-asterisk-debug-logging

Ca marche mais je n'ai pas reussi a faire quelque chose avec.

CLI>
asterisk -r
core set verbose 5
core set debug 5
sip set debug on
module logger reload


the log file /var/log/asterisk/full

Revert the logging level using the following commands:

asterisk -r
core set verbose 0
core set debug 0
sip set debug off
module logger reload


pour éviter d'avoir un espace disk full.


