---
title: FastA FastQ Format
date: 2024-04-16
description: ""
tags:
  - genomics
  - ngs
---

FastA - a text-based format for storing sequences.
  - Sequence identifier line, starting with ">"
  - Followed by *one or more* lines of sequence data

Example of FASTA file

```bash
>seq1
ATCGATCGATCGATCGATCGATCGATCGATCG
>seq2
CGATCGATCGATCGATCGATCGATCGATCGAT
```

FastQ - text-based format for storing sequencing data (coming off a sequencer).

FASTQ stores each read in 4 lines:
  1. Sequence identifier, starting with "@"
  2. Raw sequence read
  3. Separator line, "+"
  4. Quality score of each nucleotide in the read, encoded as ASCII characters.

FASTQ can contain billions of reads and be very big in size.

FastQ file example [from international genome](https://www.internationalgenome.org/category/fastq/)

```
@ERR059938.60 HS9_6783:8:2304:19291:186369#7/2
GTCTCCGGGGGCTGGGGGAACCAGGGGTTCCCACCAACCACCCTCACTCAGCCTTTTCCCTCCAGGCATCTCTGGGAAAGGACATGGGGCTGGTGCGGGG
+
7?CIGJB:D:-F7LA:GI9FDHBIJ7,GHGJBKHNI7IN,EML8IFIA7HN7J6,L6686LCJE?JKA6G7AK6GK5C6@6IK+++?5+=<;227*6054
```