# MF_PIPES line 451 fix report

## 1. Exact line 451 content

Before the fix:

`LOCAL g, A, V, reyn, hMajor, hMinor, hTotal, dP, Ploss;`

After the fix:

`LOCAL g, A, V, reyn, hMajor;`

The remaining variables were moved to the next `LOCAL` line.

## 2. Real cause

The persistent parser error was structural in the new pipe-loss helper area. The file had just entered the first new helper after the Darcy friction block, and the Connectivity Kit reported the first `LOCAL` list inside that helper.

The safest correction was to make the new pipe-loss helpers explicit top-level `EXPORT` functions and shorten the local declaration lists. This avoids relying on a fragile helper boundary in the newly added block.

## 3. Was the parser line accurate?

The parser line was partly accurate: it pointed at the first fragile line inside the new helper, but the underlying problem was the helper structure around the pipe-loss additions, not the Darcy friction algorithm.

## 4. What changed

- Added `EXPORT` to the new pipe-loss helper functions.
- Split long `LOCAL` declaration lists in the new helpers.
- Did not change the head-loss formulas.
- Did not change the Darcy friction factor calculator formulas.

## 5. Pipe losses status

The pipe losses calculator remains active. It still supports:

- `Con f dado`
- `Calcular f Colebrook`
- `Solo perdidas menores`
- `Ayuda perdidas`

## 6. Darcy friction status

The Darcy friction factor calculator remains active. Haaland, Swamee-Jain, and iterative Colebrook remain in place.

## 7. LOG or LOG10

`LOG` is used. `LOG10` is not used in `MF_PIPES.hpprgm`.

## 8. Files modified

- `MF_PIPES.hpprgm`
- `PIPES_LINE_451_DIAGNOSIS.md`
- `PIPES_LINE_451_FIX_REPORT.md`

## 9. MF_CAS status

`MF_CAS.hpprgm` was not touched.
