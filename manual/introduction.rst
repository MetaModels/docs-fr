Introduction à MetaModels
=========================

Qu'est-ce que MetaModels ?
--------------------------

MetaModels est une extension pour le système de gestion de contenu Contao. Cette extension vous permet d'ajouter une différents types de données structurées et de les afficher sur votre site en utilisant les vues en liste et détail, le filtrage, le tri, la pagination, le multilinguisme et d'autres choses encore…

"Données structurées" signifie du contenu habituellement stocké dans une base de données avec différentes tables et relations. MetaModels propose différents types de champs (appelés Attributs) comme, entre autres, le texte, les listes de sélection, les boites à cocher, les boutons radio, les nombres entiers et décimaux, les booléens oui/non, les gestionnaires de fichiers etc…

Les champs d'applications possibles sont les catalogues de produits, les événements, les menus, les listes d'adresses ou d'employés, la gestion de maisons ou de locations, les galeries d'images ou du contenu multilangues texte et image.

MetaModels permet de créer ses modèles de données directement dans le backend de Contao. Sans avoir besoin de coder contrairement à une extension spécifique. La création des masques de saisie pour le backend comme des sorties pour le frontend avec filtres optionnels sont partie itégrante de la création d'un MetaModel.

L'extension MetaModels permet une grande flexibilité dans la saisie et l'affichage des données et peut répondre ainsi à de nombreux besoins.
Vous pouvez trouver plus de détails dans :ref:`rst_features`.
Vous pouvez aussi jeter un œil aux `MetaModels show cases <https://now.metamodel.me/en/showcase>`_ ou consulter le `forum Contao <https://community.contao.org/de/showthread.php?40208-Stellt-eure-MetaModel-Websites-vor/>`_ pour différents exemples présentés sur le forum allemand.


Histoire de MetaModels
----------------------

MetaModels a été initialement créé comme la nouvelle génération de la célèbre extension Catalog.

Au fil du temps, 'Catalog' est devenue une extension complexe offrant de nombreuses possbilités à Contao. Mais il devenait malheureusement de plus en plus difficile de la maintenir et d'ajouter de nouvelles fonctions.

L'expérience acquise à développer Catalog 1 et Catalog 2 nous a convaincus qu'il nous fallait repartir de zéro.

C'est pourquoi nous avons développé "MetaModels" : une extension entièrement nouvelle intégrant des logiques de programmation modernes. Notre but était de développer un extension avec un code de base flexible et exensible.

La version actuelle MetaModels 2.0 est le résultat de nombreuses heures de discussion sur "quelle est la meilleure solution" et un gros travail de programmation.


MetaModels comparé à d'autres outils.
-------------------------------------

MetaModels répartit le travail entre administrateur et éditeur c'est à dire : l'administrateur ou le développeur crée le ou les MetaModels avec masques de saisie et options de sortie ; le ou les éditeur(s) ajoute les contenus comme on le fait d'habitude dans les autres parties du backend de Contao.

Les masques de saisie permettent de définir précisément quelle donnée peut (ou doit) être entrée et de quelle manière. Les extensions "[dma_elementgenerator]" ou "[rocksolid-custom-elements]" offrent des fonctions smilaires. La différence est que MetaModels vous permet également d'afficher des ensembles de données complexes et propose différentes options de sortie et de filtrage.

Avant de débuter un nouveau projet, vous pouvez vous demander s'il vaut mieux développer votre propre extension ou utiliser MetaModels. IL n'y a pas de réponse générale à cette question parce que les deux solutions permettent de résoudre différents problèmes. Ces différents aspects peuvent vous aider à prendre votre décision :

**Pour développer votre propre extension:**
Le produit à développer doit pouvoir être commercialisé, par exemple une extension commerciale qui devra être facilement mise à disposition des autres utilisateurs de Contao ?
Envisagez de développer votre propre extension. Vous devrez basiquement avoir des compétences en programmation PHP et connaitre de l'API Contao.

**Pour MetaModels:**
MetaModels est certainement un bon choix lorsque vous souhaitez créer une solution spécifique facilement personnalisable dans le backend de Contao. MetaModels a également des atouts à faire valoir  s'il vous faut des fonctions spécifiques comme le multilinguisme. MetaModels permet à l'utilisateur de développer des solution sans programmation. Cependant, des connaissances basiques en PHP, HTML et les bases SQL vous permettront d'utiliser à plein les possibilités offertes par MetaModels.

Ressources
----------

* `Site du projet MetaModels <https://now.metamodel.me>`_
* `MetaModels chez Github <https://github.com/MetaModels>`_
* `Manuel MetaModels chez Github <https://github.com/MetaModels/docs>`_
* `MetaModels dans le wiki de Contao <http://en.contaowiki.org/MetaModels>`_
* `Sous-forum MetaModels de la communauté Contao <https://community.contao.org/en/forumdisplay.php?184-MetaModels>`_
* `Canal IRC de MetaModels sur freenode #contao.mm <irc://chat.freenode.net/#contao.mm>`_
