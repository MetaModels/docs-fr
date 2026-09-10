.. _component_multi-language:

Multilinguisme dans MetaModels
==============================

MetaModels est très bien adapté aux contenus multilingues. MM propose pour les contenus
multilingues des attributs spécifiques comme par ex. `Texte traduit`, `Alias traduit`, `Fichier
traduit`, etc. Pour les attributs dont les valeurs sont indépendantes d'une langue, comme par ex.
les valeurs numériques, les ID de produits, etc., ces variantes n'existent pas.

Le multilinguisme dans MetaModels est conçu de telle sorte que les champs multilingues soient
remplis aussi bien dans la langue de repli (fallback) que dans les traductions souhaitées. Si cela
n'est pas le cas pour un champ, la valeur de repli est automatiquement affichée en frontend. Dans
le template, il n'est pas possible de distinguer si une valeur provient de la traduction ou du
repli.

Avant de commencer la création des Models, il convient de bien réfléchir si les contenus doivent
être stockés de manière multilingue. Les contenus multilingues sont stockés dans des tables
séparées et non dans la table propre ``mm_*``, de sorte qu'un passage ultérieur au multilinguisme
implique une reprise correspondante des données.

Si l'on a des valeurs monolingues que l'on souhaite stocker, mais qui doivent être affichées en
frontend dans différentes langues, un passage au multilinguisme n'est pas critique. Seules les
désignations des attributs sont alors étendues selon les langues -
:ref:`voir ci-dessous « Attributs » <component_multi-language_attribute>`.


Models
------

Le multilinguisme d'un Model est défini lors de sa création. En activant l'option « Traduction »,
on peut gérer une liste des langues à maintenir. Si l'on active également l'option « Support pour
l'indication de territoire dans la langue », les compléments territoriaux comme ``de_DE``,
``de_AT``, ``de_CH``, etc. sont également disponibles dans la liste en plus des langues
principales comme ``de``, ``en``, ``fr``.

Une langue doit être définie comme langue de repli (fallback), pour laquelle tous les
enregistrements doivent toujours être présents. Si la langue de repli est modifiée ultérieurement,
cela doit être vérifié et corrigé en conséquence dans la BD.

Si l'on crée plusieurs Models qui sont en outre liés par des relations, il est conseillé de définir
le même schéma de langues et la même langue de repli pour tous les Models. Il est également
judicieux de ne créer que les langues que Contao a définies via ses points de départ.

Les MetaModels multilingues sont mis en évidence par un drapeau de pays coloré |img_locale|.


.. _component_multi-language_save:
Stockage en base de données
----------------------------

Les valeurs traduites sont stockées dans des tables séparées ``tl_metamodel_translated*`` - selon
le type d'attribut, il peut s'agir de tables différentes. Les valeurs n'apparaissent donc pas dans
la table individuelle du Model créé ``mm_*``. Les tables de traduction possèdent une référence à
l'attribut ``att_id`` et à l'enregistrement ``item_id``, ainsi que l'indication de la langue
stockée ``langcode``.

Les données dans la langue de repli définie doivent être présentes, afin qu'une sortie puisse
également avoir lieu en l'absence de traduction. Depuis MM 2.4, la langue de repli est signalée
dans le masque de saisie par « |img_fallback| » aussi bien dans le sélecteur de langue que dans le
titre.

Si l'on passe, dans le masque de saisie, de la langue de repli à une langue de traduction, les
saisies de la langue de repli s'affichent dans les champs de texte. Cela facilite la traduction des
textes. Pour en savoir plus sur ce marquage, voir la section
":ref:`component_multi-language_input`".

.. note:: Si un texte de repli n'est pas traduit, il n'est pas non plus enregistré dans la langue
   de traduction. Il faut particulièrement en tenir compte lorsqu'un terme est identique dans la
   langue de repli et dans la langue de traduction, comme par ex. « Marketing » en anglais et en
   allemand. |br|
   Si, après une traduction, une saisie est de nouveau remplacée par le contenu de repli, l'entrée
   pour la langue de traduction est de nouveau supprimée dans la base de données.

.. note:: À partir de MM 2.4, lors de la copie d'un enregistrement, toutes les langues sont
   copiées avec lui -
   `voir l'issue <https://github.com/MetaModels/core/issues/598#issuecomment-1912422061>`_

.. note:: Lorsque des Models ou attributs multilingues sont supprimés, tous les contenus ne sont
   pas automatiquement supprimés avec eux - :ref:`indications ici pour vérifier et supprimer
   <rst_cookbook_specials_delete-superfluous-data>`

Depuis MM 2.4, il existe le paramètre « Désactiver le mode fallback » - lorsqu'il est actif, une
valeur pour une langue est tout de même enregistrée, même si elle est identique à la valeur de
repli. Cette option est par ex. activée pour l'attribut :ref:`Case à cocher traduite
<component_attribute_translatedcheckbox>`.

.. _component_multi-language_attribute:
Attributs
---------

Si l'option « Traduction » est activée pour un Model, les variantes multilingues sont également
disponibles lors de la création des attributs. Selon l'installation, cela peut être les suivantes :

* :ref:`Alias traduit <component_attribute_translatedalias>`
* :ref:`Case à cocher traduite <component_attribute_translatedcheckbox>`
* :ref:`Fichier traduit <component_attribute_translatedfile>`
* :ref:`Tableau multi (MCW) traduit <component_attribute_translatedtablemulti>`
* :ref:`Tableau de texte traduit <component_attribute_translatedtabletext>`
* :ref:`URL traduite <component_attribute_translatedurl>`
* :ref:`Entrées combinées traduites <component_attribute_translatedcombinedvalues>`
* :ref:`Contenu traduit d'un article <component_attribute_translatedcontentarticle>`
* :ref:`Texte long traduit <component_attribute_translatedlongtext>`
* :ref:`Texte traduit <component_attribute_translatedtext>`
* :ref:`Sélection simple traduite [select] <component_attribute_translatedselect>` |*note|
* :ref:`Sélection multiple traduite [tags] <component_attribute_translatedtags>` |*note|

|*note| : les **attributs non traduits sélection simple et sélection multiple prennent en charge
nativement le multilinguisme** pour les relations vers des tables MetaModel. Les deux attributs
mentionnés ici sont destinés à des cas particuliers, comme par ex. les relations vers des tables
non-MM avec des contenus multilingues. Ces tables doivent cependant posséder une colonne avec la
clé de langue. On peut également utiliser pour cela des tables MetaModel monolingues avec un
attribut pour la clé de langue. Il est ainsi possible, pour chaque langue, non seulement de fournir
une traduction correspondante, mais aussi des contenus complètement différents. Par exemple, une
randonnée pourrait aller « vers la gauche » pour les visiteurs anglophones et « vers la droite »
pour les visiteurs germanophones.

Pour un MetaModel multilingue, un champ est disponible par langue pour les champs « Nom » et
« Description » dans toutes les définitions d'attribut - la langue de repli est mise en évidence.
Ces indications traduites sont automatiquement affichées dans le masque de saisie dans la langue
sélectionnée si la langue de backend correspondante est choisie dans le profil utilisateur.

De plus, dans le :ref:`template du rendering <component_templates_fe-list>`, la valeur traduite
« Nom » peut être récupérée via le nœud ``attributes`` - la langue de la sortie frontend de Contao
ou la valeur de repli est automatiquement affichée. Dans le template, une sortie pourrait par ex.
ressembler à ceci :

.. code-block:: html
   :linenos:

    <p><strong><?= $arrItem['attributes']['name'] ?>:</strong> <?= $arrItem['text']['name'] ?></p>
    <p><strong><?= $arrItem['attributes']['city'] ?>:</strong> <?= $arrItem['text']['city'] ?></p>
    <p><strong><?= $arrItem['attributes']['description'] ?>:</strong> <?= $arrItem['text']['description'] ?></p>

Cela permet une gestion pratique des « labels » multilingues dans un template.


.. _component_multi-language_input:
Masque de saisie / saisie
--------------------------

Dans le masque de saisie du backend, les widgets des attributs multilingues portent une icône de
drapeau colorée |img_locale| pour les distinguer. Le changement de langue s'effectue directement
dans l'en-tête du masque de saisie. La langue de repli est signalée en conséquence aussi bien dans
le sélecteur de langue que dans le titre.

Lors de la création d'un nouvel enregistrement, c'est toujours la langue de repli qui est remplie
en premier - si une autre langue que la langue de repli est sélectionnée dans le masque, l'affichage
bascule sur la langue de repli lors de l'enregistrement.

.. warning:: L'enregistrement d'un enregistrement ou d'une saisie ne se fait pas automatiquement
   lors du basculement vers une autre langue - avant de changer de langue, les saisies doivent être
   sauvegardées avec « Enregistrer » !

.. note:: Les affichages suivants ont été mis en place ou adaptés dans MM 2.4.

Une fois les champs remplis et enregistrés dans la langue de repli, il est possible de passer à
n'importe quelle autre langue. Dans les champs multilingues, le contenu de la langue de repli est
d'abord visible. De plus, l'indication |img_fallback| s'affiche à côté du titre tant qu'aucun
contenu différent des valeurs de repli n'a été enregistré. Cela permet de mieux identifier le
statut de la traduction.

Ces contenus de repli affichés ne sont toutefois pas enregistrés dans la BD sous la langue de
traduction correspondante lors de l'enregistrement - voir
":ref:`component_multi-language_save`".

Si des contenus sont créés et enregistrés dans la langue de traduction, l'indication bascule sur
|img_translated| pour les champs de saisie correspondants.

Selon le statut d'un champ de saisie, cela peut par ex. ressembler à ceci :

|translation-hints|

**Extensions pour la traduction :**

Pour les **traductions continues**, l'extension :ref:`rst_extended_xliff_ex-import` est recommandée.
L'échange s'effectue ici via le
`format XLIFF <https://de.wikipedia.org/wiki/XML_Localization_Interchange_File_Format>`_.

Les fichiers sont exportés via l'extension puis réimportés après la traduction - des agences ou
outils de traduction correspondants peuvent être intégrés pour la traduction.

Pour la **traduction dans le backend**, l'extension :ref:`rst_extended_translator-bridge` est
disponible ; elle intègre différents fournisseurs de traduction comme `DeepL
<https://www.deepl.com>`_. Une traduction peut être effectuée pour chaque champ de saisie, ou via
un raccourci clavier pour le masque de saisie actif de la langue sélectionnée.


Vue en liste du BE
-------------------

Dans la vue en liste du backend, il existe également un sélecteur de langue dans l'en-tête - si des
attributs multilingues sont affichés dans la liste, leur affichage correspond alors à la langue
sélectionnée.


Filtre
------

La plupart des règles de filtre effectuent la recherche dans la langue qui est actuellement la
langue (Contao) active en frontend. Pour certaines règles de filtre comme ":ref:`Requête simple
<component_filter_simplelookup>`", ":ref:`Sélection simple <component_filter_select>`",
":ref:`Sélection multiple <component_filter_tags>`", ":ref:`Filtre de texte
<component_filter_text>`", il existe l'option « Rechercher dans toutes les langues », pour autant
qu'un attribut multilingue ait été sélectionné. Cette option peut par ex. être utilisée sur la page
de détail (voir ci-dessous).

Pour l'attribut ":ref:`Case à cocher traduite <component_attribute_translatedcheckbox>`", il existe
une règle de filtre propre : ":ref:`État de la case à cocher traduite
<component_filter_translated-checkbox>`".

Pour la règle de filtre ":ref:`Recherche assistée par Levenshtein
<component_attribute_levenshtein>`", il est également possible de sélectionner des attributs
multilingues dans le réglage d'attribut « Attributs à indexer ».

La règle de filtre ":ref:`Recherche plein texte assistée par Loupe <rst_extended_loupe>`" prend
actuellement en charge les attributs multilingues Texte et Texte long.


.. _component_multi-language_fe-output:
Liste FE / vue détaillée
--------------------------

En frontend, les contenus des attributs sont affichés dans les templates de la même manière que
pour les attributs monolingues. De même, les désignations d'attributs sont fournies au template
sous forme traduite.

.. note:: Si un contenu n'est pas traduit, le contenu de la langue de repli est diffusé - cela ne
   vaut pas seulement pour les attributs avec saisies de texte, mais aussi pour des attributs comme
   fichier traduit ou contenu traduit d'un article.

Cela signifie que si, par ex., aucun fichier spécifique à la langue n'est sélectionné pour la
langue de traduction, tous les fichiers de la langue de repli sont affichés.

La langue dont provient le contenu peut être interrogée dans le nœud ``raw`` du :ref:`tableau de
sortie <component_templates_fe-list>`, au niveau du nœud ``langcode``.

Si l'on a une page de détail sur laquelle on souhaite généralement afficher un enregistrement via
son alias, on doit pouvoir passer à la page de détail dans une autre langue via le sélecteur de
langue.

Des extensions comme `« ChangeLanguage » <https://github.com/terminal42/contao-changelanguage>`_ ne
« voient » que la page créée dans Contao - par ex. ``https://my-domain.tld/en/dessert/details`` -
sans l'alias du filtrage.

Pour transmettre à l'extension la valeur pour les autres langues et filtrer en conséquence,
plusieurs possibilités existent - la moins contraignante étant l'extension
:ref:`metamodels/changelanguage-bridge <rst_extended_changelanguage-bridge>` décrite plus bas.

**1. Règle de filtre « Requête simple » avec l'option « Rechercher dans toutes les langues »**

Il faut d'abord relier toutes les pages de détail des différentes langues via les propriétés de
page - bouton « Page dans la langue principale ». De plus, le « paramètre URL » de la règle de
filtre doit être renseigné dans le champ « Conserver les paramètres de requête » (alias). Le
paramètre URL ne doit pas être « auto_item », car ChangeLanguage ne peut pas fonctionner avec
celui-ci.

De plus, la règle de filtre ":ref:`Requête simple <component_filter_simplelookup>`" est créée ou
adaptée. Le paramètre URL ne doit pas être défini comme ``auto_item`` et l'option « Rechercher dans
toutes les langues » doit être activée. Le filtrage peut ainsi s'effectuer avec toutes les variantes
linguistiques, donc avec

``https://my-domain.tld/en/dessert/details/alias/marinated-strawberries`` ainsi qu'avec |br|
``https://my-domain.tld/de/dessert/details/alias/marinated-strawberries`` ou bien

``https://my-domain.tld/en/dessert/details/alias/marinierte-erdbeeren`` ainsi qu'avec |br|
``https://my-domain.tld/de/dessert/details/alias/marinierte-erdbeeren``.


**2. Hook « changelanguageNavigation »**

Si l'on ne souhaite pas activer l'option « Rechercher dans toutes les langues » ou si l'on souhaite
travailler avec ``auto_item`` comme « paramètre URL », on peut, pour le sélecteur de langue
« ChangeLanguage », remplacer pour chaque langue le paramètre de filtre (par ex. alias) de manière
adaptée via un hook - `voir la documentation
<https://extensions.terminal42.ch/docs/changelanguage/en/developers/#rewriting-an-url-parameter>`_

Comme point de départ, l'extrait suivant : il faut vérifier si l'on se trouve sur la page de détail
correspondante, par ex. ID 3, 15, 36 pour les différentes pages de langue. Avec la valeur actuelle
du paramètre de filtre et la langue actuelle, la valeur correspondante pour l'autre langue
respective peut être déterminée. Cette requête dépend de la construction respective des MetaModels.
Le hook est appelé une fois pour chaque langue dans le sélecteur de langue.

.. code-block:: php

   public function __invoke(ChangelanguageNavigationEvent $event)
   {
       // ...

       // Right page?
       $listPageIds = [3, 15, 36];
       if (!\in_array($targetPageId = $event->getNavigationItem()->getTargetPage()->id, $listPageIds, true)) {
           return;
       }

       // Get alias value.
       $currentAliasValue = Input::get('auto_item');

       // Search for attribute value in target language.
       // $newAliasValue = ....

       // Set alias value for target language/page.
       $event->getUrlParameterBag()->setUrlAttribute('auto_item', $newAliasValue);
   }

Avec cette variante, les indications pour ``hreflang`` dans les métadonnées sont également
correctement définies - :ref:`voir SEO <rst_cookbook_tips_seo_metadata-hreflang>`.


**3. Extension « ChangeLanguage-bridge » (à partir de MM 2.5)**

L'extension :ref:`metamodels/changelanguage-bridge <rst_extended_changelanguage-bridge>` décharge
du hook de la variante 2 : il suffit de cocher « Prendre en charge le sélecteur de langue » pour
chaque paramétrage de rendu, le paramètre de filtre correspondant à la langue cible respective est
alors déterminé automatiquement à partir de la configuration de saut déjà maintenue pour le
paramétrage de rendu - aucune liste fixe d'ID de pages ni de code PHP propre n'est nécessaire.

De plus, la même extension reprend automatiquement les paramètres de filtre GET (par ex.
``?alias=...``) dans le sélecteur de langue, sans cette case à cocher - une entrée manuelle dans
« Conserver les paramètres de requête » comme dans la variante 1 devient ainsi également superflue
pour les modèles monolingues. La raison pour laquelle ChangeLanguage reprend de lui-même les
segments de chemin (``/alias/...``) mais pas les paramètres GET est expliquée sous
« :ref:`rst_extended_changelanguage-bridge_slug-get` ».


Édition en frontend (FEE)
---------------------------

Avec MM 2.4, le multilinguisme est également pris en charge pour l'édition en frontend -
:ref:`en savoir plus sur ce sujet <extended_frontend_editing_multilanguage>` - y compris
l'affichage de la langue de repli et le statut de traduction - voir :ref:`édition en frontend
<extended_frontend_editing_multilanguage>`.


Personnalisation des traductions
-----------------------------------

Les textes de MetaModels, comme par ex. le libellé des boutons, peuvent être remplacés par des
textes personnalisés - pour en savoir plus, voir ":ref:`component_translations_modifications`".



.. |img_locale| image:: /_img/icons/locale.png
.. |img_fallback| image:: /_img/icons/fallback.png
.. |img_translated| image:: /_img/icons/translated.png
.. |translation-hints| image:: /_img/screenshots/components/translation-hints.png

.. |br| raw:: html

   <br />

.. |*note| raw:: html

   <strong>*</strong>
