===
Fax
===
 
voir module User Management 

J'ai le module fax pro qui permet d'envoyer des fax. Le module fax gratuit ne permet pas d'envoyer des fax.

Fax - Émission de fax
---------------------
J'ai une outbound route. Sans configuration particuliere pour les fax.  
Il n'y a pas de parametre de fax dans l'outbound route.  
Dans Admin - User management - onglet Fax pour autoriser le user à utiliser les fax.  

on va dans l'UCP est on fait "send new fax". cela marche.

A noter qu'il ne faut pas que la vielle machine de fax soit brancher sur la ligne qui envoie le fax.

Fax - Réception de fax
----------------------

On définit la detection de fax dans : Applications / Extension. 
Mais cela n'a pas marché. Je ne trouve pas de parametre de fax dans application - extension

Dans Connectivity -DHADI config - global setting - fax detection --> Yes et on essaye à nouveau.  

Configuration pour recevoir les faxs:
`Connectivity -Inbound Route`:
   - `set destination - Fax recipient user2`  
   - Fax - Fax destination user2 

Pas de configuration des ports fxo dans les inbound route. Il faut travailler avec le DID. A faire. pour voir si cela marche.

Comment faire pour ne pas recevoir le fax par email mais le retrouver dans l'inbox.
-----------------------------------------------------------------------------------
j'essaie :
Setting - Fax config - email address (not the outgoing email address) : leave empty  
Ca ne marche pas. Continue à l'envoyer par email
Si les deux champs 
Setting -Fax config - outgoing email adress : empty.
Ca ne marche 

user config - fax - email result -never ne marche pas non plus pour ne pas recevoir de fax


Setting - Fax config - outgoing email address (and email adress) : leave empty  

Rien à faire j'ai toujours mon fax qui arrive par mail.

email address --> une autre adress mail non je recois toujours francois.oph@gmail.com

Admin - Feature Code admin - Dial system fax c'est quoi?

http://www.emetrotel.com/tsd/content/dial-system-fax

Mais cela ne me dit pas ce que je peux en faire.
