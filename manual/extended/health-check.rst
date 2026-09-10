.. _rst_extended_health-check:

MetaModels Health-Check
========================

Trouve et nettoie les données MetaModels incohérentes dans le backend - par ex. des lignes qui
subsistent dans les tables de stockage propres aux attributs (tags, texte multiple/tableau de
texte, évaluations et leurs variantes traduites) après la suppression d'un attribut ou d'un
enregistrement, parce que le DCG ne voit jamais ces tables supplémentaires - un risque qui
augmente avec la complexité des structures de données et tant qu'il n'est pas entièrement
empêché par des clés étrangères (Foreign-Keys) au niveau de la base de données.


Prérequis
---------

* à partir de MetaModels 2.5
* paquet Composer autonome, sans dépendance à d'autres extensions à part MetaModels lui-même


Installation via Contao-Manager ou Composer
---------------------------------------------

.. code-block:: bash

   composer require metamodels/health-check


Accès
-----

Après l'installation, une entrée de menu supplémentaire « Health-Check » apparaît dans la liste
« Tous les MetaModels », en haut à droite, à côté de « Modifier plusieurs ».

|img_health-button|

Comme l'ensemble de l'administration MetaModels, le **Health-Check n'est accessible qu'aux
administrateurs** - appliqué côté serveur, pas seulement masqué dans la navigation.


La vue
------

|img_health-overview|

**Vérifications :** chaque vérification apparaît sur sa propre ligne avec une case à cocher à
gauche, un nom et une description, le résultat de la dernière exécution, l'horodatage de la
dernière vérification ainsi que ses propres boutons. La case à cocher en haut à gauche de l'en-tête
permet de sélectionner ou désélectionner toutes les vérifications d'un coup ; « Effectuer les
vérifications » n'exécute alors que les vérifications cochées. Une vérification individuelle peut
être déclenchée à tout moment indépendamment via son propre bouton « Vérifier ». La page
n'exécute aucune vérification automatiquement à l'ouverture - le résultat affiché est celui de la
dernière exécution réelle (ou « Pas encore vérifié »).

.. warning:: Au-dessus de la liste des vérifications figure un avertissement invitant à examiner
   attentivement les données avant un nettoyage et à créer au préalable une sauvegarde - un
   nettoyage ne peut pas être annulé.

Les vérifications suivantes sont fournies :

* Lignes orphelines dans les tables de stockage propres aux attributs (valeurs de tableau
  multiple, tableau de texte, associations de tags, évaluations, valeurs d'URL traduites ainsi que
  leurs variantes traduites) - des lignes dont l'attribut ou l'enregistrement associé n'existe
  plus.
* Références parent-enfant cassées : des enregistrements enfants (:ref:`tables enfants
  <component_relations_child-tables>`) dont l'enregistrement parent n'existe plus.
* Références de fichiers orphelines : des attributs de type « Fichier » ou « Fichier traduit »
  dont la référence enregistrée ne pointe plus vers aucun fichier dans le gestionnaire de fichiers
  - comparable à un fichier supprimé directement dans le système de fichiers plutôt que via
  Contao. Cette vérification est **purement informative et n'est pas corrigée
  automatiquement**, car une correction devrait réécrire de façon ciblée une valeur (parfois
  sérialisée) au lieu de simplement supprimer une ligne - le risque d'un nettoyage automatique est
  ici trop élevé.

**Détails :** chaque vérification qui contrôle effectivement des enregistrements (aussi bien la
vérification purement informative des références de fichiers que les vérifications réparables)
dispose de son propre bouton « Détails », qui affiche les enregistrements concernés dans un
tableau - avant même de se décider pour un nettoyage. Pour les vérifications génériques « Lignes
orphelines » (ainsi que la vérification parent-enfant), toutes les colonnes de la table concernée
sont déterminées automatiquement à partir du schéma de la base de données - cela fonctionne de
façon générique pour toute table rattachée, y compris pour des vérifications personnalisées ; les
valeurs longues ou binaires (par ex. une colonne blob) sont alors résumées sous la forme « (N
octets) » plutôt qu'affichées brutes. Pour la vérification des références de fichiers, le tableau
est en revanche organisé sur mesure : table MM, attribut, ID de l'enregistrement, langue (pour
« Fichier traduit ») et nombre (combien de références orphelines cet enregistrement apporte - la
colonne s'additionne pour former le nombre total de la vérification, même si un enregistrement
avec plusieurs fichiers orphelins dans un champ multiple génère moins de lignes de tableau que de
résultats au total). Le tableau dans la popup affiche au maximum 50 lignes avec une mention
« ... et N de plus » au-delà ; sur la console (voir plus bas), cette limitation n'existe pas.

Pour les vérifications réparables, deux boutons supplémentaires sont disponibles : **« Aperçu de
la correction »** affiche combien de lignes seraient supprimées, sans rien effacer ; **« Exécuter
la correction »** supprime effectivement. Les deux ouvrent pour cela une popup avec son propre
bouton « Démarrer » - cette étape intermédiaire délibérée remplace une confirmation « Vraiment ? »
supplémentaire - et y affichent, en plus du nombre, le même tableau de détails que ci-dessus ; pour
« Exécuter la correction », il est capturé avant la suppression, afin qu'il continue d'afficher
ensuite ce qui a été supprimé. « Aperçu de la correction »/« Exécuter la correction »/« Détails »
ne sont utilisables qu'après que « Vérifier » a effectivement trouvé quelque chose - avant cela,
ils sont grisés. Chaque nettoyage réellement exécuté est consigné dans le journal de nettoyage en
bas de la page (date, vérification, nombre de lignes supprimées, utilisateur exécutant).

Une vérification n'apparaît dans la liste que si elle peut effectivement s'appliquer à
l'installation actuelle - par ex. la vérification des valeurs de tableau multiple n'apparaît que
si ``metamodels/attribute_tablemulti`` est installé. Ainsi, on n'affiche jamais à tort « aucun
problème trouvé » pour une table qui n'existe même pas dans sa propre installation.

Sous la description de chaque vérification figure également son identifiant console (voir plus
bas, :ref:`rst_extended_health-check_console`) - la valeur transmise à
``metamodels:health:run``.

**Sauvegarde :** via « Créer une sauvegarde maintenant », une sauvegarde de la base de données peut
être déclenchée directement depuis la page - via le même mécanisme que Contao utilise lui-même
(Système > Maintenance > Sauvegarde). La restauration d'une sauvegarde ne se fait délibérément
**pas** sur cette page, mais comme d'habitude via le Contao-Manager ou la commande de console
``contao:backup:restore``.


.. _rst_extended_health-check_console:

Commandes de console
----------------------

Chaque vérification peut également être appelée via la console - par ex. pour des tâches cron ou
la CI.

.. code-block:: bash

   # Lister les vérifications disponibles avec leur id, dernière date de vérification et dernier résultat
   php bin/console metamodels:health:list

   # Exécuter une vérification individuelle via son id (id visible avec "metamodels:health:list")
   php bin/console metamodels:health:run orphaned_tag_relation

   # Exécuter toutes les vérifications d'un coup
   php bin/console metamodels:health:run --all

   # Lister en plus chaque enregistrement concerné - contrairement à la popup du backend, sans
   # aucune limitation, ce qui permet par ex. de rediriger vers un fichier
   php bin/console metamodels:health:run orphaned_tag_relation --details

   # Aperçu : combien de lignes un nettoyage supprimerait-il ?
   php bin/console metamodels:health:repair orphaned_tag_relation --dry-run

   # Nettoyer réellement
   php bin/console metamodels:health:repair orphaned_tag_relation --force

``metamodels:health:list`` et ``metamodels:health:run`` sont purement en lecture - mais chaque
exécution est consignée dans le champ « Dernière vérification » exactement comme une exécution
depuis le backend. ``metamodels:health:run`` se termine avec un code de sortie différent de 0 dès
qu'au moins une vérification a trouvé des problèmes - la commande peut ainsi être directement
intégrée comme contrôle de monitoring.

``metamodels:health:repair`` est l'équivalent console de « Nettoyage en aperçu (dry-run) » et
« Nettoyer maintenant » : exactement l'une des deux options ``--dry-run``/``--force`` est
obligatoire, il n'y a délibérément pas de cas par défaut silencieux. Un nettoyage réellement
exécuté via ``--force`` est consigné dans le même journal de nettoyage qu'un nettoyage exécuté via
le backend - comme utilisateur y figure « - », car il n'y a ici (par ex. lors d'une tâche cron)
aucun utilisateur backend connecté. Il n'existe délibérément pas d'option ``--all`` ici : un
nettoyage doit toujours être une décision consciente par vérification.


Implémenter ses propres vérifications
----------------------------------------

Les vérifications sont conçues de façon modulaire - des vérifications personnalisées peuvent être
ajoutées sans modifier ce paquet lui-même. **Il n'existe pas d'EventListener pour cela**, mais un
service Symfony classique, enregistré via un tag DI - exactement comme fonctionnent par ex. les
propres migrations de Contao (``Contao\CoreBundle\Migration\MigrationInterface``).

Une vérification implémente
``MetaModels\HealthCheckBundle\HealthCheck\HealthCheckInterface`` :

.. code-block:: php

   interface HealthCheckInterface
   {
       public function getId(): string;
       public function getLabel(): string;
       public function getDescription(): string;
       public function check(): HealthCheckResult;
   }

``check()`` est toujours purement en lecture et retourne un ``HealthCheckResult`` avec une liste
de ``HealthCheckIssue`` (chacun avec une description + le nombre de lignes concernées). Si la
vérification doit aussi pouvoir se réparer elle-même, implémenter en plus
``MetaModels\HealthCheckBundle\HealthCheck\RepairableHealthCheckInterface`` :

.. code-block:: php

   interface RepairableHealthCheckInterface extends HealthCheckInterface
   {
       public function repair(bool $dryRun): HealthCheckRepairResult;
   }

``repair()`` détermine les lignes concernées à nouveau à chaque appel (pas à partir d'une liste
éventuellement obsolète d'un ``check()`` précédent) et ne les supprime que si ``$dryRun`` vaut
false.

La vérification personnalisée est enregistrée dans son propre ``services.yml`` avec le tag
``metamodels_health_check.check`` :

.. code-block:: yaml

   services:
     App\HealthCheck\MyCustomCheck:
       arguments:
         - '@database_connection'
         - '@translator'
       tags: ['metamodels_health_check.check']

La vérification personnalisée apparaît ainsi automatiquement dans la liste sur la page
Health-Check - sans aucune modification de ``metamodels/health-check`` lui-même.

L'ordre dans la liste (backend comme ``metamodels:health:list``) suit la ``priority`` du tag - une
fonctionnalité Symfony-DI classique, pas un développement propre à ce paquet. Une priorité plus
élevée se place plus haut, la valeur par défaut est 0, et en cas de priorité égale, c'est l'ordre
d'enregistrement qui décide. Les vérifications fournies utilisent des valeurs de 100 à 10 (par pas
de dix, de « Valeurs de tableau multiple orphelines » à « Références de fichiers orphelines ») ;
une vérification personnalisée sans indication se retrouve donc automatiquement après :

.. code-block:: yaml

   services:
     App\HealthCheck\MyCustomCheck:
       arguments:
         - '@database_connection'
         - '@translator'
       tags:
         - { name: 'metamodels_health_check.check', priority: 50 }


.. |img_health-button| image:: /_img/screenshots/extended/health-check/health-button.png
.. |img_health-overview| image:: /_img/screenshots/extended/health-check/health-overview.png
