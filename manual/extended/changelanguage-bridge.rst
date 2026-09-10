.. _rst_extended_changelanguage-bridge:

ChangeLanguage-Bridge pour MetaModels
=======================================

Rend l'extension `« ChangeLanguage » <https://github.com/terminal42/contao-changelanguage>`_ sensible à
l'item sur les pages de détail d'un MetaModel : au lieu de revenir à la page d'accueil de la langue lors
du changement de langue, le sélecteur de langue renvoie directement vers le même enregistrement dans la
langue cible - y compris les paramètres de filtre correspondants (par ex. l'alias).

L'extension couvre deux cas distincts, voir ":ref:`rst_extended_changelanguage-bridge_slug-get`"
plus bas pour la distinction :

* **Attribut traduit** (la valeur de filtre diffère selon la langue, par ex.
  ``adam-de-adonis-de`` vs. ``adam-en-adonis-en``) : une case à cocher « Prendre en charge le sélecteur
  de langue » par paramétrage de rendu est nécessaire, voir
  ":ref:`rst_extended_changelanguage-bridge_aktivierung`".
* **Paramètre de filtre GET, même pour des modèles monolingues** (par ex. ``?alias=...``) :
  fonctionne automatiquement sans aucune configuration, voir
  ":ref:`rst_extended_changelanguage-bridge_get-auto`".

Plus d'informations sur :ref:`le multilinguisme dans MetaModels <component_multi-language>`, en
particulier la section ":ref:`component_multi-language_fe-output`" - les deux solutions de
contournement précédentes (règle de filtre « Rechercher dans toutes les langues » ou un hook
``changelanguageNavigation`` propre) y sont également décrites ; elles deviennent superflues avec
cette extension.


Prérequis
---------

* à partir de MetaModels 2.5
* `terminal42/contao-changelanguage <https://github.com/terminal42/contao-changelanguage>`_
* uniquement pour le cas « attribut traduit » : une ligne « Sauter vers la page » propre par langue,
  avec le réglage de filtre correspondant, dans le paramétrage de rendu - la configuration habituelle
  pour des liens de saut multilingues. Pour le cas GET, aucune configuration particulière du
  paramétrage de rendu n'est nécessaire.


Installation via Contao-Manager ou Composer
---------------------------------------------

.. code-block:: bash

   composer require metamodels/changelanguage-bridge


.. _rst_extended_changelanguage-bridge_slug-get:

Comment ChangeLanguage traite les paramètres de slug et GET
----------------------------------------------------------------

Le fait que `« ChangeLanguage » <https://github.com/terminal42/contao-changelanguage>`_ reprenne de
lui-même un paramètre de filtre lors du changement de langue dépend de la façon dont il figure dans
l'URL - c'est un comportement propre à ChangeLanguage, indépendant de cette extension ou de
MetaModels en général :

* **En tant que segment de chemin** (URL « dossier » de Contao, par ex. ``/alias/hihi-huhusss-2``) :
  ``ChangeLanguageModule::createUrlParameterBag()`` lit chaque paire ``/clé/valeur/`` de la requête
  actuelle et la reprend sans autre configuration dans l'URL cible. **Aucune** configuration n'est
  nécessaire pour cela - tant que la valeur est aussi valide dans la langue cible (voir plus bas).
* **En tant que paramètre GET** (par ex. ``?alias=hihi-huhusss-2``) : la même méthode ne reprend que
  ce qui est explicitement saisi comme nom dans le champ de page « Conserver les paramètres de
  requête » (``tl_page.languageQuery``). Sans entrée, le paramètre est perdu lors du changement de
  langue, et le sélecteur de langue atterrit alors sur la simple page cible sans lien avec
  l'enregistrement.

Le paramètre ``auto_item`` de Contao (le cas sans paramètre, par ex. ``/hihi-huhusss-2`` sans clé
précédente) est explicitement exclu de la reprise par segment de chemin -
``createUrlParameterBag()`` le retire à nouveau. Un paramètre de filtre destiné à être repris lors
du changement de langue ne doit donc pas être saisi comme ``auto_item``, mais nécessite un véritable
nom de paramètre d'URL (par ex. « alias »).

Cela explique également pourquoi un MetaModel **monolingue**, dont la page de détail utilise le
même alias dans chaque racine de langue, fonctionne dans le cas du segment de chemin sans cette
extension : il n'y a pas de valeur spécifique à la langue à traduire, et ChangeLanguage reprend déjà
de lui-même la valeur identique. Ce n'est que lorsque la valeur diffère selon la langue (attribut
traduit) ou que le paramètre est transmis en GET que l'une des deux sections suivantes devient
pertinente.


.. _rst_extended_changelanguage-bridge_get-auto:

Paramètres GET automatiques (indépendamment de la case à cocher)
-----------------------------------------------------------------

Cette extension résout automatiquement le cas GET ci-dessus, sans aucune case à cocher « Prendre en
charge le sélecteur de langue » ni entrée manuelle dans « Conserver les paramètres de requête » :
pour la page actuelle, on détermine quel élément de contenu MetaModels (ou module inclus) y filtre
des enregistrements, et lesquels de ses paramètres de filtre sont déclarés comme « GET » (ou le
réglage tolérant « Slug ou GET »). Leur valeur actuelle est transmise telle quelle au sélecteur de
langue.

Volontairement séparé de la traduction d'item de la section suivante : cette partie ne traduit rien,
elle ne fait que transmettre la valeur brute - ce qui est toujours correct pour un modèle
monolingue (valeur identique dans chaque langue). Si, pour le même paramétrage de rendu, « Prendre
en charge le sélecteur de langue » est en plus activé et fournit déjà une valeur traduite
spécifique à la langue, celle-ci est prioritaire et n'est pas écrasée par la transmission
automatique.


.. _rst_extended_changelanguage-bridge_aktivierung:

Activation pour les attributs traduits
-----------------------------------------

Pour chaque paramétrage de rendu dont la cible de saut doit prendre en charge le sélecteur de langue
avec l'enregistrement traduit, l'option **« Prendre en charge le sélecteur de langue »** est cochée
dans la zone « Sauter vers la page » - désactivée par défaut, afin qu'un hook
``changelanguageNavigation`` propre déjà existant pour le même paramétrage de rendu n'entre pas en
conflit.


Fonctionnement
---------------

Lors de la construction du sélecteur de langue, l'extension vérifie d'elle-même si la page actuelle
est la cible de saut d'un paramétrage de rendu dont l'option est activée. Si c'est le cas,
l'enregistrement actuellement affiché est déterminé, la langue du MetaModel est basculée sur la
langue cible respective, et la page cible ainsi que le slug pour cette langue sont déterminés via la
même fonction interne que MetaModels utilise par ailleurs pour générer ses liens de saut. Le
paramétrage de rendu avec sa configuration de filtre reste ainsi le seul endroit où la cible de saut
est maintenue.
