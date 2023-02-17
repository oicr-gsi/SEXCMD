# SEXCMD : Created Sex Marker and Determination

 USAGE : perl determine.pl sex_marker_filtered.hg38.final.fasta input.fastq.gz 2> /dev/null 1> input.SEX_OUTPUT

 SEXCMD is available at https://github.com/lovemun/SEXCMD

 The code is written in Perl and bash script. This tool is supported on Linux and needs Python, lastz, blastall and bwa. The tool uses gzip compressed fastq file as input and R1 and R2 file can be used in case of paired-end or mate pair. And you should give marker fasta file path, input fastq file path as auguements.

## Required software
 1. Python (https://www.python.org/ftp/python/2.7.13/python-2.7.13.msi)
 2. bwa(version 7.0 or later) (https://sourceforge.net/projects/bio-bwa/files/)
 3. blastall (ftp://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/)
 4. lastz(Release 1.02.00) (http://www.bx.psu.edu/~rsharris/lastz/lastz-1.04.00.tar.gz)
 5. samtools (https://sourceforge.net/projects/samtools/files/samtools/)
 6. R (https://www.r-project.org/)
 7. R packages e1071, seqinr, optparse
 
## Input Files
 1. Reference genome fasta file (ex. hg38.fa) - for creation of sex marker files
 2. Input sequence file (ex. ERR000000.fastq.gz)

## Example
 If you want to create marker file, you can make own sex marker file for your datafiles. There are 6 process steps for creating own sex marker file.

1. Extract sex chromosome from Reference genome(ex. Human)

  python 1.extract_chr_from_REF.py hg38.fasta X

  python 1.extract_chr_from_REF.py hg38.fasta Y


2. Mapping between sex chromosomes

 lastz hg38.chrX.fasta hg38.chrY.fasta --format=maf > chrX_chrY.maf

3. Generate sex marker text file

 python 2.sex_marker.py chrX_chrY.maf hg38.ucsc.gtf 35 > sex_marker.hg38.txt 
 (distance between marker : 35(recommended))

 python 3.sex_marker_filtered.py sex_marker.hg38.txt hg38.ucsc.gtf 151 5 > sex_marker_filtered.hg38.txt 
 (read length : 151 admit missmatch count : 5)

4. Convert sex marker file format(text -> fasta)

 python 4.make_markerFASTA.py sex_marker_filtered.hg38.txt

5. Filtered ONLY MAPPED to chrX, chrY

 blastn -db hg38.fa -query sex_marker_filtered.hg38.fasta -num_threads 4 -word_size 8 -outfmt 7 > sex_marker_filtered.hg38.fasta.blastm9
 
 python 5.blast_filter.py sex_marker_filtered.hg38.fasta sex_marker_filtered.hg38.fasta.blastm9 sex_marker_filtered.hg38.final.fasta

6. Index sex marker file

 bwa index -a is sex_marker_filtered.hg38.final.fasta

Final sex marker : sex_marker_filtered.hg38.final.fasta

## Sex determination

Rscript SEXCMD.R -m sex_marker_filtered.hg38.final.fasta -t [1/2/3] -s [XY/ZW] -f input.fastq.gz -p [Optional Output Prefix]

[1/2/3] : sequencing type (1: Exome sequencing, 2: RNA sequencing, 3: Whole genome sequencing)

[XY/ZW] : Sex determination system

## Contact

lovemun@kribb.re.kr
pruzanov@oicr.on.ca

## Citation

https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5590872
