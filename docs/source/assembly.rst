.. _ASSEMBLY:

Assembly Enhancement
====================


Additional Dependencies
+++++++++++++++++++++++

`cutadapt <https://cutadapt.readthedocs.io/en/stable/>`_  - sudo apt install cutadapt


scaffolding
-----------

In preparation

Kmer analysis, QV and completness
---------------------------------

In preparation 

Trimming
++++++++


In this section we will not use the long distance infomation that is captured by the Omni-C proximity ligation libraries. Instead, we take advandage of the uniform coverafe of Omni-C libraries and demonstrate how, after trimming, the reads can be used just as shotgun sequencing, for kmer analysis, measuring assembly completness and QV.

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


cutadapt -j 48 -b <bridge sequence> -B <bridge sequence> -o <trimmed_output_R1.fastq> -p <trimmed_output_R2.fastq> <input_R1.fastq> <input_R2.fastq>

cutadapt -j 48 -b GGTTCGTCCA -B GGTTCGTCCA -o trim_OmniC_800M_R1.fastq -p trim_OmniC_800M_R2.fastqOmniC_800M_R1.fastq OmniC_800M_R2.fastq


https://s3.amazonaws.com/dovetail.pub/HiC/fastqs/OmniC_800M_R1.fastq
for i in $(ls|grep trim);do echo $i;meryl k=21 count output ${i}.meryl ${i};done;

.. code-block:: console

  meryl union-sum output PUR1715_reads.meryl *meryl


