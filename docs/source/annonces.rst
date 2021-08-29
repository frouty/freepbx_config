===============================
Comment enregister des messages 
===============================

`Admin / System recordings`  
Va permettre d'enregistrer des messages qui pourront etre joué à l'appelant par d'autres modules.  
File list for english : j'ai l'impression que cela permet de concatener plusieurs fichiers qui serons lus l'un à la suite de l'autre c'est comme cela que je le comprends.
Si je veux pouvoir accéder à l'enregistrement via un ip phone :(pas sûr que cela soit utile)  
Link to Feature Code  - YES  

On y enregistre des messages. 
On peut concatener des messages. 
On peut utiliser ces messages dans les modules qui supportent la lecture des recordigns, comme:
- IVR
- Announcement 
- Follow Me
- Queues 
- Ring group
Ceux sont des modules qui ont des options pour selectionner un recording.

On peut mettre ces messages dans connectivity / Inbound route / set destination / Play recording / on choisit un des messages que l'on aura enregistrer auparavant.

Si un message est rouge cela veut dire qu'il n'est pas disponible.  
La croix rouge c'est pour supprimer le fichier.  

`Applications -> Annoucement`  
Ne pas confondre ce module avec le system recording.  
Il faut voir ce module comme une enveloppe d'un system record. Et c'est cette enveloppe qui va pouvoir etre appelé par le systeme.  
Le module `Annoucement` permet de jouer une annonce crée dans  `Admin \ System Recordings` et de poursuivre le call flow.  

`Application / Annoucement` : ne propose que les announces faites dans le system recording. Il faut donc créer l'annonce dans le module system recording avant.  


Jouer une annonce à tous les appels entrants:
---------------------------------------------

- 1 Créer le fichier son dans `Admin - System recordings`.  
- 2 Créer un announcement dans `Applications - Announcement`  
- 3 Connectivity - Inbound Route - Destination : choisir l'announcement     
Et cela marche. Testé  

Musique d'attente . MoH Music on Hold
-------------------------------------
`Setting - MoH`  
Je n'ai jamais entendu une telle music dans le telephone. TODO

call on hold 
------------

si l'appel est rejeté regardé https://wiki.freepbx.org/display/FOP/Calls+being+dropped+after+being+on+hold+for+5+minutes  

Comment enregistrer un message?
-------------------------------

- 1 upload à partir de fichier de l'ordinateur: `upload recording`
- 2 enregistrer avec l'ordinateur : `record in browser`. Mais il faut un micro
- 3 enregister à partir un téléphone : `record over extension`
    - 1 enter extension
    - 2 click call
    - 3 le téléphone sonne. Répondre. Et parler apres le beep. 
    - 4 raccrocher qd c'est fini
    - 5 save and name le message ou delete.

on peut utiliser audacity.
~~~~~~~~~~~~~~~~~~~~~~~~~

  http://whymailbox.blogspot.com/2009/06/create-great-sounding-recordings-in.html
et aussi 
For recording software I use Audacity.  
Go into Preference > Quality and set the “Default Sample Rate” to 8000 and “Default Sample Format” to 32 bit.  
Go ahead and record your tracks (in Mono) and do any cleanup/trimming necessary.  
Then you need to get the file out; go to File > Export. 
Use “Other uncompressed files” > Options, set “Headers” to WAV and Encoding to “U-law”.
Change the extension of filename to ulaw. 
Finally, I have found it necessary to boost the sound. You can do this by going to the "Effect" menu, then select "Amplify..." In my case, I set my "Amplification (db)" to 12-15. For some reason, that sounds a bit loud at my computer, but it sounds fine when it is uploaded to my PBX.
Upload du fichier dans asterisk
On peut aussi le convertir dans asterisk avec `file convert /full/path/before.ulaw /full/path/after.g729`

Add System recording
--------------------
permet d'ajouter n'importe quel system recording préalablement enregistré à la liste.

# Announcements module
la possibilité de diriger les appels en fonction d'horaire.
Il permet de créer une destination qui va jouer un message à un appelant. Apres le message, l'appel va vers une autre destination. Où est défini cette autre destination?  
Ce module est lié à tout module qui a un champ `Set Destination`:
- 1 IVR
- 2 Inbound Routes Module
- 3 Ring group module
- 4 Queues Module
- 5 Call Flow control module 
- 6 Time Conditions module
- 7 Miscellaneous applications module.

Announcement module va chercher les recording du module system recordiong.  

## Description
## Recording
C'est là que l'on choisit son message. Il est crée dans system recording
## Repeat
On peut choisir une touche que l'appelant appui pour rejouer le message.  
Il faudra mettre les instructions dans le msg : "To hear our hours again, press pound."  
## Allow skip
YES/No allow the caller to press any key to end the message. Et ensuite l'appel va vers la destination définie dans cet annoucement.  
## Return to IVR
YES/NO  
 If set to Yes, a caller who came from an IVR will be sent back to the IVR after the announcement, instead of being sent to the destination set below. This is handy if you have more than one IVR pointing to this announcement, because otherwise you would need to create a separate announcement for each IVR. (A single announcement can only route the caller to one defined destination.) If set to No, the caller will only be routed to the destination set below, and will not be sent back to the IVR they came from.
 ## Don't answer Channel
No Answer the call and play the message.  
YES joue le message comme early media. J'ai pas compris à quoi cela sert.  
Laisser No c'est plus sur.  
## Destination after playback

Comment réecouter les annonces?
-------------------------------

Admin / System recording  
Ce qui se passe à la fin d'une annonce se trouve dans Applications / Annoucment  

message conversion de format
----------------------------
http://wiki.kolmisoft.com/index.php/Convert_WAV_file_to_Asterisk_playable_format