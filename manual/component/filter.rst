.. _component_filter:

|svg_filter_32| |img_filter_32| Ensembles de filtres
========================================================

.. note:: créer des ensembles de filtres optionnels pour le backend et le frontend ;
  créer un ensemble de filtres et l'activer dans les composants ou les éléments de
  contenu/modules


Introduction
------------

Le composant « Ensemble de filtres » offre un outil complet permettant d'influencer l'affichage et
la sélection des enregistrements (Items) d'un MetaModel. Les ensembles de filtres réduisent
l'ensemble total des Items, c'est-à-dire qu'après un filtrage, un sous-ensemble de ceux-ci est
disponible pour la sortie. Il faut noter que chaque ensemble de filtres ne renvoie toujours qu'une
liste d'ID (des Items), c'est-à-dire qu'une règle de filtre transmet une liste d'ID à la règle de
filtre suivante - une modification des valeurs des Items, par ex. via une requête SQL, n'est pas
possible.

La création d'un ensemble de filtres suit une hiérarchie à deux niveaux : on crée d'abord un
ensemble de filtres nommé « comme conteneur », qui peut à son tour contenir une ou plusieurs règles
de filtre. Si plusieurs règles de filtre sont présentes à ce niveau, elles sont automatiquement
combinées par ET. Pour une combinaison par OU, il faut créer une règle de filtre OU, qui peut à son
tour contenir d'autres règles de filtre. Grâce aux possibilités d'imbrication, presque toutes les
combinaisons ET/OU d'une requête SQL native peuvent être reproduites.

Certaines règles de filtre disposent d'une option sélectionnable permettant de n'afficher que les
entrées de filtre associées, ou seulement les entrées restantes, afin de garantir un affichage
dynamique de l'ensemble de filtres.

Les ensembles de filtres peuvent être utilisés aussi bien dans le backend que dans le frontend.

Les règles de filtre peuvent en partie être influencées dynamiquement, par ex. via des paramètres
GET/POST, ce qui permet des filtrages très élaborés.


Types de règles de filtre
--------------------------

* **Ensemble d'Items prédéfini** (core) : |br|
  saisie d'une liste d'ID selon laquelle le filtrage doit s'effectuer
* **Requête simple** (core) : |br|
  génère un filtrage selon un attribut ; un paramètre URL peut être indiqué pour le filtrage ; avec
  l'option « Paramètre statique », une valeur peut être activée pour le filtrage à partir d'une
  liste Select dans les éléments de contenu/modules FE
* **SQL personnalisé** (core) : |br|
  conditions SQL personnalisées pour le filtrage ; tenir compte de l'assistant d'aide |img_help|
  (popup) |br|
  voir aussi dans le « livre de recettes » :ref:`rst_cookbook_filter_custom-sql`
* **Condition ET (AND)** (core) : |br|
  conteneur pour d'autres règles de filtre avec combinaison ET
* **Condition OU (OR)** (core) : |br|
  conteneur pour d'autres règles de filtre avec combinaison OU ; option pour n'exécuter que la
  première règle (case à cocher « S'arrêter après la première correspondance »)
* **État de la case à cocher** (attribute_checkbox) : |br|
  vérifie qu'une valeur d'attribut vaut 1 ; (anciennement « Statut de publication ») ; la règle pour « uniquement les
  items publiés », sans widget frontend
* **État de la case à cocher traduite** (attribute_translatedcheckbox) : |br|
  vérifie qu'une valeur d'attribut traduite vaut 1 ; (anciennement « Statut de publication
  traduit ») ; sans widget frontend
* **Oui / Non** (filter_checkbox) : |br|
  sélection Oui/Non pour les visiteurs, par ex. sous forme de boutons radio ; un widget qui ne filtre qu'une fois le
  choix fait ; template propre mm_filteritem_checkbox(.html5)
* **Valeur de/à pour un champ** (filter_fromto) : |br|
  sélection de/à pour les valeurs d'un attribut
* **Valeur de/à pour un champ de date** (filter_fromto) : |br|
  sélection de/à pour la date d'un attribut ; template propre mm_filteritem_datepicker(.html5) -
  `régler la date au format YYYY-MM-DD <https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/input/date>`_
* **Valeur de/à pour deux champs** (filter_range) : |br|
  deux champs avec des valeurs
* **Valeur de/à pour deux champs de date** (filter_range) : |br|
  deux champs avec des valeurs de date ; template propre mm_filteritem_datepicker(.html5) - `régler
  la date au format YYYY-MM-DD <https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/input/date>`_
* **Sélection simple** (filter_select) : |br|
  sélection d'une seule valeur, par ex. dans une liste Select ; alternativement, les templates
  mm_filteritem_radiobutton(.html5) ou mm_filteritem_linklist(.html5)
* **Sélection multiple** (filter_tags) : |br|
  sélection multiple de valeurs, par ex. dans une liste de cases à cocher ; alternativement, le
  template mm_filteritem_linklist(.html5)
* **Filtre de texte** (filter_text) : |br|
  filtre selon une saisie de texte
* **Recherche par périmètre** (filter_perimetersearch) : |br|
  filtre selon une adresse/des coordonnées géographiques et un périmètre, par rapport aux valeurs
  Lat/Long des enregistrements |br|
  voir :ref:`extended_perimetersearch`
* **Registre** (filter_register) : |br|
  filtre selon la lettre initiale ; génère une liste avec toutes les lettres initiales ou
  seulement celles présentes ; template propre mm_filteritem_register(.html5)
* **Recherche assistée par Levenshtein** (attribute_levenshtein) : |br|
  génère un index plein texte à partir des attributs sélectionnés, avec recherche par similarité et
  autocomplétion ; template propre mm_filteritem_levenshtein(.html5)
* **Filter-by-related** (filter_by_related) [à partir de MM 2.4] : |br|
  permet de filtrer des Items selon les propriétés d'un MetaModel lié (relation) ; les relations
  peuvent être construites via une table enfant ou une sélection simple (Select) |br|
  voir :ref:`rst_extended_filter_by_related`
* **Loupe** (filter_loupe) [à partir de MM 2.4] : |br|
  génère un index plein texte des attributs sélectionnés dans une base SQLite propre - basé sur
  `Loupe <https://github.com/loupe-php/loupe>`_ ; pour en savoir plus, voir la
  :ref:`règle de filtre Loupe <rst_extended_loupe>`
* **Règle Expression** (filter_expression) [à partir de MM 2.4] : |br|
  permet de conditionner l'exécution d'autres règles de filtre à des conditions. Un nœud est créé
  dans la liste des règles, qui peut contenir une ou au maximum deux autres règles de filtre comme
  nœuds enfants ; pour en savoir plus, voir la
  :ref:`règle de filtre Expression <rst_cookbook_filter_expression-rule>`


Paramètres de réglage
---------------------

Les différentes règles de filtre peuvent être adaptées aux besoins individuels grâce à des
possibilités de réglage spécifiques. Pour la plupart des règles de filtre, les paramètres suivants
peuvent être réglés :

* **Paramètre URL :** définit le mot-clé (Key) pour l'URL ; sans indication, il s'agit du nom de
  colonne de l'attribut. Avec le mot-clé ``auto_item``, le mot-clé n'est pas intégré dans l'URL,
  seule la valeur est affichée - ``auto_item`` ne peut être utilisé que pour une seule règle de
  filtre. Les mots-clés ``language`` et ``items`` sont réservés par Contao - à partir de MM 2.3,
  ils sont automatiquement réécrits avec un ``__`` ajouté, s'ils sont utilisés comme nom de
  colonne.
* **Type d'URL pour le paramètre :** (à partir de MM 2.4) permet de définir si le paramètre de
  filtre est transmis à l'URL comme Slug ou comme paramètre GET. Les choix disponibles sont
  « Slug uniquement », « GET uniquement » ainsi que « Slug ou GET autorisé ». Ce dernier réglage
  est déprécié et devrait être remplacé par l'une des deux valeurs univoques ; le backend le
  signale lors de son utilisation. Les règles de filtre nouvellement créées démarrent avec
  « Slug uniquement », celles déjà existantes ont été réglées sur « Slug ou GET autorisé » lors de
  l'introduction du réglage, afin que leur comportement ne change pas. Si un paramètre est transmis
  via l'autre type d'URL que celui réglé, il reste ignoré à partir de MM 2.4.25 - comme tout autre
  paramètre inconnu ; auparavant, cela entraînait une erreur 404. Pour en savoir plus, voir les
  :ref:`conseils SEO <rst_cookbook_tips_seo_filter-url>`
* **Template :** sélection du template du widget pour l'affichage FE ; outre le template
  ``mm_filteritem_default``, différentes règles de filtre apportent leurs propres templates, comme
  par ex. Checkbox, Levenshtein, Register, etc. Les templates peuvent être adaptés ou personnalisés
  de la manière habituelle sous Contao. Le template englobant (wrapper) est sélectionné dans le
  CE/module FE Filtre.
* **ID/classe CSS :** définit un ID ou une classe CSS dans le widget affiché ; cela permet un
  contrôle individuel de l'affichage/de la mise en forme.


Déroulement
-----------

Un nouvel ensemble de filtres s'ouvre via « |img_new| Nouveau » et un nom doit être attribué.

Via l'icône « |img_filter_setting| Règles de filtre », on accède à la liste de saisie des règles de
filtre, où l'on peut à nouveau créer une nouvelle règle de filtre via « |img_new| Nouveau ». Via les
icônes « presse-papiers », la hiérarchie peut être influencée lors de la création d'une règle de
filtre, et la règle de filtre peut par ex. être insérée à l'intérieur d'une règle OU.


.. seealso:: Dans le livre de recettes :

   * :ref:`rst_cookbook_checklists_filter`
   * :ref:`rst_cookbook_filter_exclusion`


.. _component_filter_list:

Détails de toutes les règles de filtre
------------------------------------------

.. toctree::
   :maxdepth: 1

   filter/idlist
   filter/simplelookup
   filter/customsql
   filter/condition-and
   filter/condition-or
   filter/expression-rule
   filter/checkbox
   filter/translated-checkbox
   filter/yes-no
   filter/fromto
   filter/fromto-date
   filter/range
   filter/range-date
   filter/select
   filter/tags
   filter/text
   filter/perimeter-search
   filter/register
   filter/levenshtein
   filter/by-related
   filter/loupe
   filter/parent


.. |svg_filter_32| image:: /_img/icons_svg/filter.svg
   :width: 32px
.. |img_filter_32| image:: /_img/icons/filter_32.png
.. |img_filter| image:: /_img/icons/filter.png
.. |img_filter_setting| image:: /_img/icons/filter_setting.png
.. |img_new| image:: /_img/icons/new.gif
.. |img_about| image:: /_img/icons/about.png
.. |img_help| image:: /_img/icons/help.svg

.. |br| raw:: html

   <br />
