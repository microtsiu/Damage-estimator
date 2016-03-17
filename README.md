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
- Mapping reads to the genome : BWA mem paired-end mode.
- Create first in pair mapped reads file (bam1) and second in pair mapped reads (bam2) and derived respective mpileup files (mpileup1 and mpileup2) using ```split_mapped_reads.pl```
- Calculate damage using ```estimate_damage_location.pl```
- plot result using ```plot_damage_location.R```

##DETAILS OF THE MAIN PROGRAMS :

### 1. split_mapped_reads.pl :



### 1. estimate_damage_location.pl :

EXAMPLE :
```estimate_damage_location.pl  --mpileup1 file1.mpileup --mpileup2 file2.mpileup ---out file.damage --id idx12 --qualityscore 25 (DEFAULT 30) ```

DESCRIPTION :

```estimate_damage_location.pl``` Is the main program to calculate the mutation rate for all the substitution including indels. The output of the program can be visualized using plot_damage_location.R. 

OPTIONS :

OUTPUT :
The output of ```estimate_damage_location.pl``` is a table delimited file that can be directly used by ```plot_damage_location.R``` to visualized the damage function of the read positions. 
****






