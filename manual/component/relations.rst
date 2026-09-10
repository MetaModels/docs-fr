.. _component_relations:

Relations dans MetaModels
===========================

L'une des tâches principales avec MM est de créer une structure de données adaptée grâce à une
construction appropriée des Models. Cela implique de répartir les valeurs de même nature dans des
Models séparés et d'établir une relation (liaison) entre ces Models. On parle généralement dans ce
cas de `normalisation <https://de.wikipedia.org/wiki/Normalisierung_(Datenbank)>`_ pour les bases
de données relationnelles.

Par exemple, on n'enregistrerait pas le nom d'un département dans l'enregistrement d'un
collaborateur. On crée à la place une table propre « Département » et on n'enregistre dans la
table « Collaborateur » que la relation - c'est-à-dire l'ID de l'enregistrement correspondant dans
« Département ».

Ainsi, on peut par ex. modifier le nom du département (de « Publicité » à « Marketing ») à un seul
endroit sans devoir modifier tous les enregistrements des collaborateurs.

MetaModels propose pour de telles liaisons des attributs et réglages prêts à l'emploi. Ceux-ci
sont présentés ci-après avec leurs possibilités d'utilisation et leurs particularités.

Pour toutes les variantes, il est recommandé de consulter les données dans la base de données avec
un outil adapté comme phpMyAdmin ou similaire.


.. _component_relations_database_structure:
Structure de base de données
-----------------------------

L'ensemble des MetaModels et leurs liaisons forment une structure de base de données permettant de
stocker, d'afficher et de filtrer les données de la manière souhaitée. En particulier pour les
tâches plus complexes, une bonne planification peut éviter des modifications ultérieures.

Il est recommandé de représenter graphiquement la structure des MetaModels et leurs liaisons. Cela
aide aussi bien à la création qu'à la documentation.

Dans le cas le plus simple, on peut esquisser le schéma avec papier et crayon - mais il existe
aussi divers outils comme `yEd <https://www.yworks.com/products/yed>`_ ou la variante en ligne
`yEd live <https://www.yworks.com/yed-live/>`_.

Exemple de structure pour des collaborateurs, avec liaisons vers un département et des projets
ainsi qu'une auto-référence pour un remplacement de congés :

|img_db-schema_01|

.. note:: **À partir de MM 2.5 :** L'extension à installer séparément :ref:`metamodels/erd-viewer
   <rst_extended_erd-viewer>` génère automatiquement un tel schéma à partir de la base de données -
   directement dans le backend, avec filtre, détails au clic et export en SVG/PNG/Graphviz/GraphML.
   Cela ne remplace pas dans tous les cas la maintenance manuelle (par ex. pour un schéma de
   documentation soigné), mais l'épargne pour l'aperçu quotidien.


.. _component_relations_standard-relations:
Relations standard
-------------------

Les relations standard d'une base de données relationnelle comprennent les liaisons simples et
multiples. MM propose pour cela des attributs correspondants, que l'on intègre dans son Model de
base.

On peut ainsi créer des liaisons aussi bien vers des tables MM que vers toutes les autres tables de
Contao, comme par ex. les membres (tl_member) ou les pages (tl_pages). Pour les liaisons vers un
Model MM, celles-ci peuvent également être multilingues - MM se charge des traductions
correspondantes.

Dans la sortie en FE, on a accès, dans le nœud ``raw``, à tous les attributs/valeurs de
l'enregistrement lié de la table de relation - si celle-ci est une table MM comportant d'autres
relations, on a également accès en profondeur à ces données. Cette structure peut être bien
analysée en mode debug via une :ref:`sortie de dump <rst_cookbook_debug_templates>`.

Par exemple : si un collaborateur a une relation vers un département et que le département a à son
tour une relation vers le responsable de département (Model « Collaborateur »), on peut, dans une
liste de tous les collaborateurs, afficher en plus du nom du département également le nom et la
photo du responsable de département - sans qu'aucune requête supplémentaire ne soit nécessaire.
Dans le template, la sortie pourrait par ex. ressembler à ceci :

``<p><strong>Abteilungsleiter:</strong> <?= $item['raw']['division']['__SELECT_RAW__']['manager']['__SELECT_RAW__']['name'] ?></p>``

Pour reprendre facilement le « chemin du tableau », on peut générer une sortie via l'
":ref:`rst_cookbook_frontend_array-helper`".

Dans le backend, les sélections des deux relations standard, simple ou multiple, peuvent être
restreintes via des filtres - par exemple si, dans le masque de saisie d'un collaborateur, on
souhaite sélectionner le remplaçant de congés et que tous les collaborateurs sont listés dans la
liste. On pourrait limiter la sélection aux collaborateurs de son propre département et s'exclure
soi-même. Un autre exemple concerne les relations dépendantes, comme un Select sur le pays et un
autre sur la région, où seuls les enregistrements correspondants doivent alors être affichés pour
les régions.

La règle de filtre « SQL personnalisé » convient très bien pour ce type de restrictions - le
manuel contient d'autres conseils :

   - :ref:`rst_cookbook_inputmask_manipulate-select-values`
   - :ref:`rst_cookbook_filter_custom-sql`


.. _component_relations_standard-relation-1ton:
Sélection simple [Select] - « 1:n »
.....................................

Si l'on souhaite établir une relation vers une valeur, comme par ex. un collaborateur vers un
département, on ajoute au Model « Collaborateur » l'attribut « Sélection simple [Select] » et on
choisit les réglages adaptés pour l'attribut.

Dans le masque de saisie du BE, l'attribut génère par défaut une sélection de type Select - il
existe cependant d'autres options dans les réglages de l'attribut au niveau du masque de saisie.

.. note:: L'attribut « Sélection simple [Select] » peut également fonctionner automatiquement avec
   des MetaModels multilingues.

Il existe également l'attribut « Sélection simple traduite [Select] » - celui-ci est principalement
destiné à la connexion de tables qui n'appartiennent pas à MetaModels et possèdent leur propre
champ pour la variante linguistique - ou pour le cas particulier où, dans le MetaModel référencé,
des Items différents doivent être sélectionnés selon la langue.

Pour un filtrage en frontend, les règles de filtre :ref:`component_filter_select` ou
:ref:`component_filter_simplelookup` peuvent être utilisées.

.. seealso:: La documentation complète de l'attribut se trouve sous :ref:`component_attribute_select`
   ou :ref:`component_attribute_translatedselect`.


.. _component_relations_standard-relation-mton:
Sélection multiple [Tags] - « m:n »
.....................................

Si l'on souhaite établir une relation vers plusieurs valeurs, comme par ex. un collaborateur vers
plusieurs langues, on ajoute au Model « Collaborateur » l'attribut « Sélection multiple [Tags] » et
on choisit les réglages adaptés pour l'attribut.

Dans le masque de saisie du BE, l'attribut génère par défaut une liste de cases à cocher - il
existe cependant d'autres options dans les réglages de l'attribut au niveau du masque de saisie.

Pour cet attribut, il faut noter que, comme pour les relations m:n, les valeurs sont enregistrées
dans une table de relation séparée ``tl_metamodels_tag_relations``. Pour des requêtes SQL
personnalisées, cette table doit être incluse.

.. note:: L'attribut « Sélection multiple [Tags] » peut également fonctionner automatiquement avec
   des MetaModels multilingues.

Il existe également l'attribut « Sélection multiple traduite [Tags] » - celui-ci est principalement
destiné à la connexion de tables qui n'appartiennent pas à MetaModels et possèdent leur propre
champ pour la variante linguistique - ou pour le cas particulier où, dans le MetaModel référencé,
des Items différents doivent être sélectionnés selon la langue.

Pour un filtrage en frontend, la règle de filtre :ref:`component_filter_tags` peut être utilisée.

.. seealso:: La documentation complète de l'attribut se trouve sous :ref:`component_attribute_tags`
   ou :ref:`component_attribute_translatedtags`. |br|
   Indications pour nettoyer les données de relation superflues :
   :ref:`rst_cookbook_specials_delete-superfluous-data`


Relations spéciales dans MetaModels
--------------------------------------

Outre les relations standard, il existe d'autres implémentations dans MetaModels, mises en place
en réponse aux souhaits des utilisateurs.


.. _component_relations_child-tables:
Tables enfants - « n:1 »
..........................

La relation des « tables enfants » suit la construction classique comme dans Contao, par ex. pour
les News ou les Events. Il existe une table (enfant) qui est rattachée dans sa hiérarchie à une
table supérieure (parent). Comme exemple pour les collaborateurs, on pourrait créer les
déplacements professionnels comme table enfant. Les données sont généralement toujours associées à
un collaborateur et sont gérées comme une liste de saisie autonome.

La relation s'établit via les colonnes système ``pid`` et ``id``, la ``pid`` des enregistrements
enfants contenant l'``id`` de l'enregistrement parent.

La liaison se configure via les réglages du masque de saisie en choisissant pour « Intégration :
comme table enfant » et « Nom de la table parente » la table correspondante - d'autres tables
Contao peuvent également être choisies comme table parente.

De plus, sauf cas particuliers, il faut choisir « Mode de rendu : élément parent présent » - cela
génère la relation souhaitée de ``pid`` vers ``id`` lors de la création de l'enregistrement enfant.

L'accès à la liste des enregistrements enfants se fait via une icône dans la ligne des
enregistrements parents, parmi les icônes d'édition - il est possible en option de choisir une
icône personnalisée.

Depuis MM 2.5, l'en-tête du backend affiche le chemin d'accès - depuis le Model de base, en
passant par tous les niveaux intermédiaires, jusqu'au niveau actuel. Chaque maillon est lié, ce qui
permet de passer d'un niveau à l'autre sans détour par la liste complète. En cas d'imbrication
profonde, le milieu est réduit en « … » et peut être déplié.

La manière dont les différents enregistrements du chemin sont désignés se définit pour chaque
masque de saisie sous « Compléments au titre du masque » - le même champ qui complète également le
titre du masque d'édition. Il accepte des Simple Tokens à partir des attributs de l'enregistrement,
par ex. ``##model_name##`` ou ``##model_name##, ##model_firstname##`` ; ``##model_id##`` affiche
l'ID. Sans indication, seul le nom du MetaModel apparaît à cet endroit.

Lorsqu'on travaille avec des tables enfants, il faut noter que « les parents ne savent pas qu'ils
ont des enfants », c'est-à-dire qu'il n'y a pas de sortie automatique des données enfants en FE. On
peut toutefois filtrer les enregistrements enfants d'un enregistrement parent à partir de la
« relation id-pid » - par ex. avec la règle de filtre « SQL personnalisé ».

Pour le filtrage des enregistrements enfants, il existe en outre une règle de filtre spéciale :
le ":ref:`filtre parent <rst_extended_filter_by_related>`". Elle permet de filtrer tous les
enregistrements enfants selon les propriétés des données parentes - par ex. « Filtrer tous les
déplacements professionnels selon les collaborateurs du département Ventes ».

Lorsqu'un enregistrement parent est supprimé, les enregistrements enfants ne sont pas
automatiquement supprimés avec lui. Si l'on souhaite ce comportement, il peut être configuré -
voir :ref:`rst_cookbook_tips_delete_child_items`.

De même, il n'y a pas d'automatisme pour que les enregistrements enfants soient copiés lorsque les
enregistrements parents sont copiés. Si l'on souhaite ce comportement, on peut par ex. l'obtenir
avec le PostDuplicateModelEvent du DC_G - voir
`« MM DeepCopy Feature » <https://github.com/w3scout/mm-deepcopy-eventlistener>`_.

En cas d'utilisation de variantes ou d'une structure hiérarchique/arborescente dans les tables
enfants, veuillez vérifier l'état actuel (voir ci-dessous).


.. _component_relations_variants:
Variantes
..........

La construction avec des variantes doit être utilisée lorsqu'un enregistrement présente des
écarts/variations sur certains attributs. Il pourrait par exemple s'agir d'un catalogue de
produits dans lequel certains produits existent en différentes couleurs ou matières mais où la
plupart des attributs restent identiques.

Pour activer les variantes pour un Model, il faut cocher la case correspondante au niveau du
Model. Ensuite, la case « Variant » est active pour les attributs et peut être cochée. Pour tous
les attributs qui doivent être variants/variables, la case doit être cochée - dans l'exemple
ci-dessus, la couleur et/ou la matière.

Une fois les enregistrements parents remplis comme d'habitude, une icône supplémentaire apparaît
dans la liste BE des enregistrements pour les variantes, permettant de créer des enregistrements
enfants. Le masque de saisie pour éditer un enregistrement enfant est pour l'essentiel identique
au masque de l'enregistrement parent, mais seuls les attributs définis comme Variant sont
modifiables - tous les autres widgets (invariants) sont automatiquement en lecture seule (readonly).

.. note:: **À partir de MM 2.5 :** À côté de l'entrée racine de la liste se trouve un lien « Tout
   ouvrir »/« Tout fermer », qui ouvre ou ferme d'un coup tous les groupes de variantes, au lieu de
   devoir cliquer sur chacun individuellement.

La particularité des variantes est que toutes les valeurs non variantes de l'enregistrement parent
sont automatiquement transférées aux enregistrements enfants - et cela non seulement lors de la
création, mais aussi lors des modifications. Les valeurs actuelles de l'enregistrement parent sont
ainsi toujours présentes dans les enregistrements enfants et n'ont pas besoin d'être interrogées
séparément depuis celui-ci.

.. note:: **À partir de MM 2.5 :** Si une valeur non variable a été modifiée ultérieurement dans
   l'enregistrement parent, elle était certes transférée aux enregistrements enfants - mais les
   attributs variables dérivés, comme les :ref:`valeurs combinées <component_attribute_combinedvalues>`
   ou l':ref:`alias <component_attribute_alias>`, qui intègrent cette valeur, n'étaient pas
   recalculés dans les enregistrements enfants et restaient sur l'ancien état jusqu'à ce que
   l'enregistrement enfant soit lui-même modifié
   (`Issue #657 <https://github.com/MetaModels/core/issues/657>`_). Dans MM 2.4, le comportement
   précédent est maintenu.

De ce fait, il faut accorder une attention particulière aux attributs contenant des valeurs uniques
(unique), par ex. Alias. La vérification de l'unicité porte sur tous les enregistrements de la
table et pas uniquement sur les enregistrements parents. En cas de combinaison non prise en charge
entre « Variant » et « Valeurs uniques », un message d'erreur correspondant s'affiche.

Cette hiérarchie à deux niveaux est contrôlée par les colonnes système ``varbase`` et ``vargroup`` :

* les enregistrements parents ont ``1`` pour ``varbase`` et leur propre ID pour ``vargroup``
* les enregistrements enfants ont ``0`` pour ``varbase`` et l'ID de l'enregistrement parent pour
  ``vargroup``

Dans l':ref:`API MM <ref_api_interf_mm>`, il existe différentes possibilités de requête selon les
types de variantes.

Depuis MM 2.3, les variantes peuvent également être utilisées dans les tables enfants.


Hiérarchie / structure arborescente
.......................................

Pour créer une structure arborescente, comme par ex. l'arborescence des pages de Contao, on peut
choisir « Mode de rendu : Hiérarchie » dans les réglages du masque de saisie. Le tri standard doit
en outre être réglé sur « Manuel ».

La relation dans la structure hiérarchique se construit classiquement via ``id`` et ``pid``, chaque
niveau inférieur contenant dans ``pid`` l'``id`` du niveau supérieur correspondant.

Si un Model avec hiérarchie est intégré depuis un autre Model via une relation (sélection simple
ou multiple), la hiérarchie ne se reflète pas dans la construction de la sélection Select ou de la
liste de cases à cocher.

Les Models avec une hiérarchie / structure arborescente ne peuvent (actuellement) pas être utilisés
comme table enfant, car la ``pid`` est utilisée pour la relation vers l'enregistrement parent.


.. |br| raw:: html

   <br />

.. |img_db-schema_01| image:: /_img/screenshots/metamodel_first/db-schema_01.png
   :width: 400px
