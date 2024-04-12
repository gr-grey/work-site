---
title: Naive Exact Matching
date: 2024-04-12
description: "Simple and exact."
tags:
  - ngs
  - algorithms
  - python
---
Naive exact match is a simple algorithm to solve alignment problems in genomics.

The alignment problem
----
To find out the location of where a pattern p occurs in text t.

For example, the pattern "orl" has an exact match in text "Hello world." at position 7.
<pre>
P:        orl
T: Hello world.
   012345678901
</pre>

In genomics sequencing, the pattern would be a single DNA read from the sequencer, which can be ~150 bases long, and we need to find out where the read lies on the entire human genome, which is 3 billion bases long.

Naive exact match
----
As the name indicates, the naive exact match algorithm is a simple/naive/straight forward way to find exact matches of pattern p in text t.

It does so by going through each possible locations in text t, one by one, and check if the substring matches the pattern p.

<pre>
P: orl
T: Hello world.
0  <span style="color:gray">orl</span>
1   <span style="color:gray">orl</span>
2    <span style="color:gray">orl</span>
3     <span style="color:gray">orl</span>
4      <span style="color:gray">orl</span>
5       <span style="color:gray">orl</span>
6        <span style="color:gray">orl</span>
7         <span style="color:green">orl</span>
<span style="color:gray">#  Hello world.</span>
8          <span style="color:gray">orl</span>
9           <span style="color:gray">orl</span>
</pre>

Imagine a slider with the same length of string p, and it slides from the beginning to the end of the string t.

Note that the last substring of length P starts at position len(T) - len(P) + 1, after that position, a substring of len(P) would exceed the end of text T.

Implementation in python.

```python
# exact match of p in t
def naive(p,t): 
    occurrences = []
    for i in range(len(t) - len(p) + 1): # check all possible positions in T
        match = True
        for j in range(len(p)): # check substring for match
            if not p[j] == t[i+j]: # mismatch; move on
                match = False
                break
        if match: # all chars matched; record
            occurrences.append(i)
    return occurrence
```

Coding practical: naive exact match
----

The coding project comes from the [Algorithms for DNA Sequencing](https://www.coursera.org/learn/dna-sequencing/exam/y9pTa/programming-homework-1) course from Johns Hopkins University.

My codes are available in this [Google Colab](https://colab.research.google.com/github/gr-grey/genomic-courses/blob/main/naive_exact_match.ipynb) file. Feel free to play around with it.

In this example, we use the naive exact match algorithm to find alignment of DNA substrings to a reference genome, and explore the cases where mismatches/substitutions (mutations) are allowed.

Alignments of DNA substring to ref genome

Find patterns that matches the given "lambda virus" genome sequence
https://d28rh4a8wq0iu5.cloudfront.net/ads1/data/lambda_virus.fa

Q1. How many times does AGGT or its reverse complement ACCT occur in the lambda virus genome? E.g. if AGGT occurs 10 times and ACCT occurs 12 times, you should report 22.

Q2. How many times does TTAA or its reverse complement occur in the lambda virus genome? Hint: TTAA and its reverse complement are equal, so remember not to double count.

Q3. What is the offset of the leftmost occurrence of ACTAAGT or its reverse complement in the Lambda virus genome? E.g. if the leftmost occurrence of ACTAAGT is at offset 40 (0-based) and the leftmost occurrence of its reverse complement ACTTAGT is at offset 29, then report 29.

Q4. What is the offset of the leftmost occurrence of AGTCGA or its reverse complement in the Lambda virus genome?

### Solution

We need to read the FastA file and store the entire genome as a string.

We need a naive exact match function that returns matching location of the patern p, given the sequence s.

To consider reverse complements, we need a function that turns a sequence string to its reverse complements, then get the reverse complements of both p and s.
If s does not equal to its reverse complement, we'll check the reverse complement matching and append the results.

```python
# download genome file
!wget https://d28rh4a8wq0iu5.cloudfront.net/ads1/data/lambda_virus.fa

# read FastA file and return the whole genome as a string
def readGenome(fastAfile): 
    genome = ''
    with open(fastAfile, 'r') as f:
        for line in f:
            if not line[0] == '>': # the line contains string
                # strip the endline char, add to genome
                genome += line.rstrip() 
    return genome

genome = readGenome('lambda_virus.fa')

def naive(p,s): # exact match of p in s
    occurrences = []
    # check all possible locations in s
    for i in range(len(s) - len(p) + 1):
        match = True
        for j in range(len(p)): # check if substring matches
            if not p[j] == s[i+j]: # find mismatch; move on
                match = False
                break
        if match: # all chars matched; record
            occurrences.append(i)
    return occurrences
    
def reverseComplement(s):
    complement = {'A':'T', 'G':'C', 'C':'G', 'T':'A', 'N':'N'}
    rc_seq = ''
    for base in s:
        rc = complement[base]
        rc_seq = rc + rc_seq # prepend so the compelment is reversed
    return rc_seq

# navie with checking of reverse complement
def naive_rc(p, s): 
    occurrences = naive(p, s)
    p_rc = reverseComplement(p)
    # skip checking if p equals to its reverse complement
    if not p == p_rc:
        occurrences.extend(naive(p_rc, s))
    return occurrences 

# Q1
print(len(naive_rc('AGGT', genome)))
# Q2
print(len(naive_rc('TTAA', genome)))
# Q3
print(min(naive_rc('ACTAAGT', genome)))
# Q4
print(min(naive_rc('AGTCGA', genome)))
```

### Allowing mismatches

Q5. Make a new version of the naive function called naive_2mm that allows up to 2 mismatches per occurrence. Unlike for the previous questions, do not consider the reverse complement here. How many times does TTCAAGCC occur in the Lambda virus genome when allowing up to 2 mismatches?

Q6. What is the offset of the leftmost occurrence of AGGAGGTT in the Lambda virus genome when allowing up to 2 mismatches?

### Solution

To allow mismatches, we'll use a counter variable to record number of mismatches.
When mismatches happen, instead of immediately break out of the comparing loop, we check if the number of mismatches has exceeded the threshold, and only break when it does (in this case >2). 

```python
# naive matching with maximum of m errors
def naive_mm(p,s,m): 
    occurrences = []
    for i in range(len(s) - len(p) + 1):
        match = True
        mistakes = 0
        for j in range(len(p)):
            if not p[j] == s[i+j]: # find mismatch
                mistakes += 1
                if mistakes > m: # exceed error limits
                    match = False
                    break
        if match:
            occurrences.append(i)
    return occurrences

# Q5
print(len(naive_mm('TTCAAGCC', genome, 2)))
# Q6
print(min(naive_mm('AGGAGGTT', genome, 2)))
```
