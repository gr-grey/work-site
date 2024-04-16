---
title: GFF GTF GFF3 Format
date: 2024-04-16
description: ""
tags:
  - genomics
  - ngs
---

[GFF3 document](https://useast.ensembl.org/info/website/upload/gff3.html)
[GFF/GTF document](https://useast.ensembl.org/info/website/upload/gff.html)

GFF - General Feature Format,
is a text-based format presenting genomic features (1 feature per line).
GFF uses 1-based indices, with 9 mandatory columns.

GTF - General Transfer Format is identical to GFF version 2.

GFF3 - GFF version 3 is more standardized with a fixed number of columns and stricter syntax.

GFF columns
  1. seqid - chromosome Ensembl identifier/scaffold ID
  2. source - program or data source (database or project name)
  3. type - exon, gene, mRNA, etc.
  4. start - 1-based start position
  5. end - 1-based end position
  6. score - A floating point value.
  7. strand - + forward or - reverse.
  8. phase - 0/1/2, indicating the 1st/2nd/3rd base of the feature is the start of a codon.
  9. attributes - A list of tag-value pairs separated by ";". Predefined tags: ID, Name, Alias, Parent, etc.

GFF3 header: GFF3 file must start with a comment that identifies the version, e.g. `##gff-version 3`

GFF file example [from Ensembl](https://useast.ensembl.org/info/website/upload/gff3.html)

```yaml
##gff-version 3
ctg123 . mRNA            1300  9000  .  +  .  ID=mrna0001;Name=sonichedgehog
ctg123 . exon            1300  1500  .  +  .  ID=exon00001;Parent=mrna0001
ctg123 . exon            1050  1500  .  +  .  ID=exon00002;Parent=mrna0001
```