# Mathematical formulation

Canonical reference for the model. The notebooks and the report must use exactly this notation; any change to the model is made here first.

Revised on 14 November 2026 after the instructor's feedback on the project proposal (11 October 2026). Section 9 lists every change with respect to the proposal.

**Notation convention.** Parameters (input data) are written in uppercase ($N_{dg}$, $E_{rdg}$, $ROT_{rd}$). Decision variables are written in lowercase ($x_{rdg}$, $h_{dg}$, $e^{\max}$). Every symbol is described stating the meaning of each of its subscripts.

**Regulatory scenarios.**

- **Scenario A — current regime.** Duty rules of RD 1146/2006: at most 7 duties per month. Applicable to duty rosters until 31 August 2027.
- **Scenario B — RD 951/2026 regime.** Duty rules applicable from 1 September 2027: at most 17 hours per duty and 68 duty hours per month.

When a constraint differs between scenarios, both versions are given.

---

## 1. Sets and indices

| Symbol | Meaning |
|---|---|
| $r \in \mathcal{R}$ | Residents (MIR) included in the roster. |
| $\mathcal{R}_S \subseteq \mathcal{R}$ | Senior residents. Which residency years count as senior is defined by the service (assumption for synthetic data documented in notebook 01). |
| $\mathcal{R}_1 \subseteq \mathcal{R}$ | First-year residents (R1). |
| $d \in \mathcal{D} = \{1, \dots, n\}$ | Days of the month being scheduled. |
| $d \in \{-1, 0\}$ | Last two days of the previous month ($d = 0$ is the last one). They appear only through input data. |
| $g \in \mathcal{G}$ | Duty types. |
| $\mathcal{F} \subseteq \mathcal{D}$ | Weekend days and public holidays of the month. |
| $\mathcal{L} = \mathcal{D} \setminus \mathcal{F}$ | Working days of the month. |
| $\mathcal{P} \subseteq \mathcal{R} \times \mathcal{D}$ | Requested days off: $(r, d) \in \mathcal{P}$ if resident $r$ asked not to be on duty on day $d$. |
| $\mathcal{R}^F \subseteq \mathcal{R}$ | Residents compared in the weekend/holiday fairness criterion: residents with $\Phi_r > 0$ who are eligible for at least one duty on a day of $\mathcal{F}$ this month. |

## 2. Parameters

| Symbol | Meaning |
|---|---|
| $N_{dg} \in \mathbb{Z}_{\ge 0}$ | Number of residents required to cover duty type $g$ on day $d$. |
| $S_{dg} \in \mathbb{Z}_{\ge 0}$ | Minimum number of senior residents required on duty type $g$ on day $d$. |
| $U_{dg} \in \mathbb{Z}_{\ge 0}$ | Maximum number of first-year residents allowed on duty type $g$ on day $d$. |
| $E_{rdg} \in \{0, 1\}$ | 1 if resident $r$ is qualified for duty type $g$ and available on day $d$ (not on holiday, external rotation, course or leave); 0 otherwise. |
| $G^{\min}_r \in \mathbb{Z}_{\ge 0}$ | Minimum number of duties that resident $r$ must do in the month (training obligation), already prorated by the days resident $r$ is available in the month. |
| $G^{\max}_r \in \mathbb{Z}_{\ge 0}$ | Maximum number of duties that resident $r$ may do in the month. Scenario A: at most 7, or fewer under local agreements. |
| $Q_{rd} > 0$ | Priority of resident $r$'s request not to be on duty on day $d$, for $(r, d) \in \mathcal{P}$. Higher values mean more important requests. |
| $H_r \in \mathbb{Z}_{\ge 0}$ | Number of weekend/holiday duties done by resident $r$ in the previous months of the reference period (e.g. the current calendar year). |
| $\Phi_r \in [0, 1]$ | Fraction of the reference period, from its start up to and including the current month, during which resident $r$ is available for duties. |
| $ROT_{rd} \in \{0, 1\}$ | 1 if resident $r$ is scheduled for ordinary activity (ward, clinics) in the service on working day $d$; 0 otherwise. |
| $PL_d \in \mathbb{Z}_{\ge 0}$ | Minimum number of residents needed for ordinary activity on working day $d$. |
| $Y_{rd} \in \{0, 1\}$, $d \in \{-1, 0\}$ | 1 if resident $r$ was on duty on day $d$ of the previous month; 0 otherwise. |
| $T_g > 0$ | Duration in hours of duty type $g$ (used in scenario B). |
| $C_r \in \{0, 1\}$ | 1 if resident $r$ has an approved exceptional extra duty module in the month (scenario B: justified training reasons and the resident's express consent); 0 otherwise. |
| $W^1, \dots, W^4 > 0$ | Objective weights. Used only in the weighted variant of the objective (Section 4). |

$E_{rdg}$ and $ROT_{rd}$ must be derived from the same availability calendar, so that a resident on holiday or on external rotation is neither eligible for duties nor counted for ordinary activity.

## 3. Decision variables

| Symbol | Meaning |
|---|---|
| $x_{rdg} \in \{0, 1\}$ | 1 if resident $r$ covers duty type $g$ on day $d$; 0 otherwise. |
| $h_{dg} \ge 0$ | Number of positions of duty type $g$ on day $d$ left uncovered. |
| $h^{S}_{dg} \ge 0$ | Number of senior residents missing on duty type $g$ on day $d$. |
| $h^{P}_{d} \ge 0$ | Number of residents missing for ordinary activity on working day $d$. |
| $h^{G}_{r} \ge 0$ | Number of duties that resident $r$ is short of their monthly minimum $G^{\min}_r$. |
| $v_{rd} \ge 0$ | Equals 1 when resident $r$ is on duty both on day $d$ and on day $d+2$ (alternate-day pattern); 0 otherwise. |
| $e^{\max}$, $e^{\min}$ | Highest and lowest normalised weekend/holiday load among the residents of $\mathcal{R}^F$. |

The shortfall variables $h_{dg}, h^{S}_{dg}, h^{P}_{d}, h^{G}_{r}$ and $v_{rd}$ are declared continuous. Since $N_{dg}$, $S_{dg}$, $PL_d$ and $G^{\min}_r$ are integers and $x_{rdg}$ is binary, they take integer values at the optimum ($v_{rd}$ takes value 0 or 1).

**Auxiliary expression.**

$$
y_{rd} = \sum_{g \in \mathcal{G}} x_{rdg} \quad (d \in \mathcal{D}), \qquad y_{rd} = Y_{rd} \quad (d \in \{-1, 0\}).
$$

$y_{rd}$ is not a separate variable. It is a linear expression equal to 1 if resident $r$ is on duty on day $d$ and 0 otherwise (constraint (3b) prevents it from exceeding 1). For the last two days of the previous month it is fixed input data.

## 4. Objective function

The four goals of the proposal are optimised in strict priority order (lexicographic optimisation):

$$
\operatorname{lex\,min} \; \bigl( f_1, \; f_2, \; f_3, \; f_4 \bigr)
$$

$$
f_1 = \sum_{d \in \mathcal{D}} \sum_{g \in \mathcal{G}} \bigl( h_{dg} + h^{S}_{dg} \bigr) + \sum_{d \in \mathcal{L}} h^{P}_{d} + \sum_{r \in \mathcal{R}} h^{G}_{r}
$$

$$
f_2 = \sum_{(r, d) \in \mathcal{P}} Q_{rd} \, y_{rd}
$$

$$
f_3 = e^{\max} - e^{\min}
$$

$$
f_4 = \sum_{r \in \mathcal{R}} \sum_{d = -1}^{n-2} v_{rd}
$$

1. **$f_1$ — unmet requirements.** Total number of people missing: uncovered duty positions, missing seniors, missing residents for ordinary activity and duties below the training minimum. In a month that can be fully covered it is 0. When it is positive, the solution states exactly which requirement fails and on which day, instead of the solver returning "infeasible". Each component is reported separately. Weighting all components equally (one unit = one missing person) is a modelling assumption.
2. **$f_2$ — violated day-off requests.** A duty assigned on a requested day off ($y_{rd} = 1$ with $(r, d) \in \mathcal{P}$) costs the priority $Q_{rd}$ of that request.
3. **$f_3$ — fairness.** Gap between the most and the least loaded resident in normalised weekend/holiday duties.
4. **$f_4$ — exhausting patterns.** Number of alternate-day duty patterns ("on duty, off, on duty").

Lexicographic optimisation first minimises $f_1$; then minimises $f_2$ among the solutions with optimal $f_1$; then $f_3$ among those; and finally $f_4$.

**Why lexicographic and not a weighted sum.** A weighted sum $W^1 f_1 + W^2 f_2 + W^3 f_3 + W^4 f_4$ reproduces a strict priority order only if each weight exceeds the largest attainable value of all lower-priority terms combined. This requires weight ratios of several orders of magnitude, and with such ratios the solver's relative optimality tolerance can leave the lower-priority terms unoptimised. Gurobi solves lexicographic problems natively (hierarchical multi-objective optimisation), so the priorities are enforced exactly. The weighted variant is kept only to study trade-offs between $f_2$, $f_3$ and $f_4$ in the sensitivity analysis.

## 5. Constraints

**(1) Coverage.**

$$
\sum_{r \in \mathcal{R}} x_{rdg} + h_{dg} = N_{dg} \qquad \forall d \in \mathcal{D}, \; g \in \mathcal{G}
$$

The residents assigned to duty type $g$ on day $d$, plus the positions left uncovered, equal the number of residents required. No more residents than required are assigned.

**(2a) Senior presence.**

$$
\sum_{r \in \mathcal{R}_S} x_{rdg} + h^{S}_{dg} \ge S_{dg} \qquad \forall d \in \mathcal{D}, \; g \in \mathcal{G}
$$

Each duty must include at least $S_{dg}$ senior residents. If not enough seniors are available, $h^{S}_{dg}$ records how many are missing.

**(2b) First-year limit.**

$$
\sum_{r \in \mathcal{R}_1} x_{rdg} \le U_{dg} \qquad \forall d \in \mathcal{D}, \; g \in \mathcal{G}
$$

At most $U_{dg}$ first-year residents on duty type $g$ on day $d$.

**(3a) Eligibility and availability.**

$$
x_{rdg} \le E_{rdg} \qquad \forall r \in \mathcal{R}, \; d \in \mathcal{D}, \; g \in \mathcal{G}
$$

A resident can only cover duty types they are qualified for, on days they are available. (Implementation: $x_{rdg}$ is only created where $E_{rdg} = 1$.)

**(3b) At most one duty per day.**

$$
y_{rd} \le 1 \qquad \forall r \in \mathcal{R}, \; d \in \mathcal{D}
$$

**(4) Rest after a duty.**

$$
y_{rd} + y_{r,d+1} \le 1 \qquad \forall r \in \mathcal{R}, \; d \in \{0, 1, \dots, n-1\}
$$

No resident is on duty on two consecutive days. For $d = 0$ the constraint reads $Y_{r0} + y_{r1} \le 1$: a resident on duty on the last day of the previous month cannot be on duty on day 1.

**(5) Alternate-day duties (soft).**

$$
y_{rd} + y_{r,d+2} \le 1 + v_{rd} \qquad \forall r \in \mathcal{R}, \; d \in \{-1, 0, \dots, n-2\}
$$

If resident $r$ is on duty on days $d$ and $d+2$ (and therefore, by (4), off on $d+1$), then $v_{rd}$ must equal 1, which is penalised in $f_4$. For $d \in \{-1, 0\}$ the pattern straddles the change of month.

**(6a) Monthly minimum (training obligation).**

$$
\sum_{d \in \mathcal{D}} y_{rd} + h^{G}_{r} \ge G^{\min}_r \qquad \forall r \in \mathcal{R}
$$

**(6b) Monthly maximum.**

$$
\sum_{d \in \mathcal{D}} y_{rd} \le G^{\max}_r \qquad \forall r \in \mathcal{R}
$$

**(7) Requested days off — handled in the objective.**

There is no constraint. For $(r, d) \in \mathcal{P}$, $y_{rd} = 1$ means that resident $r$ is on duty on a day they asked to have off, i.e. the request is violated, and $f_2$ charges its priority $Q_{rd}$. If $y_{rd} = 0$, the request is respected and costs nothing.

The proposal used an additional binary variable $s_{rd}$ with $y_{rd} \le s_{rd}$ and penalised $s_{rd}$. Since $s_{rd}$ is minimised, $s_{rd} = y_{rd}$ at every optimum, so $s_{rd}$ was redundant and has been removed.

**(8) Fair distribution of weekends and public holidays.**

$$
e^{\min} \le \frac{H_r + \sum_{d \in \mathcal{F}} y_{rd}}{\Phi_r} \le e^{\max} \qquad \forall r \in \mathcal{R}^F
$$

The numerator counts the weekend/holiday duties of resident $r$ over the reference period, including those assigned this month. Dividing by $\Phi_r$, the fraction of the same period during which $r$ is available, makes residents with different availability comparable: a resident available for half of the period is expected to carry half the load. Since $\Phi_r$ is data, the constraint is linear. $f_3 = e^{\max} - e^{\min}$ measures the gap between the most and the least loaded resident.

**(9) Residents available for ordinary activity.** For every working day $d \in \mathcal{L}$:

*Scenario A (current regime):*

$$
\sum_{r \in \mathcal{R}} ROT_{rd} \bigl( 1 - y_{r,d-1} \bigr) + h^{P}_{d} \ge PL_d
$$

*Scenario B (RD 951/2026 regime):*

$$
\sum_{r \in \mathcal{R}} ROT_{rd} \bigl( 1 - y_{r,d-1} - y_{rd} \bigr) + h^{P}_{d} \ge PL_d
$$

The left-hand side counts the residents scheduled in the service on day $d$ ($ROT_{rd} = 1$) who are actually present for ordinary activity:

- A resident who was on duty the day before ($y_{r,d-1} = 1$) is post-call (*saliente*): they leave in the morning and do not work on day $d$, so they are not counted. For $d = 1$ the previous day is $d = 0$, given by $Y_{r0}$.
- Scenario A: a resident on duty on day $d$ works the ordinary morning shift and starts the duty afterwards, so they are counted. This is the usual weekday practice; it must be validated with the hospital.
- Scenario B: an ordinary shift and a consecutive duty may not exceed 17 hours together, so a resident on duty on day $d$ is assumed not to work the ordinary shift that day and is not counted.

By constraint (4), $y_{r,d-1} + y_{rd} \le 1$, so the factor $1 - y_{r,d-1} - y_{rd}$ is always 0 or 1. If fewer than $PL_d$ residents are available, $h^{P}_{d}$ records how many are missing.

**(10) Monthly duty hours (scenario B only).**

$$
\sum_{d \in \mathcal{D}} \sum_{g \in \mathcal{G}} T_g \, x_{rdg} \le 68 + 17 \, C_r \qquad \forall r \in \mathcal{R}
$$

At most 68 duty hours per month (four modules of 17 hours), plus 17 hours if resident $r$ has an approved extra module. $C_r$ is data, not a decision: the extra module can only be justified by training reasons and requires the resident's consent, so the model must not use it to fill coverage gaps. A variant where $C_r$ becomes a decision variable may be used only as a labelled what-if analysis.

In scenario B the duty types $\mathcal{G}$ and their durations $T_g$ also change (for example, 24-hour weekend duties must be split into shifts of at most 17 hours). This is reflected in the input data, not only in constraint (10).

## 6. Feasibility guarantee

The model always has a feasible solution: $x_{rdg} = 0$ for all $r, d, g$, with $h_{dg} = N_{dg}$, $h^{S}_{dg} = S_{dg}$, $h^{P}_{d} = PL_d$, $h^{G}_{r} = G^{\min}_r$, $v_{rd} = 0$, $e^{\max} = \max_{r \in \mathcal{R}^F} H_r / \Phi_r$ and $e^{\min} = \min_{r \in \mathcal{R}^F} H_r / \Phi_r$, satisfies every constraint (in (4), $Y_{r0} + 0 \le 1$). Every requirement that can fail is measured by a shortfall variable instead of being imposed as a hard constraint. Consequently, an "infeasible" status from the solver indicates a data error (a parameter outside its domain), never a month that cannot be covered.

## 7. Implementation notes

- Lexicographic objective: Gurobi hierarchical multi-objective (`Model.setObjectiveN`) with decreasing priorities for $f_1, \dots, f_4$ and zero degradation tolerance.
- KPIs are computed from the solution $x$ (coverage, violated requests, loads, patterns), not read from the variables $h$, $e^{\max}$, $e^{\min}$, which are only guaranteed to be tight for the objective level in which they are penalised.
- Model size: for 20 residents, 31 days and 3 duty types there are at most 1,860 binary variables and about 2,300 constraints. This exceeds the size-limited license bundled with `pip install gurobipy` (2,000 variables and 2,000 linear constraints), so an academic license is required.

## 8. Assumptions to validate with a hospital service

1. Which residency years count as senior ($\mathcal{R}_S$).
2. Scenario A: a resident on duty on a working day works the ordinary morning shift first.
3. Scenario B: how the service will redesign duty types, durations and start times.
4. Whether a request for day $d$ off also excludes a duty on day $d-1$ (the resident would be post-call on day $d$).
5. Which days count as demanding (only weekends and public holidays, or also Fridays and eves of holidays).
6. Reference period for fairness (calendar year or residency year) and whether all residents are compared together or by residency year.
7. Relative importance of the different shortfalls in $f_1$.

## 9. Changes with respect to the proposal (11 October 2026)

| Item | Proposal | Revised formulation | Reason |
|---|---|---|---|
| Notation | Mixed case | Parameters uppercase, variables lowercase | Instructor feedback |
| Parameter descriptions | Short labels | Meaning of every subscript stated | Instructor feedback |
| Request priority | $w_{rd}$ | $Q_{rd}$ | Avoid confusion with the objective weights $W^k$ |
| Constraint (7) | $y_{rd} \le s_{rd}$, penalise $s_{rd}$ | Penalise $Q_{rd} \, y_{rd}$ directly | $s_{rd} = y_{rd}$ at every optimum; the instructor found the link unclear |
| Constraints (2), (6), (9) | Hard | Shortfall variables penalised in $f_1$ | The hard versions could make the model infeasible (e.g. no senior available, or total training minima above the number of duty positions), contradicting the goal of reporting uncovered duties |
| Constraint (9) | One version plus a note | One version per regulatory scenario | RD 951/2026 limits an ordinary shift plus a consecutive duty to 17 hours |
| Constraint (8), normalisation | $\Phi_r$ = fraction of the year | $\Phi_r$ = fraction of the same reference period as $H_r$, including the current month | Numerator and denominator must refer to the same period |
| Constraint (8), residents compared | All residents | $\mathcal{R}^F$ | Residents whose load cannot change this month would fix $e^{\max}$ or $e^{\min}$ |
| Constraint (5) | Linked to day 0 | Linked to days $-1$ and $0$ | Detect the pattern across the change of month |
| Uncovered positions $h_{dg}$ | Integer | Continuous | Integrality is implied |
| Objective | Weighted, $W^1 \gg W^2 \gg W^3 \gg W^4$ | Lexicographic (Gurobi hierarchical) | Exact priorities without numerical issues |
| Scenario 2027 | Draft regulation; $c_r \le \kappa_r$ as a decision | RD 951/2026 (approved, applicable from 1 September 2027); $C_r$ as data | Regulation approved on 3 November 2026; the extra module is for training reasons only |
