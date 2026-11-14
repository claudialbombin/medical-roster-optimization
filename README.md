# Medical Roster Optimization

Fair and feasible monthly duty rosters for medical residents in Spanish public hospitals, formulated as a mixed-integer linear program (MILP) and solved with Gurobi.

> **Status: in development.** Model checkpoint on 16 November 2026. The results section will be completed as the project progresses.

University project for the course *Optimization and Simulation* (academic year 2026–2027).
Team: Miguel Troyano Martín · Claudia María López Bombín · María Teresa Sangil Terrés.

## Problem and motivation

Every month, each hospital service decides which resident (MIR) covers each type of duty (*guardia*) on each day. The roster must:

- cover every duty with suitably qualified residents, including enough senior residents;
- comply with labour regulations (rest after a duty, monthly limits);
- respect holidays, external rotations and personal day-off requests;
- leave enough residents for the ordinary activity of the service (wards, clinics);
- spread the hardest duties, on weekends and public holidays, fairly over the year.

Rosters are usually built by hand in a spreadsheet. This takes hours, often produces rosters perceived as unfair, and cannot answer planning questions such as *how many residents does the service need to cover August?*

The question is timely. Royal Decree 951/2026 (BOE, 6 November 2026), which amends RD 1146/2006, limits residents' duties to 17 hours and 68 duty hours per month from September 2027, compared with up to seven duties per month today. Services will have to redesign their rosters.

## Methodology

- **Decision:** which resident covers each duty type on each day ($x_{rdg} = 1$ if resident $r$ covers duty type $g$ on day $d$).
- **Objectives, in strict priority order (lexicographic optimisation):**
  1. minimise unmet requirements: uncovered duty positions, missing senior residents, missing residents for ordinary activity, duties below the training minimum;
  2. minimise violated day-off requests, weighted by priority;
  3. minimise the gap between the most and the least loaded resident in weekend/holiday duties, normalised by availability and taking previous months into account;
  4. minimise alternate-day duty patterns.
- **Constraints:** coverage and seniority mix, eligibility and availability, rest after a duty (linked to the previous month), monthly minimum and maximum number of duties, residents available for ordinary activity, and duty-hour limits.
- **Regulatory scenarios:** A, the current regime (RD 1146/2006, at most 7 duties per month); B, the RD 951/2026 regime (duties of at most 17 hours, at most 68 duty hours per month).
- **Always a roster:** unmet requirements are measured instead of being imposed as hard constraints, so for a month that cannot be fully covered the model shows exactly which requirement fails and when, instead of returning "infeasible".

The complete formulation (sets, parameters, variables, objective and constraints, each explained in words) is in [`docs/formulation.md`](docs/formulation.md).

## Technologies

Python 3.10+, Jupyter, Gurobi (`gurobipy`), pandas, NumPy, Matplotlib.

## Repository structure

```
medical-roster-optimization/
├── data/
│   ├── README.md                          # data provenance and privacy rules
│   └── synthetic/                         # generated instances (reproducible)
├── docs/
│   └── formulation.md                     # mathematical model
├── notebooks/
│   ├── 01_synthetic_data_generation.ipynb # builds and validates instances
│   ├── 02_baseline_model.ipynb            # builds, solves and validates the model
│   └── 03_analysis.ipynb                  # results and sensitivity analysis
├── results/
│   ├── figures/
│   └── tables/
├── requirements.txt
├── LICENSE
└── README.md
```

## Installation and execution

```bash
git clone https://github.com/claudialbombin/medical-roster-optimization.git
cd medical-roster-optimization
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

**Gurobi license.** The license bundled with `pip install gurobipy` is limited to 2,000 variables and 2,000 linear constraints, which is too small for a full monthly instance. Request a free academic license through the [Gurobi Academic Program](https://www.gurobi.com/academia/academic-program-and-licenses/) and install it following Gurobi's instructions.

Run the notebooks in order:

```bash
jupyter lab
```

1. `01_synthetic_data_generation.ipynb` generates the instances in `data/synthetic/`.
2. `02_baseline_model.ipynb` builds and solves the baseline model.
3. `03_analysis.ipynb` reproduces the results and the sensitivity analysis.

## Data

No hospital data are used at this stage. All instances are **synthetic**: they are generated reproducibly (fixed random seed) in notebook 01 and calibrated with public information (regulation, Madrid public holiday calendar) and documented assumptions. Results obtained with synthetic data do not describe any real hospital. Real hospital data, if obtained, are kept outside the repository. See [`data/README.md`](data/README.md).

## Results

To be added.

## Limitations and possible extensions

- The ordinary-shift timetable is not modelled, so the 24-hour weekly rest and the 45-hour weekly average of RD 951/2026 are not enforced.
- Parameters rely on documented assumptions until they can be validated with a hospital service.
- Possible extensions: re-optimisation after unexpected absences with the minimum number of changes, Monte Carlo evaluation of roster robustness, and the minimum workforce required under each regulatory scenario.

## Development workflow

- `main`: stable versions, updated at each milestone.
- `develop`: integration branch.
- `feature/<name>`: one branch per piece of work, created from `develop` and merged back through a pull request. Current branches: `feature/synthetic-data`, `feature/baseline-model`, `feature/analysis`.

Notebooks are hard to merge, so the same notebook should not be edited in two branches at the same time.

## License

MIT. See [`LICENSE`](LICENSE).
