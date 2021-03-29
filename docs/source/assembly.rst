.. _ASSEMBLY:

Assembly Enhancement
====================


Additional Dependencies
+++++++++++++++++++++++

`cutadapt <https://cutadapt.readthedocs.io/en/stable/>`_  - sudo apt install cutadapt

`meryl <https://github.com/marbl/meryl>`_ 

`merqury <https://github.com/marbl/merqury/wiki>`_


scaffolding
-----------

In preparation

Kmer analysis, QV and completness
---------------------------------

In this section we will not use the long distance infomation that is captured by the Omni-C proximity ligation libraries. Instead, we take advandage of the uniform coverage of Omni-C libraries and demonstrate how, after trimming, the reads can be used just as shotgun sequencing, for kmer analysis, measuring assembly completness and QV.

In short, kmer composition of the assembly and the reads will be used to evaluate the completness of the assembly and bp quality. 

Trimming
++++++++

Before we build a kmer database from the Omni-C fastq files, we first need to remove the bridge sequecnce.  We use `cutadapt` to trim the bridge sequence from the reads. 

+-------------+----------------------------------------------------------------------+
|Option       |Function                                                              |
+=============+======================================================================+
|-j           |Number of threads (integer)                                           |
+-------------+----------------------------------------------------------------------+
|-b           |For specifiying a 5’ or 3’ adapters for trimming. When trimming       |
|             |paired-end reads this option is used for R1                           |
+-------------+----------------------------------------------------------------------+
|-B           |For specifiying a 5’ or 3’ adapters for trimming of the reverse read. |
|             |When trimming paired-end reads this option is used for R2             |
+-------------+----------------------------------------------------------------------+
|-o           |Output file, when trimming paired-end reads this option is used for   |
|             |output of R1                                                          |
+-------------+----------------------------------------------------------------------+
|-p           |R2 output file, in paired end reads, the second read in a pair is     |
|             |always written to the file specified by this option                   |
+-------------+----------------------------------------------------------------------+
|\*R1.fastq or|R1 input file                                                         |
|\*R1.fastq.gz|                                                                      |
+-------------+----------------------------------------------------------------------+
|\*R2.fastq or|R2 input file                                                         |
|\*R2.fastq.gz|                                                                      |
+-------------+----------------------------------------------------------------------+


Command:

.. code-block:: console

   cutadapt -j <cores> -b <bridge sequence> -B <bridge sequence> -o <trimmed_output_R1.fastq> -p <trimmed_output_R2.fastq> <input_R1.fastq> <input_R2.fastq>

Example:

.. code-block:: console

   cutadapt -j 16 -b GGTTCGTCCA -B GGTTCGTCCA -o trim_OmniC_800M_R1.fastq -p trim_OmniC_800M_R2.fastq OmniC_800M_R1.fastq OmniC_800M_R2.fastq


Generate kmer databases
+++++++++++++++++++++++

If you don't know the optimal kmer size for your genome, run the ``best_k.sh`` script from the merqury repository

Command:

.. code-block:: console

   ./best_k.sh <genome_size>

Example:

.. code-block:: console

   ./best_k.sh 3100000000


In this example, human genome size the optimal kmer size is 21. 

Next, use the ``count`` utility of ``meryl`` tool to generate a meryl database from each one of the fastq files

Command:

.. code-block:: console

   meryl k=<k size> count output <output name> <input file>

Example:

.. code-block:: console

   meryl k=21 count output R1_21.meryl trim_OmniC_800M_R1.fastq
   meryl k=21 count output R2_21.meryl trim_OmniC_800M_R2.fastq
  

The output from the example above is two directories: R1_21.meryl and R2_21.meryl, each one containing kmer database generated based on one fastq file. For downstream steps, all the kmer databases need to be merged into one database using the  ``union-sum`` utility of ``meryl``.

Command:

.. code-block:: console

   meryl union-sum output <output name> <path to inputs>

Example:

.. code-block:: console

   meryl union-sum output reads_21.meryl *meryl


Evaluate assembly completness and QV
++++++++++++++++++++++++++++++++++++

Merqury was developed to allow reference free assembly evaluation, based on k-mer spectrum of low error rate reads (Omni-C in this case). For more details see `merqury documentation <https://github.com/marbl/merqury/wiki/2.-Overall-k-mer-evaluation>`_ . Merqury accept as an input the assembly of interest and meryl kmer database and outputs information on reads and assembly spectrum, assembly kmer completness, and assembly base level QV estimation.

Command:

.. code-block:: console

   merqury.sh <kmer DB> <assembly> <output prefix>


Example:

.. code-block:: console

   merqury.sh reads_21.meryl asm.fasta merqury_out


Detailed describtion of the outputs can be found `here <https://github.com/marbl/merqury/wiki/2.-Overall-k-mer-evaluation>`_. Here are some highlights, the example below is using OmniC library of human sample HG002:

completeness.stats - details the assembly name (column #1), assembly k-mers used in the analysis (column #3), reads k-mers used in the analysis (column #4) and % kmer completness in the assembly (column #5).

Example:

.. code-block:: console
   
   HG002.pri.ctg	all	2243267808	2305938845	97.2822


K-mers that are found only in the assembly are assumed to be bp errors, the <output prefix>.qv summarize the QV results across the assembly. An additional file, <output prefix><assembly>.qv details QV values for each scaffold in the assembky. Assembly summary QV file include the following details: assembly name (column #1), assembly unique kmers (column #2), kmers shared in assembly and reads (column #3), QV (column #4), Error rate (column #5). See example below:

Example:

.. code-block:: console

   HG002.ctg	726218	3063065775	49.4726	1.12912e-05












