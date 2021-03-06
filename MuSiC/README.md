## Docker of Genome-MuSiC

This docker contains an Ubuntu 10.04 lucid system and pre-installed Genome MuSiC 0.4. Provides an environment for running MuSiC in instance.

This docker could be built in any docker environment where the internet is available. The apt-get source in this package is mirrors.163.com, if you are not in China or have poor speed connecting that source, you can change the source.list file.

### Running method
---

To build the docker image locally, just run:

```
./build [your image name]
# example: ./build readline/music
```

---

To start a container for MuSic analysis, run:
```
# Run it in a interactive mode
docker run -it -v /path/to/your/files:/path/to/mount/your/files readline/music /bin/bash
```

### Automatic running tool

```
./dockerMuSiC.py -h
Usage: dockerMuSiC.py [options]

Options:
  -h, --help            show this help message and exit
  -b BAMLIST, --bamlist=BAMLIST
                        Bamlist file path
  -a BAMPATH, --bampath=BAMPATH
                        Bampath accord with the bamlist
  -m MAF, --maf=MAF     Maf file path
  -r ROI, --roi=ROI     Roi file path
  -f REF, --ref=REF     Reference fasta path
  -n NDEPTH, --ndepth=NDEPTH
                        Normal min depth [default=14]
  -t TDEPTH, --tdepth=TDEPTH
                        Tumor min depth [default=8]
  -q MAPQ, --mapq=MAPQ  Min mapq [default=1]
  -p THREADS, --threads=THREADS
                        Num of threads [default=1]
  -o PREFIX, --prefix=PREFIX
                        Output path prefix
  -d DOCKER, --docker=DOCKER
                        Docker image name
```
Example:
```
./dockerMuSiC.py -b /path/to/bamlist -a /path/to/bamfile -m /path/to/maf -r /path/to/roi -f /path/to/reference.fa -p 24 -o /path/to/output -d finno/music
```

This tools could run the entire pipe of MuSiC, but if you can't tolerate the single thread calc-covg, you can use the docker with interactive mode and run 
```
genome music bmr calc-covg \
--roi-file /path/to/roi \
--reference-sequence /path/to/reference.fa \
--bam-list /path/to/bamlist \
--output-dir music \
--normal-min-depth 14 \
--tumor-min-depth 8 \
--min-mapq 1 
```
in parrallel.

Then, break down the automatic docker pipe, copy the music ready covg file to the /path/to/output/music/roi_covgs/ directory, then, restart the container. You would skip the calc-covg part and go on.


### Infomations about MuSiC

[MuSiC: identifying mutational significance in cancer genomes.](http://www.ncbi.nlm.nih.gov/pubmed/22759861)

[MuSiC software page](http://gmt.genome.wustl.edu/packages/genome-music/index.html)

---

The decreasing cost of sequencing has moved the focus of cancer genomics beyond single genome studies to the analysis of tens or hundreds of patients diagnosed with similar cancers. Besides the routine discovery and validation of SNVs, indels, and SVs in individual genomes, it is now paramount to systematically analyze the function and recurrence of mutations across a cohort, and to describe how they interact with one other or associate to clinical data. To this end we have developed the Mutational Significance In Cancer (MuSiC) suite of tools. It consists of downstream analysis tools that can:

Apply statistical methods to identify significantly mutated genes
Highlight significantly altered pathways
Investigate the proximity of amino acid mutations in the same gene
Search for gene-based or site-based correlations to mutations and relationships between mutations themselves
Correlate mutations to clinical features, using typical correlation measures, and generalized linear models
Cross-reference findings with relevant databases such as Pfam, COSMIC, and OMIM
Generate typical visualizations like Kaplan-Meier survival estimates, and mutation status matrices
In an attempt to remain versatile and powerful, MuSiC incorporates a command-line interface with minimal inputs, described as follows:

Coverage: Mapped reads from a group of tumor/normal sample pairs in BAM format. UCSC WIG files describing coverage may be used if BAMs are unavailable.
Variants: The predicted or validated SNVs and indels from the cohort in TCGA MAF format (VCF support coming soon).
Regions of Interest: A set of regions the user is interested in studying, typically the boundaries of coding regions, but can be expanded to non-coding RNA, conserved regions, whole genome, chromosome bands, etc.
Clinical data: Any relevant clinical data segregated as qualitative and quantitative types (to apply appropriate statistical methods).
Reference sequence: Must correspond to the BAM/WIG files used. HG18, GRCh37, non-human genomes, microbiome, squished transcriptome, etc.
The tools in the suite may be run individually, or may be automated serially. If you decide to parallelize execution, note that a few tools require the outputs of others. Here are the important bits:

bmr calc-bmr requires the files generated by bmr calc-covg
smg requires the gene_mrs file generated by bmr calc-bmr
path-scan requires the gene_covgs directory generated by bmr calc-covg
mutation-relation is computationally expensive, and impractical with more than 100 genes. So it is a good idea to set its —gene-list to the significantly mutated gene list generated by smg
Updates to MuSiC are made available for distribution and served immediately to standard updating mechanisms on Ubuntu and other Debian systems via our applications server.
