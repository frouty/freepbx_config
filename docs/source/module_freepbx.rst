################################
Gestion des modules dans FreePBX
################################

Comment installer en ligne de commande un module
************************************************
 
.. code:: 

   fwconsole ma --edge upgrade framework  

Le plus dur va être pour trouver le nom du module.

Comment lister les modules
**************************
.. code:: 

   fwconsole ma list

Comment obtenir de l'aide sur les commandes de ma
*************************************************

.. code::
   
   fwconsole ma --help

Comment downloader un module?
*****************************
.. code::

   fwconsole ma download [nom_module] (endpoint par exemple)
