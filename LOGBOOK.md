# Logbook (D4)

> Format: date — what I did — what I learned / decision made — issues — next step.

---

## Week 3 (28 Sep – 2 Oct)

**28.09** — Set up the private GitHub repository `nids-hwu` and created the initial project structure:

- `01-Planning`
- `02-Research`
- `03-Design`
- `04-Implementation`
- `05-Testing`
- `06-Meetings`
- `07-Admin`

Created the initial `LOGBOOK.md` and pushed the repository structure to GitHub.

**What I learned / decision:** I decided to keep the project organised by separating planning, research, implementation, testing and administrative material. GitHub will be used as the main D4 project-management record throughout the project.

**Next step:** begin literature review and select a suitable network intrusion detection dataset.

---

**29.09–30.09** — Downloaded the CICIDS2017 `MachineLearningCVE` dataset, consisting of eight CSV files. Began searching for literature using Google Scholar and Consensus and started collecting papers in Zotero.

The literature search initially focused on:
- machine-learning-based Network Intrusion Detection Systems;
- baseline classification models;
- adversarial evasion attacks;
- feature-space attacks;
- realistic/problem-space adversarial attacks;
- defensive approaches.

**What I learned:** Adversarial NIDS research can be divided into attacks that manipulate extracted feature values directly and attacks that attempt to respect real network constraints. This distinction appeared relevant to the project direction.

**Next step:** inspect CICIDS2017 and begin organising the literature.

---

**01.10** — Continued literature search and created the first CICIDS2017 data-exploration notebook.

Loaded the eight CSV files and inspected:
- file names;
- feature names;
- number of columns;
- class labels;
- number of records.

The raw dataset contained:
- **8 CSV files**
- **2,830,743 network-flow records**
- **79 columns**
- **15 labels including BENIGN**

Calculated the full class distribution and identified substantial class imbalance.

**What I learned:** CICIDS2017 is dominated by BENIGN traffic, while some attack types contain very few observations. Therefore, accuracy alone will not be sufficient for evaluating the intrusion detector.

**Next step:** perform data-quality checks before training any machine-learning model.

---

**02.10 — Supervisor meeting**

Presented the initial project direction, dataset choice and early literature search.

Discussed the proposed use of CICIDS2017 and the idea of evaluating machine-learning-based intrusion detection.

No major change to the overall direction was required at this stage.

**Next step agreed:** continue literature review, inspect the dataset in more detail and begin preparing the technical feasibility work.

---

## Week 4 (5 Oct – 9 Oct)

**05.10** — Prepared questions for the next supervisor meeting regarding:
- dataset suitability;
- possible machine-learning models;
- feature constraints;
- adversarial attack realism;
- reference management;
- D1 expectations.

Continued reviewing papers and organising references.

---

**06.10–07.10** — Performed detailed CICIDS2017 data-quality checks using Python.

Checked:
- missing values (`NaN`);
- infinite numerical values;
- exact duplicate rows;
- duplicated or redundant features.

Found that invalid values occurred mainly in rate-derived features such as:
- `Flow Bytes/s`
- `Flow Packets/s`

Also identified a large number of duplicate rows.

**What I learned:** Raw benchmark datasets should not be passed directly into a machine-learning model. Data-quality problems may distort the evaluation or introduce data leakage.

---

**08.10** — Investigated the apparently duplicated columns:

- `Fwd Header Length`
- `Fwd Header Length.1`

Programmatically compared them across all eight CSV files and verified that they were identical.

**Decision:** remove `Fwd Header Length.1` during preprocessing.

Also investigated duplicate records by class and found that duplicate percentages varied substantially between attack types.

**What I learned:** Exact duplicates should be handled before train-test splitting because otherwise identical observations could appear in both training and testing data and inflate measured model performance.

---

**09.10 — Supervisor meeting**

Provided a progress update on:
- dataset inspection;
- data-quality issues;
- literature review;
- planned preprocessing.

Continued with the agreed dataset and project direction.

---

**09.10** — Organised references in Zotero and exported the library to `My Library.bib`.

Fixed a GitHub repository structure issue by removing an accidentally duplicated nested directory and pushing the corrected structure.

**Next step:** create a reproducible preprocessing pipeline.

---

## Week 5 (12 Oct – 16 Oct)

**12.10–13.10** — Continued literature review and organised the papers into four main groups:

1. baseline NIDS / machine-learning studies;
2. feature-space adversarial attacks;
3. constrained/problem-space adversarial attacks;
4. defensive approaches.

The literature increasingly suggested that many adversarial NIDS studies modify already-extracted feature values without demonstrating that the resulting feature combinations can be produced by real network traffic.

**Research direction refined:** compare unrestricted feature-space perturbations with perturbations that respect network-related constraints and dependencies.

---

**14.10** — Defined the preliminary research focus.

The central idea became:

> Evaluate whether the apparent adversarial vulnerability of machine-learning-based NIDS changes when perturbations are restricted by operationally realistic network-feature constraints.

Began drafting a preliminary feature constraint table distinguishing:
- directly controllable features;
- indirectly affected features;
- derived/dependent features;
- immutable or context-limited features.

**What I learned:** Many CICIDS2017 flow features are derived statistics and cannot realistically be assigned arbitrary independent values.

---

**15.10** — Completed the main preprocessing pipeline.

The eight CICIDS2017 files were merged into one dataset.

Preprocessing included:
- stripping unnecessary spaces from column names;
- removing the verified duplicate feature;
- converting infinite values to missing values;
- removing invalid records;
- checking global duplicates across the combined dataset.

Identified:
- **2,867 rows containing NaN and/or infinity**
- **307,078 exact global duplicate rows**

---

**16.10** — Completed creation of the clean CICIDS2017 dataset.

Final dataset after preprocessing:

- Original flows: **2,830,743**
- Invalid rows removed: **2,867**
- Exact global duplicates removed: **307,078**
- Final clean flows: **2,520,798**

Created a binary target:

- `BENIGN = 0`
- `ATTACK = 1`

Final class distribution:

- BENIGN: **2,095,057 (83.11%)**
- ATTACK: **425,741 (16.89%)**

Verified:
- `NaN = 0`
- `Infinity = 0`
- exact duplicates = `0`

Saved the processed dataset as:

`processed/cicids2017_clean.csv`

---

**16.10** — Implemented the first Random Forest feasibility baseline.

Used a stratified pilot sample of **300,000 flows**:
- training set: **240,000**
- test set: **60,000**

Pilot results:

- Accuracy: **0.9982**
- Precision: **0.9959**
- Recall / Detection Rate: **0.9935**
- F1-score: **0.9947**
- False Positive Rate: **0.0008**

Confusion matrix:

- True Negatives: **49,777**
- False Positives: **42**
- False Negatives: **66**
- True Positives: **10,115**

**What I learned:** The selected dataset and implementation pipeline are technically feasible. However, the very high result may partly reflect the random stratified split, so the final dissertation should consider a stricter source/day-aware evaluation.

---

**16.10 — Supervisor meeting**

Presented progress with:
- CICIDS2017 preprocessing;
- preliminary baseline model;
- constrained-versus-unconstrained adversarial robustness idea.

The overall project direction was accepted without major changes.

**Next step:** develop the preliminary constraint model and prepare feasibility evidence for D1/D2.

---

## Week 6 (19 Oct – 23 Oct)

**19.10–20.10** — Began preparing the Heriot-Watt Research Ethics application.

Documented that:
- the project uses publicly available cybersecurity data;
- no human participants are involved;
- no new personal data will be collected;
- experiments are performed offline;
- no attacks will be conducted against live networks or third-party systems.

Identified the main ethical concern as the **dual-use nature of adversarial cybersecurity research**.

**Mitigation:** restrict experiments to stored public datasets and defensive offline evaluation.

---

**21.10–22.10** — Revised the Ethics application.

Added:
- CICIDS2017 provenance;
- Canadian Institute for Cybersecurity / University of New Brunswick as the dataset source;
- associated dataset publication;
- clearer explanation of offline analysis and adversarial experiments.

Corrected the supervisor-signature workflow.

---

**23.10** — Submitted the completed Research Ethics application.

---

**23.10** — Developed a preliminary constraint-aware adversarial perturbation prototype.

Created the implementation notebook later consolidated as:

`04-Implementation/adversarial_perturbation_prototype.ipynb`

The first prototype focused on timing-related network-flow features.

A timing factor was applied consistently:
- timing and inter-arrival-time features were increased;
- corresponding rate-derived features were decreased.

This avoided modifying all features independently.

Tested nominal timing factors corresponding to:
- 0%
- 5%
- 10%
- 20%
- 30%

On malicious flows that were initially detected correctly by the Random Forest, the constraint-aware timing prototype produced increasing evasion as the perturbation increased.

At the 30% nominal budget:

- Detection Rate: **98.94%**
- Evasion Rate: **1.06%**

**What I learned:** A simple dependency-aware feature transformation can alter model behaviour, demonstrating that the planned robustness evaluation pipeline is technically feasible.

**Important limitation:** This is still a feature-level prototype and not yet a packet-level validated problem-space attack.

---

**23.10** — Implemented an exploratory random unconstrained perturbation.

Selected numerical features were changed independently within a defined percentage range.

The effect was small.

**What I learned:** Random numerical perturbation is not the same as an adversarial attack because it does not deliberately attempt to cause misclassification.

---

**23.10** — Refined the unconstrained experiment into a **model-guided adversarial search**.

For each malicious flow:
1. generated multiple independently modified candidates;
2. evaluated each candidate with `RandomForest.predict_proba()`;
3. selected the candidate with the lowest ATTACK probability.

Tested the same nominal perturbation strengths.

At 30% nominal strength:

- Detection Rate: **99.70%**
- Evasion Rate: **0.30%**
- Mean ATTACK probability: **0.9278**

Baseline mean ATTACK probability was approximately **0.9958**.

**What I learned:** Even when classification remains ATTACK, the model-guided search reduces the classifier's confidence in the ATTACK class.

---

**23.10** — Compared the constrained timing prototype and unconstrained adversarial search on the **same sample of 5,000 initially correctly detected malicious flows**.

Results:

| Nominal Budget | Constrained Detection Rate | Unconstrained Detection Rate | Constrained Evasion Rate | Unconstrained Evasion Rate |
|---|---:|---:|---:|---:|
| 0% | 100.00% | 100.00% | 0.00% | 0.00% |
| 5% | 99.50% | 99.94% | 0.50% | 0.06% |
| 10% | 99.16% | 99.92% | 0.84% | 0.08% |
| 20% | 99.04% | 99.78% | 0.96% | 0.22% |
| 30% | 98.94% | 99.70% | 1.06% | 0.30% |

**Interpretation:** The preliminary constrained timing transformation produced more evasion than the current unconstrained search. This must not be interpreted as evidence that constrained attacks are generally stronger because the two prototypes modify different features and the nominal budgets are not physically equivalent.

**Decision:** A more rigorous definition of perturbation budget will be required for D3.

---

**23.10** — Performed a preliminary Random Forest feature-importance analysis.

The highest-ranked features were mainly related to:
- packet length;
- packet-length variance;
- average packet size;
- backward packet length;
- traffic volume.

Timing features were not individually dominant among the highest-ranked features.

**Decision for D3:** extend the constraint model beyond timing and investigate packet-size/padding-related transformations.

---

**23.10 — Supervisor meeting**

Provided a progress update on the feasibility implementation and D1 preparation.

Discussed the constrained-versus-unconstrained direction.

No major change to the overall project direction was requested.

**Next step:** incorporate the feasibility results into D1 and complete the first-semester proposal.

---

## Week 7 (26 Oct – 30 Oct)

**26.10–27.10** — Incorporated the preliminary implementation results into D1.

Updated the Feasibility section with:
- preprocessing results;
- Random Forest baseline;
- constraint-aware timing prototype;
- model-guided unconstrained prototype;
- same-sample preliminary comparison.

Added appropriate limitations to avoid presenting the feasibility prototypes as final attacks.

---

**28.10** — Refined the D1 project-status table to clearly distinguish:

### Completed for D1
- dataset inspection;
- preprocessing;
- clean dataset;
- Random Forest pilot;
- preliminary constraint model;
- preliminary constrained/unconstrained perturbation prototypes.

### Planned for D3
- final baseline design;
- stricter evaluation split;
- broader constraint model;
- refined adversarial attacks;
- rigorous perturbation budgets;
- defence evaluation;
- final results and discussion.

---

**29.10** — Research Ethics application approved.

The application had been submitted on **23 October 2026** and received approval on **29 October 2026**.

Updated the project documentation to reflect the approved status.

---

**29.10** — Performed a final structural review of the D1 proposal.

Improved:
- experimental workflow diagram;
- threat-model explanation;
- preliminary experiment limitations;
- success criteria;
- project management section;
- Gantt chart;
- risk analysis;
- PLES section;
- GenAI Summary.

Reviewed comparable literature on realistic/problem-space adversarial NIDS evaluation and refined the report wording accordingly.

---

**30.10** — Reviewed the current D1 version and confirmed that the core feasibility stage is complete.

Main-body structure:

1. Introduction
2. Background Research
3. Methodology and Work Plan
4. Feasibility
5. Conclusion

The main body remains within the required **14-page limit**.

Supporting material includes:
- References;
- Project Management;
- Gantt chart;
- Risk Analysis;
- PLES;
- GenAI Summary;
- Additional Material.

**Current status:** complete D1 working draft available for supervisor review.

---

**30.10 — Supervisor meeting**

**Progress to present:**
- Ethics approval obtained;
- D1 full draft completed;
- preliminary implementation completed;
- D4 logbook updated.

**Actions after meeting:** record supervisor feedback and make only necessary changes to D1 before final submission.

---

# Current Project Status — 30 October 2026

## Completed / demonstrated

- GitHub project-management structure
- Initial literature review
- CICIDS2017 selection
- CICIDS2017 data inspection
- Data-quality analysis
- Reproducible preprocessing pipeline
- Clean binary dataset
- Random Forest pilot baseline
- Preliminary constraint model
- Constraint-aware timing perturbation prototype
- Model-guided unconstrained adversarial prototype
- Same-sample preliminary robustness comparison
- Random Forest feature-importance diagnostic
- Research Ethics approval
- Complete D1 working draft

---

# Planned Work — November 2026

> The following entries are planning items. They should be converted into completed journal entries only after the work has actually been performed.

## Week 8 (2 Nov – 6 Nov)

### Planned activities

- Review supervisor feedback on the complete D1 draft.
- Make required D1 corrections.
- Review the Background Research section against the Zotero literature collection.
- Check all references and citations.
- Finalise figures and tables.
- Check consistency of terminology throughout D1.
- Begin preparation of the D2 five-minute Preparation and Feasibility Video.
- Draft the D2 slide structure and narration.
- Continue updating D4 project-management records.

### Target by end of Week 8

Stable D1 revision and first complete D2 structure.

---

## Week 9 (9 Nov – 13 Nov)

### Planned activities

- Incorporate any remaining supervisor comments.
- Perform final academic-English and consistency review of D1.
- Check:
  - title page;
  - declaration;
  - acknowledgements;
  - table of contents;
  - page numbering;
  - references;
  - Project Management;
  - Risks;
  - PLES;
  - GenAI Summary.

- Continue development of the D2 video.
- Finalise D2 slides.
- Prepare and rehearse the narration.
- Confirm that the video demonstrates:
  - preparation work;
  - methodology;
  - tool choices;
  - feasibility evidence;
  - project plan;
  - risks and contingency plans;
  - PLES;
  - measurable success criteria.

- Update `LOGBOOK.md`.
- Update supervisor meeting notes.
- Check GitHub repository organisation.

### Target by 13 November

D1 ready for final pre-submission review and D2 substantially complete.

---

## 14 Nov – 25 Nov

### Planned final quality-assurance period

- Final supervisor-facing review if required.
- Final proofread of D1.
- Verify all figures and references.
- Finalise D2 recording and editing.
- Check video duration and readability.
- Export final deliverables.
- Perform final submission checklist.
- Back up final files in GitHub/project storage.

---

## 26 November 2026

**D1 Project Proposal and D2 Preparation and Feasibility Video submission deadline.**

---

# Semester 2 — Planned D3 Work

Following completion of the first-semester deliverables, the main implementation will continue with:

- final baseline evaluation;
- stricter source/day-aware evaluation;
- additional machine-learning baseline(s);
- refinement of the feature constraint model;
- packet-size/padding-related constrained transformations;
- refined unconstrained adversarial attacks;
- rigorous perturbation-budget definitions;
- defence experiments;
- adversarial training and/or feature restriction;
- robustness versus false-positive-rate analysis;
- final evaluation;
- discussion;
- final dissertation preparation.

The project-management logbook will continue to be maintained throughout Semester 2 and updated after significant experiments, meetings and project decisions.
