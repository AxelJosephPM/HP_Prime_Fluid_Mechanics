# SYNTAX_RECOVERY_LINE43_REPORT.md
## FLUIDOS PRIME v1.0 - HP Prime G2 Syntax Recovery (Line 43 Error)

### 1. Error Reported
HP Prime Connectivity Kit reported: **PROGRAM LINE 43**
After the previous fix (Line 21), the CK failed again at line 43 of FLUIDOS.hpprgm.

### 2. Root Cause
The Line 21 fix had rewritten FLUIDOS.hpprgm using an `IFERR...THEN...END` block containing
11 bare `IF...THEN...END` statements as the guarded body. HP Prime PPL requires the body of
`IFERR` to be a single statement or a `BEGIN...END` block. Multiple bare IF statements inside
`IFERR` are not valid. The 7th IF statement (calling MF_PUMPS) landed on the stripped line
counted by the CK as line 43, triggering the error.

Secondary: The non-exported `dispatch_(c)` helper in the original FLUIDOS.hpprgm used a
parameter named `c`, which shadowed the `LOCAL c` in `EXPORT FLUIDOS()`. This ambiguity
was exploited by the CK's parser. (Fixed in prior session.)

### 3. Recovery Strategy
1. Created full backup of all 11 .hpprgm files before any changes:
   - Folder: `backups_before_line43_fix/`
2. Rewrote FLUIDOS.hpprgm using `REPEAT...UNTIL` (no IFERR, no helper functions):
   - 11 CHOOSE options (op=1..11), exit on op==11 or op==0
   - Direct module calls via plain `IF op == N THEN MODULE(); END`
3. Replaced all 10 module files with minimal valid placeholders (safe baseline).
4. Restored 9 modules from backup one by one after confirming each was clean:
   MF_PROPS, MF_MANO, MF_HYDRO, MF_BUOY, MF_BERN, MF_PIPES, MF_PUMPS, MF_COMP, MF_UTILS
5. MF_CAS kept as placeholder (CAS safety rule - string-concatenation CAS calls are
   risky at compile time on some G2 firmware versions).
6. Added `EXPORT MF_UTILS()` to MF_UTILS.hpprgm (combined utilities menu) because
   FLUIDOS now calls MF_UTILS() but the backup only exported MF_UNITS() and MF_HELP().

### 4. HP PPL Rules Applied
- No Unicode characters anywhere (including comments). All files are 7-bit ASCII.
- No IFERR in FLUIDOS.hpprgm.
- No helper functions with parameter names that shadow LOCAL variables in the caller.
- REPEAT...UNTIL preferred over WHILE...DO for simple menu loops.
- CHOOSE option count: 11 items (well within safe limits).
- All inline `//` comments kept on single lines only.
- No markdown, no LaTeX, no special characters in string literals.

### 5. Module Status After Recovery

| Module         | File                | Status      | Notes                                     |
|---------------|---------------------|-------------|-------------------------------------------|
| FLUIDOS        | FLUIDOS.hpprgm      | REWRITTEN   | REPEAT/UNTIL, 11 options, no IFERR        |
| Propiedades    | MF_PROPS.hpprgm     | RESTORED    | From backup; no IFERR; ASCII clean        |
| Manometros     | MF_MANO.hpprgm      | RESTORED    | From backup; no IFERR; ASCII clean        |
| Hidrostatica   | MF_HYDRO.hpprgm     | RESTORED    | From backup; no IFERR; ASCII clean        |
| Flotabilidad   | MF_BUOY.hpprgm      | RESTORED    | From backup; no IFERR; ASCII clean        |
| Bernoulli      | MF_BERN.hpprgm      | RESTORED    | Unicode fix (U+2248 -> ~=) applied        |
| Tuberias       | MF_PIPES.hpprgm     | RESTORED    | From backup; no IFERR; ASCII clean        |
| Bombas         | MF_PUMPS.hpprgm     | RESTORED    | From backup; no IFERR; ASCII clean        |
| Compresible    | MF_COMP.hpprgm      | RESTORED    | From backup; no IFERR; ASCII clean        |
| CAS            | MF_CAS.hpprgm       | PLACEHOLDER | CAS safety rule: string-concat CAS risky  |
| Utilidades     | MF_UTILS.hpprgm     | RESTORED+   | Restored + new EXPORT MF_UTILS() added    |

### 6. ASCII Validation
PowerShell byte scan of all 11 .hpprgm files: **0 non-ASCII bytes found in any file.**

### 7. EXPORT Cross-Reference

| FLUIDOS calls | EXPORT present in    |
|---------------|----------------------|
| MF_PROPS()    | MF_PROPS.hpprgm      |
| MF_MANO()     | MF_MANO.hpprgm       |
| MF_HYDRO()    | MF_HYDRO.hpprgm      |
| MF_BUOY()     | MF_BUOY.hpprgm       |
| MF_BERN()     | MF_BERN.hpprgm       |
| MF_PIPES()    | MF_PIPES.hpprgm      |
| MF_PUMPS()    | MF_PUMPS.hpprgm      |
| MF_COMP()     | MF_COMP.hpprgm       |
| MF_CAS()      | MF_CAS.hpprgm        |
| MF_UTILS()    | MF_UTILS.hpprgm      |

### 8. Expected CK Import Behavior
All files should now import without syntax errors. The CK should compile all 11 programs
and place them in the HP Prime program catalog. Running FLUIDOS() from the catalog should
display the main menu. MF_CAS() will show a "pending validation" message.

### 9. What to Test in Connectivity Kit
1. Import all 11 .hpprgm files into CK. Confirm: no "PROGRAM LINE N" error.
2. Send to calculator.
3. Run FLUIDOS() from Program catalog.
4. Navigate each menu option (1-10) and verify the sub-menu appears.
5. Option 9 (CAS) should show the placeholder MSGBOX.
6. Option 11 (Salir) or pressing Cancel should exit cleanly.
