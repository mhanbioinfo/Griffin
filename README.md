## Griffin
A flexible framework for nucleosome profiling of cell-free DNA


## Description
To run Griffin, use the snakemakes in the the 'snakemakes' directory  
See the Griffin wiki (https://github.com/adoebley/Griffin/wiki) for further instructions and a demo.

The methodology is described in:  
Doebley, et al. Griffin: Framework for clinical cancer subtyping from nucleosome profiling of cell-free DNA. (2021) MedRxiv. [doi: https://doi.org/10.1101/2021.08.31.21262867](https://doi.org/10.1101/2021.08.31.21262867)

The analysis workflow consists of 4 tasks: 

1. griffin_genome_GC_frequncy  
    - Calculate the frequency of fragments with each GC content across the mappable regions of the reference genome  
    - For hg38, this step is already complete and results are in Ref/genome_GC_frequency  
    - Griffin has not been tested on genome builds other than hg38, but this snakemake is provided in case you would like to try a different genome build or different filter for mappable regions (ex. shorter or longer reads)  
    
2. griffin_GC_correction  
    - Calculate the GC bias for a given set of bam files  
    - To run this step:  
        1. Create a samples.yaml with your list of bam files and place it in config (see config/example_samples.yaml for format)  
        2. Edit config.yaml to provide the path to the reference genome (hg38)  
        3. Follow the directions at the top of griffin_GC_correction.snakemake to run the snakemake  
      
    - Outputs:  
        1. repeat_masker.mapable.k50.Umap.hg38/GC_bias/<sample_name>.GC_bias.txt  
            - The GC bias of fragments with each length and GC content, this is used for GC correction  
        2. repeat_masker.mapable.k50.Umap.hg38/GC_counts/<sample_name>.GC_counts.txt  
            - Intermediate file with the number of fragments with each length and GC content  
        3. repeat_masker.mapable.k50.Umap.hg38/GC_plots/  
            - Assorted plots of the GC bias for each sample  
        4. samples.GC.yaml  
            - A config file for use in the nucleosome profiling step 
            - Copy this file into griffin_nucleosome_profiling/config/ to run the nucleosome profiling analysis on these samples
      
 3. griffin_nucleosome_profiling  
    - Run nucleosome profiling for a given set of site lists and a given set of bam files  
    - To run this step:  
        1. Copy the samples.GC.yaml from the griffin_GC_correction step into the config directory  
        2. Make a sites.yaml containing paths to the high mappability output files from griffin_filter_sites (see config/example_sites.yaml for format)  
        3. Edit config.yaml to provide the path to the reference genome (hg38)  
        4. Edit other config settings as needed  
        5. Follow the directions at the top of griffin_nucleosome_profiling.snakefile to run the snakemake  
        
    - Outputs:  
        1. results/coverage/all_sites/<sample_name>.all_sites.coverage.txt  
            - nucleosome profiles and metadata for each site list.   
            - Both GC corrected and non-GC corrected profiles are in this file and must be separated for downstream analysis (GC_correction column). Coverage profile data is labeled with the start coordinate of the bin. For instance, the column labeled -15 contains the coverage information for -15bp to 0bp relative to the site location.  
        2. results/coverage/<site_name>/<sample_name>.<site_name>.coverage.txt  
            - These folders contain intermediate files with the coverage profiles for individual site lists. These have been concatenated into results/coverage/all_site/<sample_name>.all_sites.coverage.txt. 

## Versions of packages used for testing  
argparse 1.1  
pysam 0.15.4   
pyBigWig 0.3.17  
pandas 1.3.2 
numpy 1.21.2  
scipy 1.7.1  
pyyaml 5.3.1  
matplotlib 3.4.1  
snakemake 5.5.4  
python 3.7.4  


## Software License   
Griffin Copyright (c) 2021 Fred Hutchinson Cancer Research Center  
All rights reserved.  

This program is free software: you can redistribute it and/or modify it under the terms of the BSD-3-Clause-Clear license. No licenses are granted to any patent rights of the Fred Hutchinson Cancer Research Center.  

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the BSD-3-Clause-Clear license for more details.  

You should have received a copy of the G BSD-3-Clause-Clear license along with this program. If not, see https://spdx.org/licenses/BSD-3-Clause-Clear.html. 
