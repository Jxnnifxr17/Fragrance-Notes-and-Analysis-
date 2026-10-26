# Fragrance Notes: Analysis and Scent Chord Diagram  

Exploratory, statistical and predictive analysis of 24,063 perfumes (Fragrantica-style data), plus an interactive chord diagram showing which fragrance notes are used together. The chord diagram was originally ranked by raw co-occurrence and has been improved to rank pairs by lift.  

## What is in this project
Analysis (Fragrance_Analysis.ipynb): cleaning, brand and accord analysis, note associations (lift and PMI), statistical tests, a "find similar perfumes" recommender, rating and gender models, scent-family clustering, and a note network. It exports the two CSVs the chord diagram needs.  

Original chord diagram (Original_Fragrance.ipynb): the 80 most common notes, 900 strongest pairs ranked by raw co-occurrence count.  

Improved chord diagram (Improved_Scent_Chord.ipynb): the same design and pipeline, but pairs come from note_pairs_lift.csv and are ranked by lift, so popular notes such as musk no longer dominate every connection.  

Report (Fragrance_Data_Analysis_Report.pdf): written summary of the findings.  

## Files

| File | Role |
| --- | --- |
| fra_cleaned.csv	| Input data. Semicolon-delimited, latin-1 encoded, ratings use a decimal comma. |
| Fragrance_Analysis.ipynb	| Analysis notebook. Writes note_pairs_lift.csv and perfume_clusters.csv. | 
| note_pairs_lift.csv |	Lift-ranked note pairs (columns: note_a, note_b, count, lift, pmi, jaccard). Input to the improved chord diagram. |
| perfume_clusters.csv	| Per-perfume cluster, rating, rating count and Bayesian-adjusted rating.|
| Original_Fragrance.ipynb	| Builds the original chord diagram (raw counts). |
| Improved_Scent_Chord.ipynb	| Builds the improved chord diagram (lift). |
| scent_chords_template.html |	Page template for the original diagram, loads d3 from a CDN. |
| scent_chords_template2.html	| Lift-aware template for the improved diagram (see the note under "Run it"). |
| scent_chords.html |	Generated page, overwritten each time a notebook runs. |
| scent_chords_offline.html |	Original diagram with d3 built in, so it works with no internet connection. |
| scent_chords_lift_offline2.html	| Improved diagram with d3 built in. |
| data.json	| Data written by the chord notebook (notes, families, pairs). |
| Fragrance_Data_Analysis_Report.pdf |	The report. |

Files ending in _1 (Fragrance_Analysis_1.ipynb, note_pairs_lift_1.csv, perfume_clusters_1.csv) look like duplicate copies of the files above. Keep one set to avoid confusion.

## Requirements
Python 3.9 or newer and Jupyter.  

pip install pandas numpy scipy scikit-learn matplotlib seaborn networkx jupyter  

The chord notebooks need only pandas. The analysis notebook needs the rest. The generated HTML pages work in any modern browser.  

Run it. 

Put the files in one folder.

1. Analysis: Open Fragrance_Analysis.ipynb, set CSV = "fra_cleaned.csv" if needed, and run all cells. The last cell writes note_pairs_lift.csv and perfume_clusters.csv.  

2. Original chord diagram: Run Original_Fragrance.ipynb. It reads fra_cleaned.csv, writes data.json and scent_chords.html, and displays the page inline. The last cell opens scent_chords_offline.html in your browser.  

3. Improved chord diagram: Run Improved_Scent_Chord.ipynb. It reads fra_cleaned.csv and note_pairs_lift.csv, then writes data.json and scent_chords.html. The first display cell shows it inline, and the second opens it in your browser with a changing query string so the browser does not show a cached copy.  

Important: the notebook loads the template named scent_chords_template.html. To get the lift version, that file must be the lift-aware one. Either copy scent_chords_template2.html over scent_chords_template.html, or pass the template path as the second argument when running the notebook as a script:  

python your_script.py fra_cleaned.csv scent_chords_template2.html  

If the browser shows a blank page, it is probably blocking the d3 download from the CDN. Open scent_chords_lift_offline2.html instead, which has d3 included.  

## How the chord diagram works  
Each perfume becomes a set of notes. The 80 most frequent notes form the wheel.  

Notes are grouped into nine families (Citrus, Floral, Fruity, Green and herbal, Spice, Woods and moss, Amber and sweet, Musk and clean, Resin and leather) using keyword rules plus hand overrides for notes the rules misclassify. These groupings are editorial judgments.  

Original: the 900 strongest pairs by raw co-occurrence count.  

Improved: pairs among those 80 notes with lift of at least 1.2, ranked by lift, top 900. Ribbon thickness is still the number of shared perfumes. Clicking a note lists its best partners with count, share and lift.  

The page can show 100, 250, 500 or 900 connections.  

Lift compares how often two notes appear together with what you would expect if they were independent: lift(a, b) = P(a, b) / (P(a) x P(b)). A lift above 1 means the pair is used together more than chance, and below 1 means they tend to avoid each other.

<img width="948" height="556" alt="Screenshot 2026-10-26 at 3 09 17 PM" src="https://github.com/user-attachments/assets/9311d12c-49c2-4b9f-9cd6-d2e2005a77a7" />

## Key findings. 
Style is learnable. A model using only notes and accords predicts the gender label with 71.9% accuracy, and k-means finds ten interpretable scent families.
Ratings are hard to predict. The best model (histogram gradient boosting) explains 16.1% of rating variance.  

Raw ratings mislead. Ranking by raw rating surfaces perfumes with fewer than 50 votes. A Bayesian adjustment (prior of 100 pseudo-votes) gives a more trustworthy ranking.  

Raw co-occurrence mostly reflects popularity. The most frequent pairs have lift of only 1.14 to 1.29. The most over-represented pairs include castoreum with civet (lift 24.7) and fig with fig leaf (15.2).  

Label noise. The lowest-lift pairs among common notes are mostly synonyms such as vanilla/vanille, cedar/virginia cedar and musk/white musk. Merging them would give cleaner results.  

Recommender. Top-10 similar perfumes share the query's lead accord 34.8% of the time, against a random baseline of about 7.5%.  

See the report for tables, test statistics and the full discussion.  

## Data notes and limitations  
Note names include variant labels (for example vanille and vanilla), which inflates the vocabulary and distorts pair statistics. The chord diagrams here keep the original names as they appear in the file.
Ratings are user-generated and reflect fan communities.  

Older decades have small samples: 67 perfumes from the 1960s against 11,533 from the 2010s.  

Release year is missing for 8.5% of perfumes and perfumer for about half.  

Models use a single 80/20 split without cross-validation.  

The data shows how perfumes are described on Fragrantica, not how they smell. Popular perfumes are overrepresented.

Original Dataset: Kaggle: https://www.kaggle.com/datasets/olgagmiufana1/fragrantica-com-fragrance-dataset
