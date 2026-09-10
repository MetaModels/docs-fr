.. _rst_cookbook_specials_delete-superfluous-data:

Suppression des données superflues
===================================

.. note:: Effectuez impérativement une sauvegarde des données avant toute suppression ! - |br|
   par ex. avec ``php vendor/bin/contao-console contao:backup:create``

.. tip:: Depuis MetaModels 2.5, l'extension :ref:`metamodels/health-check <rst_extended_health-check>` prend en
   charge exactement cette tâche directement dans le backend - avec un aperçu avant la suppression, un journal de
   nettoyage et un bouton de sauvegarde sur la même page, sans aucun accès shell, et couvre toutes les tables
   listées ci-dessous. C'est la méthode recommandée et la plus confortable ; le script ci-après continue
   toutefois de fonctionner sans changement - ou utilisez les commandes séparées de l'extension.

Lorsque des modèles ou des attributs sont supprimés, il peut arriver que tous les enregistrements ne soient pas
supprimés en même temps. C'est le cas pour tous les attributs qui ne stockent pas leurs données directement dans la
table MetaModel ``mm_*``, mais utilisent leurs propres tables. C'est le cas pour les attributs suivants :

* Évaluation [tl_metamodel_rating]
* Tableau multi (MCW) [tl_metamodel_tablemulti]
* Tableau de texte [tl_metamodel_tabletext]
* Sélection multiple (tags) [tl_metamodel_tag_relation]
* Case à cocher traduite [tl_metamodel_translatedcheckbox]
* Fichier traduit [tl_metamodel_translatedlongblob]
* Texte long traduit [tl_metamodel_translatedlongtext]
* Tableau multi traduit (MCW) [tl_metamodel_translatedtablemulti]
* Tableau de texte traduit [tl_metamodel_translatedtabletext]
* Texte traduit [tl_metamodel_translatedtext]
* URL traduite [tl_metamodel_translatedurl]

Pour afficher et supprimer ces données, un :download:`script shell (check-mm-values.sh) est disponible en téléchargement </_download/check-mm-values.sh>`.

Le script doit être chargé dans le dossier de base de Contao et rendu exécutable. Pour cela, réglez les droits sur
``755`` - via un programme SFTP ou en console avec ``chmod 755 check-mm-values.sh``.

Avant utilisation, il convient de vérifier et, le cas échéant, d'adapter le chemin PHP dans la section configuration
du fichier.

.. code-block:: shell

   ...
   # Appel Doctrine (adapter l'appel PHP si nécessaire)
   DOCTRINE_BIN="php vendor/bin/contao-console doctrine:query:sql"
   ...

Le script peut être appelé en console avec les paramètres ``show`` ou ``delete`` - ``show`` affiche toutes les
données superflues par table et ``delete`` les supprime après confirmation. L'appel est alors

* ``./check-mm-values.sh show`` ou
* ``./check-mm-values.sh delete``

D'autres tables peuvent être ajoutées ou retirées de la configuration du script.

Le script est écrit de manière à fonctionner également avec un shell simple comme celui de BusyBox.

.. note:: La vérification et le nettoyage seront le cas échéant intégrés dans la migration de l'attribut concerné.

.. |br| raw:: html

   <br />
