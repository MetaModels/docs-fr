.. _rst_cookbook_frontend_list-with-detail-page:

Liste avec page de détail
=========================

Une sortie typique se compose d'une page de liste et d'une page de détail : la liste contient un lien par entrée, la
page de détail affiche l'entrée unique avec toutes ses informations. Les étapes suivantes valent aussi bien pour un
MetaModel monolingue que multilingue - pour un MetaModel multilingue, il y a simplement les variantes des attributs et
une page par langue.

Un exemple : une gestion d'événements avec les attributs titre, date de début et « publié ».

.. contents::
   :local:
   :depth: 1


1. L'alias comme clé unique
---------------------------

L'entrée de la page de détail est retrouvée grâce à un alias présent dans l'URL, par ex.
``/evenement/forum-du-sport-2026-11-07``. Pour cela, on crée un attribut :ref:`Alias <component_attribute_alias>`
- pour un MetaModel multilingue :ref:`Alias traduit <component_attribute_translatedalias>`.

Quatre réglages sont importants :

* **Champs pour l'alias** : titre et date de début. La date permet de distinguer l'alias même si deux événements ont le
  même titre.
* **Champs obligatoires** : les attributs dont l'alias est issu sont réglés sur « Champ obligatoire » dans le masque de
  saisie. Si l'un d'eux reste vide, l'alias est incomplet et change dès qu'il est rempli plus tard - et avec lui le lien
  vers la page de détail.
* **Valeurs uniques** : l'option des réglages généraux de l'attribut garantit que l'alias n'existe qu'une seule fois.
* **Forcer la régénération de l'alias** : l'alias est regénéré à chaque enregistrement. Sans cette option, un alias une fois
  attribué reste en place même si le titre change.

.. note:: L'alias est créé lors de l'enregistrement dans le backend. Quiconque crée des entrées autrement (par ex. via
   l':ref:`API Contao <rst_extended_contao-api>`) doit fournir la valeur de l'alias.


2. Deux filtres
---------------

La liste et la page de détail ont chacune besoin de leur propre filtre. Les règles d'un niveau sont toujours combinées
avec ET. Il n'est donc **pas** nécessaire de créer une règle « ET » et d'y ranger les autres règles - cela n'est
nécessaire que pour un groupe à l'intérieur d'une règle « OU ». Cela vaut aussi pour les conditions d'affichage du
masque de saisie.

**Filtre « Liste »**

* Règle :ref:`État de la case à cocher <component_attribute_checkbox>` sur l'attribut « publié » : seules les entrées
  dont la case est cochée apparaissent.
* À côté, les règles que les visiteurs utilisent, par ex. catégorie, mots-clés et plage de dates (chacune avec un
  paramètre d'URL).

**Filtre « Détail »**

* Règle :ref:`État de la case à cocher <component_attribute_checkbox>` sur l'attribut « publié » : ainsi, une entrée
  passée à « non publié » n'est plus accessible, même via un lien enregistré en favori.
* Règle :ref:`Requête simple <component_filter_simplelookup>` sur l'attribut « Alias » avec le paramètre d'URL
  ``auto_item``. L'URL ne contient ainsi que la valeur de l'alias, sans clé.

.. warning:: Pour « uniquement les entrées publiées », on utilise la règle « État de la case à cocher » du paquet
   ``attribute_checkbox`` et non la règle « Oui / Non » (paquet ``filter_checkbox``). La règle « Oui / Non » est un
   widget pour les visiteurs : elle ne filtre qu'une fois qu'un choix est fait et place ``published/1`` dans chaque URL
   générée par le filtre - les liens vers la page de détail se terminent alors aussi par ``/published/1``.


3. Réglages de rendu
--------------------

Au moins trois :ref:`réglages de rendu <component_rendersettings>` ont fait leurs preuves :

* **Liste backend** : les valeurs par défaut de la liste et des attributs suffisent. Si la liste doit apparaître sous
  forme de tableau, on active l'option « Affichage sous forme de tableau » dans le :ref:`masque de saisie
  <component_dca>`.
* **Liste frontend** : les attributs qui doivent figurer dans la liste (par ex. titre, image d'aperçu, date, heure,
  lieu).
* **Détail** : toutes les informations de l'entrée.

Pour la sortie dans le frontend, il faut le plus souvent des templates adaptés, voir :ref:`Templates
<rst_cookbook_templates_index>` : le template du réglage de rendu (par ex. ``metamodel_prerendered``), le template de
l'élément de liste (``ce_metamodel_list``) et, le cas échéant, les templates d'attribut ``mm_attr_*``. La taille d'une
image d'aperçu n'est pas non plus définie dans MetaModels mais dans Contao sous *Thèmes > Tailles d'image*. Dans le
réglage de rendu, on ne fait que la sélectionner.


4. Page de redirection dans la liste
------------------------------------

Dans le réglage de rendu de la liste, on définit la **page de redirection** : la page de détail et le **filtre de la
page de détail** (pour un MetaModel multilingue, une ligne par langue). À partir du filtre, MetaModels génère les liens
de la liste : la valeur de l'alias est insérée dans l'URL de la page de détail.


5. Pages et éléments de contenu
-------------------------------

* **Page de liste** : un article avec l'élément de contenu « MetaModel Filtre » (si les visiteurs doivent pouvoir
  filtrer) et en dessous l'élément de contenu « MetaModel Liste ». Dans la liste, on choisit le MetaModel, le réglage
  de rendu de la liste, le filtre de la liste et le tri (par ex. par date de début décroissante).
* **Page de détail** : une page, le plus souvent absente de la navigation, avec l'élément de contenu « MetaModel
  Liste » avec le réglage de rendu et le filtre de la page de détail.

La liste et la page de détail utilisent la même technique, seuls le filtre et le réglage de rendu diffèrent.


6. Vérification
---------------

* Dans la liste, les liens pointent vers ``/<page de détail>/<alias>`` et non vers
  ``/<page de détail>/<alias>/published/1``.
* La page de détail d'une entrée affiche toutes les informations.
* Un alias inconnu et une entrée non publiée renvoient une page vide (« Il n'y a aucune entrée … »).
* Dans un MetaModel multilingue, cela vaut pour chaque langue et chaque page séparément.
