Time Condition
==============

1 Creation Time Group à faire en premier : Application / Time Group. Il y a uniquement des informations de temps
2 Time Condition qui est une enveloppe d'un time group et d'une destination si match ou si match pas.

On peut router les appels vers une time condition qui elle meme va router l'appel. A l'arrivée sur la time condition le systeme check time and date dans le time group et le systeme rooute le systeme sur une des deux destinations choisies.

On peut utiliser une time condition partout où il y a une destination:
- Inbound route
- IVR module
- Announcement module
- Time condition

Time condition Holidays / destination match / Annoucement we are in holydays. / destination non matches : destination = Time condition.

Time group que du temps

Time condition un time group et deux actions possibles si match ou match pas. 

Config actuel 
~~~~~~~~~~~~~
Inbound Route DID = 281600 --> Set Destination = Time conditions = gooen open hours  
Si c'est en open hours va sur une autre time condition qui va sur Annoucenment closed ou Misc destination. 

Bank holidays
=============

https://blog.ausics.net/archives/142-Holidays-With-FreePBX.html

# Time 
- 1 Création d'un time group  : 
- 2 On utilise ce time group dans une time condition qui est relié :
  - à un time group 
  - et qui fait une action si le time group match: Destination matches et là on peut faire d'autes actions.
  - ou ne match pas : Destination not matches : on peut faire d'autres actions. 


