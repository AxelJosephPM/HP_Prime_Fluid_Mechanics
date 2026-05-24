# Momentum Elbow Debug Report

## 1. Solver Location

The 2D elbow solver is in `MF_MOM.hpprgm`, function `MF_MOM_BEND()`. It is reached from the `MF_MOM()` menu option `2. Reaccion en codo 2D`.

## 2. Current Mx and My Formula

Before this fix, the code used:

- `Mx = mdot*(V2x - V1x)`
- `My = mdot*(V2y - V1y)`

This is the correct right-hand side of the steady linear momentum equation for one inlet and one outlet.

## 3. Angle Handling

The user entered angles in degrees, and the code converted them with:

- `rad1 = th1*PI/180`
- `rad2 = th2*PI/180`

However, `COS(rad1)` and `SIN(rad1)` can still depend on the HP Prime Home angle setting. If the calculator is in degree mode, a radian value passed to `COS` can be interpreted as degrees, which changes `V2x`, `V2y`, `Mx`, and `My`.

## 4. mdot or rho*Q

The solver supports both:

- If `Q` is entered, `mdot = rho*Q`.
- If `mdot` is entered, `Q = mdot/rho`.

The momentum terms use `mdot` directly.

## 5. Density Double Application

Density was not being applied twice in the momentum formula. The possible mismatch was not `rho*mdot`; it was angle-mode-dependent trigonometry.

## 6. Pressure Forces

The current pressure force formulas are:

- `Fpx = P1g*A1*COS(rad1) - P2g*A2*COS(rad2)`
- `Fpy = P1g*A1*SIN(rad1) - P2g*A2*SIN(rad2)`

The formulas are correct if `P1g`, `P2g`, and the trigonometric values are correct. Absolute pressures are converted to gauge pressures by subtracting `Patm`.

## 7. Weight Formula

Before this fix, the class-style weight input supported:

- ignore weight
- total mass of elbow plus fluid
- total weight of elbow plus fluid

The code used `W` in `Fanch_y = My - Fpy + W`.

## 8. Reaction Formula

Before this fix, the main class-style result was:

- `Fanchx = Mx - Fpx`
- `Fanchy = My - Fpy + W`
- `Fanch = SQRT(Fanchx^2 + Fanchy^2)`

This matches the Tema 4 support/anchor convention, but the output did not show every term requested as `Wx`, `Wy`, `Rx`, `Ry`, resultant, and angle.

## 9. Why It Did Not Match Tema 4

For the Tema 4 reducer, the theoretical momentum terms require:

- `V2x = 12*cos(110 deg) about -4.104`
- `V2y = 12*sin(110 deg) about 11.276`
- `Mx about -183.1 N`
- `My about 338.3 N`

If the HP Prime angle mode caused `COS(rad2)` to interpret `rad2` as degrees, then `V2x` and `V2y` would be close to `12*cos(1.919 deg)` and `12*sin(1.919 deg)`, producing the wrong momentum terms and final reaction.

## 10. What Was Changed

The fix makes the elbow trigonometry independent of the calculator angle mode by saving `HAngle`, temporarily setting radian mode for `SIN`, `COS`, and `ATAN`, then restoring the previous angle mode.

The output now displays:

- `Mx`, `My`
- `Fpx`, `Fpy`
- `Wx`, `Wy`
- `Rx`, `Ry`
- `R`
- approximate angle
- opposite reaction

The weight menu also adds an option for `VolCV` plus elbow mass.
