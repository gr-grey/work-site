---
title: SAM BAM Format
date: 2024-04-16
description: ""
tags:
  - genomics
  - ngs
---
[SAM document](https://samtools.github.io/hts-specs/SAMv1.pdf)

SAM - Sequence Alignment/Map format, is a text-based, Tab-delimited format for storing sequence alignment data.
BAM is the binary form of SAM (smaller size to save disk space).
SAM uses 1-based indices while BAM uses 0-based indices.

Header section: 
  - Each header line starts with '@' followed by a 2-letter code.
  - @HD: Header line with general info such as SAM format version and sorting order.
  - @SQ: Reference sequence dictionary, with info such as sequence name & length.
  - @RG: Read group info such as sequencing platform.
  - @PG: Program used to generate the alignment.

Alignments section - 11 mandatory fields/columns.

| Col | Field | Description                | Example                       |
| --- | ----- | -------------------------- | ----------------------------- |
| 1   | QNAME | Query template name        | GAII05_0002:1:113:7822:3886#0 |
| 2   | FLAG  | bitwise FLAG score         | 4                             |
| 3   | RNAME | Ref seq name               | chr1                          |
| 4   | POS   | 1-based start cor          | 11699950                      |
| 5   | MAPQ  | Mapping quality            | 60                            |
| 6   | CIGAR | CIGAR string               | 39M1D12M                      |
| 7   | RNEXT | Ref name of mate/next read | chr1                          |
| 8   | PNEXT | Pos of mate/next read      | 11700332                      |
| 9   | TLEN  | (Paired) insert size       | 433                           |
| 10  | SEQ   | read seq                   | AATGTAAAAC...                 |
| 11  | QUAL  | Phred33 quality of seq     | HHHAAB^^%CCC...               |
| 12  | OPT   | Optional tags              | XA:i:2, MD:Z:0T34G15          |

FLAG score (column 2)
----
  - FLAG score can be confusing, it's an integer number storing dense information about the read.
  - It answers 11 yes/no questions with 0/1, and string those 0 or 1 together gives you a number in binary, which is the FLAG score (often converted to decimal). The 11 questions are list in the table below.
  - A nice explanation of the FLAG score by Dr. Quinlan can be found [here](https://youtu.be/XU8atPxM0VQ?t=3096).
  - For example, if a read from a single end sequencing is unmapped, and passes quality check and not a PCR duplicate, basically if it answers no (with a 0) to all but one question in the table - question 3 "is the read itself unmapped", which means it has a 1 (yes) at third position of the binary number, then all 0 for other positions - a number of '00000000100', which translate to a FLAG score of 4 in decimal system.

| base2       | base10 | Question                                  | Pair end seq only |
| ----------- | ------ | ----------------------------------------- | ----------------- |
| 00000000001 | 1      | Read comes from paired sequencing         | N                 |
| 00000000010 | 2      | Read mapped in a proper pair              | Y                 |
| 00000000100 | 4      | Read itself is unmapped                   | N                 |
| 00000001000 | 8      | Read's mate is unmapped                   | Y                 |
| 00000010000 | 16     | Strand (0 forward, 1 reverse)             | N                 |
| 00000100000 | 32     | Strand of mate                            | Y                 |
| 00001000000 | 64     | This is the first read in the pair        | Y                 |
| 00010000000 | 128    | This is the second read in the pair       | Y                 |
| 00100000000 | 256    | The alignment is not primary              | N                 |
| 01000000000 | 512    | Read fails platform/vendor quality checks | N                 |
| 10000000000 | 1024   | Read is a PCR/optical duplicate           | N                 |

Table adapted from Dr. Quilan's [slide](https://docs.google.com/presentation/d/1_iT3btOZqjPmVb8Ryk5ssMBCMxoQ0MVmasZ6G0luA-c/edit#slide=id.g1bbc187894_79_0).

CIGAR string
----
The string stores the operations needed to turn reference string to the experimentally sequenced read. 
  - M: Alignment match, could be a seq (character) match or mismatch(SNP).
  - I: Insertion.
  - D: Deletion.
  - N: Split or spliced region
  - S: Soft clipping (clipped sequences present in SEQ).
  - H: Hard clipping (clipped sequences not present in SEQ).
  - P: Padding.
  - =: Seq match.
  - X: Seq mismatch (SNP).

Example:
<pre>
Reference: ACCTGTC<span style="color:red;">--</span>TAC<span style="color:red;">C</span>TTACG
Read:      ACCT<span style="color:red;">-</span>TCCATAC<span style="color:red;">T</span>TTA<span style="color:blue;">TC</span>
Action:    MMMMDMMIIMMMMMMMSS
CIGAR:     4M 1D2M2I   7M  2S
</pre>