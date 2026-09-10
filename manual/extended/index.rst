.. _extended_index:

Extensions
==========

Vous trouverez sur les pages suivantes des guides rapides pour les extensions
qui ont été écrites pour MetaModels, ainsi qu'une liste des extensions connues.

Toutes les extensions créées et prises en charge directement par l'équipe MM
se trouvent sur `Github <https://github.com/MetaModels>`_.

.. toctree::
    :maxdepth: 1

    frontend_editing
    perimetersearch
    attribute_color
    attribute_mcw
    loupe
    filter-by-related
    notelist
    isotope
    cowegis-layer-marker
    leaflet
    file-usage
    metadata_extractor
    xliff_ex-import
    translator-bridge
    changelanguage-bridge
    erd-viewer
    health-check


autres extensions connues
--------------------------

+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| **Nom**                                 | **Type**   | **fonctionnalité**                                      | **Lien**                                                                    | **par**       | **version du**       | **testé**                                               |
+=========================================+============+=========================================================+=============================================================================+===============+======================+=========================================================+
| Widget de géocodage (Open-Street-Map)   | Attribut   | ajoute une icône de widget à un élément de texte,       | `Github <https://github.com/kampfq/metamodels_attribute_geocode>`_          | kampfq        | v1.4 04.04.2023      | fonctionne                                              |
|                                         |            | permettant de déterminer les géocoordonnées d'une       |                                                                             |               |                      |                                                         |
|                                         |            | adresse                                                 |                                                                             |               |                      |                                                         |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| MM_Shop - boutique simple avec checkout | Module FE  | checkout avec panier et fonctions de paiement           | `Github <https://github.com/birdsinthesun/mm_shop>`_                        | birdsinthesun | v1.1 05.07.2025      | non                                                     |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| Changelanguage - imi_mm_changelanguage  | Module FE  | rend MM compatible avec l'extension 'changelanguage'    | `Github <https://github.com/iMi-digital/imi_mm_changelanguage>`_            | iMi digital   | 2.2.0 25.10.2020     | fonctionne                                              |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| Filter-Textcombine -                    | Filtre     | combine des champs texte pour une recherche commune     | `Github <https://github.com/cogizz/metamodelsfilter_textcombine>`_          | cogizz        | ? 24.07.2014         | non ; :ref:`Construction alternative avec les moyens du |
| metamodelsfilter_textcombine            |            |                                                         |                                                                             |               |                      | bord <rst_cookbook_filter_search-text-at-two-fields>`   |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| OpenImmo Integration -                  | Attribut   | attribut pour l'intégration d'OpenImmo                  | `Github <https://github.com/der-On/Contao-MetaModels-OpenImmo>`_            | der-On        | 1.1.0 13.12.2016     | 1.1.0 : fonctionne avec MM 2.1                          |
| Contao-MetaModels-OpenImmo              |            |                                                         |                                                                             |               |                      |                                                         |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| Page-ID Attribut - attribute_pageid     | Attribut   | sélection d'une page Contao et sortie du lien           | `Github <https://github.com/designs2/attribute_pageid>`_                    | designs2      | ? 26.11.2015         | non ; on peut utiliser pour cela l'attribut normal «    |
|                                         |            |                                                         |                                                                             |               |                      | Sélection unique [Select] »                             |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| xNavigation -                           | Module FE  | rend MM compatible avec l'extension 'xnavigation'       | `Github <https://github.com/netzmacht/contao-xnavigation-metamodels>`_      | netzmacht     | ? 20.09.2015         | non                                                     |
| contao-xnavigation-metamodels           |            |                                                         |                                                                             |               |                      |                                                         |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| Filtre pour groupe de membres -         | Filtre     | condition pour la chaîne de règles de filtre pour les   | `Github                                                                     | cboelter      | ? 25.01.2015         | non                                                     |
| metamodels-filter_condition_membergroup |            | groupes de membres                                      | <https://github.com/cboelter/metamodels-filter_condition_membergroup>`_     |               |                      |                                                         |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| Saisie en frontend -                    | Module FE  | saisie de valeurs en frontend                           | `Github <https://github.com/Teamsisu/contao-mm-frontendInput>`_             | Teamsisu      | ? 23.01.2015         | non ; :ref:`Alternative avec FEE                        |
| contao-mm-frontendInput                 |            |                                                         |                                                                             |               |                      | <rst_extended_frontend_editing>`                        |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| MM-Inserttags - metamodels-inserttag    | Module FE  | inserttags pour MM                                      | `Github <https://github.com/westwerk-ac/metamodels-inserttag>`_             | westwerk-ac   | ? 22.10.2015         | non                                                     |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| Filtre sélection d'année -              | Filtre     | propose la sélection de l'« année » pour l'attribut     | `Github <https://github.com/cogizz/filter_selectyear>`_                     | cogizz        | ? 09.08.2014         | non                                                     |
| filter_selectyear                       |            | timestamp                                               |                                                                             |               |                      |                                                         |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| Attribut Année-Mois -                   | Attribut   | stockage du mois et de l'année dans deux colonnes       | `Github <https://github.com/backbone87/metamodels-attribute_yearmonth>`_    | backbone87    | ? 15.04.2013         | non                                                     |
| metamodels-attribute_yearmonth          |            |                                                         |                                                                             |               |                      |                                                         |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| Protection pour groupes de membres      | Attribut   | protège les contenus pour les groupes de membres        | `Github                                                                     | richardhj     | ? 21.09.2013         | non                                                     |
|                                         |            |                                                         | <https://github.com/richardhj/contao_metamodelsattribute_protectedgroups>`_ |               |                      |                                                         |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| Chiffrement des champs texte -          | Attribut   | chiffre les textes dans la BDD avec le chiffrement      | `Github <https://github.com/davidmaack/metamodelsattribute_encryptedtext>`_ | davidmaack    | ? 03.02.2016         | non                                                     |
| metamodelsattribute_encryptedtext       |            | Contao                                                  |                                                                             |               |                      |                                                         |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
| Sélection et affichage d'un item -      | Module FE  | CE pour la sélection d'un item MM pour l'affichage en   | `Github <https://github.com/postyou/metamodels-single-ce>`_                 | postyou       | 'master' 10.10.2016  | 'master' 10.10.2016 : fonctionne pour MM 2.0            |
| metamodels-single-ce                    |            | FE                                                      |                                                                             |               |                      |                                                         |
+-----------------------------------------+------------+---------------------------------------------------------+-----------------------------------------------------------------------------+---------------+----------------------+---------------------------------------------------------+
