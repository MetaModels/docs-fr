.. _manual_install:

Installation et mise à jour de MetaModels
=========================================

Informations générales sur l'installation
-----------------------------------------

MetaModels se compose de plusieurs modules qui doivent être installés en fonction des besoins.

Dans le Contao-Manager, sous « Pakete » (paquets), la saisie de `metamodels/ <https://extensions.contao.org/?q=metamodels>`_
affiche la liste de tous les paquets MetaModels disponibles. Le paquet de base
`metamodels/core <https://extensions.contao.org/?p=metamodels%2Fcore>`_ doit être installé - en complément, d'autres
`attributs et filtres <https://extensions.contao.org/?q=metamodels>`_ sont nécessaires selon les besoins. Pour bien
démarrer avec MetaModels, une :ref:`liste de contrôle <rst_cookbook_checklists_mm-start>` est disponible.

Outre les paquets individuels, il existe des « Bundles » qui regroupent différents paquets pour simplifier
l'installation.

Pour débuter avec MetaModels, le bundle `metamodels/bundle_start <https://extensions.contao.org/?p=metamodels%2Fbundle_start>`_
est recommandé - il installe le Core ainsi que les attributs et filtres les plus importants.

Il existe également le bundle `metamodels/bundle_all <https://extensions.contao.org/?p=metamodels%2Fbundle_all>`_,
qui installe, en plus du `bundle_start`, les paquets multilingues (remarque : les paquets `translatedselect` et
`translatedtags` n'y sont plus inclus depuis MM 2.1, car ils ne s'utilisent que dans des cas particuliers).

Ces deux bundles sont uniquement destinés à l'évaluation ou aux premiers essais - sur un système en production,
seuls le Core et les attributs et filtres nécessaires devraient être installés en tant que paquets séparés.

D'autres modules comme « filtre à onglets », « recherche par périmètre géographique », « évaluation », etc. doivent
être ajoutés en tant que paquets séparés - voir :ref:`extended_index`

.. seealso:: Si l'installation se fait via le Contao-Manager et que des paquets contenant encore un ancien paquet du
   MultiColumnWizard (MCW) sont déjà installés, le Manager (ou Composer) ne peut pas l'échanger et l'installer en
   même temps. Une « astuce » consiste à d'abord marquer tous les paquets d'extension existants pour une mise à
   jour, puis à ajouter et valider le ou les paquets MM ; une alternative consiste à lancer un `composer update` en
   console - voir le `'forum' <https://community.contao.org/de/showthread.php?72871-MCW-MultiColumnWizard-als-Bundle-f%C3%BCr-Contao-4-(stable)&p=502709&viewfull=1#post502709>`_.

Outre le Contao-Manager, l'installation des paquets et des bundles est également possible directement en console via
Composer - par exemple avec

``php public/contao-manager.phar.php composer require metamodels/core``

ou

``php public/contao-manager.phar.php composer require metamodels/bundle_start``

Au lieu de `php`, il convient éventuellement d'indiquer le chemin vers le binaire PHP correspondant - voir
:ref:`rst_cookbook_symfony_mm-2-1-tips`.

Après l'installation, via l'outil d'installation de Contao, **n'oubliez pas la mise à jour de la base de données !**

Voici, ci-après, des informations complémentaires sur les différentes versions de MetaModels.


Aperçu des versions
-------------------

* C 6.x + MM 3.0 + PHP 8.x - actuellement en planification...
* C 6.3 + MM 2.6 + PHP 8.4 - actuellement en cours de développement et de test avec Contao 6.0
* :ref:`C 5.7 + MM 2.5 + PHP 8.4 <install_mm250>` - actuellement en test avec Contao 5.7
* :ref:`C 5.3 + MM 2.4 + PHP 8.2 <install_mm240>` - accès via « EAP »
* :ref:`C 4.13 + MM 2.3 + PHP 8.1 <install_mm-old>`
* :ref:`C 4.9 + MM 2.2 + PHP 7.4 <install_mm-old>`
* :ref:`C 4.4 + MM 2.1 + PHP 7.2/7.4 <install_mm-old>`
* :ref:`C 3.5 + MM 2.0 + PHP 5.6 <install_mm-old>`


.. _install_mm250:
Installation de MM 2.5 pour Contao 5.7 et PHP 8.4
-------------------------------------------------

MetaModels 2.5 apporte une compatibilité totale avec Contao 5.7 et PHP 8.4. - MM 2.5 est une adaptation de la
version 2.4 à la nouvelle version de Contao et de PHP et apporte bien entendu :ref:`tous les changements et
fonctionnalités de MM 2.4 <new_in_mm240>`.

Les prérequis d'installation pour MetaModels 2.5 sont :

* un Contao 5.7.x (LTS) fonctionnel
* PHP 8.4 minimum
* au moins MySQL 5.7.6 ou MariaDB 10.4.3
* ``memory_limit`` de 512 Mo ou plus (recommandé)
* jusqu'à la publication, une clé d'accès via l'`EAP <https://now.metamodel.me/de/unterstuetzer/fundraising#metamodels_2-5>`_
* pour les projets plus modestes, le `paquet "Basic 1" est disponible <https://now.metamodel.me/de/unterstuetzer/fundraising#metamodels_2-5>`_

Des versions plus récentes de Contao et/ou de PHP peuvent fonctionner, mais ne sont pas officiellement supportées.

Lors d'une mise à niveau ou d'une nouvelle installation, il convient de prendre en compte les :ref:`changements et
nouvelles fonctions de MM 2.5 <new_in_mm250>`, ainsi que le fonctionnement du :ref:`gestionnaire de schéma
<component_schema-manager>` et des traductions XLIFF :ref:`component_translations`.

.. toctree::
    :maxdepth: 1

    new-in-mm-25.rst


.. seealso::
   Pendant la phase de développement, les paquets fournis via git reçoivent, à chaque modification, de nouveaux noms
   de fichiers. Ceux-ci sont également enregistrés dans le composer.lock. Il peut donc arriver que, lors d'un
   `composer install`, les paquets ne puissent pas être trouvés et qu'un message d'erreur apparaisse. |br|
   Dans ce cas, veuillez lancer un `composer update` pour mettre à jour le composer.lock. |br|
   |br|
   Dans les paquets, les dépendances des paquets ne sont pas renseignées vers la version DEV - cela peut signifier
   qu'il faut par exemple saisir soi-même `attribute_numeric` pour `attribute_timestamp` dans le composer.json.
   En cas de question, le support se tient à votre disposition.

   Si le message suivant apparaît lors de la mise à jour |br|
   ``The checksum verification of the file failed...`` |br|
   veuillez supprimer le fichier ``composer.lock`` et relancer la mise à jour.

   En cas de problème lors d'une mise à jour, il peut être utile de vider le cache de Composer avec
   ``composer clearcache``.

   Si un message apparaît |br|
   ``... Failed to connect to packages.cyberspectrum.de port 443: Connection refused...`` |br|
   ou |br|
   ``... The "https://token:XXX@packages.cyberspectrum.de/r/packages.json" file could not be downloaded (HTTP/2 404 )...`` |br|
   il est très probable que le serveur Packagist soit indisponible et que Composer ne puisse pas récupérer les
   paquets. Dans ce cas, veuillez réessayer la mise à jour quelques minutes plus tard ou contacter l'équipe MM.

   Si une mise à niveau a été effectuée, veuillez supprimer les données de session de l'utilisateur dans le backend
   afin d'éviter l'affichage de « pseudo-erreurs ». Pour le faire pour tous les utilisateurs, il suffit de mettre la
   colonne `session` de la table `tl_user` à `NULL`. Le message d'erreur ressemble généralement à ceci : |br|
   ``Cannot assign null to property ContaoCommunityAlliance\DcGeneral\Panel\DefaultLimitElement::$intAmount of type int``

Avant une mise en production, le site doit être testé de manière complète. MM 2.5 peut être installé via Composer
(console) ou via le Contao-Manager. L'accès au dépôt encore protégé actuellement s'obtient via notre
« **programme early adopter** » - plus d'informations à ce sujet dans la section Fundraising du
`site web de MM <https://now.metamodel.me/de/unterstuetzer/fundraising#metamodels_2-5>`_.

**Autres fonctionnalités de MM 2.5 :** |br|
Nous avons préparé une :ref:`page récapitulative des changements et fonctions de MM 2.5 <new_in_mm250>` - merci de
prendre en compte la :ref:`liste de contrôle <check_upgrade_mm240>` lors d'une mise à niveau.


.. _install_mm240:
Installation de MM 2.4 pour Contao 5.3 et PHP 8.2
-------------------------------------------------

MetaModels 2.4 apporte une compatibilité totale avec Contao 5.3 et PHP 8.2. - MM 2.4 est une adaptation de la
version 2.3 à la nouvelle version de Contao et de PHP et apporte bien entendu :ref:`tous les changements et
fonctionnalités de MM 2.3 <new_in_mm230>`.

Les prérequis d'installation pour MetaModels 2.4 sont :

* un Contao 5.3.x (LTS) fonctionnel
* PHP 8.2 minimum
* MySQL à partir de 5.5.5 (InnoDB), MariaDB (y compris « strict mode »)
* ``memory_limit`` de 512 Mo ou plus (recommandé)
* jusqu'à la publication, une clé d'accès via l'`EAP <https://now.metamodel.me/de/unterstuetzer/fundraising#metamodels_2-4>`_
  - le `MM Core <https://github.com/MetaModels/core/tree/release/2.4.0>`_ est déjà disponible librement
* pour les projets plus modestes, le `paquet "Basic 1" est disponible <https://now.metamodel.me/de/unterstuetzer/fundraising#metamodels_2-4>`_

Des versions plus récentes de Contao et/ou de PHP peuvent fonctionner, mais ne sont pas officiellement supportées.

Lors d'une mise à niveau ou d'une nouvelle installation, il convient de prendre en compte les :ref:`changements et
nouvelles fonctions de MM 2.4 <new_in_mm240>`, ainsi que le fonctionnement du :ref:`gestionnaire de schéma
<component_schema-manager>` et des traductions XLIFF :ref:`component_translations`.

.. toctree::
    :maxdepth: 1

    new-in-mm-24.rst

Avant une mise en production, le site doit être testé de manière complète. MM 2.4 peut être installé via Composer
(console) ou via le Contao-Manager. L'accès au dépôt encore protégé actuellement s'obtient via notre
« **programme early adopter** » - plus d'informations à ce sujet dans la section Fundraising du
`site web de MM <https://now.metamodel.me/de/unterstuetzer/fundraising#metamodels_2-4>`_.

**Autres fonctionnalités de MM 2.4 :** |br|
Nous avons préparé une :ref:`page récapitulative des changements et fonctions de MM 2.4 <new_in_mm240>` - merci de
prendre en compte la :ref:`liste de contrôle <check_upgrade_mm240>` lors d'une mise à niveau.


.. _install_mm-old:
Remarques et instructions pour les anciennes versions de Contao et de MM
------------------------------------------------------------------------

* :ref:`Page récapitulative des changements et fonctions de MM 2.3 <new_in_mm230>`
* :ref:`Page récapitulative des changements et fonctions de MM 2.2 <new_in_mm220>`
* :ref:`cookbook_move_mm2.0_to_2.1`
* :ref:`cookbook_install_mm2.0-and-older`


Passage de `metamodels/bundle_*` à des modules séparés
------------------------------------------------------

Lors d'un passage, par exemple, de la version 2.0 vers une version plus récente, ou lors d'une nouvelle installation,
c'est une bonne occasion de n'installer que les attributs et filtres réellement nécessaires au projet. Si
`metamodels/bundle_start` ou `metamodels/bundle_all` était utilisé auparavant, les commandes SQL suivantes
permettent de déterminer les attributs et filtres réellement utilisés :

.. code-block:: sql
   :linenos:

   -- Attribute
   SELECT type FROM `tl_metamodel_attribute` GROUP BY type ORDER BY type
   -- Attribut "levensthein" heißt seit MM 2.5 "levenshtein" (Migration benennt Bestände um)

   -- Filter
   SELECT type FROM `tl_metamodel_filtersetting` GROUP BY type ORDER BY type
   -- Filterregeln "conditionand, conditionor, customsql, idlist, simplelookup" sind im MM-Core enthalten
   -- Filterregel "checkbox_published" im Attribut Checkbox

La liste qui en résulte peut ensuite être installée via le Contao Manager ou la console, et les modules non
utilisés sont ainsi laissés de côté.


Tester des paquets spécifiques
------------------------------

Outre les paquets actuellement disponibles et publiés de MetaModels, il existe parfois des paquets avec des
corrections de bugs ou de nouvelles fonctions qui peuvent/doivent être testés - cela pourrait être, par exemple pour
le MetaModels-core, un paquet ``hotfix/2.1.25``. Ces paquets sont visibles entre autres sur Github, dans le dépôt
correspondant (par exemple MetaModels/core), sous l'onglet `'branches' <https://github.com/MetaModels/core/branches>`_.
La désignation indiquée à cet endroit, comme ``hotfix/2.1.25``, doit être complétée par le préfixe ``dev-``, ainsi
que par un ``as 2.1.25`` à la fin.

Un aperçu des indications dans le composer.json se trouve `ici <https://devhints.io/composer>`_.

Pour tester un tel paquet, il doit être indiqué explicitement dans le Contao-Manager avec

``dev-hotfix/2.1.25 as 2.1.25``

ou dans le composer.json

``"metamodels/core": "dev-hotfix/2.1.25 as 2.1.25"``

avec sa version.

Ensuite, effectuez une mise à jour via le Contao-Manager ou en console.

Comme MetaModels est étroitement lié au DC_General (DCG), il faut souvent, pour les tests, également mettre à jour
celui-ci vers une version plus récente. La procédure est la même que pour MetaModels, y compris l'adaptation de
l'entrée JSON avec « as 2.1.x ».

Pour la mise en œuvre des paquets du Core et du DCG, le composer.json devrait à peu près comporter les entrées
suivantes dans le nœud « require » (lignes 8 et 10) :

.. code-block:: json
   :linenos:

   {
       "name": "local/website",
       "description": "A local website project",
       "type": "project",
       "license": "proprietary",
       "require": {
           "contao-community-alliance/composer-client": "~0.12",
           "contao-community-alliance/dc-general": "dev-hotfix/2.1.42 as 2.1.42",
           "metamodels/bundle_all": "^2.1",
           "metamodels/core": "dev-hotfix/2.1.25 as 2.1.25",
           ...
       },
       ...
   }

Pour revenir à l'état initial, remettez les paquets à leur déclaration d'origine, par exemple « ^2.1 », puis
effectuez une mise à jour, y compris de la base de données.

Après un test, il est important de faire un retour au développeur ou à l'équipe MetaModels via
`Github <https://github.com/MetaModels>`_.

Deux autres possibilités sont l'installation d'un fork ou d'une pull request (PR). Dans ce cas, le composer.json
doit être adapté pour l'installation.

Pour un fork (le cas échéant, renseignez votre propre jeton oAuth Github dans les réglages du gestionnaire de
paquets), par exemple :

.. code-block:: json
   :linenos:

   {
       "name": "local/website",
       "description": "A local website project",
       "type": "project",
       "license": "proprietary",
       "require": {
           "contao-community-alliance/composer-client": "~0.12",
           "contao-community-alliance/dc-general": "^2.1",
           "metamodels/bundle_all": "^2.1",
           "byteworks/metamodelsattribute_multi": ">=1.0.5.0,<1.1-dev",
           ...
       },
       ...
       "repositories": [
           ...
           {
               "type": "vcs",
               "url": "https://github.com/byteworks-ch/contao-metamodelsattribute_multi.git"
           },
           {
               "type": "git",
               "url": "git@gitlab.com:MetaModels/filter_parent.git"
           }
       ],
       ...
   }

ou, pour une PR, avec le hash du commit - celui-ci se trouve sur Github, sur la PR, sous l'onglet « Commits ».

.. code-block:: json
   :linenos:

   {
       "name": "local/website",
       "description": "A local website project",
       "type": "project",
       "license": "proprietary",
       "require": {
           "contao-community-alliance/composer-client": "~0.12",
           "contao-community-alliance/dc-general": "^2.1",
           "metamodels/bundle_all": "^2.1",
           "metamodels/attribute_alias": "dev-master#a97ec461ae1254fa616811c3ce234515238fb3c7 as 2.1.42",
           ...


.. |br| raw:: html

   <br />
