
.. _GCM:

Generating Contact Matrix
=========================

There are two common formats for contact maps, the `Cooler format <https://github.com/mirnylab/cooler>`_ and `Hic <https://github.com/aidenlab/juicer/wiki/Pre>`_ format. 
Both are compressed and sparsed formats to avoid large storage volumes; For a given ``n`` number of bins in the genome, the size of the matrix would be :math:`n^2`, in addition, typically more than one resolution (bin size) is being used. 

In this section we will guide you on how to generate both matrices types, :ref:`HiC<JHIC>` and :ref:`cool<COOL>` based on the :ref:`.pairs file<GPB>` that you generated in the :ref:`previous section<GPB>` and how to visualize them.



.. _JHIC:

Generating ``HiC`` contact maps using Juicer tools
--------------------------------------------------

Additional Dependencies
+++++++++++++++++++++++

- `Juicer Tools <https://github.com/aidenlab/juicer>`_ - Download the JAR file for juicertools and place it in the same directory as this reposity and name it as ``juicertools.jar``. You can find the link to the most recent version of Juicer tools `here <https://github.com/aidenlab/juicer/wiki/Download>`_ e.g.: 

.. code-block:: console

   wget https://s3.amazonaws.com/hicfiles.tc4ga.com/public/juicer/juicer_tools_1.22.01.jar
   mv juicer_tools_1.22.01.jar ./Omni-C/juicertools.jar

- Java - If not already installed, you can install Java as follows:

.. code-block:: console

   sudo apt install default-jre


From ``.pairs`` to ``.hic`` contact matrix
++++++++++++++++++++++++++++++++++++++++++


- `Juicer Tools <https://github.com/aidenlab/juicer>`_ is used to convert ``.pairs`` file into a `HiC <https://github.com/aidenlab/juicer/wiki/Pre>`_ contact matrix. 

- ``HiC`` is highly compressed binary representation of the contact matrix

- Provides rapid random access to any genomic region matrix

- Stores contact matrix at 9 different resolutions (2.5M, 1M, 500K, 250K, 100K, 50K, 25K, 10K, and 5K)

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


Visualizing ``.hic`` contact matrix
+++++++++++++++++++++++++++++++++++

The visualization tool ``Juicebox`` can be used to visualize the contact matrix. You can either `download <https://github.com/theaidenlab/juicebox/wiki/Download>`_ a local version of the tool to your computer or use a `web <https://www.aidenlab.org/juicebox/>`_ version of Juicebox. Load your ``.hic`` file to visualize the contact map and zoom in to areas of interest.

.. image:: /images/hic.png
   :width: 200pt
   :align: center

You can use the ``.hic`` contact matrix for calling :ref:`TADs<ITAD>`, identify :ref:`A/B compartments<IAC>` or even for finding large structural variations.


.. _COOL:


Generating ``cooler`` contact maps 
----------------------------------

Additional Dependencies
+++++++++++++++++++++++

Installing Cooler and its dependencies
######################################

- libhdf5 - ``sudo apt-get install libhdf5-dev``

- `h5py <https://docs.h5py.org/en/stable/build.html>`_ - ``pip3 install h5py`` 

- `cooler <https://cooler.readthedocs.io/en/latest/index.html>`_ - ``pip3 install cooler``


For any issues with ``cooler`` installation or its dependencies, please refer to the `cooler installation documentation <https://cooler.readthedocs.io/en/latest/quickstart.html#installation>`_


Installing Pairix 
#################

`Pairix <https://github.com/4dn-dcic/pairix>`_ is a tool for indexing and querying on a block-compressed text file containing pairs of genomic coordinates. You can install it directly from its github repository as follows:

.. code-block:: console

   git clone https://github.com/4dn-dcic/pairix
   cd pairix
   make 

Add the bin path, and utils path to PATH and exit the folder: 

.. code-block:: console

   PATH=~/pairix/bin/:~/pairix/util:~/pairix/bin/pairix:$PATH
   cd ..

.. admonition:: Important!

   make sure to modify the following example with the path to your `pairix` installation folder. If you are not sure what is the path you can check it with the command `pwd` when located in the `pairix` folder.

For any issues with ``pairix``, please refer to the `pairix documentation <https://github.com/4dn-dcic/pairix>`

From ``.pairs`` to ``cooler`` contact matrix
++++++++++++++++++++++++++++++++++++++++++++

- `Cooler tools <https://github.com/mirnylab/cooler>`_ is used to convert **indexed** ``.pairs`` file into `cool and mcool <https://cooler.readthedocs.io/en/latest/index.html>`_ contact matrices

- ``Cooler`` generates a sparse, compressed, and binary persistent representation of proximity ligation contact matrix

- Store matrix as `HDF5 <https://en.wikipedia.org/wiki/Hierarchical_Data_Format>`_ file object

- Provides python API to manipulate contact matrix

- Each cooler matrix is computed at a specific resolution

- Multi-cool (mcool) files store a set of cooler files into a single HDF5 file object 

- Multi-cool files are helpful for visualization


Indexing the ``.pairs`` file 
############################

We will use the ``cload pairix`` utility of ``Cooler`` to generate contact maps. This utility requires the ``.pairs`` file to be indexed. 
``Pairix`` is used for indexing compressed ``.pairs`` files. The files should be compresses with `bgzip <http://www.htslib.org/doc/bgzip.html>`_ (which should already be installed on your machine). If your ``.pairs`` file is not yet bgzip compressed, first compress it as follows:


**Command:**

.. code-block:: console

  bgzip <mapped.pairs> 


**Example:**

.. code-block:: console

  bgzip mapped.pairs


Following this command ``mapped.pairs`` will be replaced with its compressed form ``mapped.pairs.gz``


.. admonition:: Note!

   Compressing the ``.pairs`` file with ``gzip`` instead of ``bgzip`` will also result in a compressed file with the ``.gz`` suffix, but due to format differnces it will not be accepted as an input for ``pairix``.


Next, index the file ``.pairs.gz`` file:

**Command:**

.. code-block:: console

  pairix <mapped.pairs.gz> 


**Example:**

.. code-block:: console

  pairix mapped.pairs.gz


Genereting single resolution and multi-resolutions contact map files 
####################################################################

As mentioned above, we will use the ``cload pairix`` utility of ``Cooler`` to generate contact maps:

``cooler cload pairix`` usage:

+-------------------------+-------------------------------------------------------------------+
|Parameter                |Function                                                           |
+=========================+===================================================================+
|<genome_fils>\:<bin size>|Specifies the reference :ref:`.genome file<GENOME>`, followed      |
|                         |with``:`` and the desired bin size in bp                           |
+-------------------------+-------------------------------------------------------------------+
|\*.pairs.gz              |Path to ``bgzip`` compressed and indexed ``.pairs`` file           |
+-------------------------+-------------------------------------------------------------------+
|\*.cool or \*.mcool      |Name of output file. use ``.cool`` for a single resolution file and|
|                         |``.mcoool`` for a multi-resolution file                            |
+-------------------------+-------------------------------------------------------------------+

*Command:**

.. code-block:: console

  cooler cload pairix GRCh38.p12.fa.genome:10000 mapped.pairs.gz pairs_10kb.cool


**Example:**

.. code-block:: console

  cooler cload pairix GRCh38.p12.fa.genome:10000 mapped.pairs.gz pairs_10kb.cool


.. admonition:: Tip

   ``Cooler`` offers additional functions that were not discussed here, including generating a cooler from a pre-binned matrix, matrix normalization and more. To learn more about advanced options, please refer to the `cooler documentation <https://cooler.readthedocs.io/en/latest/cli.html#quick-reference>`_.


Visualizing cooler contact matrix
+++++++++++++++++++++++++++++++++++


.. admonition:: Tip

   When you wish to visualize the contact matrix, it is highly recommended to generate a multi-resolution ``.mcool`` file to allow zooming in and out to inspect regions of interest


