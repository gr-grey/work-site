---
title: BED Format
date: 2024-04-16
description: ""
tags:
  - genomics
  - ngs
---

[BED document](https://genome.ucsc.edu/FAQ/FAQformat.html#format1)

BED - Browser Extensible Data format,
is a text-based tab-delimited format representing intervals (chunks) of sequences, widely used in gene annotations.
BED format uses 0-based indices.

3 mandatory columns: 
  1. Chromosome 
  2. Start position
  3. End position

9 optional columns:
  4. Name
  5. Score
  6. Strand
  7. Thick start
  8. Thick end
  9. Item RGB
  10. Block count
  11. Block size
  12. block starts

BED file example [from Ensembl](https://useast.ensembl.org/info/website/upload/bed.html)

```yaml
chr7  127471196  127472363  Pos1  0  +  127471196  127472363  255,0,0
chr7  127472363  127473530  Pos2  0  +  127472363  127473530  255,0,0
chr7  127473530  127474697  Pos3  0  +  127473530  127474697  255,0,0
```
