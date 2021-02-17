.. _EPIGENETICS:

Epigenetics applications with Omni-C
====================================

In this section you will learn how to:

- :ref:`Identify Topologically Associated Domains (TADs)<ITAD>`

- :ref:`Identify A/B compartments<IAC>`



.. _ITAD:

Identify Topologically Associated Domains (TADs)
------------------------------------------------

The :ref:`.hic<PTH>` file that you generated can be used for identifying Topologically Associated Domains (TADs) in the contact map using the ``arrowhead`` utility of ``Juicer Tools``

.. csv-table::
   :file: tables/arrow.csv
   :header-rows: 1
   :widths: 25 75
   :class: tight-table


**Command:**

.. code-block:: console

   java -jar -Xmx16000m  -Djava.awt.headless=true -jar <path_to_juicer_tools.jar> arrowhead --threads <no_of_threads> -k <normalization_type> -m <sliding_window> -r <resolution> <*.hic> <*.bedpe>

**Example:**

.. code-block:: console

   java -jar -Xmx16000m  -Djava.awt.headless=true -jar ./Omni-C/juicer_tools.jar arrowhead --threads 16 -k KR -m 2000 -r 10000 contact_map.hic TAD10kb.bedpe



.. _TFORMAT:

The ``bedpe`` output file will contain `a list of contact domains <https://github.com/aidenlab/juicer/wiki/Arrowhead#domain-list-content>`_  with the following 12-field header:

.. code-block:: console

   chromosome  x1  x2  chromosome2  y1  y2  color  corner_score  Uvar  Lvar  Usign  Lsign 


.. csv-table::
   :file: tables/TAD.csv
   :header-rows: 1
   :widths: 25 75
   :class: tight-table

.. _IAC:

Identify A/B compartments
-------------------------

In preparation



