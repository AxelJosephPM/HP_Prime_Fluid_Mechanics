# MOM Elbow Algorithm Fix Report

## Old Issue

`MF_MOM_BEND()` was reporting the force of the fluid on the elbow as the main result. The solved Tema 4 exercises compare the support or anchor reaction on the physical elbow assembly, so the signs and the role of total weight did not match the class result.

The previous Bernoulli path also solved the opposite pressure direction for the reducer exercise. Tema 4 problem 5 needs `P1g` from atmospheric outlet pressure, elevation change, and velocity change.

## Correct Class Convention

- `+x` is positive to the right.
- `+y` is positive upward.
- `theta1` and `theta2` are measured from `+x`.
- Pressure forces use gauge pressures.
- Main output is `Rx,Ry = soporte/anclaje sobre codo`.

## Correct Formulas

- `rad1 = theta1*PI/180`
- `rad2 = theta2*PI/180`
- `V1x = V1*COS(rad1)`
- `V1y = V1*SIN(rad1)`
- `V2x = V2*COS(rad2)`
- `V2y = V2*SIN(rad2)`
- `Mx = mdot*(V2x - V1x)`
- `My = mdot*(V2y - V1y)`
- `Fpx = P1g*A1*COS(rad1) - P2g*A2*COS(rad2)`
- `Fpy = P1g*A1*SIN(rad1) - P2g*A2*SIN(rad2)`
- `Wx = 0`
- `Wy = -W`
- `Rx = Mx - Fpx - Wx`
- `Ry = My - Fpy - Wy`
- `R = SQRT(Rx^2 + Ry^2)`

For atmospheric outlet with Bernoulli and no losses:

- `P2g = 0`
- `P1g = P2g + rho*g*dz + 0.5*rho*(V2^2 - V1^2)`

## Tema 4 Problem 5 Hand Calculation

Given:

- `mdot = 30 kg/s`
- `rho = 1000 kg/m3`
- `Q = 0.03 m3/s`
- `A1 = 0.0150 m2`
- `A2 = 0.0025 m2`
- `theta1 = 0 deg`
- `theta2 = 110 deg`
- `dz = z2-z1 = 0.40 m`
- `P2g = 0 Pa`
- `mtotal = 50 kg`
- `g = 9.81 m/s2`

Computed:

- `V1 = 2 m/s`
- `V2 = 12 m/s`
- `P1g = 73924 Pa`
- `Fpx = 1108.9 N`
- `Fpy = 0 N`
- `Mx = -183.1 N`
- `My = 338.3 N`
- `W = 490.5 N`
- `Rx = -1292.0 N`
- `Ry = 828.8 N`
- `R = 1535 N`

This is within about 2 percent of the solved Tema 4 values `Fx about -1297 N` and `Fy about 0.834 kN`.

## Calculator Expected Output

For the Tema 4 validation path:

`Reaccion en codo 2D -> Areas + P1 Bern -> mdot masico -> Masa total`

Compare the `REACCION ANCLAJE` screen:

- `Rx` about `-1292 N`
- `Ry` about `829 N`
- `R` about `1535 N`

## Files Changed

- `MF_MOM.hpprgm`
- `TEST_PLAN.md`
- `README.md`
- `STRUCTURE.md`
- `MOM_ELBOW_CURRENT_REPORT.md`
- `MOM_ELBOW_ALGORITHM_FIX_REPORT.md`

`MF_CAS.hpprgm` was not touched.
