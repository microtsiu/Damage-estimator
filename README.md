### Damage-estimator

##REQUIREMENT : 
Prior to running the Damage estimator workflow please download and install the following programs :

**SAMTOOLS** (http://samtools.sourceforge.net/)
**GGPLOT2 (R)** (http://ggplot2.org/)

##OVERVIEW
This set of programs are designed to estimate the DNA damage when the DNA is sequenced using Illumina plateform on paired-end mode. 
The repository contains 1 basic programs :
estimate_damage_location.pl
 
 
##CONSIDERATIONS :
Damage estimation is based on the systematics mutation rate difference between the first in pair and the second in pair reads. Therefore it is essential that the sequencing is done using Illumina in a paired end mode. BWA mapping is recommended and mapping in paired-end mode is required. To estimate damage in an independent way, DNA sample can be treated with [PreCR][PreCR]. 
 
 [PreCR]: https://www.neb.com/products/m0309-precr-repair-mix 

##TYPICAL WORKFLOW :
- Adaptor trimming.
- Mapping reads to the genome : BWA mem paired-end mode. Make a bam file from the resulting sam file (samtools view -bS sam_file | samtools sort - bam_file) and index the resulting bam file (samtools index bam_file).
- Create first in pair mapped reads file (bam1) and second in pair mapped reads (bam2) and derived respective mpileup files (mpileup1 and mpileup2) using ```split_mapped_reads.pl```
- Calculate damage using ```estimate_damage_location.pl```
- concatenate all the damages into one single file for plotting. 
- plot result using ```plot_damage_location.R```

##DETAILS OF THE ANALYSIS :

### 1. split_mapped_reads.pl :

EXAMPLE :
```perl split_mapped_reads.pl -bam bam_file.bam -genome genome.fasta -mpileup1 file1.mpileup -mpileup2 file2.mpileup -Q 20 (DEFAULT 0) -q 20 (DEFAULT 10)```

DESCRIPTION :
```split_mapped_reads.pl``` is the program to split the mapped first in paired reads into one temporary bam file and the mapped second in paired reads into another temporary bam file. From these files are derived mpileup file 1 and mpileup file 2 resperctively. 

OPTIONS :

OUTPUT :
The outputs of ```split_mapped_reads.pl``` is 2 mpileup files generated by samtools containing all the positions in the genome with at least one read. The file in -mpileup1 correspond to the first in paired reads and the file in -mpileup2 correspond to the second in paired reads. Both files are intermediate to be used by the ```estimate_damage_location.pl``` program.


### 1. estimate_damage_location.pl :

EXAMPLE :
```perl estimate_damage_location.pl  --mpileup1 file1.mpileup --mpileup2 file2.mpileup ---out file.damage --id idx12 --qualityscore 25 (DEFAULT 30)  --max_coverage_limit 200 --min_coverage_limit 10 ```

DESCRIPTION :

```estimate_damage_location.pl``` Is the main program to calculate the mutation rate for all the substitution including indels. The output of the program can be visualized using plot_damage_location.R. 

OPTIONS :

```--max_coverage_limit MAX (DEFAULT 100)``` : If a position has equal or more than MAX reads (R1 or R2), the position is not used to calculate the damage. This option is put in place in order to avoid high coverage regions of the genome being the main driver for the damage estimation program.

```--min_coverage_limit MIN (DEFAULT 1)``` : If a position has equal or less than MIN reads (R1 or R2), the position is not used to calculate the damage. This option is put in place in order to calculate damage only in on-target regions (in cases of enrichment protocol such as exome ....)

```--qualityscore MIN``` : Discard the match or mismatch if the base on a read has less than MIN base quality. Important parameters. The lower this limit is, the less the damage is apparent. 

OUTPUT :
The output of ```estimate_damage_location.pl``` is a table delimited file that can be directly used by ```plot_damage_location.R``` to visualized the damage function of the read positions. 
****
