# MF_PIPES line 466 fix report

## 1. Exact cause

The line 466 syntax report was caused by the new pipe-loss helper block, specifically the shared output helper inserted immediately before the first calculation helper:

`MF_PIPES_LOSS_OUT(A, V, reyn, hMajor, hMinor, hTotal, dP, Ploss)`

That long helper name and eight-argument signature made the parser fail when it reached the next helper's LOCAL declarations.

## 2. Was line 466 the real problem?

Line 466 was where parsing failed, not the real bad line. The LOCAL declaration at line 466 was syntactically normal.

## 3. What changed

- Removed `MF_PIPES_LOSS_OUT(...)`.
- Inlined the output MSGBOX screens in the given-f and Colebrook loss helpers.
- Renamed new helper functions to shorter names:
  - `MF_PL_FG()`
  - `MF_PL_COLE()`
  - `MF_PL_MIN()`
- Updated calls from the `Perdidas tuberia` menu.

## 4. Pipe losses status

The pipe losses calculator remains active. It still supports:

- `Con f dado`
- `Calcular f Colebrook`
- `Solo perdidas menores`
- `Ayuda perdidas`

## 5. Darcy factor status

The existing Darcy friction factor calculator was not changed and still uses Haaland, Swamee-Jain, and iterative Colebrook.

## 6. LOG or LOG10

The module uses `LOG`, not `LOG10`.

## 7. Files modified

- `MF_PIPES.hpprgm`
- `STRUCTURE.md`
- `PIPES_LINE_466_DIAGNOSIS.md`
- `PIPES_LINE_466_FIX_REPORT.md`

## 8. MF_CAS status

`MF_CAS.hpprgm` was not touched.
