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