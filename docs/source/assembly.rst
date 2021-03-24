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

In this section we will not use the long distance infomation that is captured by the Omni-C proximity ligation libraries. Instead, we take advandage of the uniform coverafe of Omni-C libraries and demonstrate how, after trimming, the reads can be used just as shotgun sequencing, for kmer analysis, measuring assembly completness and QV.

In short, kmer composition of the assembly and the reads will be used to evaluate the comletness of the assembly and bp quality. 

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

   cutadapt -j 48 -b <bridge sequence> -B <bridge sequence> -o <trimmed_output_R1.fastq> -p <trimmed_output_R2.fastq> <input_R1.fastq> <input_R2.fastq>

Example:

.. code-block:: console

   cutadapt -j 48 -b GGTTCGTCCA -B GGTTCGTCCA -o trim_OmniC_800M_R1.fastq -p trim_OmniC_800M_R2.fastq OmniC_800M_R1.fastq OmniC_800M_R2.fastq


kmer database
=============


.. code-block:: console

  meryl union-sum output PUR1715_reads.meryl *meryl


