VanLang InSilico Pipeline
Virtual Screening for Organic Dialdehyde Candidates as Formalin Alternatives in IHC-Compatible Tissue Fixation
Department of Medical Laboratory Technology  ·  Van Lang University  ·  Ho Chi Minh City, Vietnam  ·  2025
github.com/lamkienthanh92/VangLangInsilico
Hypothesis-generating in silico study — all findings require experimental validation before clinical application.
1.  BACKGROUND
Formalin (10% neutral buffered formaldehyde) is the global standard for tissue fixation in surgical pathology. Its active agent, formaldehyde, is classified as a Group 1 human carcinogen by IARC, with occupational exposure limits of 0.3 ppm (ECHA) and 0.75 ppm (OSHA, 8-h TWA). In anatomical pathology laboratories, these thresholds are routinely approached during open-bench fixation, placing laboratory personnel at sustained carcinogenic risk.

A viable alternative must simultaneously satisfy two constraints: sufficient bifunctional crosslinking to prevent autolysis and preserve cellular morphology on haematoxylin-and-eosin (H&E) sections, and adequate preservation of antibody epitope accessibility to sustain accurate IHC scoring for HER2 and PD-L1, whose results directly determine oncology treatment eligibility. This pipeline provides systematic computational evaluation of the organic dialdehyde chemical space against these dual criteria.
2.  SCIENTIFIC RATIONALE
2.1  Why Non-Covalent Docking Is Appropriate for This Study
Schiff base crosslinking between an aldehyde and a lysine ε-amino group proceeds in two sequential steps:

Step	Physical event	Pipeline stage that evaluates this
Step 1	Pre-covalent proximity: aldehyde carbon must approach lysine NZ within ~3–4 Å with correct orientation for nucleophilic attack.	NB3 — Molecular Docking. AutoDock Vina optimises position and orientation of the aldehyde at the protein surface. High affinity = favourable pre-reactive geometry at the lysine NZ.
Step 2	Covalent reaction: nucleophilic addition of NH₂ to C=O → hemiaminal → Schiff base.	NB2 — Reactivity Scoring. Gasteiger charge on carbonyl carbon (on ANI-2x-optimised geometry) is a proxy for electrophilicity — susceptibility to nucleophilic attack.

Step 1 is a necessary condition for Step 2. No matter how reactive a compound is chemically (NB2), if it cannot position its aldehyde carbon near a surface lysine NZ in the correct orientation (NB3), Schiff base formation cannot occur in practice. Non-covalent docking evaluates exactly this pre-covalent spatial constraint — making it the appropriate tool for Step 1, even though the final bond is covalent.

The pipeline does not claim to model covalent bond energy. It models whether the pre-reactive complex is geometrically accessible — the rate-limiting spatial constraint for fixatives acting on a protein surface.

2.2  Protein Target Selection
HER2 and PD-L1 were selected not as drug targets but as the IHC markers whose clinical scoring is most at risk from inappropriate fixation. HER2 IHC score determines trastuzumab eligibility in breast and gastric cancer; PD-L1 IHC score determines checkpoint inhibitor eligibility. Docking was targeted to the surface-exposed lysine NZ centroid within each protein's clinically relevant epitope region, reflecting the primary crosslink sites.

2.3  Grand Score Formula and Weight Derivation
Grand Score  =  Docking × 0.58  +  Feasibility (NB5) × 0.34  +  Reactivity (NB2) × 0.08

Weights were derived from Coefficient of Variation (CV) analysis of each component score across the 10 candidate compounds (NB6 sensitivity analysis). CV measures the relative spread of each score — a higher CV indicates greater discriminative power among candidates, and therefore justifies a higher weight.

Component	CV (%)	CV-derived weight	Mechanistic role
Docking (NB3)	6.7%	0.58	Step 1 of Schiff base — pre-covalent spatial pre-condition. Highest CV = most discriminating among candidates.
Feasibility (NB5)	3.9%	0.34	Clinical workflow: penetration rate (CAP guideline), fixation kinetics, shelf-life stability.
Reactivity (NB2)	0.9%	0.08	Step 2 chemical reactivity proxy. Lowest CV — within filtered dialdehyde class, variation is modest.
SASA (NB4)	—	0 (binary)	All 10 candidates pass 85–115% accessibility. Used as go/no-go exclusion; model insufficient for ranking.

CV rank order (Docking > NB5 > NB2) is consistent with mechanistic priority (Step 1 spatial constraint > clinical feasibility > chemical reactivity), providing dual justification for the weight assignments — quantitative (CV) and mechanistic.

Sensitivity analysis (NB6): Across 129 weight combinations (each component 10–75%, step 5%), all 10 candidate compounds ranked above glutaraldehyde in 71–100% of scenarios (mean Spearman ρ = 0.79 vs baseline). Precise within-candidate ordering showed moderate weight sensitivity (top-3 set stable in 22% of scenarios). Candidate superiority over the reference fixative is therefore a robust finding; precise top-3 ordering should be treated as a direction for experimental prioritisation rather than a definitive rank.
3.  PIPELINE ARCHITECTURE
Notebook	Method	Input → Output
NB1	Chemical + ADMET filter (RDKit): 12 criteria — MW ≤ 200 Da, LogP ≤ 1.0, ≥ 2 electrophile groups, no enone / PAINS / mutagenic alert / organometallic, deduplication	1,007 PubChem CIDs → 154 compounds
NB2	Reactivity scoring: TorchANI ANI-2x GPU geometry optimisation → Gasteiger carbonyl charge → composite electrophilicity score (147/154 GPU, 95.5%)	154 → Top 30
NB3	Molecular docking (AutoDock Vina 1.2.7): HER2 (PDB: 1N8Z) + PD-L1 (PDB: 5X8M); lysine NZ centroid; 40×40×40 Å; exhaustiveness 16; 30/30 converged	Top 30 → Top 10
NB4	Epitope SASA (FreeSASA, Lee-Richards, probe 1.4 Å): binary pass/fail 85–115%; exclusion gate only	10/10 pass
NB5	Feasibility (Wilke-Chang penetration + Arrhenius kinetics proxy + stability); CV-derived Grand Score	Grand Final ranking
NB6	Sensitivity analysis: CV discriminative power + 129 weight combinations robustness test	Weight justification + robustness report
NB4_NB5_refs	Unified ranking: 10 candidates + 4 references on same Grand Score scale	14-compound comparison table
4.  PROTEIN TARGETS
	HER2 (PDB: 1N8Z)	PD-L1 (PDB: 5X8M)
Resolution	2.5 Å	2.9 Å
Epitope targeted	Domain IV (res. 161–214) — clone 4B5 site	IgV domain (res. 18–63) — atezolizumab site
LYS NZ atoms used for grid	12 atoms (5 LYS residues)	9 atoms (4 LYS residues)
Grid centre (Å)	(41.63, 46.25, 86.83)	(28.03, 105.07, 91.11)
Epitope SASA (Å²)	2,988 (26.4% of total)	2,874 (43.0% of total)
Clinical relevance	HER2 IHC → trastuzumab eligibility	PD-L1 IHC → checkpoint inhibitor eligibility
5.  KEY RESULTS
All 10 candidate compounds ranked above all 4 reference fixatives by Grand Score. The primary finding — candidate superiority over glutaraldehyde — was confirmed in 71–100% of 129 sensitivity analysis weight combinations.

Rank	Compound	SMILES	Grand Score	vs Glutaraldehyde	Type
1	CID 87944034	O=CCCC#CCCC=O	47.21	+5.98	Candidate
2	CID 91427	CCC(C=O)CC=O	45.32	+4.09	Candidate
3	CID 85655730	O=CCCC(=O)CCC=O	44.72	+3.49	Candidate
4	CID 10953466	CC(=O)CCCC=O	44.43	+3.20	Candidate
5	CID 12484446	CCOC(CC=O)CC=O	43.91	+2.68	Candidate
6	CID 21190979	CC(C)(C=O)CC=O	43.85	+2.62	Candidate
7	CID 22174207	O=CCCC(C=O)CC=O	43.77	+2.54	Candidate
8	CID 101171	CC(C=O)CCC=O	43.66	+2.43	Candidate
9	CID 80473	CC(CC=O)CC=O	43.49	+2.26	Candidate
10	CID 70620	O=CCCCCC=O	42.78	+1.55	Candidate
11	Glutaraldehyde	O=CCCCC=O	41.23	—	Reference
12	Succinaldehyde	O=CCCC=O	38.85	−2.38	Reference
13	Glyoxal	O=CC=O	37.97	−3.26	Reference
14	Formalin	C=O	25.21	−16.02	Reference

Wet laboratory priority: Succinaldehyde (Grand Score 38.85; rank 12) is the recommended first wet lab candidate despite its ranking, owing to commercial availability (Sigma-Aldrich, TCI), lowest MW in the dialdehyde class (86.1 Da), and published IHC compatibility data (Richter et al., EMBO J 2018; Criswell et al., J Histotechnol 2022). Among the top 10, CID 70620 (hexanedial; Sigma-Aldrich) and CID 80473 (3-methylglutaraldehyde) and CID 101171 (2-methylglutaraldehyde) have confirmed commercial availability. CID 87944034 (oct-4-yne-1,8-dial; rank 1) warrants biocompatibility assessment before use owing to its internal alkyne group (C≡C) at position 4, for which no published safety or fixation data are available.
6.  DECLARED LIMITATIONS
Non-covalent docking. AutoDock Vina evaluates pre-covalent surface proximity (Step 1). Covalent bond energy of Step 2 requires QM/MM or covalent docking tools. Docking affinities are relative surface-proximity indices, not absolute crosslink energies.

SASA discrimination. Empirical ΔSASA model yields 98.5–99.5% for all compounds — insufficient for ranking. All 10 pass binary criterion. Molecular dynamics simulation with explicit crosslinked complexes is required for quantitative epitope accessibility.

Tissue penetration model. Wilke-Chang derived for dilute aqueous solution. Omits ECM tortuosity, glycocalyx, and tumour microenvironment interstitial pressure. A two-phase Fickian model (Hopwood, 1969) would be more appropriate.

H&E morphological preservation not assessed. IHC epitope compatibility is necessary but not sufficient. Autolysis prevention, chromatin detail, absence of shrinkage artefacts — all mandatory experimental endpoints not evaluated here.

Reactivity proxy. Gasteiger charges on ANI-2x-optimised geometries are empirical. True quantum electrophilicity (xTB GFN2 HOMO-LUMO gap, Fukui index) would improve accuracy.

Within-candidate ranking sensitivity. Top-3 set was stable in only 22% of 129 weight combinations. Precise ranking among candidates should be treated as directional, not definitive. The robust finding is candidate superiority over reference fixatives (71–100% of scenarios).
7.  HOW TO RUN
File	GPU	Runtime	Output
NB1_chemical_filter.py	No	5–10 min	NB1_final_shortlist.csv
NB2_reactivity_scoring_v2.py	Yes (T4)	~5 min	NB2_scored_v2.csv
NB3_molecular_docking_v2.py	No	~4 min	NB3_docking_results_v2.csv
NB4_sasa_epitope_v2.py	No	<1 min	NB4_sasa_results_v2.csv
NB5_feasibility_v2.py	No	<1 min	NB5_grand_final_v2.csv
NB4_NB5_with_references.py	No	<1 min	NB5_grand_final_with_refs.csv
NB6_sensitivity_analysis.py	No	<1 min	NB6_sensitivity_results_v2.csv ← weight justification

Install before NB3: !pip install -q biopython vina
Run in order: NB1 → NB2 → NB3 → NB4 → NB5 → NB4_NB5_with_references.py → NB6
8.  REPRODUCIBILITY
•	Random seed: 42 throughout all notebooks
•	PubChem REST API: accessed January–March 2025
•	PDB 1N8Z and 5X8M: downloaded February 2025
•	Python 3.12 · RDKit 2026.03.3 · TorchANI 2.2 · PyTorch 2.x (CUDA 11.8) · AutoDock Vina 1.2.7 · FreeSASA 2.x · BioPython 1.81
•	Platform: Google Colab · NVIDIA Tesla T4 (15.6 GB VRAM)
9.  CITATION
[Author list]. VanLang InSilico Pipeline: Virtual screening for organic dialdehyde candidates as formalin alternatives in IHC-compatible tissue fixation. Van Lang University, 2025. github.com/lamkienthanh92/VangLangInsilico

Key tools: RDKit (Landrum, 2006); AutoDock Vina 1.2.0 (Eberhardt et al., J Chem Inf Model 2021;61:3891); TorchANI ANI-2x (Devereux et al., J Chem Theory Comput 2020;16:4192); FreeSASA (Mitternacht, F1000Research 2016;5:189); Schiff base mechanism (Tayri-Wilk et al., Nat Commun 2020;11:3128); glyoxal fixation (Richter et al., EMBO J 2018;37:139).
MIT License  ·  Van Lang University 2025  ·  All findings require experimental validation.
