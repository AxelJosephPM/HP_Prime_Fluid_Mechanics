# Pipe losses upgrade report

## 1. Files modified

- `MF_PIPES.hpprgm`
- `README.md`
- `TEST_PLAN.md`
- `STRUCTURE.md`
- `PIPE_LOSSES_UPGRADE_REPORT.md`

## 2. New menu options added

`MF_PIPES` now includes:

- `Perdidas tuberia`
  - `Con f dado`
  - `Calcular f Colebrook`
  - `Solo perdidas menores`
  - `Ayuda perdidas`
  - `Volver`

## 3. Formulas implemented

- `A = PI*D^2/4`
- `V = Q/A`
- `Re = rho*V*D/mu`
- `rr = eps/D`
- `hMajor = f*(L/D)*V^2/(2*g)`
- `hMinor = Ksum*V^2/(2*g)`
- `hTotal = hMajor+hMinor`
- `dP = rho*g*hTotal`
- `Ploss = dP*Q`
- `g = 9.81`

## 4. How f is selected

- `Con f dado`: user enters Darcy friction factor directly.
- `Calcular f Colebrook`: the tool computes `Re` and `rr`.
  - If there is no flow, `f = 0`.
  - If `Re < 2300`, `f = 64/Re`.
  - If `Re >= 2300`, Haaland is used as the starting value and Colebrook fixed-point iteration gives the final Darcy factor.

## 5. Major and minor losses

Major losses use Darcy-Weisbach with the Darcy friction factor. Minor losses use the summed coefficient `Ksum`. Total head loss is the sum of both.

## 6. Test cases

Added tests for:

- given `f = 0.02`
- automatic Colebrook factor from `rho`, `mu`, `Q`, `D`, `L`, `eps`
- minor losses only

Expected values are recorded in `TEST_PLAN.md`.

## 7. Known limitations

- The new loss calculator is intentionally conservative and uses short input screens.
- Colebrook mode ignores pump/turbine terms and computes only pipe losses.
- Transition results are orientative.

## 8. MF_CAS status

`MF_CAS.hpprgm` was not edited.
