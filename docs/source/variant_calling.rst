Dovetail Omni-C Libraries: SNP Variant Calling Add-on Analysis
==============================================================

Background
----------

The SNP Variant Calling analysis is available for customers purchasing Dovetail Omni-C libraries. This analysis
provides detailed insights into Single Nucleotide Polymorphisms (SNPs) within genomic data, enabling users to gain
a deeper understanding of variant phasing and precision. By leveraging advanced analytical tools and rigorous
quality control processes, this analysis ensures high reliability and accuracy in reported SNPs, aiding researchers
and scientists in their genomic studies.


Methods
-------

Reads were indexed and aligned using BWA\ :sup:`5` and samtools\ :sup:`2`. Pairtools\ :sup:`4` was used to parse, sort,
and deduplicate the resultant bam file. These results were passed into Google's Deepvariant pipeline\ :sup:`6` to
produce variants (variants.vcf). Low-quality variants were filtered using bedtools\ :sup:`1` to produce a high quality
region vcf (variants_hq.vcf.gz). Phased variants were produced using hapcut2\ :sup:`3`, and indels were filtered to
produce a variant file containing only SNPs.

QC of Variant Calling Results
-----------------------------

Our QC processes focus on two main indicators:

* Fraction of heterozygous SNPs phased: A significant criterion is the fraction of heterozygous SNPs phased. Ideally, a
  fraction greater than 70% is indicative of a successful variant calling process.
* Coverage: Coverage of >=30 is considered optimum, guaranteeing minimal to no missing SNPs and over 95% accuracy in
  the reported SNPs even at 20X coverage.

Results are categorized into three reliability levels based on a color-coded spreadsheet delivered with the results:

* **Green Contigs**: High reliability and confidence.
* **Yellow Contigs**: Moderate reliability.
* **Red Contigs**: Not recommended for trust in SNPs and phasing.

Deliverables
------------

Customers will receive the following files as part of the SNP Variant Calling deliverable package:

* Raw VCF file.
* Filtered high-quality VCF file.
* BAM file.
* .BAI (BAM Index) file.
* mosdepth summary txt file
* summary report in html


References
-----------

1. | Aaron R. Quinlan, Ira M. Hall,
   | BEDTools: a flexible suite of utilities for comparing genomic features,
   | Bioinformatics, Volume 26, Issue 6, 15 March 2010, Pages 841–842,
   | https://doi.org/10.1093/bioinformatics/btq033
2. | Petr Danecek, James K Bonfield, Jennifer Liddle, John Marshall, Valeriu Ohan, Martin O Pollard, Andrew Whitwham,
     Thomas Keane, Shane A McCarthy, Robert M Davies, Heng Li
   | Twelve years of SAMtools and BCFtools
   | GigaScience, Volume 10, Issue 2, February 2021, giab008,
   | https://doi.org/10.1093/gigascience/giab008
3. | Edge, P., Bafna, V. & Bansal, V.
   | HapCUT2: robust and accurate haplotype assembly for diverse sequencing technologies.
   | Genome Res. gr.213462.116 (2016).
   | doi: http://dx.doi.org/10.1101/gr.213462.116
4. | Open2C, Nezar Abdennur, Geoffrey Fudenberg, Ilya M. Flyamer, Aleksandra A. Galitsyna, Anton Goloborodko,
     Maxim Imakaev, Sergey V. Venev
   | Pairtools: from sequencing data to chromosome contacts
   | https://github.com/mirnylab/pairtools.
5. | Li H.
   | Aligning sequence reads, clone sequences and assembly contigs with BWA-MEM.
   | arXiv:1303.3997v2 [q-bio.GN] (2013):
   | https://arxiv.org/abs/1303.3997
6. | Ryan Poplin, Pi-Chuan Chang, David Alexander, Scott Schwartz, Thomas Colthurst, Alexander Ku, Dan Newburger,
     Jojo Dijamco, Nam Nguyen, Pegah T. Afshar, Sam S. Gross, Lizzie Dorfman, Cory Y. McLean, and Mark A. DePristo.
   | A universal SNP and small-indel variant caller using deep neural networks.
   | Nature Biotechnology 36, 983-987 (2018).
   | doi: https://doi.org/10.1038/nbt.4235p


Software Versions
-----------------


.. list-table::
   :widths: 50 50
   :header-rows: 1

   * - Package
     - Version
   * - bedtools
     - 2.28.0
   * - bwa
     - 0.7.18
   * - bzip2
     - 1.0.8
   * - Google Deepvariant
     - 1.1.0
   * - hapcut2
     - 1.2
   * - htslib
     - 1.9
   * - pairtools
     - 0.3.0
   * - samtools
     - 1.10

