# RNA-Seq analysis for: <br/> UPF3A and UPF3B are redundant and modular activators of nonsense-mediated mRNA decay in human cells

This repository contains the code, scripts and log files for the RNA-Seq analysis of the project: <br/> [__UPF3A and UPF3B are redundant and modular activators of nonsense-mediated mRNA decay in human cells__](https://doi.org/10.1101/2021.07.07.451444) <br/> (available as bioRxiv preprint)

Preprint:  
[![DOI](https://img.shields.io/badge/DOI-10.1101%2F2020.07.07.191437-red)](https://doi.org/10.1101/2021.07.07.451444)  

## Graphical abstract

<img src="https://github.com/boehmv/UPF3/blob/main/doc/graphical_abstract.png?raw=true" max-height="300">

## Scope
This repository primarily aims to provide transparent insight into the RNA-Seq analysis steps used in the study of UPF3A-UPF3B in NMD. :warning: **NOTE:** The complete pipeline is currently not optimized to run on different computing infrastructures in a standardized/portable manner. This mean that all required packages have to be installed manually and configured accordingly to reproduce the results.

## Features / Requirements
* Complete analysis of RNA-Seq data (ArrayExpress: [E-MTAB-10711](https://www.ebi.ac.uk/arrayexpress/experiments/E-MTAB-10711/), [E-MTAB-10716](https://www.ebi.ac.uk/arrayexpress/experiments/E-MTAB-10716/), [E-MTAB-10718](https://www.ebi.ac.uk/arrayexpress/experiments/E-MTAB-10718/) and [E-MTAB-11184](https://www.ebi.ac.uk/arrayexpress/experiments/E-MTAB-11184/); provided in FASTQ format; see [here](https://github.com/boehmv/UPF3/blob/main/tools/E-MTAB-sample_IDs.txt) for ID cross-reference), mapped to [Gencode v33](https://www.gencodegenes.org/human/release_33.html) / GRCh38.primary_assembly supplemented with SIRVomeERCCome (from Lexogen; [download](https://www.lexogen.com/wp-content/uploads/2018/08/SIRV_Set3_Sequences_170612a-ZIP.zip)) using STAR, followed by transcript quantification using Salmon in mapping-based mode with a decoy-aware transcriptome index, finished with analyses of differential gene expression (DGE) via DESeq2, differential transcript usage (DTU) via IsoformSwitchAnalyzeR, alternative splicing (AS) via LeafCutter and intron retention (IR) via IRFinder
* The main Bash script [CRSA_V006.sh](https://github.com/boehmv/UPF3/blob/main/CRSA_V006.sh) runs the complete pipeline or individual modules using the options (see CRSA_V006.sh -h) and requires a design file specifying the following:
  * reference type (gencode.v33.SIRVomeERCCome was used in this study)
  * sequencing design (single- or paired-end reads)
  * study name
  * folder locations (srvdir for raw file locations, mydir for analyses output)
  * location of the experiment file which specifies sample IDs and condition
* Please see the provided [design.txt](https://github.com/boehmv/UPF3/blob/main/tools/design_UPF3_dKO.txt) file example for more information concerning this design file. An example for the tab-delimited [experiment.txt](https://github.com/boehmv/UPF3/blob/main/tools/experiment_UPF3_dKO.txt) file is provided as well. Please see the comments in CRSA_V006.sh for further instructions 
* To run/reproduce the complete analysis script, please make sure you have the following tools installed and configured if required:
  * [STAR](https://github.com/alexdobin/STAR) - version 2.7.3a was used for the analyses - with genome indices generated using GRCh38.primary.SIRVomeERCCome.fa and gencode.v33.SIRVomeERCCome.annotation.gtf (both reference files can be found [here](https://uni-koeln.sciebo.de/s/RFID1U3YYBZmkkE)). The following code was used for genome index generation: 
  ```
  STAR   --runMode genomeGenerate   --runThreadN 15   --genomeDir /home/volker/reference/gencode.v33.SIRVomeERCCome   --genomeFastaFiles /home/volker/reference/Gencode/GRCh38.primary.SIRVomeERCCome.fa      --sjdbGTFfile /home/volker/reference/Gencode/gencode.v33.SIRVomeERCCome.annotation.gtf   --sjdbOverhang 99
  ```
  * [Alfred](https://github.com/tobiasrausch/alfred) - version v0.2.1 was used for the analyses
  * [samtools](http://www.htslib.org/) - version 1.9 (using htslib 1.9) was used for the analyses
  * [IGV tools](http://software.broadinstitute.org/software/igv/download) - version 2.8.0 was used for the analyses - make sure you have the gencode.v33.SIRVomeERCCome.chrom.sizes file (can be found [here](https://uni-koeln.sciebo.de/s/RFID1U3YYBZmkkE)) located in /PATH/TO/IGV/lib/genomes
  * [Salmon](https://github.com/COMBINE-lab/salmon) - version v1.3.0 was used for the analyses - with an index generated using gentrome.v33.SIRV.ERCC.fa.gz and decoys.txt (can be found [here](https://uni-koeln.sciebo.de/s/RFID1U3YYBZmkkE)). A separate conda environment was created for Salmon. The following code was used for index generation: 
  ```
  salmon index -t /home/volker/reference/Gencode/gentrome.v33.SIRV.ERCC.fa.gz -d /home/volker/reference/Gencode/decoys.txt -p 12 -i /home/volker/reference/Transcriptome/gencode.v33.SIRVomeERCCome --gencode
  ```
  * [DESeq2](https://github.com/mikelove/DESeq2) - version 1.28.1 was used for the analyses - please see [R_sessions](https://github.com/boehmv/UPF3/tree/main/doc/R_sessions) for details on R version and other installed packages. The tx2gene file used for the analyses can be found [here](https://uni-koeln.sciebo.de/s/RFID1U3YYBZmkkE)
  * [IsoformSwitchAnalyzeR](https://github.com/kvittingseerup/IsoformSwitchAnalyzeR) - version 1.10.0 was used for the analyses - please see [R_sessions](https://github.com/boehmv/UPF3/tree/main/doc/R_sessions) for details on R version and other installed packages
  * [LeafCutter](https://github.com/davidaknowles/leafcutter) - version v0.2.7 was used for the analyses - please see [R_sessions](https://github.com/boehmv/UPF3/tree/main/doc/R_sessions) for details on R version and other installed packages. :memo: **NOTE:** small changes in the /scripts of LeafCutter maintained gene IDs from Gencode (changed in [gtf_to_exons_vb.R](https://github.com/boehmv/UPF3/blob/main/tools/leafcutter/scripts/gtf_to_exons_vb.R) and [leafcutter_ds.R](https://github.com/boehmv/UPF3/blob/main/tools/leafcutter/scripts/leafcutter_ds.R))
  * [IRFinder](https://github.com/williamritchie/IRFinder) - version 1.2.6 was used for the analyses - :memo: **NOTE:** IRFinder results were not used in the publication
  * [FastQC](https://github.com/s-andrews/FastQC) - version 0.11.9 was used for the analyses
  * [MultiQC](https://github.com/ewels/MultiQC) - version v1.8  was used for the analyses
* All analyses were performed on a 16-core (2x Intel(R) Xeon(R) CPU E5-2687W v2 @ 3.40GHz) workstation with 128 GB RAM running Ubuntu 18.04.4 LTS
* Please make sure to change installation and file paths in the respective scripts to match your local environment
* :information_source: Different package versions were previously used for the analyses in the bioRxiv preprint, this repository contains the revised scripts and information 

## Log files
Please see [here](https://github.com/boehmv/UPF3/tree/main/doc/log_files) to access the log files from the complete analysis (run between 16.11.2020 - 18.11.2021)

## Quality control
Please see [here](https://github.com/boehmv/UPF3/tree/main/doc/RNAseq_QC) to access the MultiQC HTML result file for all samples, summarizing the FastQC, Salmon and STAR output. Please download the HTML file and corresponding data to view the MultiQC report properly.

## R_sessions
Please see [here](https://github.com/boehmv/UPF3/tree/main/doc/R_sessions) to access the session info for the individual R scripts

## Individual scripts
The specialized scripts called by the main CRSA_V006.sh script can be found [here](https://github.com/boehmv/UPF3/tree/main/tools).

## Feedback / Questions
Feedback is welcome! For any question, please email: boehmv@uni.koeln.de or [create an issue](https://github.com/boehmv/UPF3/issues)

## Citation
### bioRxiv preprint
Damaris Wallmeroth, Volker Boehm, Jan-Wilm Lackmann, Janine Altmüller, Christoph Dieterich and Niels H. Gehring (2021) __UPF3A and UPF3B are redundant and modular activators of nonsense-mediated mRNA decay in human cells__.
bioRxiv 2021.07.07.451444; doi: https://doi.org/10.1101/2021.07.07.451444 
