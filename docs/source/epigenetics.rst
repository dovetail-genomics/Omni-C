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

   java -jar -Xmx<memory> -Djava.awt.headless=true -jar <path_to_juicer_tools.jar> arrowhead --threads <no_of_threads> -k <normalization_type> -m <sliding_window> -r <resolution> <*.hic> <output_path>

**Example:**

.. code-block:: console

   java -jar -Xmx48000m  -Djava.awt.headless=true -jar ./Omni-C/juicer_tools.jar arrowhead --threads 16 -k KR -m 2000 -r 10000 contact_map.hic TAD_calls


The above command will result in a new file "10000_blocks.bedpe" inside the "TAD_calls" directory.

.. _TFORMAT:

The ``bedpe`` output file will contain `a list of contact domains <https://github.com/aidenlab/juicer/wiki/Arrowhead#domain-list-content>`_  with the following 16-field header:

.. code-block:: console

   chr1 x1 x2 chr2  y1 y2 name  score strand1  strand2  color score uVarScore   lVarScore   upSign   loSign


.. csv-table::
   :file: tables/TAD.csv
   :header-rows: 1
   :widths: 25 75
   :class: tight-table

.. _IAC:

Principle component of the contact matrix
-----------------------------------------

The contact matrix :ref:`.hic<PTH>` can also be used for calculating the Pearson's correlation matrix of the Observed/Expected for intra-chromosomal contacts. The eigenvector is the first principal component of the Pearson's matrix and it implies on the compartmentalization of the DNA at high level. The sign of the eigenvector typically indicates the compartment type A/B (active/inactive).

The Principle component of the contact matrix can be calculated using the ``eigenvector`` utility of ``Juicer Tools``



.. csv-table::
   :file: tables/evector.csv
   :header-rows: 1
   :widths: 25 75
   :class: tight-table

**Command:**

.. code-block:: console

   java -jar -Xmx<memory>  -Djava.awt.headless=true -jar <path_to_juicer_tools.jar> eigenvector <normalization_type> <*.hic> <chromosome> <resolution type> <resolution> <output file>


**Example 1 (resolution >= 500kb):**

.. code-block:: console

   java -jar -Xmx48000m  -Djava.awt.headless=true -jar ./Omni-C/juicer_tools.jar eigenvector KR contact_map.hic chr1 BP 1000000 PC_chr1.txt


**Example 2 (resolution <= 500kb):**

.. code-block:: console

   java -jar -Xmx48000m  -Djava.awt.headless=true -jar ./Omni-C/juicer_tools.jar eigenvector -p KR contact_map.hic chr1 BP 100000 PC_chr1.txt



As described in the table above, the output is a one columnn list of the eigenvalues as calculated for each one of the bins. For most of the common applications this format is not useful and we will guide you on how to convert it to more useful formats:

bedGraph format
+++++++++++++++

To generate a `bedgraph <https://genome.ucsc.edu/goldenPath/help/bedgraph.html>`_  which details each bin interval followed by the associated eigenvalue you will generate a list of the bins intervals using the ``bedtools makewindows`` command and combine it with the output file of the previous step (juicer tools ``eigenvector``).

Specify the *chromosome of interest*, *first bp of the chromosome*, *last bp of the chromosome* (you can find this information in the :ref:`.genome file<GENOME>`) in a tab delmitied format (as in a bed file format) and feed it to ``bedtools makewindows`` alongside the window size (same as the resolution used for juicer tools ``eigenvector``). Finally, combine the two files using the ``paste`` command:


**Command:**

.. code-block:: console

   echo -e '<chromosome>\t<start position>\t<end position>'|bedtools makewindows -b stdin -w <window>|paste - <eigenvector output file> > <output.bedgraph>


**Example:**

.. code-block:: console

   echo -e 'chr1\t0\t248956422'|bedtools makewindows -b stdin -w 1000000|paste - PC_chr1.txt > PC_chr1.bedgraph


A/B compartments
++++++++++++++++

Active regions in the genome were observed to have similar eigenvalues, the same is true for inactive regions. To identify the distinct compartments all the neighboring bins with same sign (>0 or <0) are merged into one region. In each chromosome all the regions with the same sign are classified as the same compartment type (A/B). Please note that the signs of PC values are arbitrary, to accurately assign compartment type to signs of PC values, you need knowledge beyond Omni-C data such as RNA-Seq, Methylation states, etc`   

We will use the ``bedGraph`` file for generating a ``bedpe`` file containing the borders of the calculated A/B compartments:

``bedtools`` allows merging of overlapping or neighboring (``-d`` flag) intervals based on strandness (``-s`` flag). We will use this utility by assigning ``+`` to all intervals with PC1 values >0 and ``-`` to all intervals with PC1 values <0 (A/B compartments has nothing to do with strandness, the ``+`` and ``-`` assignments are only from practical reasons for easy manupulation of the bed file). The strandness option assumes the strand information is on the 6th column, we will use the ``awk`` command to record ``+`` or ``-`` on the 6th column based on the PC values. 
Adjacent intervals with the same sign are merged together using ``bedtools merge``
Following merging, we will use the ``awk`` command to genetrate the final bedpe format.


**Command:**

.. code-block:: console

   awk '{if ($4 > 0) print $1"\t"$2"\t"$3"\t"".""\t"".""\t""+"; else if ($4 < 0) print $1"\t"$2"\t"$3"\t"".""\t"".""\t""-"}' <*.bedgraph>|bedtools merge -s -d 10  -c 6 -o distinct|awk '{print $1"\t"$2"\t"$3"\t"$1"\t"$2"\t"$3"\t"$4}'> <output.bedpe>

**Example:**

.. code-block:: console

   awk '{if ($4 > 0) print $1"\t"$2"\t"$3"\t"".""\t"".""\t""+"; else if ($4 < 0) print $1"\t"$2"\t"$3"\t"".""\t"".""\t""-"}' PC_chr1.bedgraph|bedtools merge -s -d 10  -c 6 -o distinct|awk '{print $1"\t"$2"\t"$3"\t"$1"\t"$2"\t"$3"\t"$4}'>chr1_AB.bedpe


The figure below shows the observed/expected matrix of chr1 with the A/B compartments (black boxes) as generated with the commands above (visualized using ``Juicebox``)

.. image:: /images/AB.png