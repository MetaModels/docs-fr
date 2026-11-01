.. _rst_extended_contao-api:

MetaModels Contao-API
=====================

La Contao-API met à disposition les **enregistrements (items) des MetaModels sous forme d'interface REST** - pour les
lire, les créer, les modifier et les supprimer. Elle repose sur le bundle API de Contao 6 (API Platform). Les
MetaModels sont ainsi également accessibles via le :ref:`serveur MCP de Contao <rst_extended_mcp-admin>` et donc par
des agents IA.

L'interface est conçue comme le traitement dans le backend : les mêmes droits, la même validation s'appliquent et les
mêmes événements sont exécutés (versions, journal, index de recherche).

.. note:: L'extension fait partie du **paquet standard** du « programme early adopter » (EAP) - pour en savoir plus,
   voir la rubrique **Fundraising** sur le `site web de MM <https://now.metamodel.me/de/unterstuetzer/fundraising#metamodels_2-6>`_.

.. note:: Le bundle API, le bundle MCP et le serveur OAuth de Contao ne sont pas encore stables : il n'existe
   actuellement que la branche de développement ``dev-main``, l'interface est signalée par Contao comme
   *expérimentale*. Tant que Contao n'a pas publié de versions, les bundles ne sont installables qu'avec un réglage
   supplémentaire dans le ``composer.json`` - `voir ci-dessous <#installation-via-composer>`_.

Prérequis
---------

* MetaModels core à partir de la 2.6
* Contao 6.x et PHP 8.4
* ``contao/api-bundle`` de Contao


Installation via Composer
-------------------------

Tant que ``contao/api-bundle`` n'est pas publié sur Packagist, le dépôt doit être inscrit dans le ``composer.json``
du projet :

.. code-block:: bash

   composer config repositories.contao-api-bundle vcs https://github.com/contao/api-bundle.git
   composer require contao/api-bundle:dev-main metamodels/contao-api

Mettre ensuite la base de données à jour comme d'habitude (Contao-Manager, ``contao-setup`` ou
``php vendor/bin/contao-console contao:migrate``).


Appel de l'interface
--------------------

Chaque MetaModel dispose de sa propre ressource sous

``/contao/api/mm/<nom_de_table>``  resp.  ``/contao/api/mm/<nom_de_table>/<id>``

par ex. ``/contao/api/mm/mm_employees/12``. Les opérations :

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Méthode
     - Fonction
   * - ``GET`` (liste)
     - Liste des items. Avec ``page`` et ``itemsPerPage``, on feuillette les pages.
   * - ``GET`` (``…/<id>``)
     - Lire un item.
   * - ``POST``
     - Créer un item (type de contenu ``application/ld+json``).
   * - ``PATCH``
     - Modifier un item - seuls les champs qui changent sont envoyés (type de contenu
       ``application/merge-patch+json``).
   * - ``DELETE``
     - Supprimer un item.

La description de toutes les ressources au format OpenAPI, avec tous les attributs des MetaModels, se trouve sous
``/contao/api`` - l'interface Swagger y est également accessible.


Authentification par token
--------------------------

Pour un accès sans navigateur, on utilise un **Personal Access Token** de Contao. Il est créé dans le module backend
*Sécurité* (bouton de création d'un token, avec nom et date d'expiration). Le token commence par ``ct_pat_`` et
n'est affiché **qu'une seule fois**. Il est valable pour l'utilisateur du backend qui l'a créé.

Le token est envoyé à chaque requête comme Bearer-Token :

.. code-block:: bash

   curl -H "Authorization: Bearer ct_pat_…" \
        "https://example.org/contao/api/mm/mm_employees?itemsPerPage=10"

   curl -X PATCH -H "Authorization: Bearer ct_pat_…" \
        -H "Content-Type: application/merge-patch+json" \
        -d '{"name": "Nouveau nom"}' \
        "https://example.org/contao/api/mm/mm_employees/12"

.. warning:: Un token donne les mêmes droits que l'utilisateur dans le backend. Il n'a sa place ni dans un dépôt ni
   dans des fichiers qui sont transmis. Un token qui n'est plus nécessaire est supprimé dans le backend.


Droits
------

Les droits suivent ceux du backend :

* Un utilisateur a besoin, pour le MetaModel, d'un **masque de saisie** (affectation du masque de saisie aux
  utilisateurs resp. groupes dans les :ref:`affectations saisie/rendu <component_dca-combine>`). Sans accès,
  l'interface répond par ``403``, et le MetaModel n'apparaît pas dans la description OpenAPI ni dans le MCP pour cet
  utilisateur.
* Seuls les **attributs du masque de saisie** sont visibles et inscriptibles.
* Les réglages du masque de saisie s'appliquent également ici : *lecture seule*, *champ obligatoire* ainsi que les
  droits de création, de modification et de suppression d'enregistrements.
* Les types d'attribut *Token*, *Géodistance* et *Levenshtein* ne sont pas fournis.


Lecture
-------

Les valeurs sont converties, par type d'attribut, dans un format JSON approprié, par ex. les nombres en nombre, les
tags et les sélections multiples en liste et les fichiers en liste d'UUID. La réponse est une Hydra-Collection avec
``totalItems`` et les liens vers les pages.

Les attributs de type *Horodatage* (date, heure) sont fournis et attendus sous forme de **timestamp Unix** : un entier en
secondes, par ex. ``1798675200`` pour le 31.12.2026, 00:00 UTC.


MetaModels multilingues
-----------------------

Pour les MetaModels comportant plusieurs langues, la langue est indiquée avec le paramètre de requête ``lang`` :

* **Écriture** (``POST``, ``PATCH``, ``DELETE``) : ``lang`` est **obligatoire**, sinon la langue de repli serait
  écrasée sans que l'on s'en aperçoive. Sans ``lang`` ou avec une langue inconnue, l'interface répond par ``400``.
* **Lecture** : ``lang`` est optionnel (``GET …?lang=en``), sans indication, la langue active est utilisée.


Écriture
--------

L'enregistrement se fait toujours via le MetaModel lui-même (comme dans le backend), pas directement dans la base de
données. Avant l'enregistrement, le contenu est vérifié :

* Les champs inconnus ou non inscriptibles, les types erronés et les champs obligatoires manquants sont rejetés avec
  ``422`` et un message concernant le champ en cause.
* Les **liaisons** sont résolues avant l'enregistrement : un alias inconnu dans une sélection, des tags inconnus ou
  des colonnes d'une table ne sont **pas écartés silencieusement**, mais rejetés.
* Les **fichiers** sont indiqués par UUID (pas de chemins). Le fichier doit exister, se trouver dans le dossier défini
  dans l'attribut et avoir une extension autorisée.
* Après l'enregistrement, une **version** est créée comme dans le backend (uniquement si l'enregistrement a changé) -
  avec le nom de l'utilisateur connecté. Les versions sont visibles dans le backend et peuvent y être restaurées.
  La suppression ne crée pas de version.
* Les actions sont écrites dans le **journal système** comme dans le backend, si la journalisation est activée dans
  le MetaModel.
* L'**index de recherche** de la recherche backend de Contao est mis à jour (uniquement si ``contao.backend_search``
  est activé et que le worker tourne), de même que l'index du :ref:`filtre Loupe <rst_extended_loupe>`. Une erreur à
  cette occasion n'interrompt pas l'enregistrement, elle est consignée dans le journal comme avertissement.
* Les **attributs dérivés** (alias, valeurs combinées, token, index du filtre Levenshtein) sont créés lors de la création
  d'un item, même s'ils ne figurent pas dans le masque de saisie et que la requête ne les mentionne pas. L'alias est
  formé à partir des champs source de l'attribut, comme dans le backend. Cela nécessite MetaModels à partir de 2.6 (ou
  2.5.4) : auparavant, ``saveItem()`` n'enregistrait pour un nouvel item que les attributs définis, de sorte qu'un item
  créé via l'API ou un script n'avait pas d'alias. Pour un item existant, le réglage de l'attribut s'applique, comme
  dans le backend.
* Le **cache HTTP** est informé comme dans le backend : après l'enregistrement et la suppression, les tags de cache de
  la table (``contao.db.<table>``) et de l'item (``contao.db.<table>.<id>``) expirent. Les listes de MetaModels (module
  et élément de contenu) portent les deux tags, afin que les nouveaux items, les items qui ne correspondent au filtre
  qu'après une modification, et les listes vides expirent aussi dans le cache. Sans cache HTTP prenant en charge les
  tags, rien ne se passe.

D'autres extensions peuvent réagir à l'enregistrement et à la suppression : l'interface déclenche pour cela les
événements Symfony ``metamodels.contao-api.item-saved`` (arguments ``metamodel`` et ``created``) et
``metamodels.contao-api.item-deleted`` (argument ``metamodel``). L'item est à chaque fois le sujet de l'événement.


Variantes
---------

Pour les MetaModels avec variantes, les items possèdent les champs ``varbase`` (``true`` pour un item de base) et
``vargroup`` (l'ID de l'item de base).

* Une **nouvelle variante** est créée lorsque, lors du ``POST``, l'ID de l'item de base est indiqué comme
  ``vargroup``. Elle démarre comme copie de la base et ne peut contenir que les attributs de variante.
* Les **attributs partagés** sont modifiés sur l'item de base et transmis aux variantes.
* Le ``vargroup`` d'un item existant ne peut pas être modifié.
* Lors de la suppression d'un item de base, les variantes sont supprimées avec lui.


Champs avec le MultiColumnWizard
--------------------------------

Le bundle API de Contao ne connaît pas le MultiColumnWizard. Le bundle ``menatwork/contao-multicolumnwizard-bundle``
apporte donc son propre convertisseur (à partir de la version 3.8, actuellement la branche ``develop``), qu'il ne charge
que si le bundle API est actif. Ainsi, les champs avec ce widget apparaissent dans les ressources de Contao
(``/contao/api/dc/…``), par exemple les paramètres d'un module ou d'un élément de contenu MetaModels :

* Un champ est une **liste d'objets**, une propriété par colonne. Chaque colonne est convertie comme le widget qu'elle
  utilise dans le backend (texte, sélection, fichier, etc.). ``minCount`` et ``maxCount`` du widget valent comme nombre
  minimal et maximal de lignes.
* Lors de l'écriture, le widget vérifie chaque cellule comme dans le backend (par ex. champs obligatoires) ; les erreurs
  figurent dans la réponse avec le chemin de la cellule (``422``).
* Ne sont pas pris en charge les champs dont les colonnes proviennent d'une fonction (``columnsCallback``) et les champs
  avec une colonne pour laquelle il n'existe pas de convertisseur. Ils manquent dans l'interface afin qu'aucune valeur
  ne se perde lors de l'écriture.
* Si une colonne est en lecture seule, tout le champ est en lecture seule : lors de l'ajout, de la suppression ou du
  réordonnancement de lignes, la valeur de cette colonne ne pourrait pas être conservée.


Accès en lecture public
-----------------------

Contao lui-même n'offre pas encore d'interface pour le frontend : toutes les ressources du bundle API se trouvent sous
``/contao/api`` et exigent un utilisateur backend. Pour les pages qui doivent lire des MetaModels en JSON sans connexion
(par ex. avec Vue ou Stimulus), l'extension apporte donc un **accès en lecture seule** qui **ne passe pas par API
Platform** et se situe en dehors du backend :

* ``GET /api/mm/<table>`` renvoie une liste, ``GET /api/mm/<table>/<id>`` un seul item.
* **Rien n'est public tant que ce n'est pas dans la configuration.** Pour chaque MetaModel, un **réglage de rendu** et un
  **réglage de filtre** sont indiqués :

  .. code-block:: yaml

     # config/config.yaml
     meta_models_contao_api:
         public:
             mm_evenements:
                 render_setting: 35   # id du réglage de rendu
                 filter: 16           # id du réglage de filtre
                 sort_by: startdate   # facultatif, par défaut : id
                 sort_direction: ASC  # facultatif
                 per_page: 30         # facultatif, limite supérieure max_per_page (par défaut 100)
                 cache_ttl: 3600      # facultatif, secondes dans le cache partagé

* Les **attributs** du réglage de rendu sont ceux qui sont fournis, sous forme de textes rendus, tels qu'une liste du
  frontend les affiche (par ex. une date comme date et une sélection avec son libellé).
* Le **réglage de filtre** est obligatoire. Il limite les items (par ex. avec la règle *Publié*, sinon les items non
  publiés seraient aussi visibles) et définit les paramètres acceptés comme paramètres de requête. Les autres paramètres
  sont ignorés. Un item unique doit satisfaire le même filtre que la liste, sinon l'interface répond par ``404``.
* La liste connaît ``page``, ``itemsPerPage`` et, pour les MetaModels multilingues, ``lang``. La réponse contient
  ``total``, ``page``, ``itemsPerPage`` et ``items``. Les erreurs sont également renvoyées en JSON.
* Les réponses sont autorisées pour le cache HTTP et marquées avec ``contao.db.<table>`` et
  ``contao.db.<table>.<id>``. L'enregistrement d'un item dans le backend ou via l'interface les fait expirer.
* Il n'y a pas d'en-tête CORS : la page qui récupère les données se trouve sur le même hôte.

Exemple : affichage avec Vue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Une liste avec recherche, pagination et vue détaillée ne nécessite qu'un peu de HTML et de JavaScript qui récupère le
JSON. L'exemple de l'équipe est construit comme partie de la structure de pages, pas comme un fichier HTML fini :

* **Une page** (par ex. « API Demo ») dans l'arborescence des pages avec un élément de contenu **HTML**. Comme template
  de l'élément, on choisit une variante propre, ``ce_html_api_demo_events`` (fichier
  ``templates/ce_html_api_demo_events.html.twig``). Elle contient le balisage, les styles et le script. Le template par
  défaut de l'élément supprimerait ``<script>``, la variante restitue son contenu tel quel. Le champ *HTML* de l'élément
  reste donc vide ou reçoit un commentaire.
* **La bibliothèque** (``vue.global.prod.min.js``) se trouve dans un sous-dossier public de ``files/`` (par ex.
  ``files/api-demo/``). Le dossier doit être **public** : dans le gestionnaire de fichiers, activer l'interrupteur
  *Public*, ce qui crée le fichier ``.public``. Ensuite, exécuter ``contao:symlinks``. Ainsi, l'exemple n'a pas besoin
  de CDN.
* **Les expressions de Vue** s'écrivent avec ``v-text`` et des liaisons (``:href``), pas avec ``{{ … }}`` : Contao prend
  ces accolades pour des balises d'insertion.
* **Liens avec ``#`` :** Contao place ``<base href>`` dans la page. Un lien constitué uniquement de ``#/4`` mène donc à la
  page d'accueil. L'exemple fait précéder le chemin de la page actuelle (``window.location.pathname``).
* La page appelle l'interface sur le même hôte, sans connexion :

  .. code-block:: javascript

     const query = new URLSearchParams({ page: 1, itemsPerPage: 5, q: 'api' });
     const response = await fetch('/api/mm/mm_evenements?' + query);
     const { total, items } = await response.json();

     // Vue détaillée d'un item
     const item = await (await fetch('/api/mm/mm_evenements/4')).json();

* ``q`` et d'autres paramètres sont les paramètres du réglage de filtre (dans l'exemple une recherche textuelle dans le
  titre et une dans le lieu). Un item non publié renvoie ``404``, l'exemple affiche alors un message au lieu de la page
  de détail.

.. warning:: Tout ce qui figure dans le réglage de rendu et que le filtre laisse passer est accessible à tous. Un
   attribut qui ne doit pas être public n'a rien à faire dans le réglage de rendu.


Accès pour les membres connectés
--------------------------------

Les membres du site peuvent lire, créer, modifier et supprimer leurs **propres enregistrements** via la même interface,
par exemple pour une édition frontend avec Vue ou Stimulus. Le core de Contao ne peut (pas encore) émettre de token pour
les membres, c'est donc la **session du membre connecté** qui s'applique : la page appelle l'interface sur le même hôte,
le navigateur envoie le cookie de session.

* Les adresses : ``/api/member/mm/<table>`` (liste avec ``GET``, création avec ``POST``) et
  ``/api/member/mm/<table>/<id>`` (``GET``, ``PATCH``, ``DELETE``). ``GET /api/member/me`` indique le nom d'utilisateur,
  sans connexion toutes les adresses répondent par ``401``.
* Ici aussi, **rien n'est accessible tant que ce n'est pas dans la configuration** :

  .. code-block:: yaml

     # config/config.yaml
     meta_models_contao_api:
         member:
             - mm_evenements

* Les droits sont ceux de l':ref:`édition frontend <rst_extended_frontend_editing>` : le **masque de saisie** affecté
  aux groupes de membres (:ref:`affectations saisie/rendu <component_dca-combine>`) détermine quels attributs les
  membres voient et écrivent et s'ils peuvent créer, modifier et supprimer des enregistrements.
* Avec le réglage **Vérification des droits** du masque de saisie (attribut pour le membre : une sélection simple sur
  ``tl_member`` avec l'alias ``username``), les membres ne voient et ne modifient que les enregistrements dans lesquels
  ils sont désignés comme propriétaires. Les enregistrements d'autrui répondent par ``404``. Un enregistrement créé
  appartient au membre connecté, le contenu de la requête ne peut pas désigner un autre propriétaire. Sans ce réglage,
  tous les membres du groupe peuvent modifier tous les enregistrements, comme dans l'édition frontend.
* L'écriture fonctionne comme avec l'interface du backend (validation des valeurs, versions, index de recherche,
  cache). Les erreurs dans les valeurs arrivent avec ``422`` et une liste des problèmes. L'alias d'un nouvel
  enregistrement est créé automatiquement, même si l'attribut ne figure pas dans le masque de saisie des membres.

.. warning:: Une affectation pour le groupe de membres ``*`` s'applique à **chaque** membre et l'emporte par sa
   position. Quiconque crée une ligne distincte pour les utilisateurs backend lui attribue donc un groupe de membres dans
   lequel aucun membre ne se trouve (par ex. « Invités »), afin que les membres obtiennent leur propre masque de saisie.

L'exemple pour les membres est une **sous-page d'une page protégée** (dans le test « API Demo FEE » sous « Mon compte
TEST », protégée pour le groupe de membres). La sous-page hérite de la protection, seuls les membres connectés y
accèdent. Comme la page publique, elle comporte un élément de contenu **HTML** avec une variante de template
(``ce_html_api_demo_member``) qui restitue le formulaire de création et de modification et la liste des propres
enregistrements. Si la session expire, la page affiche une indication avec le lien vers la connexion.

Contre l'injection de requêtes par des sites tiers (CSRF), chaque requête modifiante exige ``Content-Type:
application/json`` et l'en-tête ``X-Requested-With``. Les deux déclenchent sur un site tiers une vérification
préalable que l'interface n'autorise pas (il n'y a pas d'en-tête CORS). Un token n'est pas nécessaire pour cela. Les
réponses ne sont pas destinées au cache partagé.


Limitations connues
-------------------

* Les types d'attribut inconnus (par ex. issus d'extensions) sont fournis avec la valeur du widget et un schéma non
  typé.
* Les traductions des attributs de variante ne sont pas copiées depuis la base lors de la création d'une variante.
* Les bundles de Contao ne sont pas encore stables (voir ci-dessus). L'extension est donc étroitement liée à l'état
  actuel et peut encore changer jusqu'à la publication des bundles de Contao.
