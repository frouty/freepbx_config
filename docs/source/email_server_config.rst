================================================
Comment permettre à freepbx d'envoyer des emails
================================================

https://wiki.freepbx.org/display/FPG/System+Admin+-+Email+Setup

Admin / System Admin / Email Setup  


Choix du SMTP server
--------------------

gmail
~~~~~
J'ai essayé gmail mais je n'ai pas réussi.  



Use built in SMTP server  
~~~~~~~~~~~~~~~~~~~~~~~~

- hostname = ? je ne comprends pas les explications du wiki
- origin = ?
- Domain = ?  
 
 J'ai essayé Use external SMTP server avec gmail mais cela n'a pas marché. Debug : network unreachable.
 
 J'ai essayé USe built in SMTP server et ca marche. j'ai changé My Origin  localhost.localdomain --> FreePBX.MLP. 

 Mais cela part dans les spams.
 
 Avec la nouvelle appliance pour l'instant avec gmail je n'ai pas réussi, avec un builtin SMTP server je n'ai pas recu de mail encore.
 
 **My Hostname** : 
    vide
 
 **My Origin**
    localhost
 
 **My Domain**
    localdomain
    
 Le probleme c'est que je n'ai rien de tout cela. Je mets des valeurs au pif.
 
https://wiki.freepbx.org/display/PPS/Setup+Postfix+Manually
 
Debug
-----

Click sur debug , en bas à droite à coté de Submit. 

On va sur la page de debug et on peut lancer un test de mail. 

Network unrecheable alors que le ping fonctionne.

je vois que le from est : asterisk@voIPFreepbxOPH.freepbxOPH


https://www.youtube.com/watch?v=b1tNmc-jqok mais je n'ai pas réussi. 

Comment configurer les notifications par mail
---------------------------------------------

Admin / System Admin / Notification Settings

System Admin Pro
----------------

Pas de changement au niveau de l'interface. J'essaie avec Use built in smtp server 
Mais en Debug j'ai network unreachable.

