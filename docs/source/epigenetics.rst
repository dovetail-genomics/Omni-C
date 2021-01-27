.. _EPIGENETICS:

Epigenetics applications with Omni-C
===================================

In this section you will learn how to:

- :ref:`Generate contact maps<GCM>`

- :ref:`Identify Topologically Associated Domains (TADs)<ITAD>`

- :ref:`Identify A/B compartments<IAC>`



Additional Dependencies:
------------------------

- `Juicer Tools <https://github.com/aidenlab/juicer>`_ - Download the JAR file for juicertools and place it in the same directory as this reposity and name it as ``juicertools.jar``, e,g,: 

.. code-block:: console

   wget https://s3.amazonaws.com/hicfiles.tc4ga.com/public/juicer/juicer_tools_1.22.01.jar
   mv juicer_tools_1.22.01.jar ./Omni-C/juicertools.jar

- Java - If not already installed, you can install Java as follows:

.. code-block:: console

   sudo apt install default-jre


.. _GCM:

Generating contact maps using Juicer tools
------------------------------------------

`HiC <https://github.com/aidenlab/juicer/wiki/Pre>`_ is a widely used contact matrix format. Here we will show you how to use ``Juicer tools`` for generating a ``HiC`` contact map.

- ``HiC`` is highly compressed binary representation of the contact matrix

- Provides rapid random access to any genomic region matrix

- Stores contact matrix at 9 different resolutions

- Can be programmatically manipulated using straw python API


The :ref:`.pairs<GPB>` file that you generated in the :ref:`From fastq to final valid pairs bam file<FTB>` section can be used directly with ``Juicer tools`` to generate the `HiC` contact matrix: 

.. csv-table::
   :file: tables/matrix_hic.csv
   :header-rows: 1
   :widths: 25 75
   :class: tight-table


.. admonition:: Tip no.1
   
   Please note that if you have an older vesrion of ``Juicer tools``, generating contact map directly from ``.pairs`` file may not be supported. We recommend updating to a newer version. As we tested, the ``pre`` utility of the version 1.22.01 support the .pairs to HiC function. 

**Command:**

.. code-block:: console

   java -Xmx16000m  -Djava.awt.headless=true -jar <path_to_juicer_tools.jar> pre --threads <no_of_threads> <mapped.pairs> <contact-map.hic> <ref.genome>

**Example:**

.. code-block:: console

   java -Xmx16000m  -Djava.awt.headless=true -jar ./Omni-C/juicer_tools_1.22.01.jar pre --threads 16 mapped.pairs contact_map.hic GRCh38.p12.genome


.. admonition:: Tip no.2

   ``Juicer tools`` offers additional functions that were not discussed here, including matrix normalization and generating matrix for only specified regions in the genome. To learn more about advanced options, please refer to the `Juicer Tools documentation <https://github.com/aidenlab/juicer/wiki/Pre>`_.

The visualization tool ``Juicebox`` can be used to visualize the contact matrix. You can either `download <https://github.com/theaidenlab/juicebox/wiki/Download>`_ a local version of the tool to your computer or use a `web <https://www.aidenlab.org/juicebox/>`_ version of Juicebox. Load your ``.hic`` file to visualize the contact map and zoom in to areas of interest.

.. image:: /images/hic.png
   :width: 200pt
   :align: center

You can use the contact matrix for calling :ref:`TADs<ITAD>`, identify :ref:`A/B compartments<IAC>` or even for finding large structural variations.


.. _ITAD:

Identify Topologically Associated Domains (TADs)
------------------------------------------------

In preparation


.. _IAC:

Identify A/B compartments
-------------------------

In preparation



