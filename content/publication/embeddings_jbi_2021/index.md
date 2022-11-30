---
abstract: "Objective: 
Quantify tradeoffs in performance, reproducibility, and resource demands across several strategies for developing clinically relevant word embeddings.

Materials and methods:
We trained separate embeddings on all full-text manuscripts in the Pubmed Central (PMC) Open Access subset, case reports therein, the English Wikipedia corpus, the Medical Information Mart for Intensive Care (MIMIC) III dataset, and all notes in the University of Pennsylvania Health System (UPHS) electronic health record. We tested embeddings in six clinically relevant tasks including mortality prediction and de-identification, and assessed performance using the scaled Brier score (SBS) and the proportion of notes successfully de-identified, respectively.

Results:
Embeddings from UPHS notes best predicted mortality (SBS 0.30, 95% CI 0.15 to 0.45) while Wikipedia embeddings performed worst (SBS 0.12, 95% CI −0.05 to 0.28). Wikipedia embeddings most consistently (78% of notes) and the full PMC corpus embeddings least consistently (48%) de-identified notes. Across all six tasks, the full PMC corpus demonstrated the most consistent performance, and the Wikipedia corpus the least. Corpus size ranged from 49 million tokens (PMC case reports) to 10 billion (UPHS).

Discussion:
Embeddings trained on published case reports performed as least as well as embeddings trained on other corpora in most tasks, and clinical corpora consistently outperformed non-clinical corpora. No single corpus produced a strictly dominant set of embeddings across all tasks and so the optimal training corpus depends on intended use.

Conclusion:
Embeddings trained on published case reports performed comparably on most clinical tasks to embeddings trained on larger corpora. Open access corpora allow training of clinically relevant, effective, and reproducible embeddings."
authors:
- Zachary Flamholz
- Andrew Crane-Droesch
- Lyle Ungar
- Gary Weissman
date: "22021-12-14T00:00:00-04:00"
doi: "10.1016/j.jbi.2021.103971"
featured: false
projects: []
publication: '*Journal of Biomedical Informatics* 2022;125:103971.'
summary: ""
title: "Word Embeddings Trained on Published Case Reports Are Lightweight, Effective for Clinical Tasks, and Free of Protected Health Information"
url_code: ""
url_dataset: ""
url_pdf: "/papers/flamholz_jbi_2021.pdf"
url_poster: ""
url_project: ""
url_slides: ""
url_source: ""
url_video: ""
---


