# Exome Pipeline: Variant Calling (VC)

## GenomicsDBImport & GenotypeGVCFs Pipeline (Ensembl & UCSC)

This Docker image provides a streamlined pipeline for joint genotyping of genomic variant data using GATK's **GenomicsDBImport** and **GenotypeGVCFs** tools. It supports both **Ensembl** and **UCSC** reference genome workflows with chromosome-parallel processing.

## Overview Of Exome Pipeline:

The overall pipeline consists of multiple steps as outlined below:

| Step                     | Description                                                                                   |
|--------------------------|-----------------------------------------------------------------------------------------------|
| Step 0: Optional MNP Removal | Filter out Multi-Nucleotide Polymorphism (MNP) sites                                            |
| Step 1: PreProcessing        | Preprocessing steps including indexing GVCF files and building a sample map for variant calling |
| Step 2a: **Variant Calling (VC)**   | Import and merge GVCFs from multiple samples using GenomicsDBImport; perform joint genotyping on the GenomicsDB workspace (For Ensembl, use Step_2a___Part_1___Ensembl_GenomicsDBImport.sh script) |
| Step 2b: Variant Filtering (VF) | Filter variant calls and select a subset of variants from callset; merge all cohort VCF files into a single VCF file |
| Step 2c: Quality Control (QC) | Perform quality control on merged VCF file and generate quality control metrics. Please refer to the [Quality Control](https://github.com/yr542/ExomePipeline___Quality_Control_QC/tree/main) Github repository for details.              |
| Step 3: ANNOVAR             | Annotate variants with GnomAD4.0                                                             |
| Step 4: Mendelian Filtering | Perform Mendelian Filtering on variants                                                      |
| Step 5: Post Processing  | Perform post processing                                                                      |
| Step 6: Manual Checks                        | Conduct manual review and verification, typically performed by experts                                     |
| Step 7: Variant Identification Application   | Uses the [Variant Identification Application Version 2 (VIA V2)](https://github.com/yr542/Variant_Identification_Applicaton___VIA___V2/tree/main); for details, refer to the VIA V2 GitHub repository |


* Ensembl in this docker can be used for Part 1 GenomicsDBImport and Genotyping GVCFs, UCSC can only be used for GenomicsDBImport.

## Features

- **Step 1: GenomicsDBImport**  
  Import GVCF files into a GenomicsDB workspace, split by chromosome for parallel processing.

- **Step 2: GenotypeGVCFs**  
  Joint genotyping from GenomicsDB workspaces, outputting per-chromosome VCF files.

- Supports **Ensembl** and **UCSC** reference genomes with configurable directory structure.

- Robust command-line interface with required parameters and memory management flags.

## Flags Used:

| Short Flag | Full Flag           | Description                              | Default   |
|------------|---------------------|------------------------------------------|-----------|
| `-s`       | `--samples_directory` | Directory containing sample GVCF files   | Required  |
| `-r`       | `--reference_genome`  | Reference genome fasta file               | Required  |
| `-o`       | `--output_directory`  | Output directory                          | Required  |
| `-t`       | `--reference_type`    | Reference type ('ensembl' or 'ucsc')     | `ensembl` |
|            | `--memory_max`        | Maximum Java heap size in GB              | `32`      |
|            | `--memory_min`        | Minimum Java heap size in GB              | `16`      |

## Output Directory Format

Given an exome pipeline output directory it outputs to a folder called `variant_filtration___vf` and creates the following folders based on the reference type (UCSC or Ensembl). 

* *Warning:* In our lab (Dr. Schrauwen's lab) we do not use UCSC so the UCSC script was not tested.

**UCSC Reference**  
- Step_2a___Part_1___UCSC___GenomicsDBImport

**Ensembl Reference**  
- Step_2a___Part_1___Ensembl___GenomicsDBImport  
- Step_2a___Part_2___Ensembl___Genotype_GVCFs

**Note:** Requires SLURM array jobs for scalable HPC deployment.
