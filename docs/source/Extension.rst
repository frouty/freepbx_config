Extension
=========

Application / Extension 

Une extension est un objet lié à un user. 

Comment Creer une extension
---------------------------

- 1 On crée l'extension : Application / Extension / Add Extension / Add New CHAN_SIP Extension

- 2 On la link à un user : Link to a default user : Create New User. Le systeme crée automatiquement un nouveau user que l'on peut configurer dans Admin / User Management.  


Add Extension:

- Custom extension

- DAHDI Extension

- IAX2 Extension

- SIP chan_pjsip Extension

- SIP chan_sip extension

Laquelle choisir je ne comprends pas tres bien.

J'ai essayé avec chan_sip et chan_pjsip les deux marchent.

Pour les orthoptistes: 

**user extension** 

    5

**Display name**

    Orthoptistes

**Outbound CID**

    je laisse vide

**Emergency CID**

    je laisse vide

**Secret**

    j'ai un password donné par le freepbx

User manager settings

    **Link to a default user**

        je choisi orthop

    **Password for new user**

        donné par le systeme qui n'est pas celui que j'avais donné lors
de la creation du user orthop.

## Une fois que l'on a crée l'extension on va la linker à un poste IP phone.
- Settings / EndPoint Manager / Extension Mapping
- Add Mapping  Extension  
- On choisit l'extension  qui elle même est mappée à un user.
    - un account (cet account est quelque chose dans le téléphone)
    - Une marque de téléphone 
    - MAC adress d'IP Phone.  
    - Template qui est un fichier de configuration qui correspond à un type de téléphone.
    - Modele de téléphone.
    

# Comment savoir à quelle IP phone un user est relié?
Application / Extension  
Et on peut relier un user à une extension dans:  
Admin - User Management - Primary Linked Extension

Apres je vais lier cette extension à un téléphone : Endpoint /

On va d'abord créer un template pour les orthoptistes.

Brand  /  sangoma / New template / Save

Extension mapping / select un account je prends toujours Account 1 / on
renseigne la mac address / on selecte le bon template / on selecte le model

ensuite il faut que l'ipphone se provisionne

OKpour le fichier sur le sujet rajouter saveset et reboot


Ou trouver le Webgui admin password de l'ipphone une fois qu'il s'est
autoprovisionné : endpoint / general setting / phone admin password.


