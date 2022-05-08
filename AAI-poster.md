# AAI 2022 poster presentation

## contact 
Eric A. Wilson
Arizona State University 
[google scholar](https://scholar.google.com/citations?user=s-YMZVwAAAAJ&hl=en)
[email](eawilso6@asu.edu)

---

## pre print

We are expecting to release the full preprint of this work within the next 2 weeks. The link will be posted here.

---


## Abstract 

The ability to accurately identify peptide ligands for a given major histocompatibility complex class I (MHC-I) molecule has immense value for targeted anticancer and antiviral therapeutics. However, the highly polymorphic nature of the MHC-I protein makes universal prediction of peptide ligands challenging due to lack of experimental data describing most MHC-I variants, and the vast number of protein variants precludes comprehensive experimental determination. Therefore, there is a need for a framework to cluster MHC-I alleles to prioritize for experimental validation as well as identify alleles with potential disease associations.

To address this challenge, we have developed a deep convolutional neural network, HLA-Inception, capable of predicting MHC-I peptide binding motif using data derived from the structure of the MHC-I binding pocket. By approaching this problem from a 3-dimensional perspective, we can fully consider the impact of sidechain arrangement and topology of the MHC-I binding pocket on peptide binding motif, which is not inherently captured by the popular protein sequence-based approaches. Through a combination of homology modeling and biophysical simulations, we created protein structure models for all full-length HLA-ABC alleles. The topology and interaction forces within the MHC-I binding pocket were accounted for by solving the 3-dimensional electrostatic potential near the surface of the protein. HLA-Inception was then trained on all MHC-I alleles with known MHC-I binding motifs and applied to the full set of MHC-I models. We found that predicted peptide binding motifs fell into distinct and well-defined clusters which maintained known peptide binding and disease associations.

---
## Panel A: Input generation and model architecture

### A1: modeling

5,281 unique MHC-I binding pockets were created using a homology modeling-based approach. First, each MHC-I allele with an unknown structure was aligned to an existing template structure(MHC-I allele with known high quality structure). The crystallized peptide ligand was removed and the template structure was mutated until it matched the target sequence. The final structure was truncated so that it only contained residues from the peptide binding pocket

**Figure:** This shows an MHC-I binding pocket with a residue that has been targeted for mutation.

### A2: ensemble generation

In order to generate a more realistic representation of MHC-I peptide binding pocket, we created an ensemble of structures for each modeled MHC-I  using the Rosetta biophysical simulation software. During each simulation, the side chains of binding pocket residues were allowed to freely rotate and explore the configurational space. Each MHC-I model was simulated 40 times with the final state of each trajectory being selected. This resulted in a total ensemble size of 40 unique structures for each MHC-I model.

**figure:** This shows side chain sampling for one MHC-I allele. Each unique ensemble member was aligned by the peptide backbone. The side chains were then colored by ensemble member (transitions from blue to red as you move from ensemble member 0 to 39). The cartoon representation was included (cyan) for visualization purposes.

### A3: electrostatic map generation
The 3D electrostatic environment of the MHC-I binding pocket was solved using the Adaptive Poisson-Boltzmann Solver software. This resulted in a 3-dimensional grid space where each voxel (3D cube within a larger 3D cube) encodes the electrostatic potential at the center of the voxel.

**figure:** This shows the electrostatic potential of voxels near the MHC-I protein surface. Electrostatic potential is a continuous number that ranges from negative to positive depending on charge of the environment (acidic amino acids lead to negative environments, neutral residues lead to electrostatic potentials close to 0, and positive amino acids lead to positive environments). For illustrative purposes, voxels with a electrostatic potential > 0 were colored blue while voxels with a potential < 0 were colored red.


### A4: inception CNN

An inception-based convolutional neural network model was trained on electrostatic maps derived from MHC-I molecules with known peptide binding motifs. The model is able to predict an amino acid distribution for each position of a 9mer peptide binding motif. The model was used to predict binding motifs for all 5,281 modeled MHC-I alleles.

---
## Panel B: Motif clustering

### B1: MHC-I motif clustering

The predicted MHC-I motifs for all alleles were projected onto a two dimensional space using a UMAP projection. Clusters were then defined using the DBSCAN algorithm. We found that predicted motifs fell into 55 clusters with 10% of the motifs falling into a noise cluster(these motifs did not neatly adhere to other well defined motifs).

**Figure:** This shows the clustered MHC-I binding motif space. In each faceted plot, alleles falling into the gene specified by the title of each facet are colored. 

### B2: cluster composition:

**Figure:** The gene composition of each cluster is shown. cluster 0 represents the noise cluster.

### B3: mapping alleles associated with HIV control or progression

This shows the UMAP projection with clusters containing alleles associated with HIV control (circles) and HIV progression colored (the triangles and circles themselves correspond to the specific allele). The dashed line represents a value of 0 for UMAP dimension 1. The table in the top right shows the contingency table for the HIV association of alleles that are found on either side of the UMAP dimension 1 line.

### B4: correlation of log odds-ratio of being an HIV controller with UMAP dimension 1:

This shows the correlation of the odds ratio of someone with a given MHC-I allele being an elite HIV controller and the UMAP dimension 1 coordinate(higher odds ratio indicate stronger association). Alleles with an odds ratio > 1 are colored red while alleles with an odds ratio < 1 are colored blue.

---

## Panel C: MHC-I peptide binding predictions

Due to the fact that we are able to generate a prediction of MHC-I binding motifs, we can build a position-weighted matrix (PWM) that can be used to score a peptide for a given allele. We represent this matrix as the log odds ratio of the predicted frequency of a specific amino acid at a given position over the background frequency of that amino acid in the human proteome. A peptide can be scored by taking the sum of matrix elements for a given peptide sequence with more positive numbers representing a peptide that better fits the expected amino acid distribution. 

**NOTE:** the red/blue color scheme for C.2-C.6 is not consistent. Please consider the colors relative to each subplot alone. The captions here will indicate the correct colors for each plot.

### C1: Precision-Recall curve for natural presentation data for 53 MHC-I alleles

Precision-recall curves were then generated by calculating  both precision (true positive / true positive + false positives) and recall (true positives / total number of true positives in dataset) for peptides eluted from mono-allelic cell lines. These peptides were then mixed with a 100-fold excess of decoy peptides extracted from the human proteome.

### C2: Precision at 50% recall

We looked at the precision of HLA-Inception(red dots) and netMHCpan(blue dots) for the same 53 alleles in the above plots at a score threshold that would capture at least 50% of the true positive peptides. We found that netMHCpan only slightly outperformed HLA-Inception (0.03 increase in average precision).

### C3: Precision using percentile cutoffs

We converted HLA-Inception scores into a percentile score, by calculating the score distribution for each MHC-I allele on the full human proteome. We found that when using percentile score HLA-Inception(blue dots/bar) performed slightly better than netMHCpan(red dots/bar). 


### C4: Correlation of experimental resolved binding affinities

We look at the correlation between peptide scores from the PWM score and experimentally measured binding affinity data. We looked at all peptides within the IEDB database with a measured affinity of less than or equal to 10,000 nM. We compared the correlation between netMHCpan(red dots) and HLA-Inception(blue dots). 

### C5: Correlation of experimental resolved binding binding stability

We look at the correlation between peptide scores from the PWM score and experimentally measured stability data. We looked at all peptides within the IEDB database with a measured complex stability (seconds). we compared the correlation between netMHCpan(red dots) and HLA-Inception(blue dots). 

### C6: individual correlations by HLA 

This is a summary of the correlations to experimental affinity/stability data. 

The left box plot shows the summary for the correlations with complex stability. Each dot represents a different HLA.

The right box plot shows the summary for the correlations with binding affinity data. Each dot represents a different HLA.

This shows that netMHCpan does perform better at predicting  binding affinities (~12% better). However, we should note that netMHCpan is trained directly on the binding affinities of the peptides tested here. Not only has HLA-Inception never explicitly seen these peptides, but it has never been trained on binding affinity data.

---

## Panel D: Applications to viral proteomes 

### Application to SARS-Cov-2, Influenza A, and HIV

We predicted binder for all 5,821 MHC-I alleles for the full proteome of SARS-CoV-2, Influenza A, and HIV. Each point represents a different MHC-I allele, The y-axis shows the number of total binders for that allele from the reference self proteome normalized across all alleles, while the x-axis shows the normalized number of binders predicted from the viral proteome. The points are colored by the average similarity of the TCR contact residues (positions 3 - 8 of a 9mer peptide) of peptides predicted from the self proteome to those predicted from the specified viral proteome. The dashed line represents a slope of 1.

### prediction timing

This shows the performance of HLA-inception on peptide prediction for different proteomes. 

---

## References 

- International HIV Controllers Study, 2010. The major genetic determinants of HIV-1 control affect HLA class I peptide presentation. _Science_, _330_(6010), pp.1551-1557.

- Vita, R., Mahajan, S., Overton, J.A., Dhanda, S.K., Martini, S., Cantrell, J.R., Wheeler, D.K., Sette, A. and Peters, B., 2019. The immune epitope database (IEDB): 2018 update. _Nucleic acids research_, _47_(D1), pp.D339-D343.

- Robinson J, Barker DJ, Georgiou X, Cooper MA, Flicek P, and Marsh SGE, IPD-IMGT/HLA Database. Nucleic Acids Research (2020), 48:D948-55

- Jurrus E, Engel D, Star K, Monson K, Brandi J, Felberg LE, Brookes DH, Wilson L, Chen J, Liles K, Chun M, Li P, Gohara DW, Dolinsky T, Konecny R, Koes DR, Nielsen JE, Head-Gordon T, Geng W, Krasny R, Wei G-W, Holst MJ, McCammon JA, Baker NA. Improvements to the APBS biomolecular solvation software suite. _Protein Sci_, 27 (1), 112-128, 2018.

- P. Conway_, M. Tyka_, F. DiMaio*, D. Konerding and D. Baker (2013). Relaxation of backbone bond geometry improves protein energy landscape modeling. Protein Science

- Abadi, Martín. "TensorFlow: learning functions at scale." _Proceedings of the 21st ACM SIGPLAN International Conference on Functional Programming_. 2016.

- Szegedy, C., Liu, W., Jia, Y., Sermanet, P., Reed, S., Anguelov, D., Erhan, D., Vanhoucke, V. and Rabinovich, A., 2015. Going deeper with convolutions. In _Proceedings of the IEEE conference on computer vision and pattern recognition_ (pp. 1-9).

- Abelin, J.G., Keskin, D.B., Sarkizova, S., Hartigan, C.R., Zhang, W., Sidney, J., Stevens, J., Lane, W., Zhang, G.L., Eisenhaure, T.M. and Clauser, K.R., 2017. Mass spectrometry profiling of HLA-associated peptidomes in mono-allelic cells enables more accurate epitope prediction. _Immunity_, _46_(2), pp.315-326.

- Sarkizova, S., Klaeger, S., Le, P.M., Li, L.W., Oliveira, G., Keshishian, H., Hartigan, C.R., Zhang, W., Braun, D.A., Ligon, K.L. and Bachireddy, P., 2020. A large peptidome dataset improves HLA class I epitope prediction across most of the human population. _Nature biotechnology_, _38_(2), pp.199-209.

