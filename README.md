# CallMeMaybe | Operator Performance Analysis

**Final Data Analytics Project — TripleTen**  
**Author:** Thais Cristina da Costa

This project analyzes virtual telephony data for CallMeMaybe to identify operators who may warrant further investigation. The analysis focuses on incoming missed-call rates, waiting times, and active outgoing-call volume, combining exploratory data analysis, statistical tests, and an interactive Tableau dashboard.

## Project Deliverables

- **Interactive Tableau dashboard:** [View the dashboard on Tableau Public](https://public.tableau.com/views/CallMeMaybeOperatorPerformanceAnalysis/CallMeMaybeOperatorPerformance?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)
- **Final presentation:** [View the project presentation](https://docs.google.com/presentation/d/1V2OpDKnY3KgdMZhGd_GLhCgZx1PnpsEK/edit?usp=drive_link&ouid=107642534024121673319&rtpof=true&sd=true)
- **Analysis notebook:** `notebooks/CallMeMaybe_Notebook_EN.ipynb`

## Business Problem

CallMeMaybe provides virtual telephony services to organizations that handle large volumes of incoming and outgoing calls. The business goal is to help supervisors identify operators whose call patterns may indicate a need for closer review.

The analysis investigates three potential signals:

1. A high rate of missed incoming calls.
2. Long average waiting times for incoming calls that were not missed.
3. Low active outgoing-call volume, where outgoing activity is relevant to the operator's responsibilities.

**Business question:** How can call data help supervisors identify operators who warrant further investigation?

## Data

The project uses two datasets:

| Dataset | Description | Original size |
|---|---|---:|
| `telecom_dataset_new.csv` | Call records, including direction, operator, missed-call status, call count, and durations | 53,902 rows × 9 columns |
| `telecom_clients.csv` | Client information, including tariff plan and registration date | 732 rows × 3 columns |

The call dataset includes fields such as `user_id`, `date`, `direction`, `internal`, `operator_id`, `is_missed_call`, `calls_count`, `call_duration`, and `total_call_duration`.

The source datasets are not included in this repository. Place the files in the `data/` directory before running the notebook. Use the dataset files supplied for the project and make sure their filenames match the paths in the notebook.

## Data Preparation

The following data-quality steps were completed:

- Identified and removed 4,900 exact duplicate rows, leaving 49,002 rows in the analysis dataset.
- Corrected data types for dates, identifiers, and Boolean fields.
- Identified 8,172 records with a missing `operator_id` (15.16% of the original call dataset).
- Checked call counts and durations for invalid or inconsistent values.
- Examined missing operator identifiers by call direction and missed-call status.

Because the dataset has no unique call identifier, it was not possible to confirm that every exact duplicate represented an ingestion error. Missing `operator_id` values were concentrated among missed incoming calls, limiting how reliably those calls could be attributed to individual operators.

## Methodology

The notebook follows these main steps:

1. **Exploratory data analysis:** inspect dataset structure, missing values, duplicates, variable types, and distributions.
2. **Metric engineering:** calculate operator-level incoming missed-call rates, weighted average waiting time per call, and active outgoing-call volume.
3. **Statistical analysis:** evaluate the relationship between waiting time and incoming missed-call rate using Spearman correlation, and compare waiting-time distributions using a Mann–Whitney U test.
4. **Sensitivity analysis:** check whether the relationship between outgoing activity and incoming missed-call rate persists when minimum incoming-call volume requirements change.
5. **Exploratory prioritization:** combine three signals to identify operators who may warrant further review.
6. **Communication:** summarize the operational indicators in a Tableau Public dashboard and a final presentation.

### Exploratory prioritization rules

- **High waiting time:** weighted average waiting time per non-missed incoming call above the 95th-percentile threshold of **33.54 seconds**, among operators eligible for the waiting-time analysis.
- **High incoming missed-call rate:** missed-call rate above the 95th-percentile threshold of **4.25%**, considering operators with at least 50 incoming calls.
- **Low active outgoing activity:** fewer than 50 active outgoing calls, among operators with at least 50 total outgoing calls.

These thresholds are exploratory screening criteria, not official company standards.

## Key Findings

- **Waiting time:** 12 of the 230 eligible operators had weighted average waiting times above the 95th-percentile threshold of 33.54 seconds.
- **Incoming missed calls:** 12 of the 234 eligible operators had incoming missed-call rates above the 95th-percentile threshold of 4.25%.
- **Waiting time and missed-call rate:** Spearman's correlation showed a weak positive association (**ρ = 0.2146, p = 0.001058**). The relationship was statistically significant, but weak.
- **Group comparison:** the Mann–Whitney U test found a statistically significant difference in waiting-time distributions between groups split at the median incoming missed-call rate (**p = 0.029544**), with a small effect size and a median difference of approximately 3.24 seconds.
- **Outgoing activity:** a weak positive association appeared in the full sample, but it was not statistically significant when the analysis was restricted to operators with minimum incoming-call volumes of 10, 20, 50, or 100. The relationship was therefore not robust to these volume filters.

### Operators flagged for further investigation

Five operators had at least two positive exploratory signals:

| Operator ID | Average incoming waiting time | Incoming missed-call rate | Signals |
|---:|---:|---:|---|
| `944226` | 41.25 sec | 16.67% | High waiting time + high incoming missed-call rate |
| `944216` | 33.56 sec | 10.21% | High waiting time + high incoming missed-call rate |
| `944220` | 35.13 sec | 6.79% | High waiting time + high incoming missed-call rate |
| `944218` | 35.93 sec | 6.12% | High waiting time + high incoming missed-call rate |
| `906070` | 41.11 sec | 0.62% | High waiting time + low active outgoing-call volume |

The first four operators showed both high waiting time and high incoming missed-call rates. Operator `906070` showed high waiting time and low active outgoing-call volume, but did not exceed the missed-call-rate threshold.

## Recommendations

- Review the five priority cases in context, checking call routing, workload, call volume, and each operator's responsibilities.
- Use the dashboard to help supervisors explore the indicators and guide follow-up, rather than making decisions from a single metric.
- Improve the completeness of `operator_id` recording, especially for missed incoming calls, so future analyses can attribute performance indicators more reliably.
- Reassess the screening thresholds with business stakeholders before using them operationally.

## Limitations

- The missing `operator_id` values limit operator-level analysis, particularly for missed incoming calls.
- Without a unique call identifier, exact duplicate rows cannot be conclusively classified as erroneous records.
- The thresholds are data-driven exploratory cut-offs and may change with the dataset or business context.
- Statistical associations do not establish causation, and the flagged patterns do not prove that an operator is inefficient.
- Low outgoing-call volume may be appropriate for some operators depending on their assigned responsibilities.

## Tools and Technologies

- **Python** — data analysis
- **Pandas and NumPy** — data preparation and metric calculation
- **Matplotlib and Seaborn** — data visualization
- **SciPy** — statistical tests
- **Jupyter Notebook** — analysis workflow
- **Tableau Public** — interactive dashboard
- **Git and GitHub** — version control and project sharing

## Repository Structure

```text
CallMeMaybe/
├── data/
│   ├── telecom_dataset_new.csv
│   └── telecom_clients.csv
├── dashboard/
│   └── CallMeMaybe — Operator Performance
├── notebooks/
│   └── CallMeMaybe_Notebook_EN.ipynb
└── README.md
```

## How to Run the Notebook

1. Clone or download the repository.
2. Add `telecom_dataset_new.csv` and `telecom_clients.csv` to the `data/` folder. These files are not included in this repository.
3. Install the required Python packages:

   ```bash
   pip install pandas numpy matplotlib seaborn scipy jupyter
   ```

4. Open the notebook from the `notebooks/` directory:

   ```bash
   jupyter notebook CallMeMaybe_Notebook_EN.ipynb
   ```

5. Run the notebook cells from top to bottom. The notebook reads the input files from `../data/`.

---

*This project was completed as part of the TripleTen Data Analyst program.*
