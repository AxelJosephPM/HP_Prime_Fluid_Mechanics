# Persistent MF_PIPES line 419 fix

## 1. Exact cause

The persistent syntax error was caused by the large Darcy friction block added to `MF_PIPES_FD()`. After the previous rename from `Re` to `reyn`, the parser still stopped at the first LOCAL line of that helper, which means the previous diagnosis was incomplete.

The safest conclusion is that the new block was too fragile for HP Prime import because it combined a large menu, many LOCAL variables, repeated Colebrook loops, long INPUT calls, long MSGBOX concatenations, and percentage comparison output.

## 2. Was line 419 the real error?

Line 419 was where parsing failed, not necessarily the exact bad token. The structure above it was balanced: `MF_PIPES_KVAL()` closed with `END;`, and no unclosed IF, WHILE, CHOOSE, or INPUT block was found before the Darcy helper.

## 3. Controlled reduction

Yes. The Darcy option was reduced to a compact syntax-safe version instead of continuing to patch individual tokens.

## 4. Darcy functionality status

The Darcy factor calculator remains active, but temporarily simplified. It currently supports:

- input from `Re` and `rr`
- laminar `f = 64/Re`
- Haaland
- Swamee-Jain
- Colebrook fixed-point iteration

The broader submenu modes from the previous upgrade were removed for now to make the file import safely.

## 5. LOG or LOG10

The module uses `LOG`, not `LOG10`. No `LOG10` calls are present.

## 6. Files modified

- `MF_PIPES.hpprgm`
- `PIPES_LINE_419_DEEP_DIAGNOSIS.md`
- `PIPES_LINE_419_FIX_REPORT.md`

## 7. MF_CAS status

`MF_CAS.hpprgm` was not touched.
