.. Omni-C documentation master file, created by
   sphinx-quickstart on Thu Jan 21 21:15:26 2021.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.


.. image:: /images/DOV_FINAL_LOGO_2017_RGB.svg
   :width: 100pt

Welcome to Omni-C's documentation!
==================================

.. image:: /images/omni-kit_gw.png


Overview
========
- The Dovetail™ Omni-C™ library uses a sequence-independent endonuclease for chromatin digestion prior to proximity ligation and library generation.

- Chromatin interactions: Omni-C Libraries Enable Genome Wide Resolution of Chromatin Interactions. By employing a sequence-independent endonuclease for chromatin digestion, Omni-C™ offers all the characteristics of Hi-C libraries, without the sequence bias inherent to restriction enzyme (RE) based Hi-C approaches. Omni-C™ data contains a significant overlap with data generated using RE based approaches, but are enriched in long-range cis reads. Improved resolution for chromatin conformation and looping interactions can be measured due to this lack of sequence bias. Omni-C™ libraries enable the most complete view of genome-wide chromatin conformation by dramatically increasing resolution of topological interactions that occur in regions with low restriction enzyme density.

- Key benefits of Omni-C:

  - Sequence independent chromatin fragmentation enables fully genome-wide detection of chromatin contacts (up to 20% of the genome lacks coverage using restriction enzyme based Hi-C approaches)
  - Shotgun sequencing-like even genome coverage enabling SNP calling, chromosome phasing and structural variant detection
  - Lower sequencing burden to reach desired sequence depth saving time and cost

- SNPs & Chromosome Phasing: The even sequence coverage from Omni-C™ libraries enables genome-wide SNP calling and downstream applications reliant on SNP information, such as chromosome phasing due to low switch error rates. Omni-C™ technology offers the best possible approach for whole genome physical phasing using Illumina short reads.

- Large SVs Are Captured In Omni-C™ Data: The proximity ligation data can be used to detect and confirm chromosomal rearrangements in cancer samples at a high resolution. Using open-source software tools such as HiGlass, contact matrices enable the quick visualization of such large structural variants.

- This guide will take you step by step on how to QC your Omni-C library, how to interparate the QC results and how to generate :ref:`contact maps <GCM>`, study :ref: `chromatin structure<ITAD>`, use Omni-C data analyzing and enhancing your assembly and more. If you don't yet have a sequenced Omni-C library and you want to get familiar with the data, you can download Omni-C sequenced libraries from our publicaly available :ref:`data sets<DATASETS>`.

- To :ref:`QC<LQ>` your library, first you will need to align the reads to the reference, filtering and additional alignment processing will be done using `pairtools package <https://github.com/open2c/pairtools>`_ and the final output `bam` file will be used for QC-ing the library and downstream steps.

- If this is your first time following this tutorial, please check the :ref:`Before you begin page <BYB>` first.

.. raw:: html

   <iframe src="https://player.vimeo.com/video/483661602" width="640" height="360" frameborder="0" allow="autoplay; fullscreen; picture-in-picture" allowfullscreen></iframe>


.. toctree::
   :maxdepth: 2
   :caption: Contents:

   before_you_begin
   
   pre_alignment
   
   fastq_to_bam
   
   library_qc

   epigenetics
   
   assembly
   
   data_sets

   support

.. toctree::
   :maxdepth: 2
   :caption: Contents:



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
