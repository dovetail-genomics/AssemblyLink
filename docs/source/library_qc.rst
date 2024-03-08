.. _LQ:


Alignment and Proximity-Ligation QC
===================================

This workflow enables you to QC your library and compare between samples. If you have prepared a library with a different chemistry, this below workflow is sufficient. 
To perform QC analysis on your AssemblyLink library, first downsample its sequencing data to one million (1M) read pairs using SeqTK:

**Command:**

.. code-block:: console

   seqtk sample -s100 <input R1 fastq> 1000000 > <output R1 fastq>
   seqtk sample -s100 <input R2 fastq> 1000000 > <output R2 fastq>

**Example:**

.. code-block:: console

   seqtk sample -s100 LinkPrep_R1.fastq 1000000 > LinkPrep_1M_R1.fastq
   seqtk sample -s100 LinkPrep_R2.fastq 1000000 > LinkPrep_1M_R2.fastq

Next, follow the procedures described on :ref:`From fastq to final valid pairs bam file<FTB>` up to and including the step titled :ref:`Removing PCR duplicates<DUPs>`, using the downsampled datasets generated above as input. 

In the step :ref:`Removing PCR duplicates<DUPs>` you used the flag `--output-stats` to generate a stats file in addition to the pairsam output (e.g. --output-stats stats.txt).  
This pairtools stats file provides an extensive output of pairs statistics calculated by pairtools, including total reads, total mapped, total dups, total pairs for each pair of chromosomes, etc., 
which are useful in understanding the characteristics of your AssemblyLink library.

We have provided a script (`get_qc.py`) that extracts values from the pairtools stats file that we find helpful in assessing the quality of an AssemblyLink sequencing library:

**Command:**

.. code-block:: console

   python3 ./AssemblyLink/get_qc.py -p <pairtools stats>


**Example:**

.. code-block:: console

   python3 ./AssemblyLink/get_qc.py -p LinkPrep_1M_pairtools.stats

**Example output**::

   Total Read Pairs                              1,000,000  100%
   Unmapped Read Pairs                           38,923     3.89%
   Mapped Read Pairs                             835,985    83.6%
   PCR Dup Read Pairs                            1,011      0.1%
   No-Dup Read Pairs                             834,974    83.5%
   No-Dup Cis Read Pairs                         698,506    69.85%
   No-Dup Trans Read Pairs                       136,468    13.65%
   No-Dup Valid Read Pairs (cis >= 1kb + trans)  612,394    61.24%
   No-Dup Cis Read Pairs < 1kb                   222,580    22.26%
   No-Dup Cis Read Pairs >= 1kb                  475,926    47.59%
   No-Dup Cis Read Pairs >= 10kb                 362,853    36.29%


Note: We have observed duplicate rates vary between libraries sequenced on different platforms. For example, libraries sequenced on the
NextSeq and NovaSeq platforms appear to have higher duplicate rates that other platforms. We recommended performing shallow sequencing QC 
on a MiniSeq or MiSeq system to determine final library complexity most accurately.  


Complexity
----------

To best estimate library complexity use full shallow sequenced data (not subsampled, as above) and `preseq` prior to moving to deep sequencing. 

The `lc_extrap` utility of the `preseq` package aims to predict the complexity of sequencing libraries. 


``preseq`` options:


.. csv-table::
   :file: tables/preseq.csv
   :header-rows: 1
   :widths: 20 20 60
   :class: tight-table

Please note that the input bam file should be a version prior to dups removal.

``preseq lc_extrap`` command example for extrapolating library complexity:

**Command:**

.. code-block:: console

  preseq lc_extrap -bam -pe -extrap 2.1e9 -step 1e8 -seg_len 1000000000 -output <output file> <input bam file>


**Example:**

.. code-block:: console

   preseq lc_extrap -bam -pe -extrap 2.1e9 -step 1e8 -seg_len 1000000000 -output out.preseq mapped.PT.bam


In this example, the output file (`out.preseq`) details the extrapolated complexity curve of your library, with the total number of read pairs in the first column 
and the number of expected distinct read pairs in the second column. For a typical experiment (human sample) check the expected complexity at 300M read pairs 
(to show the contents of the file, type `cat out.preseq`). We expect a minimum of 125M distinct read pairs for every 300M read pairs sequenced.

.. image:: /images/3.Complexity.png

.. _QCA:

Sequencing Recommendations
--------------------------

Assuming your library complexity QC is as expected, each AssemblyLink library can be sequenced up to 300M read pairs (2 x 150bp).  
