---
title: "Libmamba - a (much) faster env solver for conda"
date: "2023-08-16"
summary: "Saving you from the pain of Conda's amazingly slow env solving."
tags:
- python
---

Getting bored waiting ages for conda to solve the environment?

I did. 

I am trying to install [Orca](https://github.com/jzhoulab/orca/),  the  `conda env create -f orca_env.yml` command was stuck at "Solving environment: -| / \ / \" for 1hr+. 

Bored and frustrated, I searched for a better option, 
then I found  [Libmamba: A Faster Solver for Conda](https://www.anaconda.com/blog/a-faster-conda-for-a-growing-community), and it was amazing.

Installation and setup of Libmamba is simple. After installing miniconda3, run these commands
```bash
conda update -n base conda
conda install -n base conda-libmamba-solver
conda config --set solver libmamba
```

With Libmamba, it took only a couple of minutes to solve the environment!!!

I highly recommend this. Try it out today, no more hours long waiting, it'll change your life for the better  📣 (ad person voice).

P.S.
The solution came from this conda issue:
["Solving Environment" takes forever, as always, and it remains without result. · Issue # 11919 · conda/conda (github.com)](https://github.com/conda/conda/issues/11919).

Where the person complained:

> The message I received after 4 hours of waiting
```
Collecting package metadata (current_repodata.json): done
Solving environment: failed with initial frozen solve. Retrying with flexible solve.
Solving environment: failed with repodata from current_repodata.json, will retry with next repodata source. Collecting package metadata (repodata.json): done
Solving environment: failed with initial frozen solve. Retrying with flexible solve.
Solving environment: /
Found conflicts! Looking for incompatible packages.
This can take several minutes.  Press CTRL-C to abort.
```
> Thank you for wasting all the time.

😂