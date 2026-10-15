# Frequency-Secure Real-Time AC Dispatch: Supplementary Material and Test-System Data

This repository accompanies the paper

> "Nodal frequency-secure real-time dispatch of inverter-dominated power
> systems with learned constraints monotone in activatable reserve."

It holds the Supplementary Material of the paper and the data of its test
systems: the IEEE 118-bus and IEEE 300-bus systems with part of the
synchronous generation replaced by grid-forming (GFM) and grid-following
(GFL) inverter-based resources (IBRs), and the inputs of the twelve-day
study with storage on the IEEE 118-bus system.

## Layout

```
supplementary.pdf                        Supplementary Material of the paper
LICENSE                                  CC BY 4.0
ieee118/
    pglib_opf_case118_ieee.m             network
    synchronous_generators.csv           synchronous generators (SGs)
    inverter_based_resources.csv         IBRs
    penetration_levels.csv               IBR penetration levels
    sg_dynamic_parameters.csv            SG dynamic parameters
    ibr_capability.csv                   IBR derating envelope
    contingencies.csv                    contingency family
ieee300/
    pglib_opf_case300_ieee.m             network
    synchronous_generators.csv           synchronous generators
    inverter_based_resources.csv         IBRs
    penetration_levels.csv               IBR penetration levels
    sg_dynamic_parameters.csv            SG dynamic parameters
    contingencies.csv                    contingency family
storage_study/
    inverter_based_resources_51pct.csv   IBRs online at 51 % penetration
    storage.yaml                         storage stations
    contingencies.csv                    contingency family
    profiles/                            load, wind, and solar of the twelve days
```

## Supplementary Material

`supplementary.pdf` details the nodal post-contingency model and the
training settings of the partially input convex neural network (PICNN).

## Networks

`pglib_opf_case118_ieee.m` and `pglib_opf_case300_ieee.m` are the IEEE
118-bus and 300-bus cases of PGLib-OPF v23.07 in MATPOWER format, with a
100 MVA base. Their bus, branch, load, and shunt data are used unchanged.
The generator fleet is given by the CSV files below, which supersede the
generator records of the `.m` files. All bus numbers refer to the `.m`
files.

## Synchronous generators

`synchronous_generators.csv`, one row per unit:

| Column | Meaning |
|---|---|
| `gen_id`, `bus` | identifier and bus |
| `p_max`, `p_min` | active-power limits (MW) |
| `q_max`, `q_min` | reactive-power limits (Mvar) |
| `r_gov_max` | governor reserve limit (MW) |
| `energy_offer` | energy offer ($/MWh) |
| `reserve_offer` | reserve offer ($/MWh) |

## Inverter-based resources

`inverter_based_resources.csv`, one row per unit:

| Column | Meaning |
|---|---|
| `ibr_id`, `bus` | identifier and bus |
| `type` | `GFM` or `GFL` |
| `S_rated_MVA` | apparent-power rating (MVA) |
| `p_max`, `p_min` | active-power limits (MW) |
| `q_max`, `q_min` | reactive-power limits (Mvar) |
| `energy_offer` | energy offer ($/MWh) |
| `reserve_offer` | reserve offer ($/MWh) |
| `penetration_level` | lowest penetration level (%) at which the unit is online |
| `replaces_sg` | synchronous generator that the unit replaces |

The IBRs at one bus form one plant.

## Penetration levels

The IBR penetration is the share of the IBRs in the online nameplate
active-power capacity. `penetration_levels.csv` lists the levels
(`penetration_level_pct`), their values to one decimal (`actual_pct`), and the
installed IBR capacity (`cumulative_ibr_mw`):

- IEEE 118: 34, 70, and 97 %
- IEEE 300: 31, 50, and 75 %

At a given level, the online fleet consists of every IBR whose
`penetration_level` does not exceed that level and every synchronous
generator except those named in the `replaces_sg` column of these IBRs.

## Synchronous-generator dynamic parameters

`sg_dynamic_parameters.csv` gives, for each synchronous generator, the
rating `Sn_MVA` and, on this rating, the inertia constant `H_s` (s), the
transient reactance `X_d_prime` (p.u.), the governor droop `gov_R` (p.u.),
and the time constants `gov_T1`, `gov_T2`, and `gov_T3` (s) of the TGOV1
governor-turbine model. Table SI of the Supplementary Material lists the
other parameters of the nodal post-contingency model.

## Contingency family

`contingencies.csv` lists the contingency family of each penetration level,
one contingency per row: `penetration_level`, `contingency_id`, `bus`, and
`tripped_units`, the units that trip together, separated by semicolons.

## IBR derating envelope

On segment `seg_id` of `ibr_capability.csv`, for a squared bus voltage `W`
(p.u.) between `w_low` and `w_high`, the derating factor `t` of an IBR
satisfies `t <= a_coeff * W + b_coeff`, with `0.05 <= t <= 1`.

## Storage study

`storage_study/` holds the inputs of the twelve-day study on the IEEE
118-bus system:

- `inverter_based_resources_51pct.csv` lists the seven IBRs online at 51 %
  penetration (3,334 MW), with the columns above except
  `penetration_level`. The online synchronous generators are all those not
  named in its `replaces_sg` column.
- `storage.yaml` defines the two storage stations at buses 26 and 80.
- `contingencies.csv` lists the contingency family of the study, with the
  columns above except `penetration_level`.
- `profiles/` holds the twelve days in the order of the study
  (`day01`–`day12`). Each file gives, at five-minute resolution, the
  day-ahead forecasts (`*_forecast_dam_mw`) and actual values
  (`*_actual_mw`) of load, wind, and solar in MW, scaled to the IEEE
  118-bus system, with timestamps in GMT (`ts_gmt`).

## Units

Active power is in MW, reactive power in Mvar, apparent power in MVA,
offers in $/MWh, and voltages in p.u. of the bus base voltage. The system
base is 100 MVA.

## License

The data are released under the Creative Commons Attribution 4.0
International license (`LICENSE`). The networks are from PGLib-OPF
(https://github.com/power-grid-lib/pglib-opf), also released under CC BY
4.0, and the profiles are derived from public data of the California ISO.
