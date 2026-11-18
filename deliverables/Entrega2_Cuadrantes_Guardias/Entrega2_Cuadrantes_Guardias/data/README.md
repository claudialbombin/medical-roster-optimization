# Data

## Provenance

| Type | Location | Committed to git |
|---|---|---|
| Synthetic instances | `data/synthetic/` | Yes. Reproducible from `notebooks/01_synthetic_data_generation.ipynb` with a fixed random seed. |
| Real hospital data | `data/raw/` (local only) | Never. The folder is git-ignored. |

All data currently in the repository are **synthetic**. They do not describe any real hospital, and results obtained with them must not be presented as results of a real hospital.

## Privacy rules

- No real names, health conditions or any other information that could identify a person.
- Residents are identified only by codes (e.g. `R01`).
- Every dataset states whether it is real, anonymised, synthetic or assumed.
- Real data are only used after authorisation from the hospital. Only anonymised derived data may be committed, after review by the team.

## Instance format

Defined in notebook 01. Each instance contains the data for every parameter of [`docs/formulation.md`](../docs/formulation.md), Section 2, together with the assumptions used to generate it.
