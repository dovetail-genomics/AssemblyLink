.. AssemblyLink documentation master file, created by
   sphinx-quickstart on Fri Jan 22 15:26:09 2021.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.
.. image:: /images/DOV_FINAL_LOGO_2017_RGB.svg
   :width: 100pt


Welcome to AssemblyLink documentation
=====================================


Overview
========
- Dovetail® AssemblyLink™ Kit is designed for the rapid generation of genomic interaction data, or Hi-C-like data. This workflow enables researchers to go from sample prep to a sequencing ready library in under one day.
  
- This guide will take you step by step on how to process and QC your AssemblyLink library, how to interpret the QC results, how to generate contact maps, and how to perform chromatin conformation feature calling. If you don’t yet have a sequencing AssemblyLink library and you want to get familiar with the data, you can download AssemblyLink sequence libraries from our publicly available data sets. 

- The QC process starts with aligning the reads to a reference genome then retaining high quality mapped reads. From there the mapped data will be used to generate a pairs file with pairtools, which categorizes pairs by read type and insert distance, this step both flags and removes PCR duplicates. Once pairs are categorized, counts of each class are summed and reported.

- If this is your first time following this tutorial, please check the :ref:`Before you begin page <BYB>` first.















































































































  

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   before_you_begin
   
   pre_alignment
   
   fastq_to_bam
   
   library_qc

   contact_map

   conf_analysis
   
   data_sets

   support


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
