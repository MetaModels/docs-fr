Notes to work with Sphinx:

Docu:
http://thomas-cokelaer.info/tutorials/sphinx/rest_syntax.html

Online editor:
http://rst.ninjs.org/

Online table generator:
http://www.tablesgenerator.com/markdown_tables
http://truben.no/table/

~~~~~~~~~~~~~~~~~~~~~~~~~

Tools:

Generator for test data:
http://www.generatedata.com/#generator

SQL:
random numbers:
FLOOR(RAND() * (<max> - <min> + 1)) + <min>

~~~~~~~~~~~~~~~~~~~~~~~~~

Notes for this document:

Internal links:

above at headline
.. _rst_features:

and add per links e.g.
:ref:`rst_features`

or add with linked text
:ref:`New functions <rst_features>`

At new links I used the form <filename>_<headline-with-hyphen> - e.g.
.. _introdution_what-is-metamodels:


External links:
`Google <https://www.google.de>`_
its not possible to add attribute "target" :-(


Headlines:

H1 Headline
===========

H2 Headline
-----------

H3 Headline
...........


Tables:
http://www.tablesgenerator.com/markdown_tables


Images:
In text with "token" e.g.. Lorem ipsum |img_filter| bla bla...

and at the end of page 
.. |img_filter| image:: /_img/filter.png


Note:
.. warning:: 
.. note::


HTML special chars:
with "token" e.g. cost 2.000 |nbsp| EUR

.. |nbsp| unicode:: 0xA0 
   :trim:
   
.. |br| raw:: html

   <br />