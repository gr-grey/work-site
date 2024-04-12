---
title: Indexing The Genome - Pattern Matching, Kmer, Sorted List and Hash Table
date: 2024-04-12
description: ""
tags:
  - ngs
  - algorithms
  - python
---

The [[Boyer-Moore Matching Algorithm]] uses the bad character and the good suffix rules to skip futile alignments and speed up the search.
It preprocesses the pattern P to build up look up tables for the two rules.

Another way to speed up the search is to preprocess the text T.
Just like indices at the end of a book, the purpose of indexing/preprocessing T is to help us quickly locate the content of interest (pattern P) in T.

Matching/searching algorithms where T is preprocessed is called an **offline search**, otherwise it's called an **online search**.

Note that this definition does not care if P is preprocessed or not, as algorithms that preprocesses P usually do it on the fly, whereas the preprocessing of T is usually done before hand and the indices stored somewhere for a quick look up or search. 
For example, the Boyer-Moore algorithm is an online searching algorithm, as it does not preprocess the text T.

In our read alignment problem, many sequencing reads need to be aligned to the same reference genome, so even it might take a long time to index the genome, the cost vanishes over billions of alignments.

Indexing approaches: sorted lists, hash tables
----

To index the reference genome, which is a string of text T we want to align the pattern P to, we need to pick a length of substring and chop T up to get all possible substrings in T, each substring is called a **key**, we need to store the keys and their locations/offsets for **query** the index.

If length of each key is k, it's called a **k-mer** index. Here's an example of building a 5-mer index for T, which is to get all possible substrings of T with length 5.

 <pre>
   0123456    
T: CGTGCGTGCTT
   <span style="color:crimson;text-decoration:underline">CGTGC</span>GTGCTT 
   C<span style="color:darkorange;text-decoration:underline">GTGCG</span>TGCTT
   CG<span style="color:goldenrod;text-decoration:underline">TGCGT</span>GCTT
   CGT<span style="color:green;text-decoration:underline">GCGTG</span>CTT
   CGTG<span style="color:darkcyan;text-decoration:underline">CGTGC</span>TT
   CGTGC<span style="color:dodgerblue;text-decoration:underline">GTGCT</span>T
   CGTGCG<span style="color:blueviolet;text-decoration:underline">TGCTT</span>

Index of T
CGTGC: <span style="color:crimson">0</span>, <span style="color:darkcyan">4</span>
GTGCG: <span style="color:darkorange">1</span>
TGCGT: <span style="color:goldenrod">2</span>
GCGTG: <span style="color:green">3</span>
GTGCT: <span style="color:dodgerblue">5</span>
TGCTT: <span style="color:blueviolet">6</span>
</pre>

 Two common structures for building indices are sorted lists and hash tables.
 
### Sorted list

- Sort keys alphabetically for quick searches/queries.
- A sorted list allows us to perform a binary search and find the key in Log2(N) time.
- The sorted list can be implemented in an array or a linked list, with each element of the list consisting of a key and a pointer to the corresponding data item. 
<pre>
Sort keys alphabetically
CGTGC: <span style="color:crimson">0</span>, <span style="color:darkcyan">4</span>
GCGTG: <span style="color:green">3</span>
GTGCG: <span style="color:darkorange">1</span>
GTGCT: <span style="color:dodgerblue">5</span>
TGCGT: <span style="color:goldenrod">2</span>
TGCTT: <span style="color:blueviolet">6</span>
</pre>

### Hash tables

- The keys can also be stored as a hash table/ hash map/ dictionary.
- The data structure allows constant O(1) look up time.

Index assisted exact search: query & verification
----
Once a k-mer index is built for the text T, for each pattern P, a match can be found by 2 steps.
1. Take the first k characters of P (`P[:k]`), it has to match to one of the keys in the index. The matches/hits can be retrieved by querying the index.
2. For each hit, verify if that the rest of P (`P[k:]`) is also matched with T. Say a hit has offset h, we need to compare whether `P[k:]` matches `T[h+k: h+k+len(p)]`

Note: it doesn't have to be the first kmer in P for the query, the second, third, or any kmer in P would work the same, but for each hit, we still need to check whether the remaining part (all characters outside of that kmer we query) matches T.

Not all k-mers required: every nth kmer
----
When we record all k-mers (substrings) in the index, we can query using *any* (the 1st, 2nd, 3rd, etc.) k-mer of P, which indicates there may be some redundancy to the k-mer library - if we can utilize multiple kmers from P.

### Every other k-mer
What if instead of every k-mer, we only record every other k-mer in T? 

It means the k-mers are taken form either even offsets (0, 2, 4, 6, ...) or odd offsets (1, 3, 5, 7, ...).
Let's take the even offsets as an example.

- When recording all k-mers of T (t_kmer), for each P, we can take any k-mer of P (p_kmer) and query and verify if it's a match.
- If the index only has k-mers with even offset, for a p_kmer, we lose the hits where p_kmer matches a t_kmer with an odd offset.
- However, for each case where p_kmer matches a t_kmer with odd offset and eventually leads to a *full match*, the kmer right next to the p_kmer in P (say p_kmer') would match to the kmer right next to t_kmer - which would be a t_kmer with *an even offset*.
- So if we query both p_kmer and p_kmer' (the kmer right next to it) against t_kmers with even offset, we can cover the matches yielded from p_kmer matching an odd offset t_kmer. 

Note that p_kmer' doesn't need to be right next to p_kmer, just one of them needs to have a even offset (in P), and another to have an odd offset.

### Every nth k-mer
In the generalized case of recording every nth k-mer in T, we need to check n different kmers in P, the offset of those p_kmers need to cover every module of n, i.e., there needs to be a p_kmer with offset % n equals to 0, another with offset % n equals to 1, etc., all the way to offset % n equals n - 1.

For example, we can take every 3rd t_3mer, and for each P, we need to query 3 p_3mers with offset mod 3 equals to 0, 1, 2, respectively, there we chose 3mers starting at offset 3, 1 and 5, respectively. 

<pre>
Every 3rd k-mer
       T: <span style="color:dodgerblue">∎∎∎∎∎∎∎∎∎∎∎∎∎∎</span>
   Index: <span style="color:dodgerblue">∎∎∎   ∎∎∎</span>   
every 3rd    <span style="color:dodgerblue">∎∎∎   ∎∎∎</span>

       P: <span style="color:forestgreen">∎∎∎∎∎∎∎∎</span>
              <span style="color:forestgreen">∎∎∎</span> 0 mod 3
   Query:  <span style="color:forestgreen">∎∎∎</span> 1 mod 3
                <span style="color:forestgreen">∎∎∎</span> 2 mod 3
</pre>

Taking only every nth k-mer will make the index smaller and faster to query.

Subsequences vs substrings
----
So far we've taken strings - continuous chunks of k-mers.

More generally the index can be sliced with any pattern, like the following pattern of extracting characters at offset `[0-1, 3, 5-6]`.

<pre>
   012345678    
T: CGTGCGTGCTT
   <span style="color:dodgerblue;text-decoration:underline">CG</span>T<span style="color:dodgerblue;text-decoration:underline">G</span>C<span style="color:dodgerblue;text-decoration:underline">GT</span>GCTT 
   C<span style="color:dodgerblue;text-decoration:underline">GT</span>G<span style="color:dodgerblue;text-decoration:underline">C</span>G<span style="color:dodgerblue;text-decoration:underline">TG</span>CTT
   CG<span style="color:dodgerblue;text-decoration:underline">TG</span>C<span style="color:dodgerblue;text-decoration:underline">G</span>T<span style="color:dodgerblue;text-decoration:underline">GC</span>TT
   ...
Sorted index of T
CGGGT: 0
CGGTT: 4
GCTCT: 3
GTCTG: 1
TGGGC: 2

T: CGTGCGTGCTT
P: <span style="color:dodgerblue;text-decoration:underline">GC</span>G<span style="color:dodgerblue;text-decoration:underline">T</span>A<span style="color:dodgerblue;text-decoration:underline">CT</span>
</pre>

In this case we need to slice out the same pattern in P for query, like shown above.

Similarly, we can skip to take every nth pattern in T, and check every offset with every mod of n in P

Using subsequences instead of substrings allows us to match different locations in P at the same time, which leads to a higher successful rate during the verification steps.

Coding problems: kmer, sorted list, pigeonhole principle and subsequence
====

Here we implement the index assisted mathcing algorithm by recording substrings/subsequences of text T into a sorted list, and implement the approximate matching according to pigeonhole principle. 

Play around with the codes in this [Google Colab](https://colab.research.google.com/github/gr-grey/genomic-courses/blob/main/index_assisted_match_approximate_pigeonhole_kmer_and_subsequences.ipynb) file.  

Coding project
----
Download the genome file (part of human chromosome 1) and address the questions below.

### Kmer index in sorted list and approximate matching
Implement the pigeonhole principle using Index to find exact matches for the partitions. Assume P always has length 24, and that we are looking for approximate matches with up to 2 mismatches (substitutions). We will use an 8-mer index. 

Write a function that, given a length-24 pattern P and given an Index object built on 8-mers, finds all approximate occurrences of P within T with up to 2 mismatches. Insertions and deletions are not allowed. Don't consider any reverse complements.

Q1.How many times does the string GGCGCGGTGGCTCACGCCTGTAAT, which is derived from a human Alu sequence, occur with up to 2 substitutions in the excerpt of human chromosome 1?  (Don't consider reverse complements here.)

- Note: Multiple index hits might direct you to the same match multiple times, but be careful not to count a match more than once.
  
Q2. Using the instructions given in Question 4, how many total index hits are there when searching for occurrences of GGCGCGGTGGCTCACGCCTGTAAT with up to 2 substitutions in the excerpt of human chromosome 1?  (Don't consider reverse complements here.)  


```python
# genome file
!wget http://d28rh4a8wq0iu5.cloudfront.net/ads1/data/chr1.GRCh38.excerpt.fasta

# read and store genome

def readGenome(fastAfile): 
    genome = ''
    with open(fastAfile, 'r') as f:
        for line in f:
            if not line[0] == '>':
                genome += line.rstrip()
    return genome

genome = readGenome('./chr1.GRCh38.excerpt.fasta')

# creat sorted indices and query method
import bisect # for binary search
class Index(object): 
    def __init__(self, t, k): # kmer index for t
        self.k = k
        self.index = []
        for i in range(len(t) - k + 1):
            self.index.append((t[i: i+k], i)) # each element is (kmer_string, offset of kmer string)
        self.index.sort()
    
    def query(self, p):
        kmer = p[:self.k]
        i = bisect.bisect_left(self.index, (kmer, -1)) # i is the position in the sorted index list where we find the first /most left hit
        hits = []
        while i < len(self.index): # keep going to the right to record all hits
            if self.index[i][0] != kmer:
                break
            hits.append(self.index[i][1])
            i += 1
        return hits

def queryIndex(p, t, index):
    k = index.k
    offsets = []
    for i in index.query(p): # check if each kmer match/hit is an exact match, i.e. if the rest of string mataches
        if i + len(p) > len(t): # p goes out of bound
            continue
        if p[k:] == t[i+k: i + len(p)]: 
            offsets.append(i)
    return offsets

def approximate_match_index(p, t, index, n): # need k to build a kmer index obj
    k = index.k

    # divide p to n+1 segments
    seg_len = int(round(len(p) /  (n+1)))
    all_matches = set() # avoid recording the same match from multiple segment at same alignment/offset position

    num_hits = 0

    for i in range(n+1): # loop trough each segment p[i * seg_len, (i+1) * seg_len], last segment edge case
        start = i * seg_len
        end = min( (i+1) * seg_len, len(p) ) # the last segment end should not surpass len(p)
        seg = p[start:end]

        seg_matches = queryIndex(seg, t, index) 
        num_hits += len(seg_matches)

        # check if rest of p matched within the allowed mistake counts
        for m in seg_matches:
            if m - start < 0 or m - start + len(p) > len(t):
                continue # p goes out of t in this alignment
            mismatches = 0
            for j in range(start): # check p[:start]
                if p[j] != t[m - start +j]:
                    mismatches += 1
                    if mismatches > n:
                        break
            for j in range(end, len(p)): # check p[end:] 
                if p[j] != t[m - start +j]:
                    mismatches += 1
                    if mismatches >n:
                        break
            if mismatches <= n:
                all_matches.add(m - start)
    return list(all_matches), num_hits

p2 = 'GGCGCGGTGGCTCACGCCTGTAAT'
index = Index(genome, 8)

print(f'Number of matches with upto 2 mismatches: {len(approximate_match_index(p2, genome, index, 2)[0])}')
print(f'Total index hits: {approximate_match_index(p2, genome, index, 2)[1]}') 
```

## Q3 Subsequence Index

Let's examine whether there is a benefit to using an index built using subsequences of T rather than substrings, as we discussed in the "Variations on k-mer indexes" video.  We'll consider subsequences involving every N characters.  For example, if we split ATATAT into two substring partitions, we would get partitions ATA (the first half) and TAT (second half).  But if we split ATATAT into two  subsequences  by taking every other character, we would get AAA (first, third and fifth characters) and TTT (second, fourth and sixth).

Another way to visualize this is using numbers to show how each character of P is allocated to a partition.  Splitting a length-6 pattern into two substrings could be represented as 111222, and splitting into two subsequences of every other character could be represented as 121212.

Implement a SubseqIndex class that build index with every Nth (ival) character, which would be a more general implementation of Index.

Write a function that, given a length-24 pattern P and given a SubseqIndex object built with k = 8 and ival = 3, finds all approximate occurrences of P within T with up to 2 mismatches.

Q3. When using this function, how many total index hits are there when searching for GGCGCGGTGGCTCACGCCTGTAAT with up to 2 substitutions in the excerpt of human chromosome 1?  (Again, don't consider reverse complements.)


```python
import bisect
   
class SubseqIndex(object):
    """ Holds a subsequence index for a text T """
    
    def __init__(self, t, k, ival):
        """ Create index from all subsequences consisting of k characters
            spaced ival positions apart.  E.g., SubseqIndex("ATAT", 2, 2)
            extracts ("AA", 0) and ("TT", 1). """
        self.k = k  # num characters per subsequence extracted
        self.ival = ival  # space between them; 1=adjacent, 2=every other, etc
        self.index = []
        self.span = 1 + ival * (k - 1)
        for i in range(len(t) - self.span + 1):  # for each subseq
            self.index.append((t[i:i+self.span:ival], i))  # add (subseq, offset)
        self.index.sort()  # alphabetize by subseq
    
    def query(self, p):
        """ Return index hits for first subseq of p """
        subseq = p[:self.span:self.ival]  # query with first subseq
        i = bisect.bisect_left(self.index, (subseq, -1))  # binary search
        hits = []
        while i < len(self.index):  # collect matching index entries
            if self.index[i][0] != subseq:
                break
            hits.append(self.index[i][1])
            i += 1
        return hits

def approx_query_subseq(p, t, subseq_ind, n):
    k = subseq_ind.k

    num_index_hits = 0
    offsets = set()
    for i in range(k): # need to query p[0:], p[1:], ..., p[k-1:]
        hits = subseq_ind.query(p[i:]) 
        num_index_hits += len(hits)
        for h in hits: # check if the entire p matches
            start = h - i
            end = h - i + len(p)
            if start < 0 or end > len(t):
                continue
            mismatch = 0
            for j in range(len(p)):
                if p[j] != t[start + j]:
                    mismatch += 1
                    if mismatch > n:
                        break
            if mismatch <= n:
                offsets.add(start)
    return offsets, num_index_hits


read = 'GGCGCGGTGGCTCACGCCTGTAAT'
subseq_ind = SubseqIndex(genome, 8, 3)

occurrences, num_index_hits = approx_query_subseq(read, genome, subseq_ind, 2)
print(f'Total index hits: {num_index_hits}')
```
