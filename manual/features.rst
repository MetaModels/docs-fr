.. _rst_features:

Aperçu des fonctions
====================

Modèles de données
------------------

Metamodels vous permet de concevoir des modèles de données facilement depuis le backend de Contao sans (presque) aucune restriction et sans compétence en programmation.

Différents types de données sont disponibles pour les champs (attributs) comme, par exemple, texte, image, nombres, date et fichiers.
Si vous atteignez une limite et que le type de donnée souhaité n'est pas disponible, il est aussi possible de l'implémenter.

Les tables créées peuvent être liées par des relations 1 à n ou n à n. Il est aussi possible de connecter des tables aux tables de Contao pour établir des connexion "parent-enfant" ou implémenter des entrées de variantes.

Masques de saisie
-----------------

Vous concevez des masques de saisie complexes pour le backend vous permettant d'offrir aux éditeurs le "look-and-feel" du backend de Contao auquel ils sont déjà habitués. Au sein d'un masque, vous pouvez faire afficher ou non certains éléments ("sub-palettes") en fonction de valeurs saisies ou de cases à cocher.

Il est possible de compléter les vues par des filtres, des champs de recherche et des fonctions de regroupement pour une meilleure présentation des données.

Le système de droits utilisateurs flexible développé pour MetaModels vous permet de définir des vues backend différentes pour différents groupes d'éditeurs et d'administrateurs.

Le backend peut-être personnalisé au point de restreindre l'accès de certains champs à certains groupes d'utilisateurs. Vous pouvez également personnaliser l'ordre des champs par groupe d'utilisateur.

Multilinguisme
--------------

Dès le départ, MetaModels a été pensé pour être multilangue. Les attributs supportent donc nativement la traduction des données qui sont directement stockées dans chaque langue.
Sélectionnez le langage voulu dans les réglages du backend et vous pouvez modifier les données.

Encore mieux : les champs qui n'ont pas à être traduits ne le seront pas ! Vous pouvez donc rendre traduisibles le nom et la description du produit mais pas le code EAN ni les dimensions.

Filters
Filtres
-------

MetaModels propose un système de filtres très puissant qui permet de réaliser des tâches complexes. L'administrateur du site peut adapter les interactions des filtres selon les besoins. Ceci en configurant et paramétrant les filtres et leurs données.

MetaModels n'impose aucune limite aux combinaisons de filtres possibles permettant des scénarions de filtrage complexes. Et grâce à la structure ouverte de l'API, vous pouvez-même facilement programmer vos propres filtres.

MetaModels est fourni avec de nombreux réglages de filtres pour afficher en frontend des champs de saisie de filtrage comme menu de sélection, écarts, recherche en texte libre etc. Vous pouvez combiner ces filtres avec des conditions type ET/OU ou des requêtes SQL personnalisées pour obtenir des filtres complexes et interactifs.

Vues dynamiques
---------------

Dans MetaModels, le concept de template "partiel" utilisé par Contao a été amélioré par l'utilisation des réglages de sortie. L'utilisateur peut personnaliser chaque aspect d'une vue depuis le niveau d'un enregistrement à celui d'un attribut.

De nombreux réglages globaux sont gérés directement depuis le backend. Mais ils peuvent être écrasés, modifiés ou ignorés à l'aide de templates personnalisés au niveau des enregistrements comme des champs de données (attributs).

Les concepteurs peuvent créer une vue (totalement) différente pour chaque usage, que ce soit une simple liste, une accroche pour la page d'accueil ou la vue de détail d'un ensemble de données. Ils peuvent définir où et quand cette vue doit être utilisée.

Coup d'œil
----------

Nous travaillons continuellement pour améliorer les fonctionnalités de MetaModels.
Parmi les fonctions à venir :

* Types de sorties avancés comme les RSS et les autres systèmes de syndications, XML, CSV
* Fonctions d'import-export
* Édition en front-end
# une API pour le module de commerce en ligne 'Isotope'

Par votre soutien financier ou des développement sur commande, vous nous permettez d'ajouter rapidement de nouvelles fonctions. - Plus d'informations sur le site du projet :  <https://now.metamodel.me>`_.
