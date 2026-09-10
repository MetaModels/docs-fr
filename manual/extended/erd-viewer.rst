.. _rst_extended_erd-viewer:

MetaModels ERD
===============

Affiche un `schéma entité-association
<https://fr.wikipedia.org/wiki/Mod%C3%A8le_entit%C3%A9-association>`_ généré automatiquement de
toutes les tables MetaModels et de leurs relations dans le backend - en complément du schéma tenu
à jour manuellement recommandé sous :ref:`Structure de la base de données
<component_relations_database_structure>`. Le schéma est régénéré à chaque appel à partir de la
base de données et est donc toujours à jour.

Cette vue est utile pour se faire rapidement une idée des tables existantes et de leurs relations,
par exemple sur un projet repris ou sur un projet sur lequel on n'a pas travaillé depuis longtemps.

Il faut noter que toutes les tables de la base de données ne sont pas représentées ici - par
exemple, pour l'attribut Tags (sélection multiple), il existe une table de relation entre les deux
tables liées - celle-ci n'apparaît pas dans le schéma.


Prérequis
---------

* à partir de MetaModels 2.5
* paquet Composer autonome, sans dépendance à d'autres extensions à part MetaModels lui-même


Installation via Contao-Manager ou Composer
---------------------------------------------

.. code-block:: bash

   composer require metamodels/erd-viewer


Accès
-----

Après l'installation, une entrée de menu supplémentaire « Vue ERD » apparaît dans la liste « Tous
les MetaModels », en haut à droite, à côté de « Nouveau MetaModel » et « Modifier plusieurs ».

|img_erd-button|

La flèche « Retour » sur la page ERD ramène exactement à cette liste.

Comme l'ensemble de l'administration MetaModels, la **vue ERD n'est accessible qu'aux
administrateurs**.


La vue
------

|img_erd-overview|

**Schéma :** chaque table MetaModels apparaît comme un rectangle bleu, chaque table Contao
référencée (par ex. ``tl_member`` ou ``tl_page``) qui n'est pas elle-même un MetaModel comme un
rectangle gris en pointillés avec la mention « externe ». Un clic sur un rectangle ouvre à droite
un panneau de détail avec le nom du MetaModel, la relation parent/enfant et la liste complète des
attributs ; simultanément, tous les rectangles non directement liés s'estompent. Échap ou un clic à
côté annule cela.

Deux types de relations sont représentés :

* **Relations d'attributs** via les attributs Sélection, Tags ainsi que leurs variantes traduites
  (sélection simple, sélection multiple) - étiquetées avec le nom de l'attribut et la cardinalité
  entre crochets : ``[1:n]`` pour sélection simple/sélection simple traduite (Select), ``[m:n]``
  pour sélection multiple/sélection multiple traduite (Tags).
* **Relations parent-enfant** (:ref:`tables enfants <component_relations_child-tables>`) comme
  flèche orange en pointillés avec la mention « Enfant de [n:1] ».

**Filtre :** le champ de recherche ou la liste de cases à cocher à gauche permettent de limiter le
schéma à un sous-ensemble des tables - les deux agissent ensemble et en direct sur le schéma. Les
boutons « Tout »/« Aucun » cochent ou décochent toutes les cases d'un coup.

**Vues :** une sélection de tables actuellement définie peut être enregistrée sous un nom au choix.
Les vues enregistrées sont visibles et utilisables **par tous les administrateurs du backend**,
applicables en cliquant sur leur nom, et peuvent être à nouveau supprimées via le « × » à côté par
**l'utilisateur qui les a créées**.

**Pan/Zoom :** la molette de la souris permet de zoomer, un déplacement dans une zone vide avec le
bouton de la souris enfoncé permet de déplacer la vue (le curseur devient une main) ; des boutons de
zoom et une icône « Tout réinitialiser » sont également disponibles. La carte de vue d'ensemble en
haut à droite affiche le graphe complet ainsi qu'un cadre pour la zone actuellement visible.

**Export :** la zone actuellement visible du schéma peut être téléchargée en SVG ou PNG, la
sélection de tables actuellement filtrée en plus sous forme de fichier Graphviz ``.dot`` ou GraphML.

.. tip:: Le fichier GraphML peut être ouvert gratuitement et sans installation dans `yEd Live
   <https://www.yworks.com/yed-live/>`_ et y être librement retravaillé - par exemple pour une mise
   en page ajustée proprement à la main ou une documentation en dehors du backend.


.. |img_erd-button| image:: /_img/screenshots/extended/erd-viewer/erd-button.png
.. |img_erd-overview| image:: /_img/screenshots/extended/erd-viewer/erd-overview.png
