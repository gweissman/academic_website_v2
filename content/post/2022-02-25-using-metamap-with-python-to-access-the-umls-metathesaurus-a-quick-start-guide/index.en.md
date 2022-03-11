---
title: Using MetaMap with Python to access the UMLS MetaThesaurus - A Quick Start
  Guide
author: Gary Weissman
date: '2022-02-25'
slug: using-metamap-with-python-to-access-the-umls-metathesaurus-a-quick-start-guide
categories:
  - python
  - nlp
tags: []
subtitle: ''
summary: ''
authors: []
lastmod: '2022-02-25T08:43:12-05:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

The National Library of Medicine (NLM) created a tremendous resource called the [Unified Medical Language Systems (UMLS)](https://www.nlm.nih.gov/research/umls/index.html). The UMLS is a suite of data and software resources that offers several tools for processing, representing, and analyzing biomedical text data. One exciting use case that we'll explore here is using UMLS to identify signs and symptoms that are documented in the text of clinical encounter notes.

[MetaMap](https://lhncbc.nlm.nih.gov/ii/tools/MetaMap.html) is a software tool that identifies concepts in the text of clinical notes that are found in UMLS ontologies. MetaMap is written in Java and there is a convenient wrapper in Python:  [PyMetaMap](https://github.com/AnthonyMRios/pymetamap) written by Anthony Rios. There are other Python wrappers around MetaMap but PyMetaMap was the most straightforward to get running on my machine so that's what we'll use here.

## Step 1: Installation

Make sure you have have MetaMap installed. Instructions are here: https://metamap.nlm.nih.gov/Installation.shtml

I used the default settings for the installation and it has worked out fine.

**NB** To use most UMLS resources you will have to [register for an account](https://uts.nlm.nih.gov/uts/) and sign a data use agreement. This is free!

## Step 2: Get some text data

Because working with clinical notes is often challening due to issues of de-identification, here are a few sample text notes we'll use here. These are completely made up and not based on any real people.

```{python}
note1 = "Mrs. Jones came in today complaining of a lot of chest pain. She denies shortness of breath and fevers."

note2 = "Mr. Smith has been having pain in his knee for many weeks. It hurts when he walks. There is no swelling, erythema, or micromotion tenderness."

note3 = "Sandy Lemon has been having headaches for two months that are associated with blurry vision and numbness in her right arm."

# Put them all together in a list
note_list = [note1, note2, note3]
```

## Step 3: Install PyMetaMap

The last time I tried this, the version available via `pip` was old. So installed the latest version (0.2) directly from the GitHub site here: https://github.com/AnthonyMRios/pymetamap

I accomplished this using:

```{python}
pip install git+https://github.com/AnthonyMRios/pymetamap.git
```

Now load the necessary modules and set up the relevant servers in the background:

```{python}
# Load MetaMap
from pymetamap import MetaMap

# Import os to make system calls
import os

# For pausing
from time import sleep

# Setup UMLS Server
metamap_base_dir = '/gwshare/umls_2021/metamap/public_mm/'
metamap_bin_dir = 'bin/metamap20'
metamap_pos_server_dir = 'bin/skrmedpostctl'
metamap_wsd_server_dir = 'bin/wsdserverctl'

# Start servers
os.system(metamap_base_dir + metamap_pos_server_dir + ' start') # Part of speech tagger
os.system(metamap_base_dir + metamap_wsd_server_dir + ' start') # Word sense disambiguation 

# Sleep a bit to give time for these servers to start up
sleep(60)
```

Set up a MetaMap object to do the work. If you are running into memory issues with this approach, see some tips here: https://lhncbc.nlm.nih.gov/ii/tools/MetaMap/Docs/OutOfMemory.pdf


```{python}
metam = MetaMap.get_instance(metamap_base_dir + metamap_bin_dir)
```

Now let's process some text.

```{python}
cons, errs = metam.extract_concepts(note_list,
                                word_sense_disambiguation = True,
                                restrict_to_sts = ['sosy'], # signs and symptoms
                                composite_phrase = 1, # for memory issues
                                prune = 30)
                                
# Look at the output:
print(cons)

## [ConceptMMI(index='USER', mm='MMI', score='5.18', preferred_name='Dyspnea', cui='C0013404', semtypes='[sosy]', trigger='["SHORTNESS OF BREATH"-tx-1-"shortness of breath"-noun-1]', location='TX', pos_info='73/19', tree_codes=''), ConceptMMI(index='USER', mm='MMI', score='5.18', preferred_name='Fever', cui='C0015967', semtypes='[sosy]', trigger='["Fevers"-tx-1-"fevers"-noun-1]', location='TX', pos_info='97/6', tree_codes=''), ConceptMMI(index='USER', mm='MMI', score='3.45', preferred_name='Mastodynia', cui='C0024902', semtypes='[sosy]', trigger='["of breast pain"-tx-1-"of chest pain"-noun-0]', location='TX', pos_info='38/2,50/10', tree_codes=''), ConceptMMI(index='USER', mm='MMI', score='3.71', preferred_name='Knee pain', cui='C0231749', semtypes='[sosy]', trigger='["Pain in knee"-tx-1-"pain in knee"-noun-0]', location='TX', pos_info='27/7,39/4', tree_codes=''), ConceptMMI(index='USER', mm='MMI', score='3.68', preferred_name='Sore to touch', cui='C0234233', semtypes='[sosy]', trigger='["TENDERNESS (NOS)"-tx-1-"tenderness"-noun-1]', location='TX', pos_info='131/10', tree_codes=''), ConceptMMI(index='USER', mm='MMI', score='3.57', preferred_name='Headache', cui='C0018681', semtypes='[sosy]', trigger='["Headaches"-tx-1-"headaches"-noun-0]', location='TX', pos_info='29/9', tree_codes=''), ConceptMMI(index='USER', mm='MMI', score='3.56', preferred_name='Numbness', cui='C0028643', semtypes='[sosy]', trigger='["NUMBNESS"-tx-1-"numbness"-noun-0]', location='TX', pos_info='97/8', tree_codes='')]
```

And try to put it into a more usable format:

```{python}
import pandas as pd

def get_keys_from_mm(concept, klist):
    conc_dict = concept._asdict()
    conc_list = [conc_dict.get(kk) for kk in klist]
    return(tuple(conc_list))
        
        
keys_of_interest = ['preferred_name', 'cui', 'semtypes', 'pos_info']
cols = [get_keys_from_mm(cc, keys_of_interest) for cc in cons]
results_df = pd.DataFrame(cols, columns = keys_of_interest)

# See results
print(results_df)

## preferred_name       cui semtypes    pos_info
## 0        Dyspnea  C0013404   [sosy]       73/19
## 1          Fever  C0015967   [sosy]        97/6
## 2     Mastodynia  C0024902   [sosy]  38/2,50/10
## 3      Knee pain  C0231749   [sosy]   27/7,39/4
## 4  Sore to touch  C0234233   [sosy]      131/10
## 5       Headache  C0018681   [sosy]        29/9
## 6       Numbness  C0028643   [sosy]        97/8

```

And there you found the signs and symptoms with their associated plain language name, cui, and position. For processing many notes in parallel, I've found it works to load the UMLS servers once but create a new `MetaMap` object inside each process.


Good luck, and happy processing!

