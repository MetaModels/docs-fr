.. _rst_cookbook_mcp_event-management:

Créer une gestion d'événements avec un agent IA
===============================================

**État : 03.11.2026**

Cette recette montre comment un agent IA met en place, via :ref:`MCP <rst_extended_mcp-admin>`, une gestion d'événements
complète : MetaModels multilingues, masque de saisie, réglages de rendu, filtres, pages et données d'exemple. C'est un
**déroulé de conversation par étapes**, pas un script : l'agent travaille mieux quand on le guide par petites étapes et
qu'on vérifie le résultat après chacune. La construction manuelle d'une liste et d'une page de détail est décrite dans
la recette :ref:`Liste avec page de détail <rst_cookbook_frontend_list-with-detail-page>`.

.. note:: Les réponses dépendent de l'agent et du modèle de langage et seront différentes de celles décrites ici. Les
   prompts sont des modèles à adapter et ne garantissent pas un résultat précis.

.. contents::
   :local:
   :depth: 1


Prérequis
---------

* L'agent est connecté au serveur MCP et l'écriture est activée, voir
  :ref:`Administration MCP <rst_extended_mcp-admin>`. L'écriture est désactivée par défaut parce que l'agent agit avec
  les droits de l'utilisateur backend - elle est prévue pour les systèmes de développement et de test, pas pour un site
  en production.
* Dans le premier prompt, on indique l'adresse du site et du serveur MCP - par ex. ``https://example.org/`` et
  ``https://example.org/contao/mcp``.
* Avec Claude, on lance de préférence l'agent dans la **console** (``claude`` dans le dossier du projet) et on vérifie
  avant le premier prompt, avec ``/mcp``, que le serveur indique *connected* et *authenticated*. L'application de bureau
  a perdu la connexion à plusieurs reprises lors du test, voir :ref:`Administration MCP <rst_extended_mcp-admin>`. Un
  ``codex mcp login contao`` (Codex) ou ``/mcp`` (Claude) suffit lorsque l'authentification a expiré.
* L'agent a besoin de **toute la mission dans la session**. Si l'on démarre une nouvelle session après une interruption,
  on redonne le souhait de l'étape 1 et les décisions issues du plan.
* Si les pages doivent être créées en plusieurs langues, Contao a besoin d'une **page d'accueil** (page racine) par
  langue. Sans elle, l'agent ne crée pas de pages pour cette langue et pose la question - les textes des événements
  sont néanmoins multilingues. On crée la page d'accueil au préalable ou on la fait créer par l'agent dans une étape
  distincte.


1. Décrire le souhait et le faire vérifier
------------------------------------------

On décrit ce dont on a besoin et on fait d'abord **vérifier** l'agent, pas construire :

.. code-block:: text

   Je souhaite créer une gestion d'événements avec les fonctions suivantes :

   * Multilingue avec DE, EN, FR - DE est la langue par défaut
   * Affectation à une catégorie : Conférence, Atelier, Séminaire
   * Statut de l'événement : publié et annulé
   * Affectation à un ou plusieurs mots-clés : Sport, Musique, Théâtre, développement personnel, Écologie
   * Champs d'un événement : titre, description, image, date, sur plusieurs jours, heure, lieu, prix,
     nombre max. de participants, informations de contact
   * Si sur plusieurs jours, il doit aussi y avoir une date de fin
   * Une liste des événements publiés avec titre, image (miniature), date, heure, lieu,
     triée par date décroissante
   * Filtrage par catégorie, par mot-clé, par plage de dates de à
   * Lien vers les détails de l'événement - avec une page propre affichant tous les détails de l'événement

   Contao tourne sur https://example.org/, le serveur MCP sur https://example.org/contao/mcp
   Vérifie d'abord mes indications et dis-moi comment nous pourrions procéder.

**À vérifier :** l'agent signale-t-il des paquets manquants ? Le multilinguisme nécessite par ex.
``attribute_translatedtext`` et ``attribute_translatedalias``. L'agent nomme les paquets et l'accès (beaucoup font
partie du programme early adopter) et ne les installe pas lui-même. On les installe, on met à jour la base de données
et on fait revérifier les types par l'agent avec ``metamodels_check_types`` avant de passer à l'étape 2. Une solution de
repli monolingue n'est pas une option, car elle manque la tâche.

L'agent pose-t-il des questions en cas d'ambiguïté (par ex. « statut de l'événement ») ? Dit-il ce qu'il ne peut pas
faire ? Pour la condition « date de fin uniquement si sur plusieurs jours », il crée une :ref:`condition d'affichage
<component_dca_visibility-conditions>`. Il doit la mentionner dans le plan.


2. Faire planifier, ne rien écrire
----------------------------------

.. code-block:: text

   Planifie les MetaModels (n'écris rien) et montre-moi le plan et les commandes de base de données.

Le plan nomme les tables, attributs, masque de saisie, réglages de rendu et filtres et contient des remarques du
planificateur. C'est le bon moment pour des corrections :

.. code-block:: text

   Modifie le plan : la table principale s'appelle mm_event_events, afin que tous les MetaModels aient le préfixe
   mm_event_. Au lieu d'un modèle auxiliaire pour le statut, prends deux cases à cocher published et cancelled.
   published pilote ce que la liste affiche (filtre), cancelled n'est qu'une indication dans la sortie. Le prix est
   un nombre décimal (type decimal), max_participants de type numeric. Le lieu est traduit (translatedtext), les
   champs de contact restent identiques pour toutes les langues. Planifie aussi le masque de saisie, le réglage de
   rendu et le filtre (catégorie, mot-clé, date de-à) et montre-moi la modification.

**À vérifier :** les noms de tables, les types et les traductions sont-ils corrects ? Le plan montre chaque ligne comme
*créer*, *modifier* ou *existante* - seul ce qu'il mentionne est modifié.


3. Appliquer et créer les tables
--------------------------------

.. code-block:: text

   Applique les plans pour les catégories, les mots-clés et les événements et crée les tables. Montre-moi le
   résultat avec metamodels_describe pour les trois et donne-moi le journal_id de chaque opération d'écriture.
   Les pages et les enregistrements viennent à l'étape suivante.

L'agent applique les MetaModels l'un après l'autre : d'abord les catégories et les mots-clés, puis les événements, car
ceux-ci y font référence. S'il signale qu'un plan est périmé, il planifie à nouveau - c'est normal et ce n'est pas une
erreur.

**À vérifier :** les trois MetaModels sont-ils visibles dans le backend et peuvent-ils être ouverts ? Sinon,
l'affectation au backend manque - on demande alors à l'agent de vérifier et de compléter les ``assignments`` avec
``metamodels_describe``.

Dans le backend, la date de fin d'un nouvel événement n'apparaît-elle que lorsqu'on clique sur « Sur plusieurs jours » ?
Cela suppose que la case à cocher ait « Enregistrer lors de la modification », le planificateur le signale. Si la condition
manque, on demande à l'agent de l'ajouter, par ex. « La date de fin ne doit apparaître que si Sur plusieurs jours est
coché. Planifie d'abord et montre-moi le plan. » Si le titre et la date de début, dont l'alias est issu, ne sont pas des
champs obligatoires, le planificateur le signale aussi.


4. Pages, articles et éléments de contenu
-----------------------------------------

.. code-block:: text

   Crée maintenant les pages, dis-moi brièvement avant ce que tu crées. Une page avec la liste des événements (filtre
   et liste, par date de début décroissante, uniquement les événements publiés) et une page de détail pour un
   événement. La page de détail est accessible via le lien de la liste. Utilise pour cela les outils contao_api_*.
   Les enregistrements viennent à l'étape suivante. Là où quelque chose n'est pas possible avec les outils, dis-le-moi.

.. tip:: Si l'agent cherche un outil dans la Contao-API, un **seul mot au singulier** aide comme terme de recherche
   (par ex. « page » au lieu de « créer des pages »).

S'il existe déjà des pages pour la liste et le détail (par ex. d'un essai précédent), l'agent les réutilise au lieu de
créer des doublons, et le dit. Il vaut mieux savoir au préalable quelles pages existent, sinon on ne sait plus ce qui
vient de lui (important pour l'annulation, voir l'étape 7). Les filtres se trouvent avant la liste dans le même article,
la page de détail est masquée et exige un élément (``requireItem``). Les filtres passent par l'**adresse**
(``/categorie/atelier``, ``/mot-cle/musique``, ``/date/2026-11-01,2026-11-30``), pas par ``?categorie=atelier``.

Pour les pages et les éléments de contenu, l'agent a besoin de la Contao-API. Codex demande une confirmation à chaque
appel de ``contao_api_execute``.


5. Créer des données d'exemple
------------------------------

.. code-block:: text

   Crée les enregistrements : les 3 catégories (Conférence, Atelier, Séminaire) et les 5 mots-clés chacun en DE, EN
   et FR, puis 10 événements factices, également en DE, EN et FR, avec une catégorie et un à trois
   mots-clés, certains sur plusieurs jours avec date de fin, un ou deux annulés, deux non publiés.
   Utilise pour cela contao_api_mm_<table>_post et lang. Chaque événement reçoit une image : de simples aplats de
   couleur. S'il te manque quelque chose, dis-le-moi.

Pour un MetaModel multilingue, l'agent écrit chaque langue séparément - pour 10 événements en 3 langues, cela fait 30
opérations d'écriture. Cela prend du temps.

.. warning:: MetaModels ne génère l'**alias** qu'à l'enregistrement dans le backend. Via la Contao-API, l'agent doit
   fournir la valeur, sinon les liens de détail manquent, voir
   :ref:`Liste avec page de détail <rst_cookbook_frontend_list-with-detail-page>`.
   Lors du test, l'alias est resté vide après la création. Seule une seconde opération d'écriture (``PATCH``) l'a formé à
   partir du titre et de la date de début (par ex. ``klimaschutz-konferenz-2026-2026-11-20``) ; les valeurs fournies par
   l'agent ont alors été écrasées. On lui fait vérifier, après la création, que chaque événement a un alias dans chaque
   langue.


6. Vérifier la sortie et améliorer
----------------------------------

On ouvre la page de liste dans le navigateur et on la vérifie. Améliorations typiques et prompt correspondant :

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - Observation
     - Prompt (essentiel)
   * - La liste affiche aussi des événements non publiés.
     - « Trouve la cause et corrige-la. » (La règle de filtre *État de la case à cocher* sur ``published`` manque.)
   * - L'image apparaît sous forme de nom de fichier.
     - « L'image doit apparaître comme miniature dans la liste. »
   * - La taille de l'image ne convient pas.
     - « Crée dans Contao la taille d'image 120 x 68 pixels (proportionnelle) et sélectionne-la dans le réglage de
       rendu de la liste. » (Les tailles d'image sont un réglage de Contao, MetaModels ne fait que les sélectionner.)
   * - Les liens se terminent par ``/published/1``.
     - « Trouve la cause : les liens doivent être /fr/evenement/<alias>, un événement non publié ne doit pas
       apparaître même via un lien enregistré en favori, les alias doivent être créés dans le backend à partir du
       titre et de la date de début. »
   * - Le badge « Annulé » manque, ou on lit « Annulé : 1 ».
     - « Crée la mention « Annulé » dans la liste : au lieu de « Annulé : 1 », une mention bien visible doit apparaître
       pour les événements annulés, rien pour les autres. Utilise pour cela un template. » L'administration MCP n'écrit
       pas de templates, l'agent crée le fichier avec les outils de template de Contao ou l'écrit lui-même et
       l'indique dans le réglage de rendu de la liste. Voir ci-dessous et
       :ref:`Templates <rst_cookbook_templates_index>`.
   * - Sur la page de détail, on lit toujours « Annulé : 1 ».
     - L'agent ne modifie que ce qui a été demandé. Avec « Fais-le aussi sur la page de détail », il change le réglage de
       rendu de la page de détail.
   * - Il n'y a pas de pages pour une langue.
     - La page d'accueil de cette langue manque, voir les prérequis.
   * - Le filtre au-dessus de la liste manque ou n'a aucun effet.
     - « Vérifie que l'élément de filtre se trouve au-dessus de la liste et appelle la liste avec /categorie/atelier. »
       Les filtres sont un élément de contenu à part et passent par l'adresse, pas par ``?categorie=``.

Pour les corrections, on demande à l'agent de **recharger l'aide et les remarques du planificateur**
(``metamodels://guide/overview``) si le système a changé depuis le début de la conversation.


7. Journal et annulation
------------------------

.. code-block:: text

   Montre-moi le journal. Que se passerait-il si j'annulais tout (seulement afficher, ne rien confirmer encore) ?
   Dis-moi ce qui peut être annulé, ce qui ne peut pas l'être et pourquoi.

Tant que des enregistrements se trouvent dans les tables, ``metamodels_undo`` refuse d'annuler les MetaModels. Les pages,
articles et éléments de contenu ne figurent pas dans le journal, ils sont créés via la Contao-API. Plus de détails sous
:ref:`Annulation <rst_extended_mcp-admin>`.

Lors du test, l'annulation s'est déroulée ainsi, et c'est ainsi qu'il faut la conduire :

1. L'agent montre le journal (ici cinq entrées) et vérifie chaque entrée sans ``confirm``.
2. Il indique que la configuration ne peut être annulée que si les tables sont vides, et supprime d'abord ses éléments de
   contenu, articles, pages, la taille d'image et le fichier de template, puis les enregistrements (pour les MetaModels
   traduits avec ``lang``).
3. Ensuite, il annule les entrées du journal, la plus récente d'abord.
4. Il vérifie le résultat (nombre de MetaModels, de pages, d'éléments de contenu) et dit ce qui reste.

Sont restées les **tables vides** ``mm_event_categories``, ``mm_event_tags`` et ``mm_event_events`` (``DROP TABLE`` à la
main si on n'en veut pas) et les pages qui existaient déjà auparavant. L'agent ne les a pas supprimées lui-même et l'a
dit - c'est ainsi que cela doit se passer. La consigne « annule tout » est toutefois très large : l'agent a supprimé sans
demander les données d'exemple et ses pages. Qui ne le souhaite pas l'écrit en plus (« Les enregistrements restent »).


Ce qui est ressorti du test
---------------------------

Le déroulé a été joué avec Claude Code dans la console (paquets ``attribute_translated*`` installés, écriture activée) :

* **Planifier :** trois plans, tables et commandes de base de données, rien d'écrit. L'agent a signalé lui-même ce qui
  reste ouvert (filtre de date, mention « Annulé », taille d'image).
* **Appliquer :** trois MetaModels (de, en, fr), catégories et mots-clés, sept événements d'exemple en trois langues,
  liste et page de détail, la condition pour la date de fin dans le backend. Toutes les indications de l'agent
  correspondaient à la base de données.
* **Frontend :** la liste affichait les événements publiés par date décroissante, sans le brouillon. Les filtres par
  catégorie, mot-clé et plage de dates fonctionnaient via l'adresse.
* **Mention « Annulé » :** un template affichait un badge rouge uniquement pour l'événement annulé, rien sinon.
* **Annulation :** toutes les entrées du journal ont pu être annulées, les chiffres correspondaient à l'état précédent.

Les obstacles et comment les contourner :

.. list-table::
   :header-rows: 1
   :widths: 35 65

   * - Obstacle
     - Ce qui aide
   * - Les paquets pour le multilinguisme manquent.
     - L'agent les nomme et n'installe rien. On les installe, on met à jour la base de données et on fait revérifier les
       types avec ``metamodels_check_types`` avant de continuer.
   * - Les outils manquent dans la session ou disparaissent.
     - Vérifier avec ``/mcp`` dans la console, se réauthentifier, démarrer une **nouvelle** session, redonner la mission
       complète. Ne pas utiliser l'application de bureau pour le test.
   * - Le jeton expire (60 minutes).
     - Dans la console, le client le renouvelle lui-même. En cas d'erreur comme ``401``, se réauthentifier.
   * - Le planificateur exige ``fe_memberAttribut``.
     - L'agent indique une colonne quelconque. Sans effet tant que les droits des membres sont désactivés.
   * - Le filtre de date ne trouve pas les événements de plusieurs jours qui commencent avant la période.
     - Le filtre ``fromtodate`` ne vérifie que la date de début. Pour les chevauchements, il faut un filtre sur le début
       et la fin (``rangedate``) - à décider consciemment et à consigner dans le plan.
   * - Le champ alias est vide après la création.
     - Faire vérifier l'alias après la création. MetaModels le forme à l'écriture à partir du titre et de la date de
       début et écrase les valeurs fournies.
   * - Pas de pages dans la troisième langue.
     - La page d'accueil de cette langue manque.
   * - « Annulé : 1 » au lieu d'une mention.
     - Un template pour l'attribut, voir l'étape 6. Les outils n'ont pas de simulation pour
       ``contao_template_create_override`` : l'agent doit dire au préalable quel fichier sera créé, puis vérifier
       qu'aucun override vide n'a été créé pour toutes les cases à cocher.
   * - L'annulation est refusée.
     - D'abord supprimer les enregistrements, puis ``metamodels_undo``, entrée la plus récente d'abord. Les tables vides
       restent.
   * - Le journal n'enregistre qu'une partie.
     - Les pages, éléments de contenu, tailles d'image, enregistrements et fichiers de template n'y figurent pas. On fait
       nommer à l'agent ce qu'il a créé et on vérifie ``git status`` et la structure des pages.

Qui veut répéter le test sauvegarde au préalable la base de données, ``files/`` et le journal, et les restaure lorsqu'aucune
session de l'agent n'est en cours.


La suite possible
-----------------

La recette s'arrête à la **mise en place** : MetaModels, masque de saisie, réglages de rendu, filtres, pages et données
d'exemple. L'**adaptation des templates** n'en fait pas partie et n'a pas été traitée lors du test. C'est l'étape
suivante, car la sortie dans le frontend correspond ensuite au standard du réglage de rendu. Par exemple :

* une mention « Annulé » pour les événements dont la case ``cancelled`` est cochée, dans la liste et sur la page de
  détail,
* la présentation de l'image, de la date et du lieu dans la liste et la structure de la page de détail,
* des classes et une mise en forme pour sa propre mise en page.

Les outils n'écrivent pas de templates. On les crée à la main, voir :ref:`Templates <rst_cookbook_templates_index>`, ou
on en discute avec l'agent dans une étape distincte, en précisant ce que le template doit afficher.
