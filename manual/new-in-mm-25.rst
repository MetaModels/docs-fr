.. _new_in_mm250:

Modifications et fonctionnalités de MM 2.5
==========================================

Vous trouverez ci-dessous un aperçu des modifications et fonctionnalités de MetaModels 2.5, rendues possibles
grâce au « programme early adopter » - pour en savoir plus sur le financement, voir la rubrique Fundraising sur le
`site web de MM <https://now.metamodel.me/de/unterstuetzer/fundraising#metamodels_2-5>`_.

Pour une vérification après une mise à niveau vers MM 2.5, voir :ref:`plus d'indications ci-dessous
<check_upgrade_mm250>`.


Généralités et Core
-------------------

MetaModels 2.5 nécessite **Contao 5.7** et **PHP 8.4**.

Les principales nouvelles fonctionnalités sont :

- prise en charge des **templates Twig** en complément des templates ``.html5`` existants
- **nouvelles icônes SVG** pour le backend
- plus de MooTools
- **sections de backend personnalisées par configuration**
- **fil d'Ariane pour les tables enfants**
- templates d'attributs avec sortie du label associé à la valeur
- nouvel **attribut pour les valeurs Lat/Long**
- **variantes avec pagination**
- enregistrements MetaModels trouvables dans la **recherche backend de Contao**
- nouvelle extension **vue ERD** de toutes les tables MetaModels et de leurs relations
- nouvelle extension **Health-Check** pour détecter et nettoyer les données MetaModels orphelines
- nouvelle extension **ChangeLanguage-Bridge** pour le sélecteur de langue sur les pages de détail
- modifications d'enregistrements dans le journal système
- **gestion des versions** pour la configuration MM et les items MM
- **diverses accélérations** au niveau du DCG, de la recherche par périmètre/géodistance, du rendu différé (Lazy-Rendering)


Templates Twig (NOUVEAU)
........................

Chaque template MetaModels peut désormais être proposé en complément sous forme de **template Twig**. Si une
variante Twig existe pour un template, elle a la **priorité** sur le template classique ``.html5`` - exactement
comme dans Contao lui-même. En l'absence de variante Twig, le ``.html5`` est rendu sans changement (repli complet
assurant la rétrocompatibilité).

Cette priorité s'applique aussi bien au **frontend qu'au backend**, et pour **les deux formats de sortie** : la
sortie visible (format ``html5``) et la sortie texte (format ``text``, utilisée pour l'index de recherche et le
tri). La sortie texte est rendue via un template propre portant l'extension ``.text.html.twig`` - ainsi, l'ancien
``mm_attr_text.text`` devient ``@Contao/metamodels/attribute/text.text.html.twig``. La répétition dans le nom de
l'attribut de texte n'est pas une erreur de frappe : la première partie est le type d'attribut, la seconde le
format. En l'absence de variante Twig, le template legacy est là aussi utilisé sans changement.

.. note:: Les variantes textuelles Twig ne sont fournies par MetaModels **que pour le groupe** ``attribute`` -
   c'est là qu'est produit le contenu du nœud ``text`` dans ``raw`` ou pour l'index de recherche. Il n'y en a pas
   pour ``filter`` et ``item``.

**Schéma de nommage :** Les templates Twig se trouvent dans l'espace de noms géré ``@Contao``, dans un
sous-groupe propre ``metamodels/``. L'identifiant Twig est formé à partir de l'ancien nom de template (à plat) en
supprimant le préfixe conventionnel et en faisant précéder le tout du groupe (``attribute``, ``filter`` ou
``item``) :

============================  ===========  =========================================================
Auparavant (``.html5``)       Groupe       Twig
============================  ===========  =========================================================
``mm_attr_text``              attribute    ``@Contao/metamodels/attribute/text.html.twig``
``mm_filteritem_default``     filter       ``@Contao/metamodels/filter/default.html.twig``
``metamodel_prerendered``     item         ``@Contao/metamodels/item/prerendered.html.twig``
============================  ===========  =========================================================

Comme les templates se trouvent dans l'espace de noms ``@Contao``, ils peuvent être modifiés sans effort
supplémentaire dans le **Template Studio** de Contao, et être surchargés via les **dossiers de thème** ainsi que
le répertoire global ``templates/`` du projet.

**Fournir ses propres templates Twig :** Comme dans les propres bundles de Contao, les templates Twig
personnalisés sont déposés sous une *racine d'espace de noms* (Namespace-Root) : un dossier ``twig/`` contenant
un fichier marqueur vide ``.twig-root``. Dans le projet, le dossier ``templates/`` est déjà une racine d'espace
de noms, de sorte que la structure de sous-dossiers peut y être utilisée directement :

.. code-block:: text

   templates/
   └── metamodels/
       ├── attribute/
       │   └── text.html.twig
       ├── filter/
       │   └── default.html.twig
       └── item/
           └── prerendered.html.twig

À l'intérieur d'un template, les mêmes variables sont disponibles que dans le ``.html5`` (par exemple
``{{ raw }}`` pour l'attribut). Les blocs connus depuis les ``.html5`` sont de véritables blocs Twig - par
exemple, un widget de filtre étend le template standard avec
``{% extends "@Contao/metamodels/filter/default.html.twig" %}`` et surcharge les blocs ``formlabel`` et
``formfield``.

**Surcharges existantes :** Une surcharge existante d'un nom de template **à plat** dans le dossier
``templates/`` du projet (ou dans un dossier de thème) - par exemple ``templates/metamodel_prerendered.html5`` -
conserve la **priorité** sur un template Twig fourni. Les adaptations existantes continuent donc de fonctionner
sans changement après la mise à niveau.

.. note:: Cette prise en compte des surcharges à plat est une **solution transitoire** et disparaîtra dans
   MetaModels 2.6 (Contao 6.3), en même temps que les templates ``.html5``. Les adaptations personnelles
   devraient donc être déplacées vers ``templates/metamodels/<groupe>/…`` - soit en tant que ``.html.twig``,
   soit (provisoirement) en tant que ``.html5`` sous le nouveau chemin.

**Les templates de l'édition frontend sont désormais eux aussi compatibles Twig.** Sont concernés le masque de
saisie lui-même (``dcfe_general_edit``) ainsi que les widgets pour les fichiers (``form_upload-on-steroids``), le
MultiColumnWizard (``form_mcw``) et le champ de texte multiple (``form_text_multiple``).

Ceux-ci ne suivent **pas** le schéma ``metamodels/<groupe>/<leaf>`` décrit plus haut, mais la propre convention
de Contao avec un nom à plat : un ``@Contao/dcfe_general_edit.html.twig`` a priorité sur le ``.html5`` de même
nom. Il ne s'agit pas d'un mécanisme MetaModels, mais de la priorité Twig intégrée de Contao pour les templates
legacy - elle s'applique à tout template affiché via la classe de template de Contao.

En pratique, cela signifie : quiconque souhaite surcharger l'un de ces templates crée, comme auparavant, un
``.html5``, ou désormais un ``.html.twig`` sous le même nom. Une surcharge ``.html5`` existante de priorité
supérieure conserve sa priorité, les adaptations existantes continuent donc de fonctionner sans changement.

Pour sa propre version Twig des templates de widget, une règle est à respecter - le bloc ``label`` est remplacé
pour les champs comportant un badge de langue, voir :ref:`rst_extended_frontend_editing`.

.. seealso:: En mode debug, un commentaire de début et de fin ainsi que le nom du template sont affichés dans le
   code source pour les templates html5 du frontend - cela permet de voir quel template affiche quoi où. Pour
   les templates Twig, ces informations sont également disponibles avec l'extension `contao-twig-debug-marker-bundle
   <https://github.com/e-spin/contao-twig-debug-marker-bundle>`_.


Icônes du backend (révisées)
............................

Toutes les icônes du backend sont passées du **PNG au SVG**. Elles restent ainsi nettes dans toutes les
tailles - y compris en cas d'affichage agrandi dans le navigateur ou sur des écrans haute résolution.

**Les six sections d'un MetaModel se distinguent par leur couleur :** Attributs (bleu), réglages de rendu
(vert), masque de saisie (orange), réglages de recherche (violet), filtres (rouge) et affectations (magenta).
Les icônes des différents types d'attributs et de filtres restent volontairement d'un gris uni - elles se
retrouvent les unes sous les autres dans de longues listes, où la couleur ne ferait qu'apporter de l'agitation
visuelle.

|mm_list_with_icons|

Les couleurs retenues ont été choisies pour s'intégrer au schéma de couleurs de Contao, se distinguer les unes
des autres, fonctionner autant que possible aussi bien en mode clair qu'en mode sombre, et rester distinguables
même en cas de daltonisme rouge-vert.

**Le mode sombre est pris en charge de bout en bout.** Pour chaque symbole dont la couleur ne fonctionne pas
dans le design sombre, une version propre est disponible ; Contao affiche celle qui convient. Les boutons
désactivés apparaissent dans une version pâle du même symbole, également pour les deux designs.

Une **page récapitulative** se trouve ici : :ref:`manual_new_icons-25`.

Trois endroits ont par ailleurs reçu de nouvelles icônes :

* **Les conditions dans le masque de saisie** portaient jusqu'ici toutes le même symbole. Chaque type a
  désormais le sien - ET, OU, NON, « la propriété est visible », « la propriété a la valeur » et « la propriété
  contient l'une des valeurs ». Dans une condition imbriquée, on voit ainsi d'un coup d'œil comment elle est
  construite.
* **L'attribut Case à cocher** ne se bascule plus, dans la liste des items, à l'aide d'un œil, mais avec une
  case cochée ou vide. Un œil suggère la visibilité, alors qu'un attribut de type case à cocher peut signifier
  tout et n'importe quoi - « payé », « vérifié », « membre ». Les couleurs correspondent à celles que Contao
  utilise pour publié et non publié.
* **La liste de favoris** indique, au niveau du MetaModel, si une liste de favoris est réellement configurée :
  le symbole est plein dès qu'il en existe une, et reste vide sinon. Cela vaut aussi bien dans la liste des
  MetaModels que dans le fil d'Ariane.

.. seealso:: Pour qui trouve les icônes du backend fondamentalement trop petites, il est possible de les
   agrandir dans son propre profil utilisateur grâce à l'extension
   `contao-backend-size-bundle <https://github.com/e-spin/contao-backend-size-bundle>`_ - le réglage
   s'applique par utilisateur, pas pour toute l'installation. Cette extension **ne fait pas** partie de
   MetaModels et peut être utilisée indépendamment ; grâce au passage au SVG, les icônes de MetaModels restent
   toutefois nettes.


Les entrées désactivées sont barrées
....................................

Dans les listes du backend, il était jusqu'ici difficile de voir si une entrée était désactivée - le symbole en
fin de ligne le signalait, mais pas le nom lui-même. Le nom d'une entrée désactivée s'affiche désormais
**barré**. Cela concerne les réglages de rendu, le masque de saisie, les règles de filtre et la sélection de
fichiers.

Dans les listes de fichiers, la mention « [Standard] » figure après le nom. Elle reste lisible et n'est pas
barrée avec le reste, afin que les deux informations ne se confondent pas.


Accès rapide aux sections d'un MetaModel (NOUVEAU)
..................................................

Quiconque configure un MetaModel passe constamment d'une section à l'autre : des attributs au masque de saisie,
puis de là aux réglages de rendu, puis aux filtres. Jusqu'ici, chacun de ces changements repassait par la liste
globale de tous les MetaModels.

À droite du fil d'Ariane figurent désormais les icônes de **toutes les sections du MetaModel** dans lequel on se
trouve - attributs, réglages de rendu, masque de saisie, réglages de recherche, filtres, affectations et, si
elles sont installées, les listes de favoris. Un clic y mène directement.

|mm_breadcrumb_icons|

Cet accès rapide apparaît partout où l'on sait clairement de quel MetaModel il s'agit : dans les listes de
chaque section comme dans les masques d'édition qu'elles contiennent. Il n'apparaît pas dans la **liste
globale** de tous les MetaModels - car aucun MetaModel particulier n'y est visé. Un MetaModel nouvellement créé
le possède dès qu'il est enregistré.

Les icônes qui apparaissent dépendent des sections existantes ; si une extension en ajoute une supplémentaire,
elle figure automatiquement dans la rangée.


Sections de backend personnalisées par configuration (NOUVEAU)
..............................................................

Une section (groupe) personnalisée dans la navigation du backend - jusqu'ici constructible uniquement à la main
via un listener ``MenuEvent`` propre et une icône SVG - peut désormais être créée directement par configuration
(`Ticket core#1519 <https://github.com/MetaModels/core/issues/1519>`_) :

.. code-block:: yaml

   meta_models_core:
       be_sections:
           products:
               name:
                   de: 'Produkte'
                   en: 'Products'
               tooltip:
                   de: 'Produkte erstellen'
                   en: 'Create products'
               icon: 'files/theme/mm/products.svg'
               add:
                   before: design

``products`` est l'alias unique de la section. ``name`` et ``tooltip`` sont des cartes de langues - la langue
actuelle du backend est affichée, sinon l'anglais, sinon la première entrée présente. ``icon`` est un chemin web
(typiquement sous la gestion de fichiers de Contao ``files/…``) ; sans indication, ou si le fichier est
introuvable, l'icône grise standard de MetaModels apparaît à la place (la même que celle utilisée par les
sections propres à « MetaModels », mais en bleu dans ce cas). Sous ``add``, exactement l'une des deux
indications ``before`` ou ``after`` fixe la position par rapport à une entrée de navigation existante (par
exemple ``design``, ``content``, ``accounts`` ou une autre section créée par configuration) ; ``collapsed:
true`` fait démarrer la section repliée lors du premier appel.

.. note:: L'alias cible est le nom de groupe **interne** de Contao, et non le libellé affiché - l'ancienne
   section « Layout » s'appelle en interne ``design`` depuis Contao 4/5, et non ``layout``. Si l'alias indiqué
   ne se trouve pas dans la navigation, la section personnalisée est alors ajoutée à la fin à la place.

Cette configuration ne crée que le **groupe vide** - il se remplit comme d'habitude : via ses propres modules
ou, pour des écrans MetaModels autonomes, via l'indication de la section directement au niveau du masque de
saisie.

.. seealso::


Fil d'Ariane pour les tables enfants (NOUVEAU)
..............................................

Lorsqu'on se trouve dans la liste d'une **table enfant**, l'en-tête n'affichait jusqu'ici que le nom du module.
D'où l'on venait et à quel enregistrement la liste appartenait n'apparaissait nulle part - en cas d'imbrication
multiple, on perdait vite ses repères.

Le chemin complet apparaît désormais à cet endroit, depuis le modèle de base jusqu'au niveau actuel :

.. code-block:: text

   Mitarbeiter: Mayer, Herbert  ›  Dienstreisen

Chaque maillon est un lien et mène à la vue en liste de son niveau, de sorte qu'un changement entre les niveaux
ne passe plus par la liste globale de tous les MetaModels. En cas d'imbrication profonde, le modèle de base, le
dernier niveau parent et le niveau actuel restent visibles ; ce qui se trouve entre les deux se replie en ``…``
et peut être déplié :

.. code-block:: text

   Mitarbeiter  ›  …  ›  Kind 3: Drittes Kind  ›  Dienstreisen

Dans le **masque d'édition**, l'enregistrement en cours de modification s'ajoute comme dernier maillon.

Le nom sous lequel un enregistrement apparaît dans le chemin est déterminé par le masque de saisie de son
niveau, sous **« Compléments au titre du masque »** - le même champ qui complétait déjà auparavant le titre du
masque d'édition. Il accepte des Simple Tokens portant sur les attributs, ``##model_id##`` affiche l'ID :

.. code-block:: text

   ##model_name##, ##model_firstname##
   ##model_city## [##model_id##]

Sans indication, seul le nom du MetaModel apparaît à cet endroit. Il n'y a donc pas de nouveau champ, et les
indications déjà renseignées prennent effet immédiatement.

.. note:: Les listes **sans** parenté conservent leur titre habituel. Contao affiche à cet endroit soit un fil
   d'Ariane, soit un titre, et au niveau le plus élevé, le fil d'Ariane disait de toute façon la même chose que
   le titre.

L'apparence et le fonctionnement proviennent de Contao lui-même - c'est le même fil d'Ariane que dans les
modules du noyau, y compris le menu déroulant derrière l'ellipse. Voir aussi :ref:`component_relations` au
sujet des tables enfants.


La recherche backend trouve les enregistrements MetaModels (NOUVEAU)
......................................................................

La recherche backend globale de Contao (champ de recherche en haut de l'en-tête, raccourci clavier Ctrl+K)
parcourt depuis Contao 5.5 également les enregistrements de certaines tables - à condition que leur
``dataContainer`` soit exactement ``Contao\DC_Table``. Chaque table MetaModels - aussi bien les tables de
configuration ``tl_metamodel_*`` que chaque table d'items générée - utilise à la place le DC_General, raison
pour laquelle **rien** n'y apparaissait jusqu'ici. MetaModels 2.5 fournit pour cela son propre fournisseur de
recherche, qui couvre les **tables d'items** - donc les enregistrements proprement dits d'un MetaModel, pas sa
configuration.

**Ce qui est parcouru :** exactement les attributs déjà marqués comme « Recherchable » dans le masque de
saisie - la même case à cocher qui alimente déjà la sélection des champs de la recherche en liste dans le
backend. Il n'y a donc **aucun réglage supplémentaire** : si un attribut y est coché, il apparaît
automatiquement aussi dans l'index de recherche global.

**MetaModels traduits :** pour chaque langue dans laquelle un enregistrement possède réellement sa propre
valeur, un résultat distinct apparaît avec son propre lien d'édition - le clic ouvre le masque directement
sur l'onglet de langue correspondant. Une langue qui n'a jamais été traduite pour l'enregistrement et qui
n'affiche donc que la valeur de la langue de repli ne génère **aucun** résultat propre (en double) -
exactement les cas que l'on reconnaît dans le masque de saisie lui-même au badge orange « Repli ».

**Titre et autorisation :** le résultat affiche le nom du MetaModel et le titre de l'enregistrement (le même
schéma que sous « Compléments au titre du masque », voir ci-dessus), et pour les traductions, en plus, le
code de langue. La visibilité d'un résultat pour un utilisateur connecté dépend des droits d'accès MetaModels
déjà en place sur la section concernée - il n'existe pas d'autorisation propre à cet effet.

.. note:: La recherche backend de Contao a besoin d'un worker en arrière-plan qui tourne en permanence
   (``messenger:consume``), qui construit l'index de recherche et le maintient à jour. Sans lui, le champ de
   recherche n'apparaît pas du tout dans l'en-tête - indépendamment de MetaModels, cela concerne de la même
   façon toute table Contao parcourable.


Sélecteur de langue sur les pages de détail (NOUVEAU)
......................................................

La nouvelle extension à installer séparément :ref:`metamodels/changelanguage-bridge
<rst_extended_changelanguage-bridge>` rend `« ChangeLanguage »
<https://github.com/terminal42/contao-changelanguage>`_ sensible à l'item sur les pages de détail
MetaModels : le sélecteur de langue renvoie alors directement vers le même enregistrement dans la langue
cible avec le slug correspondant, au lieu de revenir à la page d'accueil de la langue. Remplace les deux
solutions de contournement précédentes (règle de filtre « Rechercher dans toutes les langues » ou un hook
``changelanguageNavigation`` propre) par une simple case à cocher par paramétrage de rendu. De plus, les
paramètres de filtre GET (par ex. ``?alias=...``) sont automatiquement repris sans cette case à cocher -
même pour des modèles monolingues, sans entrée dans « Conserver les paramètres de requête ». Plus de
détails sous :ref:`Multilinguisme <component_multi-language_fe-output>`.


Vue ERD de toutes les tables MetaModels (NOUVEAU)
..................................................

La nouvelle extension à installer séparément :ref:`metamodels/erd-viewer <rst_extended_erd-viewer>` affiche
un `schéma entité-association <https://fr.wikipedia.org/wiki/Mod%C3%A8le_entit%C3%A9-association>`_ de
toutes les tables MetaModels, généré automatiquement à partir de la base de données, dans le backend -
accessible via une nouvelle entrée de menu dans la liste « Tous les MetaModels ». Sont représentées aussi
bien les relations d'attributs (sélection, tags et leurs variantes traduites, chacune avec sa cardinalité)
que les :ref:`relations parent-enfant <component_relations_child-tables>`. La vue peut être filtrée, les
sélections filtrées peuvent être enregistrées comme « vue » pour tous les utilisateurs du backend, et
l'extrait actuel ou la sélection actuelle peuvent être exportés en SVG, PNG, Graphviz ``.dot`` ou GraphML -
ce dernier par ex. pour un retravail dans `yEd Live <https://www.yworks.com/yed-live/>`_, gratuit. Voir
aussi :ref:`Structure de la base de données <component_relations_database_structure>`.


Health-Check pour nettoyer les données orphelines (NOUVEAU)
.............................................................

La nouvelle extension à installer séparément :ref:`metamodels/health-check <rst_extended_health-check>`
trouve et nettoie les données MetaModels incohérentes - par ex. des lignes dans les tables de stockage
propres aux attributs (tags, texte multiple/tableau de texte, évaluations et leurs variantes traduites) qui
subsistent après la suppression d'un attribut ou d'un enregistrement, parce que le DCG ne voit jamais ces
tables supplémentaires. Accessible via une nouvelle entrée de menu dans la liste « Tous les MetaModels »,
tout comme la vue ERD, réservée aux administrateurs. Les vérifications sont conçues de façon modulaire
(possibilité d'en ajouter via ``services.yml``) et n'apparaissent que si elles peuvent effectivement
s'appliquer à l'installation en cours ; chaque nettoyage dispose d'un aperçu (dry-run) avant la suppression
effective, chaque nettoyage réellement exécuté est journalisé, une sauvegarde peut être déclenchée
directement depuis la page - elle se restaure comme d'habitude via le Contao-Manager ou la console - et
chaque vérification peut aussi être exécutée via une commande de console (par ex. pour des tâches cron).


DC_General
----------

Le DC_General a été porté, en **version 2.5**, sur **Contao 5.7**. Les principaux changements :

* **Nouvelle gestion du Referer :** Contao 5.7 ne détermine plus la page de référence via la session, mais via
  le ``DcaUrlAnalyzer``. Cela ne s'applique pas aux tables MetaModels ; les liens « Retour » et « Enregistrer et
  fermer » sont donc désormais générés de manière **déterministe** (nouveau : ``ViewHelpers::getBackUrl()``).
  L'ancien ``StoreRefererListener`` disparaît.
* **Bouton « Enregistrer et retour » (saveNback) supprimé :** Le DC_General suit ici Contao Core 5.7.0 - le
  bouton ``saveNback`` a été supprimé du masque de saisie et de « Tout modifier ». « Enregistrer et fermer »
  (``saveNclose``) est conservé.
* **Suppression du legacy Contao :** Les classes et chemins devenus inutiles issus des versions de Contao
  antérieures à 5.7 ont été supprimés (entre autres ``TreeSelect``, ``FileSelect`` ainsi que le chemin mort du
  sélecteur de fichiers dans le widget ``FileTree``).
* **Listes de sélection triables passées à la technique Contao 5 :** Le widget ``fileTree`` et les sélecteurs
  arborescents (Tree-Picker) utilisent désormais les contrôleurs Stimulus ``contao--sortable`` et
  ``contao--input-map`` de Contao, à la place de ``Backend.makeMultiSrcSortable()``, marqué comme obsolète
  depuis Contao 5.7. Le bouton de suppression d'un fichier est à cette occasion rendu côté serveur plutôt
  qu'ajouté après coup en JavaScript. Le widget ``fileTree`` exploite en outre l'option de widget
  ``isSortable``, avec laquelle Contao remplace depuis la version 5.0 l'option supprimée ``orderField`` - cette
  dernière reste prise en charge. Rien ne change dans l'utilisation.
* **Les Tree-Picker peuvent être restreints (NOUVEAU) :** Un Tree-Picker construit sa propre vue de la table
  cible et proposait donc toujours tous les enregistrements - même là où la liste voisine n'affichait qu'une
  sélection filtrée. Grâce à la nouvelle option de widget ``sourceFilter``, l'appelant peut transmettre les ID
  autorisés ; le sélecteur s'y limite alors. Une liste vide signifie ici « rien ne correspond » et non « pas de
  filtre » - qui indique une restriction l'obtient aussi dans ce cas. Rien ne change sans cette option. Elle
  est utilisée par les attributs Sélection et Tags, voir à ces entrées.
* **Tooltips des boutons d'opérations corrigés :** Dans les vues en liste, l'affichage des tooltips (y compris
  les icônes pour ouvrir les tables enfants) a été corrigé.
* **Cycle dans les conditions de visibilité intercepté :** Lorsque les conditions de visibilité de deux champs
  se référaient l'une à l'autre (le champ A visible uniquement si le champ B l'est, et inversement), le masque
  de saisie plantait jusqu'ici avec une erreur fatale. Un tel cycle est désormais détecté ; les champs concernés
  restent simplement masqués, et le masque reste utilisable.
* **Message compréhensible en cas d'autorisation manquante :** Lorsqu'un enregistrement ne pouvait pas être
  supprimé, créé ou modifié, le DC_General affichait jusqu'ici un écran d'erreur technique avec un texte de
  développeur en anglais. Les rédacteurs connectés sans l'autorisation nécessaire voient désormais un message
  compréhensible, et les visiteurs non connectés (par exemple dans l'édition frontend) la page de connexion à
  la place.
* **Une erreur technique lors du rechargement automatique reste visible :** Si un masque de saisie déclenchait,
  lors d'un rechargement automatique (par exemple dû à une condition de visibilité), une erreur technique en
  traitant la valeur d'un champ - par exemple à cause d'une extension défectueuse -, le message disparaissait
  jusqu'ici silencieusement lors de la reconstruction du masque. Il reste désormais affiché jusqu'à ce que le
  champ soit à nouveau modifié.
* **Langues en dehors de la liste des langues de Contao :** Si un MetaModel prend en charge une langue qui
  n'est pas activée comme langue dans les réglages de Contao (par exemple ``en_DE``), le sélecteur de langue
  du masque de saisie produisait une alerte PHP et une entrée vide. Le nom d'affichage ICU est désormais
  utilisé, et en dernier recours le code de langue lui-même, de sorte que la langue reste dans tous les cas
  sélectionnable.
* **Les services sont transmis via le constructeur :** Plusieurs classes du DC_General récupéraient jusqu'ici
  les services nécessaires à l'exécution depuis le conteneur Symfony ; elles les reçoivent désormais comme
  argument du constructeur. Rien ne change en fonctionnement, et **aucun paquet MetaModels n'est concerné**.
  Cela n'a d'importance que pour les **propres extensions** qui s'appuient sur le DC_General : le récepteur
  d'événements ``WidgetBuilder`` est passé d'une méthode statique à une méthode ordinaire, et son constructeur
  prend désormais quatre arguments obligatoires. Quiconque appelle ``WidgetBuilder::handleEvent()`` de manière
  statique ou instancie la classe avec deux arguments doit adapter son code - détails dans le
  ``docs/upgrade-2.5.md`` du DC_General.
* **MooTools entièrement supprimé :** L'ensemble du JavaScript backend du DC_General est passé au JS natif
  (Vanilla-JS) et aux contrôleurs Stimulus de Contao 5.7. Cela concerne aussi le **markup** : les classes
  marqueurs obsolètes ``click2edit``, ``picker_selector`` et l'ID ``sbtog`` ont disparu des templates, de même
  que tous les attributs ``onclick`` avec des appels ``Backend.*``. Les fichiers JS fournis ont à cette
  occasion été renommés (``dcGeneralAjax.js`` → ``generalAjax.js``, ``vanillaGeneral.js`` → ``generalBase.js``,
  ``generalDriver_src.js`` → ``generalDriver.js``) ; il n'y a plus d'étape de build, le fichier livré **est**
  la source. Quiconque surcharge ses propres templates DC_General ou intègre directement ces fichiers doit
  adapter son code - détails dans le ``docs/upgrade-2.5.md`` du DC_General.
* **Les variantes suivent désormais le tri de leur base :** Dans les vues arborescentes, les entrées enfants
  étaient jusqu'ici toujours affichées selon la colonne interne ``sorting``, alors que le niveau supérieur
  suivait le tri défini dans le masque de saisie. Pour une liste triée selon un attribut, les enregistrements
  de base apparaissaient donc dans l'ordre souhaité, tandis que leurs variantes en dessous semblaient dans un
  ordre arbitraire. Les deux niveaux utilisent désormais le même tri.

  Seules les listes avec un **tri par attribut** sont concernées. Rien ne change pour un tri manuel, ni
  lorsqu'aucun tri n'est défini du tout - l'ordre reste alors, comme auparavant, celui de ``sorting``. Le
  changement s'applique à toutes les vues arborescentes du DC_General, pas seulement aux variantes.

* **Le bouton de visibilité suit désormais le modèle de Contao :** Jusqu'ici, après un clic, le bouton
  échangeait uniquement, en JavaScript, le symbole de l'entrée cliquée. Cela ne pouvait être correct que pour
  cette seule entrée : dans une **hiérarchie de variantes**, les variantes héritent la valeur de
  l'enregistrement non-variant - en basculant l'enregistrement parent, l'état des variantes changeait bien sur
  le fond, mais leurs icônes restaient inchangées jusqu'au rechargement de la page. Le bouton est désormais un
  lien ordinaire : le serveur enregistre le nouvel état et renvoie la liste à nouveau, de sorte que **toutes**
  les lignes concernées s'affichent immédiatement correctement. Rien ne change dans l'utilisation.
* **Les valeurs dérivées dans les variantes suivent désormais les modifications de l'enregistrement parent :**
  Lorsqu'une valeur non variable était modifiée après coup dans l'enregistrement parent, elle était certes
  transmise aux enregistrements enfants comme auparavant - mais les attributs variables dérivés, tels que
  Valeurs combinées ou Alias, qui intègrent cette valeur, n'étaient pas recalculés dans les enregistrements
  enfants et restaient sur leur ancien état jusqu'à ce que l'enregistrement enfant soit lui-même modifié
  (`Issue #657 <https://github.com/MetaModels/core/issues/657>`_). Voir aussi
  :ref:`component_relations_variants`.
* **« Tout basculer » dans la vue arborescente (NOUVEAU) :** À côté de l'entrée racine se trouve désormais un
  lien qui déplie ou replie d'un coup tous les nœuds de la vue arborescente - à l'image de ce que Contao propose
  lui-même dans son propre arbre de pages. Cela concerne aussi bien la vue des variantes que les MetaModels avec
  le mode de rendu « Hiérarchie » (`Issue #560
  <https://github.com/contao-community-alliance/dc-general/issues/560>`_). Voir aussi
  :ref:`component_relations_variants`.
* **Modifications d'enregistrements dans le journal système (NOUVEAU) :** La création, la duplication et la
  suppression d'enregistrements MetaModels apparaissent désormais sous « Système → Journal » - exactement comme
  Contao le fait depuis toujours pour ses propres tables. Jusqu'ici, les modifications des données MetaModels
  n'y apparaissaient pas du tout (`Issue #577
  <https://github.com/contao-community-alliance/dc-general/issues/577>`_,
  `Issue #1461 <https://github.com/MetaModels/core/issues/1461>`_). L'entrée mentionne le nom de
  l'enregistrement, pas seulement la table et l'ID - le même nom que celui affiché par le masque de saisie et
  la navigation en fil d'Ariane. La modification n'est - comme pour les propres tables de Contao - volontairement
  pas journalisée, c'est le versionnement qui s'en charge. La journalisation peut être désactivée via une option
  du MetaModel, mais elle est active par défaut.
* **Gestion des versions dans la configuration MM et les items (NOUVEAU) :** Le masque de saisie affiche
  désormais une sélection des versions antérieures d'un enregistrement, avec la date et l'auteur des
  modifications, permettant de restaurer un état antérieur - comme on le connaît des propres tables de Contao.
  Cela concerne aussi bien les enregistrements MetaModel eux-mêmes que la configuration des masques de saisie,
  des réglages de rendu et des règles de filtre. Jusqu'ici, cette option restait sans effet malgré son existence
  (`Issue #52 <https://github.com/contao-community-alliance/dc-general/issues/52>`_, ouverte depuis 2014) - pour
  les enregistrements MetaModel, le raccordement n'existait tout simplement pas. Peut être désactivée par table
  comme d'habitude via sa propre DCA. Pour les MetaModels traduits, toutes les variantes linguistiques d'un
  enregistrement partagent un historique de versions commun.
* **Barre de pagination sous les vues en liste (NOUVEAU) :** Si une liste contient plus d'enregistrements que
  n'en affiche le bloc de page, une barre apparaît sous le tableau avec « page x de y » et les numéros de page -
  comme on le connaît du backend de Contao. « Début », « Précédent », « Suivant » et « Fin » mènent en plus aux
  pages extrêmes. Le sélecteur de bloc de page dans le panneau reste inchangé ; la barre s'ajoute sans rien
  remplacer. Si tout tient sur une seule page, elle ne s'affiche pas. La page sélectionnée est conservée comme
  les autres réglages du panneau, mais elle est volontairement réinitialisée dès qu'un filtre ou la taille du
  bloc est modifié - sinon on se retrouverait sur une page qui n'existerait plus du tout selon la nouvelle
  sélection.

  **La vue arborescente aussi** dispose désormais d'une barre de pagination, et le sélecteur de bloc de page y
  est également nouveau - il était jusqu'ici masqué et l'arbre était toujours chargé intégralement. Seuls les
  **enregistrements du niveau le plus élevé** sont comptés, c'est-à-dire les bases pour les variantes. Chaque
  base apparaît avec toutes ses variantes ; « page 1 de 3 » signifie donc « bases 1-3 sur 7 » et non « lignes 1-3
  sur 20 ». Parcourir tous les nœuds séparerait les bases de leurs variantes. Ce qui est déplié reste déplié lors
  de la navigation entre les pages.
* **Turbo Drive est actif dans le backend :** La navigation entre les pages du backend MetaModels passe par
  Turbo Drive de Contao, c'est-à-dire sans reconstruction complète de la page ; la position de défilement est
  ainsi conservée. Les **formulaires** du DC_General en sont volontairement exclus (``data-turbo="false"``), car
  l'envoi automatique dû aux conditions d'affichage re-rend le masque sans redirection - une réponse que Turbo
  rejetterait.
* **Masque de saisie et enregistrement accélérés :** Lors de la construction du masque de saisie, le modèle de
  données était jusqu'ici entièrement recomposé pour **chaque champ pris individuellement** - soit 27 fois pour
  un masque de 27 champs. Cela ne se produit désormais qu'une fois par passage. Le nombre de conversions
  d'attributs diminue de ce fait considérablement, passant, dans le cas de test, de 1 785 à 221 appels par
  enregistrement. Autre point désamorcé : la détermination de savoir si une requête provient du backend était
  refaite environ 6 000 fois par enregistrement et est désormais mémorisée une seule fois par requête. Rien ne
  change dans l'utilisation ni dans le résultat, il s'agit exclusivement de temps d'exécution.

.. note:: **Quiconque trouve le backend lent devrait d'abord vérifier son environnement** - pas MetaModels.
   Dans une mesure effectuée sur le même masque de saisie, un enregistrement a duré environ 10,9 secondes en
   mode Symfony **dev** avec Xdebug actif, 4,2 secondes en mode dev sans Xdebug, et 0,75 seconde en mode
   **prod**. Le seul fait de laisser en permanence ``xdebug.mode=debug`` avec ``start_with_request=yes`` coûte
   déjà un facteur 2,6, car une tentative de connexion au débogueur est effectuée à **chaque** requête. Sur les
   systèmes de production, Xdebug doit être désactivé et ``APP_ENV=prod`` défini.


Attributs
---------

Pour les templates d'attributs, des **variantes Twig** existent désormais systématiquement sous
``metamodels/attribute/<type>`` (voir la section « Templates Twig »). Les templates ``.html5`` existants sont
conservés comme repli et ne sont plus utilisés que s'ils ont été surchargés dans le répertoire ``templates/``
du projet ; ils disparaîtront dans MetaModels 3.0.

* **Le bloc englobant provient désormais du template d'attribut :** Jusqu'à la 2.4, le template de liste
  produisait, autour de chaque valeur, le bloc
  ``<div class="field …"><div class="label">…</div><div class="value">…</div></div>``, le template d'attribut
  ne fournissant que le fragment le plus interne. Quiconque voulait mettre en forme la sortie ne pouvait donc
  pas accéder au conteneur englobant (`core#660 <https://github.com/MetaModels/core/issues/660>`_). À partir de
  la 2.5, c'est le template d'attribut lui-même qui produit le bloc.

  **Rien ne change pour les sorties existantes.** Les réglages de rendu disposent pour cela de l'option
  « Wrapper dans le template de liste (ancien comportement, déprécié) », et une migration l'active lors de la
  mise à niveau pour tous les réglages de rendu existants. Seuls les réglages de rendu nouvellement créés
  démarrent sans cette option. Elle est marquée dépréciée dès le départ et disparaîtra en 3.0.

  À noter : les templates d'attributs personnalisés ne produisent pas le bloc tant qu'ils n'ont pas été
  adaptés - il manque donc dans un réglage de rendu nouvellement créé. En mode colonnes de la liste du backend,
  aucun bloc n'est volontairement produit, car l'en-tête de colonne porte déjà le libellé. Et quiconque utilise
  le nœud ``html5`` en dehors du template de liste, par exemple via ``parseAll()`` dans son propre code, obtient
  d'autres valeurs pour les nouveaux réglages de rendu ; ``text``, ``raw`` et ``attributes`` restent inchangés.

  Pour les templates d'attributs personnalisés, quatre nouvelles valeurs sont disponibles à cet effet :
  ``label``, ``colName``, ``hideLabels`` et ``legacyAttributeWrapper``. Le modèle et un exemple se trouvent
  sous :ref:`component_templates_attribute-wrapper`.

* **Rendu des attributs à la demande (NOUVEAU) :** Nouvelle option « Rendre les attributs uniquement à la
  demande (Lazy) » par réglage de rendu. Jusqu'ici, MetaModels rendait toujours, pour chaque attribut, les deux
  formats de sortie (HTML5 et texte), indépendamment du fait que le template de liste les utilise ou non. Si
  Lazy est activé, un attribut n'est rendu qu'au moment où le template y accède réellement - et cela séparément
  pour chaque format, de sorte que l'accès à un seul format ne rend pas l'autre en même temps.

  Cela vaut la peine pour les templates qui ne produisent qu'une partie des attributs configurés ou qui
  n'utilisent systématiquement qu'un seul format de sortie - selon l'ampleur de la partie inutilisée, le gain de
  vitesse va de sensible à net. Si un template accède en revanche de toute façon à tous les attributs dans les
  deux formats, l'option n'apporte rien et peut représenter une légère surcharge, car l'accès générique aux
  objets de Twig est un peu plus lent qu'un simple tableau.

  Contrairement au bloc wrapper ci-dessus, cette option **n'est pas** un ancien comportement activé par
  migration sur les réglages de rendu existants, et elle **n'est pas** dépréciée : la pertinence de Lazy dépend
  du template concerné, il n'y a donc pas de camp fondamentalement « meilleur ». Elle est donc désactivée par
  défaut aussi bien pour les réglages de rendu nouveaux qu'existants - pour en savoir plus sur la structure et
  les benchmarks, voir :ref:`component_rendersettings`.

* Sélection (select), Sélection traduite (translatedselect), Tags et Tags traduits
    * **Saut vers la table de relation (NOUVEAU) :** À côté du libellé du champ se trouve, dans le backend, un
      symbole qui ouvre la table vers laquelle pointe l'attribut - dans un nouvel onglet, afin de préserver le
      masque de saisie non enregistré.
    * Si l'attribut pointe vers un **MetaModel**, le saut mène à son module de backend ; s'il pointe vers une
      **table Contao**, il mène au module Contao correspondant. Pour ces dernières, une correspondance est
      enregistrée (entre autres ``tl_page``, ``tl_article``, ``tl_news``, ``tl_calendar_events``, ``tl_faq``,
      ``tl_member``, ``tl_user``).
    * **Les autorisations sont respectées.** Le symbole apparaît toujours, mais est grisé et non cliquable si
      la table cible n'est pas autorisée pour son propre groupe d'utilisateurs, si le champ est en lecture
      seule, ou si aucun module n'est enregistré pour une table Contao. La raison figure à chaque fois dans le
      tooltip. Les administrateurs voient tout.
    * Il n'y a pas de symbole lorsque le MetaModel cible n'est géré que comme **table enfant** - il n'existe
      pas d'appel propre vers celle-ci, mais uniquement l'opération dans la liste parente.
    * Dans l'**édition frontend**, le symbole n'apparaît pas.
    * **Le filtre défini s'applique désormais aussi dans le popup Tree-Picker (NOUVEAU) :** Lorsque l'attribut
      est configuré en Tree-Picker, le popup ouvrait jusqu'ici la table cible complète - la restriction
      n'agissait que sur la liste de sélection voisine. Il était donc possible de sélectionner aussi ce qui
      n'était pas censé figurer dans la sélection. Le popup respecte désormais la même restriction.
    * Cela vaut pour **les deux façons** de restreindre une cible : le *réglage de filtre*, lorsque l'attribut
      pointe vers un MetaModel, et la *condition* (SQL) pour une table Contao. Les deux sont alimentés par la
      même requête que celle dont la liste de sélection tire ses entrées - les deux vues ne peuvent donc pas
      diverger.
    * Pour la condition, il faut tenir compte de l'**alias de table** ; il diffère selon le type d'attribut.
      Pour Tags, c'est ``t.``, pour Sélection ``sourceTable.`` - donc par exemple
      ``sourceTable.username='tester'``. L'alias valide à chaque fois figure dans la description du champ de
      saisie.
    * Si **aucun** filtre n'est défini, rien ne change. La restriction n'est alors même pas déterminée - un
      Tree-Picker est justement utilisé là où la table cible est trop grande pour une liste de sélection.
    * **À noter pour Sélection et Sélection traduite :** Si une restriction est resserrée après coup, les
      valeurs déjà enregistrées qui n'y correspondent plus disparaissent du masque - dans le sélecteur comme
      dans la liste de sélection, comme c'était déjà le cas auparavant. Lors du prochain enregistrement de
      l'enregistrement, elles disparaissent alors aussi des données. Avant un resserrement, il vaut donc la
      peine de vérifier si des enregistrements sont concernés.
    * **Ce n'est plus le cas pour Tags et Tags traduits (NOUVEAU) :** Les références que masque un réglage de
      filtre du masque survivent désormais à l'enregistrement sans changement - que la restriction vienne
      d'être resserrée ou existe déjà depuis longtemps. Jusqu'ici, chaque enregistrement d'un enregistrement
      supprimait toutes les références absentes de l'extrait actuellement visible, même si elles n'avaient
      jamais été proposées au choix du rédacteur, qui ne pouvait donc pas non plus les désélectionner. Étaient
      concernés aussi bien l'attribut ``tags`` que les références Tags vers un autre MetaModel.

* Levenshtein (levenshtein)
    * **Orthographe corrigée partout :** Le type d'attribut s'appelait, depuis sa toute première version,
      ``levensthein`` - avec ``h`` et ``t`` inversés. Le nom des classes, le paquet Composer et le template
      avaient déjà été corrigés auparavant, mais pas le nom du type lui-même, les deux tables d'index ni deux
      colonnes de ``tl_metamodel_attribute``. C'est désormais chose faite : ``levenshtein`` s'écrit correctement
      partout.
    * Sont concernées les tables ``tl_metamodel_levensthein`` et ``tl_metamodel_levensthein_index``, les
      colonnes ``levensthein_distance`` et ``levensthein_attributes``, ainsi que le nom de type stocké dans
      ``tl_metamodel_attribute`` et ``tl_metamodel_filtersetting``. La règle de filtre de l'attribut portait
      elle aussi le nom erroné.
    * Une **migration** renomme les deux éléments et met à jour les noms de type enregistrés - l'index de
      recherche existant est conservé et **ne doit pas** être reconstruit. Il n'y a rien à faire manuellement.

* Évaluation (rating)
    * la **variante MooTools a été supprimée** (template ``mm_attr_rating_moo.html5`` ainsi que les fichiers JS
      MooTools ``moostarrating.js``/``moostarrating_src.js``) - seule reste la variante Vanilla-Star-Rating
    * nouveaux templates Twig ``metamodels/attribute/rating`` (intègre le JS via ``{% add … to body %}``) et
      ``metamodels/attribute/rating_raw``

* Fichier (file) et Fichier traduit (translatedfile)
    * la **colonne séparée pour le tri disparaît** - l'ordre de plusieurs fichiers est désormais contenu dans
      la valeur elle-même, exactement comme Contao le fait depuis la version 5.0
    * jusqu'ici, lorsque l'option *Plusieurs fichiers* (``file_multiple``) était activée, MetaModels créait une
      colonne supplémentaire ``<nom_de_colonne>__sort`` dans la table des items ; pour *Fichier traduit*, c'est
      la colonne ``value_sorting`` de ``tl_metamodel_translatedlongblob`` qui assurait cette fonction
    * contexte : Contao a supprimé l'option de widget ``orderField`` avec la version 5.0 - le widget
      ``fileTree`` ne connaît plus que ``isSortable`` et place l'ordre directement dans la valeur du champ. Le
      tri manuel était donc devenu, de fait, sans effet sous Contao 5
    * une **migration** transfère l'ordre existant dans la valeur, puis supprime la colonne : d'abord les
      entrées issues de la colonne de tri, puis les fichiers restants dans leur ordre antérieur
    * les attributs auxiliaires virtuels ``<nom_de_colonne>__sort`` disparaissent ; les classes ``FileOrder``
      et ``TranslatedFileOrder`` sont marquées *dépréciées* et seront supprimées dans MM 3.0
    * rien ne change dans l'utilisation : plusieurs fichiers continuent d'être triés par glisser-déposer dans
      le masque de saisie et retirés individuellement de la sélection via le bouton sur l'image d'aperçu

* **LatLong (NOUVEAU) :** nouvel attribut ``metamodels/attribute_latlong`` - stocke une paire de coordonnées
  (latitude/longitude) sous forme de ``POINT`` natif dans une seule colonne, au lieu de deux attributs décimaux
  ou d'un attribut texte avec des valeurs séparées par des virgules - :ref:`en savoir plus...
  <component_attribute_latlong>`

    * en option, un **index spatial** sur la colonne, que la :ref:`recherche par périmètre
      <component_filter_perimeter-search>` utilise automatiquement pour une recherche nettement plus rapide
      (voir plus bas dans « Filtres »)
    * si `cowegis/cowegis-contao-geocode-widget-bundle
      <https://github.com/cowegis/cowegis-contao-geocode-widget-bundle>`_ est installé, la saisie manuelle des
      coordonnées peut être remplacée par une **recherche d'adresse avec sélection sur carte** - au choix,
      toujours sous forme de deux champs ou d'une valeur séparée par une virgule

* Distance géographique (geodistance)
    * le calcul de distance utilise désormais la fonction spatiale native ``ST_Distance_Sphere()`` au lieu de
      la formule précédente - celle-ci s'appelait certes « Haversine », mais n'était en réalité qu'une
      approximation plane, sans véritable prise en compte de la courbure terrestre
    * en mode simple, seul un :ref:`attribut LatLong <component_attribute_latlong>` est sélectionnable
      (auparavant inutilisable - le sélecteur filtrait sur un type d'attribut qui n'a jamais existé)
    * nouvelle option **Pas d'arrondi (km)** - arrondit la valeur de distance affichée à un multiple de cette
      valeur, sans influencer le tri (qui reste toujours exact)


Filtres
-------

* Les widgets de filtre du frontend sont désormais rendus via le moteur de templates de MetaModels et suivent
  ainsi le même schéma ``@Contao/metamodels/filter/<nom>`` que les attributs et les items (voir la section
  « Templates Twig »).
* **Paramètre statique pour les attributs multilingues :** La règle de filtre « requête simple » avec
  « Paramètre statique » activé permet une présélection dans l'élément de contenu ou le module FE. Lorsqu'un
  attribut dont les valeurs sont traduites se trouve derrière la règle, cette présélection était jusqu'ici liée
  à la langue dans laquelle elle avait été définie : quiconque la réglait en allemand et ouvrait ensuite
  l'élément avec une langue de profil anglaise voyait, à la place de la sélection, une entrée illisible
  « Unknown option: … » - et perdait le réglage dès qu'il touchait au champ.

  La sélection est désormais résolue via l'enregistrement référencé, et non via la valeur elle-même. Le champ
  affiche ainsi l'entrée appropriée indépendamment de la langue du profil. Cela vaut aussi lorsque le
  MetaModel gère une langue pour laquelle il n'existe aucune langue de profil backend - c'est alors la langue
  de repli qui s'applique.

  **Le filtrage n'a jamais été concerné.** La valeur enregistrée est convertie, avant la requête, en l'ID de
  l'enregistrement référencé, indépendamment de la langue. Les présélections existantes restent valides,
  aucune migration n'est nécessaire. Il faut seulement noter : quiconque enregistre un élément dont il a
  trouvé la valeur dans une langue étrangère la réécrit dans sa propre langue - de manière équivalente, mais la
  valeur enregistrée change en conséquence.

  Il en va de même pour la présélection dans les **réglages de recherche** d'un MetaModel, qui utilisent les
  mêmes paramètres de filtre.
* **Recherche par périmètre : le rayon disparaît avec l'adresse.** Lorsque le champ d'adresse était vidé, la
  sélection de rayon précédemment choisie restait jusqu'ici visible dans le widget - bien qu'elle ne soit de
  toute façon jamais évaluée sans adresse (`Issue #31
  <https://github.com/MetaModels/filter_perimetersearch/issues/31>`_). Elle est désormais réinitialisée en
  même temps que l'adresse. Cela ne concerne que l'affichage dans le widget, le filtrage n'a jamais été
  erroné. Voir aussi :ref:`component_filter_perimeter-search`.
* **Recherche par périmètre : nettement plus rapide avec le nouvel attribut LatLong.** Le calcul de distance
  utilise désormais ``ST_Distance_Sphere()`` au lieu de la formule précédente (voir « Attributs » ci-dessus) -
  cela seul apporte déjà pratiquement le double de la vitesse. Si le mode de données « Attribut unique » est
  utilisé avec un :ref:`attribut LatLong <component_attribute_latlong>` sur lequel un index spatial a été
  créé, la recherche par périmètre combine en plus un préfiltre par boîte englobante (bounding box) appuyé sur
  l'index avec le calcul exact. Mesuré sur 500 000 enregistrements et une recherche à 50 km : de 0,40 s à
  0,014 s - **environ 28× plus rapide** qu'auparavant. Détails : :ref:`Fonctions spéciales de l'attribut
  LatLong <component_attribute_latlong_special>`.


Édition frontend (FEE)
----------------------

L'édition frontend elle-même n'a pas changé dans MM 2.5 - son périmètre fonctionnel correspond à celui de
MM 2.4. Deux points la concernent toutefois indirectement :

* Les **templates du masque de saisie et de ses widgets** peuvent désormais eux aussi être surchargés sous
  forme de templates Twig - voir la section « Templates Twig ».
* Le **marquage des champs traduits** par un badge coloré derrière le libellé fonctionne sans changement -
  vert pour une traduction propre, orange pour une valeur héritée de la langue de repli, avec la phrase
  explicative en tooltip. Rien ne change pour les rédacteurs.

  Cela a nécessité un travail en coulisses : Contao 5.7 rend les champs de formulaire du frontend via des
  templates Twig qui échappent le libellé - du HTML dans le label y apparaîtrait comme du code source.
  Jusqu'à Contao 5.3, les anciens templates ``.html5`` le produisaient sans modification, ce qui permettait au
  badge de figurer simplement dans le label. Il est désormais produit via un template propre, que MetaModels
  n'affecte qu'aux champs concernés ; les formulaires en dehors de l'édition frontend ne sont pas touchés. Les
  champs encore produits via un template ``.html5`` continuent de recevoir le badge directement dans le label.

Pour l'utilisation de l'édition frontend dans son ensemble : :ref:`rst_extended_frontend_editing`.


Problèmes connus
----------------

* lors du basculement vers/depuis le mode débogage dans le BE via le bouton, la page de référence n'est plus
  correcte et il faut y accéder à nouveau - par exemple avec « retour » dans le navigateur et rechargement de la
  page |br|
  La cause est le lien de bascule généré par Contao ``?do=debug&key=enable&referer=…`` : sur les pages de
  backend MetaModels basées sur des routes (par exemple ``/contao/metamodel/mm_employees``), le paramètre
  ``referer`` reste **vide**, de sorte qu'après la bascule, Contao ramène vers le tableau de bord du backend au
  lieu de la page de départ. Cela concerne le propre bouton de bascule de débogage de Contao et n'est pas pris
  en charge par la nouvelle gestion du Referer du DC_General (ses propres boutons « Retour ») - Contao n'offre
  à cet endroit aucun moyen d'influencer le referer.


.. _check_upgrade_mm250:
Vérifications pour la mise à niveau vers MM 2.5
-----------------------------------------------

En principe, une mise à niveau au sein de la branche MM 2.x est possible sans problème, et les adaptations
nécessaires aux libellés ainsi qu'aux modifications de base de données sont prises en charge par les
migrations. Il y a toutefois quelques éléments qui ne peuvent pas être pris en charge de cette manière, ou
seulement très difficilement. C'est pourquoi, lors du passage à MM 2.5, les points suivants doivent être gardés
à l'esprit :

* veuillez suivre toutes les indications de :ref:`MM 2.4 <check_upgrade_mm240>`
* vérifier les prérequis : **Contao 5.7** et **PHP 8.4**
* les **surcharges de template personnalisées** avec un nom à plat (par exemple
  ``templates/metamodel_prerendered.html5``) continuent de fonctionner, mais devraient être déplacées vers
  ``templates/metamodels/<groupe>/…`` - la priorité des surcharges à plat disparaîtra dans MM 3.0
* pour quiconque fournit ses propres templates Twig : utiliser un dossier ``twig/`` avec le marqueur
  ``.twig-root`` et la structure ``metamodels/<groupe>/<leaf>.html.twig``
* **DC_General :** tenir compte des adaptations à la gestion du Referer et de la suppression du bouton
  « Enregistrer et retour » (saveNback) ; adapter ses propres templates/développements qui s'appuient sur
  ``saveNback``
* **Liens de tri :** quiconque avait surchargé les clés Contao ``MSC.orderMetaModelListByAscending``/
  ``…Descending`` pour le libellé des liens de tri passe à ``sorting_direction_label`` (avec
  ``%attribute_name%`` et ``%direction%``), ``sorting_direction_asc`` et ``sorting_direction_desc`` dans le
  domaine ``metamodels_default``
* **Extensions DC_General personnalisées :** le récepteur d'événements ``WidgetBuilder`` a une signature
  modifiée (``handleEvent()`` n'est plus statique, quatre arguments obligatoires dans le constructeur). Seul
  est concerné qui l'appelle ou l'instancie lui-même - les paquets MetaModels fournis ne le font pas
* **JavaScript backend du DC_General :** quiconque surcharge ses propres templates DC_General, fait tourner
  son propre JavaScript contre leur markup, ou intègre directement les fichiers JS fournis, doit adapter son
  code - MooTools a disparu, les classes marqueurs (``click2edit``, ``picker_selector``, ``sbtog``) et les
  attributs ``onclick`` sont remplacés, les fichiers JS ont été renommés
* **Attribut Levenshtein :** la correction systématique de ``levensthein`` en ``levenshtein`` est effectuée
  par migration, l'index de recherche est conservé. Seul doit adapter son code qui utilise **lui-même** les
  anciens noms : ses propres analyses SQL ou exports sur ``tl_metamodel_levensthein``/
  ``tl_metamodel_levensthein_index`` ou sur les colonnes ``levensthein_distance``/``levensthein_attributes``,
  ainsi que son propre code PHP qui teste en dur le nom de type ``levensthein``. La classe
  ``LevenstheinSearchRule`` est conservée à titre transitoire comme alias *déprécié* et disparaîtra dans MM 3.0
* **Icônes :** les icônes sont désormais fournies en SVG, les fichiers PNG remplacés ont été **supprimés**.
  Rien ne change dans l'utilisation. Seul doit adapter son code qui utilise lui-même les anciens fichiers : son
  propre CSS qui intègre un symbole MetaModels comme image de fond, ou ses propres indications DCA qui pointent
  vers un chemin ``.png`` sous ``bundles/metamodels…/images/``. Il faut y changer l'extension en ``.svg``
* **Attributs Fichier :** les colonnes de tri ``<nom_de_colonne>__sort`` et ``value_sorting`` sont transférées
  dans la valeur par migration, puis **supprimées** - effectuer impérativement une sauvegarde des données
  auparavant, la suppression des colonnes est irréversible. Les développements ou analyses personnalisés qui
  accèdent directement à ces colonnes doivent être adaptés ; l'ordre se trouve désormais dans la valeur
  elle-même. Dans la valeur analysée, les clés existantes ``bin_sorted``/``value_sorted``/``path_sorted``/
  ``meta_sorted`` (Fichier) et ``value_sorting`` (Fichier traduit) sont conservées, elles correspondent
  désormais à leur pendant non trié. La clé ``sort``, déjà marquée obsolète depuis la 2.1, disparaît


Refinancement
-------------
.. seealso:: Pour un refinancement des travaux importants réalisés, l'équipe MM sollicite un soutien financier.
   Comme ordre de grandeur, il convient de se baser sur l'ampleur du projet à réaliser et de prévoir environ 10 %
   de son montant - d'après l'expérience des derniers soutiens reçus, il s'agit de montants compris entre 100 €
   et 500 € (net) - une facture incluant la TVA est bien entendu toujours établie. `En savoir plus...
   <https://now.metamodel.me/de/unterstuetzer/spenden>`_


.. |mm_list_with_icons| image:: /_img/screenshots/new_in_2-5/mm_list_with_icons.png
.. |mm_breadcrumb_icons| image:: /_img/screenshots/new_in_2-5/mm_breadcrumb_icons.png


.. |br| raw:: html

   <br />
