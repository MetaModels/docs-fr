.. _rst_extended_mcp-admin:

MetaModels MCP-Administration
=============================

Avec l'administration MCP, un **agent IA** (par ex. Claude ou Codex) peut **consulter et configurer** MetaModels via
le serveur MCP de Contao : il apprend quels composants existent, vérifie quels paquets sont nécessaires pour une
tâche et, sur demande, crée un MetaModel avec attributs, masques de saisie, réglages de rendu et filtres.

MCP (*Model Context Protocol*) est un standard ouvert par lequel les programmes d'IA communiquent avec des
applications. Contao 6 apporte pour cela son propre serveur MCP (``/contao/mcp``) ; l'administration MCP l'étend avec
la connaissance de MetaModels. Les **enregistrements** des MetaModels sont accessibles via la
:ref:`Contao-API <rst_extended_contao-api>`, qui est également proposée via ce serveur.

.. note:: L'extension fait partie du **paquet standard** du « programme early adopter » (EAP) - pour en savoir plus,
   voir la rubrique **Fundraising** sur le `site web de MM <https://now.metamodel.me/de/unterstuetzer/fundraising#metamodels_2-6>`_.

.. note:: L'extension est dans un état précoce (*early*). Les bundles de Contao sur lesquels elle repose (API, MCP,
   serveur OAuth) ne sont pas encore stables - il n'existe que la branche de développement ``dev-main``.

.. warning:: L'agent agit avec les droits de l'utilisateur du backend qui l'a connecté. **L'écriture de la
   configuration est donc désactivée par défaut** et prévue pour les systèmes de développement - pas pour les sites
   en production.

Prérequis
---------

* MetaModels core à partir de la 2.6
* Contao 6.x et PHP 8.4
* ``contao/mcp-bundle`` et ``contao/oauth-server-bundle`` de Contao (avec le ``contao/api-bundle``)
* pour l'accès aux enregistrements, en plus :ref:`metamodels/contao-api <rst_extended_contao-api>`
* Un compte administrateur dans le backend - tous les outils exigent le rôle ``ROLE_ADMIN``


Installation via Composer
-------------------------

Tant que les bundles de Contao ne sont pas publiés sur Packagist, les dépôts doivent être inscrits dans le
``composer.json`` du projet :

.. code-block:: bash

   composer config repositories.contao-api-bundle vcs https://github.com/contao/api-bundle.git
   composer config repositories.contao-mcp-bundle vcs https://github.com/contao/mcp-bundle.git
   composer config repositories.contao-oauth-server-bundle vcs https://github.com/contao/oauth-server-bundle.git
   composer require contao/api-bundle:dev-main contao/mcp-bundle:dev-main contao/oauth-server-bundle:dev-main
   composer require metamodels/contao-api metamodels/mcp-admin

Mettre ensuite la base de données à jour (Contao-Manager, ``contao-setup`` ou
``php vendor/bin/contao-console contao:migrate``) - le serveur OAuth crée alors la table ``oauth_server_token``.

.. note:: Les adresses sous ``/.well-known/`` doivent être transmises à Contao par le serveur web. Certaines
   configurations bloquent tous les fichiers commençant par un point - l'authentification de l'agent est alors
   impossible (le serveur répond par ``403``).


Connecter un agent
------------------

Le serveur MCP a l'adresse ``https://<domaine>/contao/mcp``. L'authentification passe par OAuth : l'agent ouvre le
backend ; après la connexion, Contao demande *« Connect … with Contao? »* et signale que l'agent peut faire tout ce que
l'utilisateur peut faire dans le backend. Avec l'accord, l'agent reçoit un token qu'il renouvelle lui-même.

Avec Codex, cela se présente ainsi :

.. code-block:: bash

   codex mcp add contao --url https://example.org/contao/mcp
   codex mcp login contao

Avec Claude Code, le serveur est enregistré comme serveur HTTP. On lance la connexion dans le programme avec ``/mcp`` :
choisir le serveur, *Authenticate*, se connecter dans le navigateur et accepter.

.. code-block:: bash

   claude mcp add --transport http contao https://example.org/contao/mcp

Contao n'autorise que les clients qui s'identifient avec un document public (*Client ID Metadata Document*) et dont
le domaine est autorisé. Sont préconfigurés ``chatgpt.com`` (Codex), ``claude.ai`` et ``vscode.dev``. D'autres
clients sont autorisés avec :

.. code-block:: yaml

   # config/config.yaml
   contao_oauth_server:
       cimd_trusted_domains: ['example.org']

Le serveur récupère lui-même le document du client sur Internet - le système a besoin pour cela d'une connexion
sortante. L'authentification et le déroulement ont été testés avec Codex et avec Claude Code (lire, écrire, planifier,
appliquer et annuler).

.. note:: Pour l'appel de ``contao_api_execute`` (lire et écrire des données), Codex demande une confirmation à chaque
   appel, car l'outil n'est pas marqué comme *en lecture seule*.


Ce que l'agent sait faire
-------------------------

L'administration MCP complète le serveur de Contao par les éléments suivants :

.. list-table::
   :header-rows: 1
   :widths: 35 15 50

   * - Nom
     - Type
     - Fonction
   * - ``metamodels://guide/overview``
     - Ressource
     - Guide : les composants d'un MetaModel, l'ordre de création et comment trouver les paquets nécessaires.
   * - ``metamodels://catalog/types``
     - Ressource
     - Tous les types d'attribut et de filtre connus avec paquet, brève description, disponibilité et indication
       s'ils sont installés ici.
   * - ``metamodels://catalog/articles``
     - Ressource
     - Pages du manuel par thème (par ex. relations, multilinguisme) et recettes du cookbook.
   * - ``metamodels://schema/{kind}/{type}``
     - Ressource
     - Les réglages que connaît un type : saisie, règles, valeurs par défaut, valeurs de sélection et libellés.
   * - ``metamodels_search_catalog``
     - Outil
     - Trouve des types, des extensions et des pages du manuel pour une tâche, à partir de quelques mots dans
       n'importe quelle langue.
   * - ``metamodels_list``
     - Outil
     - Tous les MetaModels avec table, langues et nombre d'attributs, de masques de saisie, de réglages de rendu et de
       filtres.
   * - ``metamodels_describe``
     - Outil
     - La configuration d'un MetaModel : attributs, masques de saisie avec champs et conditions d'affichage,
       réglages de rendu, filtres.
   * - ``metamodels_check_types``
     - Outil
     - Vérifie les types dont une tâche a besoin : les types manquants sont indiqués avec la commande
       ``composer require``.
   * - ``metamodels_plan``
     - Outil
     - Vérifie la description d'un MetaModel et montre les lignes et commandes de base de données. N'écrit rien.
   * - ``metamodels_apply``
     - Outil
     - Écrit les lignes planifiées dans une transaction. Nécessite l'interrupteur ``write``.
   * - ``metamodels_migrate_schema``
     - Outil
     - Crée les tables et colonnes du MetaModel. Nécessite l'interrupteur ``write``.
   * - ``metamodels_journal``
     - Outil
     - Montre ce que ``metamodels_apply`` a créé et modifié.
   * - ``metamodels_undo``
     - Outil
     - Annule une opération d'écriture. Nécessite l'interrupteur ``write``.

L'installation de paquets reste toujours du ressort de l'humain : l'agent apprend ce qui manque et indique la
commande - Composer n'est pas exécuté via MCP.

Exemples de demandes à l'agent :

* *« Quels MetaModels existent et quels attributs possède le MetaModel Collaborateurs ? »*
* *« J'ai besoin d'un MetaModel pour des événements avec titre, date, lieu et un prix. De quels paquets ai-je besoin ? »*
* *« Crée le MetaModel et montre-moi d'abord le plan. »*
* *« Montre-moi tous les collaborateurs du service Ventes. »* (via la Contao-API)

Un exemple détaillé figure dans le cookbook : :ref:`Gestion d'événements avec un agent IA <rst_cookbook_mcp_event-management>`.


Vérifier les paquets et l'accès
-------------------------------

Tous les paquets ne sont pas librement disponibles - de nombreuses extensions sont encore en phase de fundraising.
L'agent obtient ces informations à partir des métadonnées (voir ci-dessous) : pour un paquet du programme early
adopter, il ne recommande pas de ``composer require``, mais indique la page où l'on obtient l'accès.


Écrire la configuration
-----------------------

L'écriture est prévue pour les systèmes de développement et désactivée par défaut :

.. code-block:: yaml

   # config/config.yaml
   meta_models_mcp_admin:
       write: true

Le déroulement comprend trois étapes :

1. ``metamodels_plan`` reçoit la description (MetaModel, attributs, masques de saisie, réglages de rendu, filtres et
   l'affectation au backend) et la vérifie comme le backend : types installés, noms de tables et de colonnes,
   réglages du type. La réponse contient les problèmes ou les lignes planifiées avec un ``plan_id``.
2. ``metamodels_apply`` écrit les lignes dans une transaction - uniquement pour le ``plan_id`` du plan actuel.
3. ``metamodels_migrate_schema`` exécute les commandes de base de données de Contao pour les tables de ce MetaModel
   (``mm_<nom>`` et ``mm_<nom>_*``). Les commandes qui suppriment, renomment ou vident quelque chose ne sont jamais
   exécutées.

Un MetaModel a besoin d'un masque de saisie et de réglages de rendu affectés aux utilisateurs du backend pour pouvoir
être ouvert dans le backend. Les deux sont créés automatiquement pour le premier MetaModel.

Si le MetaModel doit apparaître dans le backend dans un **groupe de navigation propre**, l'agent renseigne la zone du
backend sur le masque de saisie. Le groupe lui-même est de la configuration (``meta_models_core.be_sections`` dans
``config/config.yaml``, voir :ref:`Section propre dans la navigation du backend <rst_cookbook_tips_backend-section>`) et
l'agent ne peut pas l'écrire. Il indique ce qu'il faut saisir. Ensuite, vider le cache. Sans le groupe, le MetaModel
n'apparaît pas dans la navigation.

Pour les **cases à cocher**, l'agent règle lui-même deux choses :

* Le champ d'une nouvelle case à cocher dans le masque de saisie reçoit les classes CSS ``w50 cbx m12``, afin qu'il soit
  aligné avec les champs voisins. Une ``tl_class`` indiquée dans les réglages du champ les remplace.
* Si un filtre utilise la case à cocher comme « publié » (règle ``checkbox_published`` ou
  ``translatedcheckbox_published``), l'agent active sur l'attribut l'option **Icône de bascule** (``check_publish``). La
  liste du backend affiche alors l'œil habituel pour basculer. Avec ``"check_publish": false`` dans les réglages de
  l'attribut, elle reste désactivée. Cela vaut aussi pour un attribut existant auquel l'option manque encore.

Un nouvel attribut **alias** (``alias`` et ``translatedalias``) démarre, comme dans le backend, avec les options
**Valeurs uniques** (``isunique``) et **Toujours mettre à jour** (``force_alias``, ``force_talias`` pour l'alias
traduit). Dans un MetaModel avec variantes, l'agent ne les active pas, car les valeurs uniques n'y sont pas possibles.
Avec ``false`` dans les réglages de l'attribut, une option reste désactivée. Les autres types d'attribut ne reçoivent
pas ces valeurs par défaut.

Un champ du masque de saisie peut être lié à une :ref:`condition d'affichage <component_dca_visibility-conditions>`, par
ex. la date de fin d'un événement uniquement si la case « Sur plusieurs jours » est cochée. L'agent la crée sur le champ
(``conditions``). Les règles suivantes s'appliquent :

* Les conditions d'un niveau sont combinées avec ET, un wrapper ``conditionand`` n'est pas nécessaire.
* Le champ de l'attribut auquel se rapporte la condition (ici « Sur plusieurs jours ») nécessite l'option « Enregistrer lors de
  la modification ». Sans elle, le backend n'affiche l'effet qu'après l'enregistrement. Le planificateur le signale.
* Les types proviennent du core : *La valeur de la propriété est*, *La propriété contient l'un de*, *La propriété est
  visible* ainsi que les conteneurs *ET*, *OU* et *NON*.

Après la création, la Contao-API connaît immédiatement le MetaModel. Pour qu'il apparaisse dans le frontend, l'agent
crée une page, un article et un élément de contenu de type *Liste MetaModel* (``metamodel_content``).

La description est l'**état souhaité**, y compris pour l'existant : le plan montre chaque ligne comme *créer*,
*modifier* (avec les nouvelles et les anciennes valeurs) ou *existante*. Seul ce que la description mentionne est
modifié ; tout le reste demeure inchangé.

Non pris en charge (signalé comme problème) : un autre type ou une autre colonne pour un attribut existant, un autre
type ou un autre attribut pour une règle de filtre ou une condition d'affichage existante, ajouter des langues, basculer les variantes et modifier
l'ordre. Rien n'est supprimé - sauf lors de l'annulation d'une opération d'écriture.


Annuler
-------

La plupart des tables de configuration ne connaissent pas de versions dans le backend. C'est pourquoi chaque
``metamodels_apply`` écrit un **journal** (un fichier JSON dans ``var/mm_mcp_admin/journal``) avec les lignes créées et
modifiées - dans la même transaction : si le journal ne peut pas être écrit, rien de l'écriture ne subsiste.

``metamodels_undo`` supprime les lignes créées et restaure les valeurs des lignes modifiées. Sans ``confirm``, il
montre seulement ce qui se passerait. Il refuse l'annulation et ne modifie rien lorsque

* une ligne a été modifiée depuis l'écriture,
* quelque chose a été ajouté en dessous (un attribut, un champ, une règle de filtre) ou
* un MetaModel doit être supprimé alors que sa table contient des enregistrements.

Les tables et colonnes subsistent, la mise à jour de la base de données ne supprime rien. Un journal ne peut être
annulé qu'une seule fois. Toutes les opérations d'écriture figurent dans le journal système de Contao.


Templates
---------

Les templates ne sont pas écrits par l'administration MCP, mais par le serveur MCP de Contao avec ses outils de
template (``contao_template_*``) : trouver et lire des templates, vérifier le code, déterminer les utilisateurs d'un
template, créer et enregistrer un override, ainsi que créer, comparer et restaurer des points de restauration. L'agent
peut ainsi aussi surcharger les templates de MetaModels, par exemple un bloc de la navigation de pages
(``mm_pagination``) ou du filtre (``mm_filter_default``). Les :ref:`blocs Twig <component_templates_twig-blocks>` de ces
templates restent accessibles dans l'override.

Important :

* L'agent crée un **point de restauration** avant chaque modification. Il vaut pour tout le répertoire ``templates/`` et
  se trouve dans le cache de l'application - ce n'est pas une sauvegarde durable.
* La vérification du code montre seulement que le template peut être compilé. Si la sortie est correcte, seule la page
  dans le frontend le montre.
* L'analyse des utilisateurs ne trouve pas les templates de MetaModels : MetaModels les charge depuis PHP, pas via Twig.
  L'agent ne signale alors que les références non résolubles. Quelles listes utilisent un template doit être vérifié
  dans le frontend.


Base de connaissances (métadonnées)
-----------------------------------

Un paquet non installé ne peut pas indiquer quels types il apporterait. Les informations sur les paquets et leurs
types d'attribut et de filtre proviennent donc d'un fichier de métadonnées du dépôt
`MetaModels/mcp-metadata <https://github.com/MetaModels/mcp-metadata>`_ :

* par paquet : s'il est librement disponible ou dans le programme early adopter, où l'on obtient l'accès, une brève
  description, des mots-clés et la page dans le manuel,
* par type : le paquet, une brève description, des mots-clés dans les langues des utilisateurs et la page du manuel,
* par article : une page du manuel sur un thème ou une recette du cookbook avec titre, chemin et mots-clés
  - uniquement un renvoi, le texte reste dans le manuel.

Le fichier est publié sous ``https://metamodels.github.io/mcp-metadata/metadata.json``. L'extension le récupère de là
(uniquement via https), le conserve un jour et mémorise la dernière version valide. Si le serveur n'est pas
joignable, le fichier livré avec l'extension est utilisé.

Un système sans accès à Internet, ou qui n'a pas le droit d'envoyer de telles requêtes, désactive la récupération :

.. code-block:: yaml

   meta_models_mcp_admin:
       metadata:
           url: null        # Standard: 'https://metamodels.github.io/mcp-metadata/metadata.json'
           ttl: 86400       # secondes, durée de conservation du fichier

.. note:: Les métadonnées sont des données, pas des instructions : les textes avec balisage ou de plus de 240
   caractères et les liens ne commençant pas par ``https`` sont écartés à la lecture.


Configuration
-------------

.. code-block:: yaml

   # config/config.yaml
   meta_models_mcp_admin:
       write: false                     # autoriser l'écriture de la configuration (par défaut : false)
       journal_dir: '%kernel.project_dir%/var/mm_mcp_admin/journal'
       metadata:
           url: 'https://metamodels.github.io/mcp-metadata/metadata.json'
           ttl: 86400

``journal_dir`` définit où se trouvent les journaux des opérations d'écriture. Avec ``write``, l'écriture est
autorisée.


Conseils et questions fréquentes
--------------------------------

Un exemple complet pas à pas figure dans le cookbook sous
:ref:`Gestion d'événements avec un agent IA <rst_cookbook_mcp_event-management>`.

**Comment procéder au mieux ?**

* Par petites étapes, en vérifiant le résultat après chaque étape (backend, frontend).
* Faire d'abord **planifier** l'agent (« Planifie …, n'écris encore rien »), lire et corriger le plan, et ne l'appliquer
  qu'ensuite.
* Dire ce qui ne doit **pas** se produire (« Les pages et les enregistrements viennent à l'étape suivante »).
* Faire indiquer le ``journal_id`` à l'agent après chaque opération d'écriture.
* Le faire poser des questions en cas de doute : « Là où quelque chose n'est pas possible avec les outils, dis-le-moi. »

**L'agent ne trouve pas les outils.**
Certains agents ne chargent les outils qu'après quelques secondes. Dans le prompt, on nomme le serveur (par ex.
« utilise le serveur MCP contao ») et on indique son adresse. Si la connexion a expiré, une nouvelle connexion aide (avec
Codex ``codex mcp login contao``).

**L'agent signale qu'un type n'est pas installé.**
Il manque alors un paquet, par exemple pour le multilinguisme (``attribute_translated*``). L'agent nomme le paquet et
l'accès. On l'installe soi-même et on fait revérifier les types avec ``metamodels_check_types``. L'agent ne doit pas se
rabattre sur une variante qui manque la tâche (par ex. monolingue au lieu de multilingue).

**Faut-il dire « multilingue » ?**
Oui, avec les langues et la langue par défaut (« DE, EN, FR - DE est la langue par défaut »). Cela inclut quels champs
doivent être traduits et lesquels valent pour toutes les langues (par ex. les champs de contact).

**Que ne peut pas faire l'agent ?**

* Adapter des templates uniquement avec les outils de template de Contao (voir ci-dessus), pas avec l'administration
  MCP (par ex. un badge « Annulé » dans la sortie),
* modifier l'ordre des attributs et des champs, supprimer quelque chose et renommer des colonnes,
* ajouter des langues et basculer les variantes,
* installer des paquets.

Il doit le dire et indiquer une voie dans le backend. Sinon, on le lui demande.

**Les données sont dans le backend, mais les liens de détail manquent.**
MetaModels génère l'alias lors de l'enregistrement dans le backend. Quiconque crée des enregistrements via la Contao-API
doit fournir la valeur de l'alias. Les attributs dont l'alias est issu (par ex. titre et date) devraient être des champs
obligatoires, sinon l'alias est incomplet et change plus tard (voir :ref:`Liste avec page de détail
<rst_cookbook_frontend_list-with-detail-page>`).

**La liste affiche aussi des entrées non publiées.**
Le filtre a besoin de la règle *État de la case à cocher* (paquet ``attribute_checkbox``) sur l'attribut « publié ». La
règle « Oui / Non » (``filter_checkbox``) est un widget pour les visiteurs et ne filtre que lorsque quelqu'un l'utilise.

**Que se passe-t-il si la réponse de l'agent change après une mise à jour ?**
L'aide (``metamodels://guide/overview``) et les remarques du planificateur sont chargées au début de la conversation. On
demande à l'agent de les recharger après une mise à jour du système ou de l'extension.

**Est-ce adapté aux sites en production ?**
Non, l'écriture est prévue pour les systèmes de développement. L'agent agit avec les droits de l'utilisateur backend.
