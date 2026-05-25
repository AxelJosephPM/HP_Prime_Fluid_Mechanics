# MF_PIPES line 451 diagnosis

## 1. Exact text of line 451

Before the fix, line 451 was:

`LOCAL g, A, V, reyn, hMajor, hMinor, hTotal, dP, Ploss;`

After the fix, line 451 is:

`LOCAL g, A, V, reyn, hMajor;`

## 2. Lines 420 to 470

Before the fix, lines 420 to 470 closed the working Darcy factor helper and then started the first new pipe-loss helper:

- Lines 420-434: Colebrook iteration loop inside `MF_PIPES_FD()`.
- Lines 437-445: Darcy factor output `MSGBOX` screens.
- Line 446: `END;` closing `MF_PIPES_FD()`.
- Line 448: `MF_PL_FG()`.
- Line 449: `BEGIN`.
- Lines 450-451: `LOCAL` declarations.
- Lines 453-470: first small `INPUT` calls for the given-f pipe-loss option.

## 3. Whether line 451 itself is invalid

The formula variables on line 451 were valid names, but the parser was failing at this new helper boundary. To reduce parser risk, the declaration was split into shorter `LOCAL` lines.

## 4. Nested EXPORT check

No `EXPORT` was nested inside another `EXPORT`. The previous helper closed with `END;` before the pipe-loss helpers.

## 5. Missing END before line 451

No missing `END;` was found before line 451. The Darcy friction helper closed correctly at line 446.

## 6. LOCAL after executable statements

No `LOCAL` declaration appeared after executable statements in the failing helper. The `LOCAL` declarations were immediately after `BEGIN`.

## 7. Block closure check

The nearby `IF`, `WHILE`, `CHOOSE`, and `INPUT` blocks were closed correctly. No `REPEAT` block was present in this area.

## 8. MSGBOX/string check

No malformed `MSGBOX` string or bad concatenation was found near line 451.

## 9. Pipe losses or Darcy friction

The issue belongs to the newly added pipe-loss helper block. The Darcy friction factor helper above it was already working and was not changed.

## 10. Exact fix applied

- Made the new pipe-loss helpers explicit top-level `EXPORT` blocks:
  - `EXPORT MF_PL_FG()`
  - `EXPORT MF_PL_COLE()`
  - `EXPORT MF_PL_MIN()`
  - `EXPORT MF_PIPES_LOSSES()`
- Split longer `LOCAL` lists in the new helpers into shorter consecutive `LOCAL` declarations.
- Left the Darcy friction formulas and pipe-loss formulas unchanged.
