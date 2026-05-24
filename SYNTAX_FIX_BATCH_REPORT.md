# Syntax Fix Batch Report

## 1. FLUIDOS line 20

`FLUIDOS.hpprgm` line 20 calls `MF_DIFF()`. The menu syntax and option counts are valid: page 1 uses 9 options plus title, and page 2 uses 8 options plus title. The reported error was a cascade from `MF_DIFF.hpprgm`, which failed to parse at its first oversized `LOCAL` block. No `FLUIDOS.hpprgm` edit was required.

## 2. MF_HYDRO line 102

`MF_HYDRO_RECT()` declared 10 variables in one `LOCAL` statement. HP Prime G2 has previously been confirmed to fail above 8 variables per `LOCAL`. Split it into two `LOCAL` statements.

## 3. MF_BERN line 14

`MF_BERN_BAL()` declared 14 variables in one `LOCAL` statement. Split it into two `LOCAL` statements. The same file had additional oversized `LOCAL` declarations in later Bernoulli helpers, so those were split as well to avoid the next import failures.

## 4. MF_PIPES line 98

`MF_PIPES_TRAMO()` declared 9 variables in one `LOCAL` statement. Split it into two `LOCAL` statements. Additional oversized `LOCAL` statements in later pipe helpers were also split. `Re1` and `Re2` were renamed to `reyn1` and `reyn2` to avoid the known `RE()` built-in ambiguity.

## 5. MF_PUMPS line 108

`MF_PUMPS_NPSHA()` declared 10 variables in one `LOCAL` statement. Split it into two `LOCAL` statements.

## 6. MF_DIFF line 30

`MF_DIFF_TAU()` declared 9 variables in one `LOCAL` statement. Split it into two `LOCAL` statements. `MF_DIFF_PUIS()` also had a 9-variable `LOCAL`; it was split and `Re` was renamed to `reyn` to avoid the known `RE()` built-in ambiguity. The same `Re` rename was applied in `MF_DIFF_PUIS2()`.

## 7. Exact Files Changed

- `MF_HYDRO.hpprgm`
- `MF_BERN.hpprgm`
- `MF_PIPES.hpprgm`
- `MF_PUMPS.hpprgm`
- `MF_DIFF.hpprgm`
- `SYNTAX_FIX_BATCH_REPORT.md`

## 8. Functionality Temporarily Simplified

None. No block was replaced with a placeholder.

## 9. MF_CAS

`MF_CAS.hpprgm` was not touched.

## 10. First HP Connectivity Kit Tests

1. Import all `.hpprgm` files.
2. Run `FLUIDOS()`.
3. Open `Mas modulos...` and select `Diferencial` first, because `FLUIDOS` line 20 routes there.
4. Test `MF_HYDRO()` option 5, `MF_BERN()` option 1, `MF_PIPES()` option 2, `MF_PUMPS()` option 3, and `MF_DIFF()` option 1.
