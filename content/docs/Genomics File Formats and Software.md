---
title: Genomics File Formats
collection: courses
date: 2024-04-16
description: "SAM, BAM, BED, GFF/GTF, VCF/BCF, FastA FastQ, samtools, bedtools, bcftools, etc."
tags:
  - courses
  - linux/unix
  - genomics
---

> 90% of bioinformatics is just converting from one file format to another.

Here are some notes taken from[Coursera - Command Line Tools for Genomic Data Science](https://www.coursera.org/learn/genomic-tools) course.
It covers common file formats: SAM/BAM, BED, GFF/GTF, VCF/BCF, FastA/Q; 
as well as showcase the usage of common software (samtools, bedtools, bcftools, etc.) for read alignments, genome arithmetic and genetic variants calling via some course projects/ problems.

Common workflow:
 - FastA files are often used to store reference genomes, FastQ for sequencing reads.
 - Aligning the reads to a reference genome involves SAM format to store alignment info.
 - With reads aligned, genetic variants can be called, generating VCF files.
 - Genetic intervals/segments/annotations can be presented in BED and GFF/GTF files.

[[SAM BAM Format]]
----
Headers, fields, FLAG scores, CIGAR strings

[[BED Format]]
----

[[GFF GTF GFF3 Format]]
----
General feature format.

[[VCF BCF Format]]
----

[[FastA FastQ Format]]
----
And quality scores.


Format Summary
----

| Format       | Usage               | 0/1 based   | Column Names (in bioawk)                                                                                               | Metadata                              | Software                                                        |
| ------------ | ------------------- | ----------- | ---------------------------------------------------------------------------------------------------------------------- | ------------------------------------- | --------------------------------------------------------------- |
| BED          | interval arithmetic | 0           | 1:chrom 2:start 3:end 4:name 5:score 6:strand 7:thickstart 8:thickend 9:rgb 10:blockcount 11:blocksizes 12:blockstarts | .                                     | bedtools                                                        |
| SAM/BAM      | alignment           | SAM:1 BAM:0 | 1:qname 2:flag 3:rname 4:pos 5:mapq 6:cigar 7:rnext 8:pnext 9:tlen 10:seq 11:qual                                      | HD, SQ, RG, PG                        | samtools, BWA-mem, Novoalign, bowtie2, Tophat, STAR, GSNAP, IGV |
| VCF/BCF      | variant call        | VCF:1 BCF:0 | 1:chrom 2:pos 3:id 4:ref 5:alt 6:qual 7:filter 8:info                                                                  | fileformat, INFO, FILTER, ALT, config | bcftools, vcftools                                              |
| GFF3/GFF/GTF | genetic feature     | 1           | 1:seqname 2:source 3:feature 4:start 5:end 6:score 7:filter 8:strand 9:group 10:attribute                              | version                               | .                                                               |
| FastA/Q      | seq reads           | .           | 1:name 2:seq 3:qual 4:comment                                                                                          | .                                     | fastp                                                           |

0 and 1 index systems
----

0-based
  - python style, start from 0 with an OPEN end (up to but NOT include the end point). 
  - E.g. the first 5 characters in a string have indices 0, 1, 2, 3, 4; the segment is represented as \[start:end) - \[0:5) 
  - number of elements = end - start
  - BAM, BCF, BED, and PSL formats are 0-based.
1-based
  - closer to the natural way of counting things, start from 1 with a CLOSED end (include the end point).
  - E.g. the first 5 characters in a string have indices 1, 2, 3, 4, 5; the segment is represented as \[start:end\] - \[1:5\] 
  - number of elements = end - start + 1
  - SAM, VCF, GTF, GFF and Wiggle formats are 1-based.

Common traps
----
**BEWARE**
- inconsistent chromosome labels (chr1 v Chr1 v CHR1)
- different sorting criteria (alphabetic v numerical)
- mixed UNIX/Windows newlines
- file violate spec with vigor
- program expects exact extension
- file is gzipp'ed not bgzipp'ed
- annotations us diff. genome builds
- tool only works for one format
- tools is hard-coded for specific build
- tool requires act of gods to compile

Source [Applied Computational Genomics](https://www.youtube.com/watch?v=tq3GeDXbZXA&t=2079s).

So be careful and cautious, and make sure to understand the columns, parameters, and values being presented/calculated.

When in doubt, don't assume, check the document - it saves time to get it right the first time :D

Problems/ Projects
====
I personally found the descriptions of the problem hard to understand (so if you feel that way too you're not alone). I ended up rephrasing a lot of it.

1: unix basic (grep, cut, awk, comm, sort, uniq)
----

Files needed for the project are [here](https://github.com/gr-grey/genomic-courses/blob/46af89aa31b5e30a75b834fcc6cf81c8a153f98c/cmd-tools/files/project1/gencommand_proj1_data.tar.gz)
for download and extraction. 

```bash
tar -xvf gencommand_proj1_data.tar.gz
```

### Q1, in apple.genome, each chromosome starts on a line with ">", how many chromosomes are there?

```bash
grep -c ">" apple.genome
# A: 3
```

### Q2-3, in apple.genes, the first column stores gene, the second column stores transcript, what's the number of unique genes and unique transcripts in the file?

For genes, cut out first column and `sort -u` to find uniq values, then count.
For transcripts, same operation for the second column.


```bash
# Solution 1, with cut, change to cut -f 2 for transcripts
cut -f 1 apple.genes | sort -u | wc -l 
# Solution 2, with awk, change to $2 for transcripts
awk '{print $1}' apple.genes | sort -u | wc -l 
# A: genes 5453, transcript 5456
```

### Q4-5, a gene can have multiple transcripts (two lines can have the same value in first column and differ in the second column), how many genes have only one transcripts, how many genes have more than one transcripts?

Cut out the first column and count the how many time each genes occurs, which gives the number of their transcripts. 
Then 

```bash
# genes with 1 transcript
cut -f 1 apple.genes | sort | uniq -c | grep -c " 1 "
cut -f 1 apple.genes | sort | uniq -c | awk '$1 == 1' | wc -l

# genes with >1 transcripts
cut -f 1 apple.genes | sort | uniq -c | grep -v " 1" | wc -l
cut -f 1 apple.genes | sort | uniq -c | awk '$1 > 1' | wc -l

# A: 1 transcript 5450, more than 1 transcripts 3
```

### Q6-7, in apple.gene, column 4 stores strand (+ or -) information, how many genes are on + strand, how many on - strand?
### Q8-13, column 3 stores chromosome information, count the number of genes on chr1, chr2, chr3; count the number of transcripts on chr1/2/3.

Question 6 through 13 are about filtering out lines that match certain value in a column. The solutions below covers the situation of counting genes. To count transcripts, change genes (column 1) to transcripts (col2).  

- Solution 1: cut out both gene column and filter column (strand or chromosome), uniq the combination, then count how many +/- strand or chromosomes there are. A problem with this method is if one gene maps to 2 strands/chromosomes (which shouldn't occur here), it'll count the gene twice. 
- Solution 2: similar to solution 1, after getting the uniq combination, we can also get the counts by `grep -c`
- Solution 3: awk can be used to filter value in a column. `awk '$4=="var"'` will return lines where column 4 matches "var". We can filter to get matching values, then count the number of unique genes.

To me, solution 3 is the most intuitive and easiest to read one, awk is a command worth learning for handling column based files.   

```bash
# solution 1
cut -f 1,4 apple.genes | sort -u | cut -f 2 | sort | uniq -c

# solution 2 with grep, 
#-w means work match with regular expression pattern 
# $ is end of line in regular expression.
cut -f 1,4 apple.genes | sort -u | grep -c -w "+$"
cut -f 1,4 apple.genes | sort -u | grep -c -w "\-$" # '-' is special char in reg exp

# solution 3 with awk
awk '$4 == "+"' apple.genes | cut -f 1 | sort -u | wc -l
awk '$4 == "-"' apple.genes | cut -f 1 | sort -u | wc -l

# for chromosome filters, change column 4 to col 3
cut -f 1,3 apple.genes | sort -u | cut -f 2 | sort | uniq -c
cut -f 1,3 apple.genes | sort -u | grep -c -w "chr1"
awk '$3 == "chr1"' apple.genes | cut -f 1 | sort -u | wc -l
# change to chr2/3, then for transcripts change column 1 to column

# A: genes on +/- strand: 2662/2791 
# genes on chr1/2/3: 1624/2058/1771
# transcripts on chr1/2/3: 1625/2059/1772
```

### Q14-17, column 1 in apple.conditionA/B/C stores gene, find out the number of genes exists (1) in both condition A and B, (2) only in A, (3) only in B, (4) in A,B,C.

First we need to cut out the gene columns in those 3 files for further comparison.

`comm file1 file2` compares two *sorted* files, returns 3 columns - lines only in file1; only in file2; in both file1 and file2.

We can pass in the number of columns to be *omitted* in the output, e.g. `comm -12 file1 file2` only returns the third column with common lines.

The last question asks to compare 3 files, we can output common genes of A and B, then compare it with C. 
Or a whacky way to achieve this is to concatenate genes from all 3 files, then count the genes that occur 3 times.

```bash
# prepare sorted files for comparison
cut -f 1 apple.conditionA | sort -u > condA_genes
cut -f 1 apple.conditionB | sort -u > condB_genes
cut -f 1 apple.conditionC | sort -u > condC_genes

# comm 3 column outputs: only in A; only in B; in both A and B 
# pass the column you want to omit as parameters
comm -12 condA_genes condB_genes | wc -l # genes in both A and B
comm -23 condA_genes condB_genes | wc -l # genes in only A
comm -13 condA_genes condB_genes | wc -l # genes in only B

# output common in A and B and compare with C
comm -12 condA_genes condB_genes > AB_genes
comm -12 AB_genes condC_genes| wc -l
# alternative solution, cat all three genes and count genes that occur 3 times
# cat file{1,2,3} is the same as saying cat file1 file2 file3
cat cond{A,B,C}_genes | sort | uniq -c | grep -c " 3 "

# A: genes in A&B 2410; only A 1206; only B 1243; ABC 1608
```

2: process SAM/BAM/BED/GTF files
----

Files needed for the project are [here](https://github.com/gr-grey/genomic-courses/blob/474475e7a1e95277e73314bac412c75676f7febc/cmd-tools/files/project2/gencommand_proj2_data.tar.gz)

```bash
tar -xvf gencommand_proj2_data.tar.gz
```

### Q1-5 Inspect the "athal_wu_0_A.bam" file with samtools and answer the following questions:
  - How many alignments are there in the bam file? (Q1)
  - For SAM format, column 7 is the "RNEXT" field, which contains the reference name of the mate/next read, a value of "=" in this field means the mate is mapped to the same chromosome (most common for pair end sequencing); whereas a "\*" means the mate is unmapped. In the given bam file, find out the number of alignments that (a) with a mate mapped to the same chromosome (Q4); and (b) with an unmapped mate (Q2)? 
  - How many alignments have a deletion (have 'D' in its CIGAR string / column 6)? (Q3)
  - How many alignments have a intron (have 'N' in its CIGAR string)? (Q5)

```bash
# Q1, samtools flagstat shows an overview of the file, including the number of alignment
samtools flagstat athal_wu_0_A.bam

# Q2 and Q4, cut out column 7 
# and count the occurrences of "=" (same chromosome) and "*" (unmapped)

# Solution 1: use uniq to count instances of "=" and "*"
samtools view athal_wu_0_A.bam | cut -f7 | sort | uniq -c
# Solution 2: use awk to count occurrences of "=" and "*"
samtools view athal_wu_0_A.bam | awk '$7 == "="' | wc -l # same with "*"
# Solution 3: bioawk supports $rnext variable, without having to remembering it's column 7
samtools view -h -o sam_athal.sam athal_wu_0_A.bam # covert bam to same file
bioawk -c sam '$rnext=="="' sam_athal.sam | wc -l # rnext is column 7

# Q3 and Q5, cut out column 6 and count the strings that contain 'D' (Q3) and 'N' (Q5)
# Solution 1: use grep to count instances strings that have 'D' in it
samtools view athal_wu_0_A.bam | cut -f6 | grep -c "D"
# Solution 2: use awk to match patterns with 'D' in it
samtools view athal_wu_0_A.bam | awk '$6 ~ /D/' | wc -l
# Solution 3: use bioawk to match patterns with 'D' in it
bioawk -csam '$cigar ~ /D/' sam_athal.sam | wc -l

# A: Q1 221372; Q2 65521; Q3 2451; Q4 150913; Q5 0
```

### Q6-10 from the original "athal_wu_0_A.bam" file, extract alignments with in the range of Chr3:11777000-1179400 to a new bam file, then answer the same set of question as Q1-5 for the new bam file.

```bash
# Extract alignments within the range and store them in range.bam
samtools view -b athal_wu_0_A.bam "Chr3:11777000-11794000" > range.bam
# repeat commands from Q1 to Q5 with the new range.bam file

# A: Q6 7081; Q7 1983; Q8 31; Q9 4670; Q10 0
```

Note: the bam file here are already sorted and indexed (the index file "athal_wu_0_A.bam.bai" is included).
However, in the case where a bam file is not sorted, you need to sort and index it before you can extract the range
```bash
# sort bam file
samtools sort athal_wu_0_A.bam -o mysorted.bam
# index, will create mysorted.bam.bai file
samtools index mysorted.bam
# extract range as before
samtools view -b mysorted.bam "Chr3:11777000-11794000" > range.bam
```

### Q11-13: check the header of the "athal_wu_0_A.bam" file to get more information
  - How many reference genomes were used to align the sequences? In header, @SQ SN lines has ref sequence info. (Q11)
  - Among all ref genomes above, what's the length of the first reference genome? (Q12)
  - What alignment program/tools was used? (In header, @PG shows the program used.) (Q13)

```bash
# Reference genome and alignment program can be found in the header
samtools view -H athal_wu_0_A.bam 
```

```
@HD     VN:1.0  GO:none SO:coordinate
@SQ     SN:Chr1 LN:29923332     AS:wu_0.v7.fas  SP:wu_0.v7.fas
@SQ     SN:Chr2 LN:19386101     AS:wu_0.v7.fas  SP:wu_0.v7.fas
@SQ     SN:Chr3 LN:23042017     AS:wu_0.v7.fas  SP:wu_0.v7.fas
@SQ     SN:Chr4 LN:18307997     AS:wu_0.v7.fas  SP:wu_0.v7.fas
@SQ     SN:Chr5 LN:26567293     AS:wu_0.v7.fas  SP:wu_0.v7.fas
@SQ     SN:chloroplast  LN:154478       AS:wu_0.v7.fas  SP:wu_0.v7.fas
@SQ     SN:mitochondria LN:366924       AS:wu_0.v7.fas  SP:wu_0.v7.fas
@RG     ID:H100223_GAII05_0002  PL:SLX  LB:wu_PII       PI:400  DS:wu_0_Genome  SM:wu_0
@RG     ID:Wii_PER01    PL:SLX  LB:wu_phaseI    PI:400  DS:wu_0_Genome  SM:wu_0
@RG     ID:Wii_PER02    PL:SLX  LB:wu_phaseI    PI:400  DS:wu_0_Genome  SM:wu_0
@RG     ID:Wii_SR03     PL:SLX  LB:wu_phaseI    PI:400  DS:wu_0_Genome  SM:wu_0
@PG     ID:stampy       VN:1.0.3_(r627) CL:-g /lustre/scratch103/sanger/xcg/tmp/tmp.zYfXz26246 -h /lustre/scratch103/sanger/xcg/tmp/tmp.zYfXz26246 --readgroup=ID:Wii_PER01,LB:wu_phaseI,SM:wu_0,PI:400,PL:SLX,DS:wu_0_Genome --bwaoptions=-q10 /lustre/scratch103/sanger/xcg/wu_0.v7.fas -o /lustre/scratch103/sanger/xcg/wu_0/A/aln_A1.sp0.sam -M /lustre/scratch103/sanger/xcg/wu_0/read_1_1.sp0.fq.gz /lustre/scratch103/sanger/xcg/wu_0/read_1_2.sp0.fq.gz
@PG     ID:samtools     PN:samtools     PP:stampy       VN:1.16.1       CL:samtools view -H athal_wu_0_A.bam
@CO     TM:Fri, 17 Sep 2010 12:20:13 BST        WD:/lustre/scratch103/sanger/xcg/wu_0/self     HN:bc-16-1-07.internal.sanger.ac.uk     UN:xcg
```

```bash
# Reference genome has SN: in the pattern
samtools view -H athal_wu_0_A.bam | awk '/SN:/' | wc -l
# or use grep
samtools view -H athal_wu_0_A.bam | grep -c "SN:"
# the length can be read off of SN:Chr1 LN:29923332
# the line @PG shows program used
samtools view -H athal_wu_0_A.bam | awk '/@PG/'

# A: Q11 7; Q12 29923332; Q13 stampy
```

### Q14-15, check the first alignment in the "athal_wu_0_A.bam" file and find out (a) the name/identifier (column 1) and (b) the start position of its mate (column 7, 8).
```bash
# display first alignment in the sam file
samtools view athal_wu_0_A.bam | head -1
samtools view athal_wu_0_A.bam | awk 'NR==1' # NR is line number in aw
# for bioawk, the first line has NR of 16 for some reason
bioawk -csam 'NR==16' sam_athal.sam

# cut out column 1 for name, col 7&8 for mate's position
samtools view athal_wu_0_A.bam | head -1 | cut -f1
samtools view athal_wu_0_A.bam | head -1 | cut -f7,8

# A: Q14 GAII05_0002:1:113:7822:3886#0; Q15 Chr3:11700332
```

### Q16-20 use bedtools to investigate overlapping intervals between the given "athal_wu_0_A_annot.gtf" file and the range.bam file in Q6-10, and answer the following questions
  - How many overlapping intervals (output with `-wo` option) are there between the 2 files? (Q16)
  - The length of the overlaps can be found in column 22 with the `-wo` option. How many overlaps has a length of 10 bases or longer? (Q17)
  - How many alignments in the bam file overlap with an interval in the gtf file?  (Q18)
  - Of all the intervals in gtf file that overlap with an alignment in the bam file, how many of them are exons? (Q19) 
  - How many unique transcripts (in column 9, first part of the string) are there in the gtf file (Q20)
 
```bash
# Q16 when use a bam file as input, need to add the -bed flag for outputting in bed format
bedtools intersect -a range.bam -b athal_wu_0_A_annot.gtf -wo -bed | wc -l

# Q17 Count the lines where column 22 has a value >= 10
bedtools intersect -a range.bam -b athal_wu_0_A_annot.gtf -wo -bed | bioawk -t '$22 >=10' | wc -l
# bioawk -t sets the delimiter to tab
# when use awk, need to specify awk -F'\t' -v OFS="\t" 
# F sets input sep, OFS stands for output field separator

# Q18 the "-c" option counts how many intervals in file B overlaps with each intervals in file A
# The counts are stored in column 7, count the number of alignments that has >0
bedtools bamtobed -i range.bam > range.bed
bedtools intersect -a range.bed -b athal_wu_0_A_annot.gtf -c | bioawk -t '$7 >0' | wc -l
# I ran into some problem with the -c flag with bam file, so I converted to bed file first

# Q19 similar to 18, use the gtf as file a, either bam or bed file as b
# the counts are stored in column 10 since gtf came with more columns
bedtools intersect -a athal_wu_0_A_annot.gtf -b range.bam -bed -c | bioawk -t '$10>0' | wc -l

# Q20 cut out column 9 with both transcript and gene name as a string
# then get only the transcripts part and count uniq instances.
cut -f9 athal_wu_0_A_annot.gtf | cut -d ' ' -f1,2 | sort -u | wc -l

# A: Q16 3101; Q17 2899; Q18 3101; Q19 21; Q20 4
```

3: align FastQ reads with bowtie2 and variant calling with bfctools
----

**Important notes**: you need bowtie v.2.2.5 to get the correct answers, a different version will generate a index and different alignment results. The older version can be found [here](https://sourceforge.net/projects/bowtie-bio/files/bowtie2/2.2.5/).
I saved a copy of the pre-compiled [bowtie2](https://github.com/gr-grey/genomic-courses/blob/ada982bc446e5b052b3c2bb06867b8ae5c4ec87f/cmd-tools/files/project3/bowtie2-2.2.5-linux-x86_64.zip) linux version in case the link doesn't work in the future.

In an linux environment (or WSL2), extract the file and add it to PATH variable
`export PATH=/extracted_directory/bowtie2-2.2.5:$PATH`, you can verify by running `which bowtie2`.

Python3 is needed for the bowtie2 program to run.

Files needed for the project can be found [here](https://github.com/gr-grey/genomic-courses/blob/ada982bc446e5b052b3c2bb06867b8ae5c4ec87f/cmd-tools/files/project3/gencommand_proj3_data.tar.gz).

### Q1-3, inspect the "wu_0_v7.fas" file for the following information
  - How many sequences (each starts with ">") are there in the file? (Q1)
  - What's the name of 3rd sequence? (Q2)
  - What's the name of the last sequence? (Q3)

```bash
# Q1
grep -c ">" wu_0.v7.fas

# Q2
grep ">" wu_0.v7.fas | head -3

# Q3
grep ">" wu_0.v7.fas | tail -1

# A: Q1 7; Q2 Chr3; Q3 mitochondria
```

### Q4-5, use botie2 to index the "wu_0_v7.fas" genome. 
  - How many files are created? (Q4) 
  - What's the 3-character extension of the index file? (Q5)

```bash
mkdir wu_0_index
# building index might take a while 
bowtie2-build wu_0.v7.fas wu_0_index/wu_0

# A: Q4 6; Q5 bt2
```

### Q6-14, align the reads in "wu_0_A_wgs.fastq" using bowtie2 with full-match (default) and local-match setting.
  - How many reads are there in the given wu_0_A_wgs.fastq file? (Q6)
  - With full-match setting alignment 
    - How many reads mapped to at least one place in the genome? (Q9)
    - How many reads mapped to *more than* one place in the genome? (Q11)
    - What's the number of total matches reported? (Q7) Although some reads map to >1 loci, bowtie2 by default only report 1 alignment, so this is the same as Q9.
  - With the local-match setting alignment, answer the same set of questions, what's the number of total matches (Q8), which is the same as the number of reads with >= 1 match (Q10), what's the number of reads with > 1 match (Q12).
  - How many alignments contains insertions and/or deletions (has either 'I' or 'D' in CIGAR string) in the full match setting (Q13)? What about the local-match setting (Q14)? CIGAR string is stored in column 6 in SAM format.


```bash
# Q6 In fastq, number of reads = number of lines / 4
wc -l wu_0_A_wgs.fastq # divide line counts by 4

# align reads in the fastq file with default full match setting,
# output in sam format
bowtie2 -p 4 -x wu_0_index/wu_0 wu_0_A_wgs.fastq -S aligned-allend.bt2.sam
# Q9 (Q7) Mapped reads = 147354 (total) - 9635 (unmapped) = 137719
# Q11 43939 reads aligned >1 times (matched to >1 places on the genome)
bowtie2 -p 4 -x wu_0_index/wu_0 wu_0_A_wgs.fastq -S aligned-allend.bt2.sam

# local alignment
bowtie2 -p 4 --local -x wu_0_index/wu_0 wu_0_A_wgs.fastq -S aligned-local.bt2.sam
# Q8 (Q10) 147354 - 6310 = 141044
# Q12 56105 aligned >1 times

# Q13 alignments with I or D in CIGAR string
# [ID] is regular expression of pattern that contains either character in the bracket
awk '$6 ~ /[ID]/' aligned-allend.bt2.sam | wc -l 
# change to local sam output file for Q14

# A: Q6 147354; Q7 137719; Q8 141044; Q9 137719 
# Q10 141044; Q11 43939; Q12 56105; Q13 2782; Q14 2614
```

Alignment summary (default full-match):

147354 reads; of these:
  147354 (100.00%) were unpaired; of these:
    9635 (6.54%) aligned 0 times
    93780 (63.64%) aligned exactly 1 time
    43939 (29.82%) aligned >1 times
93.46% overall alignment rate

Local alignment summary:

147354 reads; of these:
  147354 (100.00%) were unpaired; of these:
    6310 (4.28%) aligned 0 times
    84939 (57.64%) aligned exactly 1 time
    56105 (38.07%) aligned >1 times
95.72% overall alignment rate

### Q15-19, with reads aligned to the genome, we can generate a pileup file to prepare for variant calling later.

Here `bcftools mpileup` was used to generate [pileup format](http://www.htslib.org/doc/samtools-mpileup.html#:~:text=Pileup%20Format,encoded%20as%20individual%20ASCII%20characters.), where the reads are lined up to the genome, and we take a slice of one nucleotide position (one column), and store that information in one line for variant caller to inspect this position and judge if there's a mutation.

One site/locus can have multiple lines representing different situations such as SNP and INDEL. 

The questions in the course practical originally used samtools (older version of samtools can output in VCF format with -g or -u). However this feature is deprecated and removed, and we need `bcftools mpileup` instead.

Prepare a pileup file (using the alignment with the full-match default setting) in VCF format with bcftools.
Note that the alignment sam file needs to be sorted before generating the VCF file.
Find the following information of the output file:
  - How many lines are located in Chr3 (chromosome name in column1)? (Q15)
  - How many lines have "A" in the reference genome (REF base in column 4)? (Q16)
  - How many lines have depth cover of exactly 20 (DP=20 in column 8)? (Q17)
  - How many lines are reporting "INDEL" value in column 8? (Q18)
  - How many lines correspond to position Chr1:175672 (column 1,2)? (Q19)

```bash
# sort and index the alignment results
samtools view -bT wu_0.v7.fas aligned-allend.bt2.sam > aligned.bam
samtools sort aligned.bam -o sorted.bam

bcftools mpileup -f wu_0.v7.fas -O v sorted.bam > mpileup.vcf

# Q15 lines with a value of Chr3 in column 1
awk '$1=="Chr3"' mpileup.vcf | wc -l
# Q16 lines with a value of A in column 4
awk '$4=="A"' mpileup.vcf | wc -l
# Q17 lines with DP=20 in column8
awk '$8 ~ /DP=20/' mpileup.vcf | wc -l
# Q18 lines with INDEL in column8
awk '$8 ~ /INDEL/' mpileup.vcf | wc -l
# Q19 lines with Chr1 in col1 and 175672 in col2 
awk '$1=="Chr1" && $2==175672' mpileup.vcf | wc -l

# A: Q15 360180; Q16 1150900; Q17 1816; Q18 250; Q19 1
```
Note: Here Q15, 16 and 19 have different answers than the courses answer sheet for project 2,
due to software version difference - they used an older version of samtools to generate the VCF file.
The point is to learn about the file format and the workflow of variant calling.
If you need to pass the exam, the answer you need are Q15 360295; Q16 1150985; Q19 2.

### Q20-24 with the pileup file ready, we can call variants with bfctools

Bcftools has a [call](https://samtools.github.io/bcftools/bcftools.html#call) command to call variants from a mpileup file, here we use `-m` for multiallelic caller and `-v` for variant only. The variant calling result is output in a VCF file by specifying `-Ov`.

Inspect the VCF file with all the variants generated by `bcftools call` for the following information.
  - How many variants are located on Chr3 (chromosome name in column1)? (Q20)
  - How many lines represents an A->T SNP (has "A" in col4 and "T" in col5)? (Q21)
  - How many lines represents INDELS (contain "INDEL" value in column 8)? (Q22)
  - How many lines have depth cover of exactly 20 (DP=20 in column 8)? (Q23)
  - What's the type of variant (SNP or INDEL) at position Chr3:11937923 (column 1,2)? (Q19)

```bash
# variant calling with bcftools call
# store output in variants.vcf
bcftools call -mv mpileup.vcf -Ov -o variants.vcf

# Q20 lines with Chr3 in column 1
awk '$1=="Chr3"' variants.vcf | wc -l
# Q21 lines with A in col4 and T in col5
awk '$4=="A" && $5=="T"' variants.vcf | wc -l
# Q22 lines with INDEL in col8
awk '$8 ~ /INDEL/' variants.vcf | wc -l
# Q23 line with DP=20 in col8
awk '$8 ~ /DP=20/' variants.vcf | wc -l
# Q24 line with Chr3 in col1 and 11937923 in col2
awk '$1=="Chr3" && $2==11937923' variants.vcf

# A: Q20 448; Q21 495; Q22 94; Q23 2; Q24 SNP
```
Again, different answers for Q20-22 in the course exam, caused by different software versions.
If you need to pass the exam, here are the answers you need:
Q20 398; Q21 392; Q22 320.