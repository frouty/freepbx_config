Polycom phone config
=====================

Création d'un template
----------------------

Phone Polycom 
http://kb.digium.com/articles/Configuration/Polycom-Phone-Provisioning-Guide?retURL=%2Fapex%2FknowledgeProduct&popup=false

Achat du module EPM pro

Add brand /  polycom

**destination**:
    ipfreepbx

**provisionning server protocol**
    HTTP

...

SAVE  / apparotion du choix du modele / config des touches du téléphone /
Save and rebuild config.

Maintenant on va créer l'extension qui va utiliser ce nouveu template. 

Application / Extension / Add new chan-pjsip extension .

Qd on crée une extension on la lie à un user mais pas un téléphone.

extension management / extension mapping / add extension

il nous faut la mac address du polycom phone on la trouve sur le
téléphone Home/settings/status/platform/phone

ensuite webGUI admin / password dans le MLP gestionnaire de mot de passe
/ et je n'arrive plus à me connecter en webGUI au polycom qui a été provisionné. j'essaie avec
le password de MLP et aussi le password de freepbx (endpoint management
/ Global setting ) mais cela ne marche pas. par contre le password de
freepbx (endpoint management / Global setting ) marche sur le téléphone.

