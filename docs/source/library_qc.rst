.. _LQ:


Alignment and Proximity-Ligation QC
===================================

To perform QC analysis on your TopoLink library, first downsample its sequencing data to one million (1M) read pairs using SeqTK:

**Command:**

.. code-block:: console

   seqtk sample -s100 <input R1 fastq> 1000000 > <output R1 fastq>
   seqtk sample -s100 <input R2 fastq> 1000000 > <output R2 fastq>

**Example:**

.. code-block:: console

   seqtk sample -s100 TopoLink_R1.fastq 1000000 > TopoLink_1M_R1.fastq
   seqtk sample -s100 TopoLink_R2.fastq 1000000 > TopoLink_1M_R2.fastq

Next, follow the procedures described on :ref:`From fastq to final valid pairs bam file<FTB>` up to and including the step titled :ref:`Removing PCR duplicates<DUPs>`, using the downsampled datasets generated above as input. 

In the step :ref:`Removing PCR duplicates<DUPs>` you used the flag `--output-stats` to generate a stats file in addition to the pairsam output (e.g. --output-stats stats.txt).  
This pairtools stats file provides an extensive output of pairs statistics calculated by pairtools, including total reads, total mapped, total dups, total pairs for each pair of chromosomes, etc., 
which are useful in understanding the characteristics of your TopoLink library.

At this stage of QC analysis, the following two values from the pairtools stats file are required: "No-Dup Read Pairs" (`total_no_dups`) and "No-Dup Cis Read Pairs >= 1kb" (`cis_1kb+`). 
These values can be extracted as follows:

**Command:**

.. code-block:: console

   grep -wE "total_nodups|cis_1kb\+" <pairtools stats file>

**Example:**

.. code-block:: console

   grep -wE "total_nodups|cis_1kb\+" TopoLink_1M_pairtools.stats


Please use the values outputted by the above command with the criteria listed in the following table to assess the quality of your TopoLink library:

+--------------------------------+-----------------------------+
| Metric                         | Recommended Passing Values  |
+================================+=============================+
| No-Dup Read Pairs              | >500,000 pairs (>50% of 1M) |
+--------------------------------+-----------------------------+
| No-Dup Cis Read Pairs >= 1kb   | >250,000 pairs (>25% of 1M) |
+--------------------------------+-----------------------------+

Complexity
----------

If you performed a shallow sequencing experiment (e.g. 10M reads) and are running a QC analysis to decide which library to use for deep sequencing (DS), it is recommended to evaluate the complexity of the library before moving to DS. 

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


In this example, the output file (`out.preseq`) details the extrapolated complexity curve of your library, with the total number of read pairs in the first column and the number of expected distinct read pairs in the second column. For a typical experiment (human sample) check the expected complexity at 400M read pairs (to show the contents of the file, type `cat out.preseq`). For a sequencing library at 400M read pairs, the expected distinct read pairs should be at least 125 million.

.. image:: /images/3.Complexity.png

.. _QCA:

Sequencing Recommendations
--------------------------

AssemblyLink was designed to support looping calling with one sample. This requires generating four libraries from a single proximity-ligation reaction. This does not mean you need to sequence all four libraries. The amount of sequencing and the number of libraries you need to to sequence is dependent on the feature you are trying to detect and the resolution (or bin size) at which you wish to call features. The table below outlines the number of libraries, total sequencing depth in read pairs, and how many read pairs are needed per library, and finally the minimal amount of no-dup read pairs summed across the libraries for each feature at given resolutions:

+------------------+--------------+-------------------+--------------------+--------------------------------+--------------------------------------------------------+
| Feature          | Resolution   | Total # libraries | Total # read pairs | Total # read pairs per library | Minimal # of no-dup read pairs summed across libraries |
+==================+==============+===================+====================+================================+========================================================+
| A/B Compartments | 50-100 kb    | 1                 | 200 Million        | 200 Million                    | >80 Million                                            |
+------------------+--------------+-------------------+--------------------+--------------------------------+--------------------------------------------------------+
| TADS             | 25 kb        | 2                 | 400 Million        | 200 Million                    | >150 Million                                           |
+------------------+--------------+-------------------+--------------------+--------------------------------+--------------------------------------------------------+
|                  | 10 kb        | 2                 | 600 Million        | 300 Million                    | >300 Million                                           |
+------------------+--------------+-------------------+--------------------+--------------------------------+--------------------------------------------------------+
|                  | 5 kb         | 4                 | 800 Million        | 200 Million                    | >400 Million                                           |
+------------------+--------------+-------------------+--------------------+--------------------------------+--------------------------------------------------------+
| Loops            | 10 kb        | 4                 | 800 Million        | 200 Million                    | >400 Million                                           |
+------------------+--------------+-------------------+--------------------+--------------------------------+--------------------------------------------------------+
|                  | 5 kb         | 4                 | 1200 Million       | 300 Million                    | >500 Million                                           |
+------------------+--------------+-------------------+--------------------+--------------------------------+--------------------------------------------------------+

To generate the most complete matrix you can from a single 500 thousand cell input, you need sequence 4 libraries to a total of 1200 million read pairs (300 million per library).
