.. _rst_cookbook_debug_templates:

Déboguer les templates
=======================

Si l'on a besoin, par ex. pour l'affichage d'une liste en frontend,
d'un template personnalisé, ou si l'on souhaite savoir, pour un
template existant, quels attributs sont transmis au template, on peut
les afficher très confortablement avec la barre de débogage de
Symfony.

Le template par défaut est « metamodel_prerendered », ou respectivement
le template sélectionné dans le réglage de rendu pour la sortie.

Si aucun template personnalisé n'est encore utilisé, il faut créer une
copie de « metamodel_prerendered » dans le dossier Contao « /templates ».

Le template concerné est complété au début par les lignes suivantes :

.. code-block:: php
   :linenos:

   <?php
   // Debug items.
   if (\Contao\System::getContainer()->get('kernel')->isDebug()) {
       dump($this->data);
   }
   ?>

Ensuite, il faut consulter la page en frontend en mode débogage. Pour
cela, activer le mode débogage dans l'en-tête du backend, ou, pour un
mode débogage permanent, créer dans le dossier du projet un fichier
`.env` ou `.env.local` avec le contenu `APP_ENV=dev` (la page ne
devrait alors pas être accessible publiquement).

On peut ensuite examiner le tableau dans la barre de débogage via
l'icône « réticule » :

|img_symfony-toolbar|

Grâce à la vérification « isDebug() », le `dump` ne perturbe pas
l'appel normal de la page.

Depuis MM 2.2, il existe un template dédié `metamodel_prerendered_debug.html5`,
que l'on peut sélectionner dans les réglages de rendu pour la sortie
FE - avec ce template, aucune valeur de la liste MM n'est encore
affichée dans un premier temps.

Pour reprendre facilement les données du tableau dans un template FE,
il existe l'aide :ref:`rst_cookbook_frontend_array-helper`, qui génère une
sortie dans le code source prête pour un `copier-coller`.

On peut également restreindre ou rediriger davantage la sortie de
débogage - par ex.

- limiter la sortie aux seules requêtes HTML :

.. code-block:: php
   :linenos:

   <?php
   // Debug – pas pour les réponses API/JSON, sinon dump() casse la sortie.
   $request = \Contao\System::getContainer()->get('request_stack')->getCurrentRequest();
   if (
       \Contao\System::getContainer()->get('kernel')->isDebug()
       && 'html' === $request?->getRequestFormat()
   ) {
       dump($this->data);
   }
   ?>

- exclure spécifiquement la sortie pour un chemin donné :

.. code-block:: php
   :linenos:

   <?php
   // Debug – pas pour les réponses API/JSON, sinon dump() casse la sortie.
   $request = \Contao\System::getContainer()->get('request_stack')->getCurrentRequest();
   if (
       \Contao\System::getContainer()->get('kernel')->isDebug()
       && !str_starts_with($request?->getPathInfo() ?? '', '/cowegis/api')
   ) {
       dump($this->data);
   }
   ?>

- rediriger complètement vers le répertoire de logs de Contao - généralement ``var/logs/`` - via une entrée dans le config.yaml :

.. code-block:: yaml
   :linenos:

   when@dev:
      debug:
          dump_destination: "%kernel.logs_dir%/dump.log"

.. note:: En mode debug, un commentaire de début et de fin ainsi que le nom du template sont affichés dans le
   code source pour les templates html5 du frontend - cela permet de voir quel template affiche quoi où. Pour
   les templates Twig, ces informations sont également disponibles avec l'extension `contao-twig-debug-marker-bundle
   <https://github.com/e-spin/contao-twig-debug-marker-bundle>`_.


Débogage dans MM 2.0
---------------------

Dans Contao 3, la barre d'outils Symfony n'est pas disponible et il
faut passer par un `print_r`.

Le template concerné est complété par quelques lignes de sortie et
devrait ensuite commencer comme suit :

.. code-block:: php
   :linenos:

   <?php
   echo "<!-- DEBUG START \n";
   echo "<pre>\n";
   print_r($this->items->parseAll($this->getFormat(), $this->view));
   echo "</pre>\n";
   echo "\n DEBUG ENDE -->";
   ?>

Si la page correspondante avec le listing est appelée dans le
navigateur, la sortie de débogage devrait se trouver dans le code
source.

Si la sortie est très volumineuse, l'affichage dans le navigateur peut
devenir très lent - on peut y remédier par ex. en n'affichant qu'un seul
nœud d'item :

.. code-block:: php
   :linenos:

   <?php
   echo "<!-- DEBUG START \n";
   echo "<pre>\n";
   // nur 0.-Knoten
   print_r($this->items->parseAll($this->getFormat(), $this->view)[0]);
   echo "</pre>\n";
   echo "\n DEBUG ENDE -->";
   ?>

Si, dans les réglages de rendu, la redirection et le filtre pour la
page de détail sont configurés, la sortie du tableau dans le code
source devient très volumineuse et entraîne souvent une erreur
« Allowed memory size... ». On peut y remédier par ex. en désactivant
temporairement le filtre pour la redirection.

On peut à nouveau supprimer la sortie en commentant le bloc de sortie,
en le supprimant ou en changeant de template.


.. |img_symfony-toolbar| image:: /_img/screenshots/cookbook/debug/symfony-toolbar.jpg
