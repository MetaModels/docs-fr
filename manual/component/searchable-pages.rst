.. _component_searchable-pages:

|img_searchable_pages_32| Define search settings
================================================

.. note:: How to include the detail pages of a MetaModel both into the Contao search index and sitemap.xml

Introduction
------------

With the menu item "Define search settings" you can include the detail pages of a MetaModel rendering (list) into the frontend module of Contao's search engine as well as into the sitemap.xml.

Unlike the standard list views, this "special treatment" of the MetaModel detail pages results from their way to be called. The pages that have been created in the Contao page tree have to be called with specific GET or URL routing parameters in order to output a (useful) detail page with values. The functions for the Contao search engine and the sitemap.xml generator are not able to access those parameters. That's why they need special support.

The "normal list views" do not need this special treatment. These pages are automatically included with the help of the Contao functions into the Contao search engine or the sitemap.

Options
-------

* **Name**: |br|
  Designation for the backend
* **Filtersetting**: |br|
  Selection of the filter set for the detail view
* **Rendersetting**: |br|
  Selection of the render setting for the detail view

Workflow
--------

You can create a new indexation with a click on the icon "|img_new| New searchable page". After choosing a name you can select a filter setting and a render settings. The indexation will be done by the automatic update mechanism of Contao or you can go to the "Maintenance" area, purge data and then click "Rebuild index".


.. |img_searchable_pages_32| image:: /_img/icons/searchable_pages_32.png
.. |img_searchable_pages| image:: /_img/icons/searchable_pages.png
.. |img_new| image:: /_img/icons/new.gif


.. |br| raw:: html

   <br />