# FLUIDOS PRIME v1.0 — Plan de Pruebas

Casos de prueba con valores conocidos para verificar la implementación.

---

## TEST 1: Número de Reynolds

**Menú:** Propiedades → Número de Reynolds → Usar ρ y μ

**Entrada:**
- V = 2.0 m/s
- D = 0.05 m
- ρ = 1000 kg/m³ (agua a 20 °C)
- μ = 0.001 Pa·s

**Esperado:**
```
Re = ρVD/μ = 1000 × 2.0 × 0.05 / 0.001 = 100,000
ν  = 1.0 × 10⁻⁶ m²/s
Régimen: TURBULENTO (Re > 4000)
```

**Verificación:** Re = 1.0 × 10⁵ ✓

---

## TEST 2: Manómetro diferencial

**Menú:** Manómetros → Manómetro Diferencial

**Entrada:**
- ρ_m = 13600 kg/m³ (mercurio)
- ρ_f = 1000 kg/m³ (agua)
- g = 9.81 m/s²
- h = 0.10 m
- P₁_man = 200,000 Pa

**Esperado:**
```
ΔP = g·h·(ρ_m − ρ_f) = 9.81 × 0.10 × 12600 = 12,360.6 Pa ≈ 12.36 kPa
P₂ = P₁ − ΔP = 200,000 − 12,360.6 = 187,639.4 Pa
```

---

## TEST 3: Ecuación de Bernoulli — Hallar P₂

**Menú:** Bernoulli → Hallar P₂

**Entrada (tubería horizontal, sin pérdidas ni máquinas):**
- P₁ = 200,000 Pa
- V₁ = 1.0 m/s
- z₁ = 0.0 m
- V₂ = 2.0 m/s
- z₂ = 0.0 m
- h_L = 0.0 m
- h_bomba = 0.0 m
- h_turb = 0.0 m
- ρ = 1000 kg/m³
- g = 9.81 m/s²

**Esperado (Bernoulli ideal):**
```
P₂ = P₁ + ½ρ(V₁² − V₂²) = 200,000 + 500×(1−4) = 198,500 Pa
```
El programa devuelve P₂ = 198,500 Pa ✓

---

## TEST 4: Darcy-Weisbach y factor de fricción

**Menú:** Tuberías → Cálculo Completo de Tubería

**Entrada:**
- D = 0.1 m
- Q = 0.01 m³/s
- ρ = 1000 kg/m³
- μ = 0.001 Pa·s
- ε = 0.000046 m (acero comercial)
- L = 100 m
- K_total = 2.0
- g = 9.81 m/s²

**Cálculos paso a paso:**
```
A = π(0.1)²/4 = 7.854×10⁻³ m²
V = 0.01 / 7.854×10⁻³ = 1.2732 m/s
Re = 1000 × 1.2732 × 0.1 / 0.001 = 127,320  → turbulento
ε/D = 0.000046/0.1 = 0.00046
arg = 0.00046/3.7 + 5.74/127320^0.9
    = 0.0001243 + 5.74/32742 ≈ 0.0001243 + 0.0001753 = 0.0002996
f = 0.25 / (log10(0.0002996))² = 0.25 / (-3.5235)² = 0.25/12.415 ≈ 0.02013

h_f = 0.02013 × (100/0.1) × (1.2732²)/(2×9.81)
    = 0.02013 × 1000 × 0.08265 = 16.63 m

h_m = 2.0 × (1.2732²)/(2×9.81) = 2.0 × 0.08265 = 0.1653 m

h_L = 16.63 + 0.165 = 16.80 m
ΔP = 1000 × 9.81 × 16.80 = 164,808 Pa ≈ 164.8 kPa
```

**Esperado en pantalla:**
```
V    ≈ 1.273 m/s
Re   ≈ 127,300
f    ≈ 0.0201
h_f  ≈ 16.63 m
h_m  ≈ 0.165 m
h_L  ≈ 16.80 m
ΔP   ≈ 164.8 kPa
```

---

## TEST 5: Relaciones isentrópicas (flujo compresible)

**Menú:** Flujo Compresible → Relaciones Isentrópicas

**Entrada:**
- M = 2.0
- k = 1.4
- R = 287 J/(kg·K)
- T = 250 K
- P = 50,000 Pa

**Esperado:**
```
T₀/T = 1 + (1.4-1)/2 × 4 = 1 + 0.4 = 1.8    → T₀ = 450 K
P₀/P = 1.8^(1.4/0.4) = 1.8^3.5 = 7.824       → P₀ = 391,200 Pa
a = √(1.4 × 287 × 250) = √(100,450) = 317.0 m/s
V = 2.0 × 317.0 = 634.0 m/s
```

---

## TEST 6: Onda de choque normal

**Menú:** Flujo Compresible → Onda de Choque Normal

**Entrada:**
- M₁ = 2.0
- k = 1.4

**Esperado:**
```
M₂² = (1 + 0.2×4)/(1.4×4 − 0.2) = 1.8/5.4 = 0.3333
M₂  = 0.5774

P₂/P₁   = 1 + 2×1.4/2.4 × (4−1) = 1 + 3.5 = 4.5
ρ₂/ρ₁   = 2.4×4/(0.4×4 + 2) = 9.6/3.6 = 2.667
T₂/T₁   = 4.5/2.667 = 1.688
```

---

## TEST 7: Relación Área-Mach (A/A* = 2.0, rama subsónica)

**Menú:** Flujo Compresible → Relación Área-Mach → M dado A/A* (subsónico)

**Entrada:**
- A/A* = 2.0
- k = 1.4

**Esperado:**
M (subsónico) ≈ 0.3059

**Verificación:**
```
A/A*(0.3059, 1.4) = (1/0.3059) × [(2/2.4)×(1 + 0.2×0.0936)]^3
               ≈ 2.000 ✓
```

---

## TEST 8: Bomba — NPSHa vs NPSHr

**Menú:** Bombas → Evaluación de Cavitación

**Entrada:**
- NPSHa = 8.5 m
- NPSHr = 3.0 m

**Esperado:**
```
Margen = 8.5 − 3.0 = 5.5 m
Estado: SIN RIESGO DE CAVITACIÓN
```

**Variante crítica:**
- NPSHa = 2.5 m
- NPSHr = 3.0 m

```
Margen = −0.5 m
Estado: RIESGO DE CAVITACIÓN (NPSHa ≤ NPSHr)
```

---

## TEST 9: ESC / Volver / Salir (prueba de navegación)

**Procedimiento:**
1. Ejecutar `FLUIDOS()`.
2. Seleccionar cualquier módulo (ej. opción 1 Propiedades).
3. Dentro del módulo, seleccionar una función.
4. En la pantalla INPUT, presionar ESC.
   - **Esperado**: Regresa al menú del módulo sin calcular.
5. En el menú del módulo, seleccionar "0. Volver".
   - **Esperado**: Regresa al menú principal.
6. En el menú principal, seleccionar "0. SALIR".
   - **Esperado**: Aparece diálogo de confirmación.
   - Seleccionar "No, volver al menú".
   - **Esperado**: Permanece en el menú principal.
7. Seleccionar "0. SALIR" y confirmar con "Sí, salir".
   - **Esperado**: El programa termina.

**Variante ESC en menú principal:**
1. En el menú principal, presionar ESC.
   - **Esperado**: Aparece diálogo de confirmación de salida (no sale inmediatamente).

---

## TEST 10: Entrada inválida

**Procedimiento:**
1. Ir a Propiedades → Reynolds.
2. En el campo V (m/s), ingresar −1.0 (negativo).
3. Presionar OK.

**Esperado:**
```
Mensaje de error: "Error: V debe ser mayor que cero."
Regresa al menú del módulo.
```

**Variante — rho negativa:**
Ingresar ρ = −1000 → Mensaje: "Error: rho debe ser positiva."

---

## TEST 11: Flotabilidad

**Menú:** Flotabilidad → Análisis de Flotabilidad

**Entrada:**
- ρ_fluido = 1000 kg/m³
- ρ_cuerpo = 800 kg/m³
- g = 9.81 m/s²
- V_cuerpo = 0.1 m³

**Esperado:**
```
W    = 800 × 9.81 × 0.1 = 784.8 N
E    = 1000 × 9.81 × 0.1 = 981 N
W_ap = 784.8 − 981 = −196.2 N (peso aparente negativo → flota)
Estado: FLOTA (ρ_b < ρ_f)
```

**Fracción sumergida:**
V_sub/V = 800/1000 = 0.8 → 80% sumergido, 20% fuera

---

---

## TEST 12: Continuidad 1-1 — Calcular V2

**Menú:** Continuidad → Continuidad 1 entrada-1 salida → Calcular V2

**Entrada:**
- A1 = 0.02 m² (tubería mayor)
- V1 = 1.5 m/s
- A2 = 0.005 m² (tubería menor)

**Cálculo:**
```
Q = A1*V1 = 0.02 × 1.5 = 0.030 m3/s
V2 = Q/A2 = 0.030 / 0.005 = 6.0 m/s
```

**Esperado en pantalla:**
```
V2 = 6.0 m/s
Q  = 0.03 m3/s
```

---

## TEST 13: Caudal volumétrico y conversor

**Menú:** Continuidad → Caudal Q = A*V

**Entrada:**
- A = 0.007854 m² (tubería D = 0.1 m)
- V = 2.0 m/s

**Cálculo:**
```
Q = 0.007854 × 2.0 = 0.015708 m3/s
Q = 15.708 L/s
Q = 56.549 m3/h
```

**Esperado en pantalla:** Q ≈ 0.0157 m3/s, ≈ 15.71 L/s, ≈ 56.55 m3/h

---

## TEST 14: Tasa de cambio de nivel en depósito

**Menú:** Continuidad → Tasa de nivel en deposito

**Entrada:**
- Qin = 0.05 m³/s
- Qout = 0.02 m³/s
- As = 10.0 m²

**Cálculo:**
```
dh/dt = (0.05 - 0.02) / 10.0 = 0.003 m/s = 10.8 m/h
Estado: LLENADO
```

**Entrada crítica (vaciado):**
- Qin = 0.01, Qout = 0.05, As = 10.0

```
dh/dt = (0.01 - 0.05) / 10.0 = -0.004 m/s → VACIANDO
```

---

## TEST 15: Fuerza axial en tobera (MF_MOM_NOZ)

**Menú:** Cant. Movimiento → Tobera/Difusor - Fuerza axial

**Entrada:**
- rho = 1000 kg/m³
- Q = 0.01 m³/s
- A1 = 0.02 m², P1 = 50000 Pa (man.)
- A2 = 0.005 m², P2 = 0 Pa (man.)

**Cálculo:**
```
V1 = 0.01/0.02 = 0.5 m/s
V2 = 0.01/0.005 = 2.0 m/s
mdot = 1000 × 0.01 = 10 kg/s
Rx = 50000×0.02 - 0×0.005 + 10×(0.5 - 2.0)
   = 1000 - 0 - 15 = 985 N
Fanc = -985 N (fuerza de anclaje)
```

**Esperado en pantalla:**
```
V1   = 0.5 m/s
V2   = 2.0 m/s
mdot = 10.0 kg/s
Rx   = 985.0 N
Fanc = -985.0 N
```

---

## TEST 16: Chorro contra placa fija

**Menú:** Cant. Movimiento → Chorro contra placa fija

**Entrada:**
- rho = 1000 kg/m³
- V = 8.0 m/s
- A = 0.01 m²

**Cálculo:**
```
Q = 0.01 × 8.0 = 0.08 m3/s
mdot = 1000 × 0.08 = 80 kg/s
F = mdot × V = 80 × 8.0 = 640 N
```

**Esperado en pantalla:**
```
mdot = 80.0 kg/s
F    = 640.0 N
F    = 0.64 kN
```

---

## TEST 17: Reacción en codo 90° (MF_MOM_BEND)

**Menú:** Cant. Movimiento → Reaccion en codo 2D

**Entrada (tubería uniforme, codo 90°):**
- rho = 1000, Q = 0.02 m³/s
- A1 = 0.01 m², P1 = 30000 Pa, th1 = 0° (entrada en eje x)
- A2 = 0.01 m², P2 = 15000 Pa, th2 = 90° (salida en eje y)

**Cálculo:**
```
V1 = V2 = 0.02/0.01 = 2.0 m/s
mdot = 1000 × 0.02 = 20 kg/s
Rx = 20*(2.0*cos90 - 2.0*cos0) - 30000*0.01*cos0 + 15000*0.01*cos90
   = 20*(0 - 2.0) - 300 + 0 = -40 - 300 = -340 N
Ry = 20*(2.0*sin90 - 2.0*sin0) - 30000*0.01*sin0 + 15000*0.01*sin90
   = 20*(2.0 - 0) - 0 + 150 = 40 + 150 = 190 N
|R| = sqrt(340² + 190²) = sqrt(115600+36100) = sqrt(151700) ≈ 389.5 N
```

**Esperado:** Rx ≈ -340 N, Ry ≈ 190 N, |R| ≈ 389.5 N

---

---

## TEST 18: Velocidad en tuberia (MF_PIPES opcion 1)

**Menu:** Tuberias → V, Re, f

**Entrada:**
- Q = 0.004 m3/s
- D = 0.05 m
- rho = 1000 kg/m3
- mu = 0.001 Pa*s
- eps = 0.000046 m

**Calculo:**
```
A = PI*(0.05)^2/4 = 0.001963 m2
V = 0.004 / 0.001963 = 2.037 m/s  ~  2.04 m/s
Re = 1000 * 2.037 * 0.05 / 0.001 = 101880  -> TURBULENTO
```

**Esperado en pantalla:**
```
V   ~ 2.037 m/s
Re  ~ 101880
Reg = TURBULENTO
f   ~ 0.018x
```

---

## TEST 19: Reynolds esperado ~101000

**Mismo caso TEST 18.**

**Verificacion:** Re = rho*V*D/mu = 1000*2.037*0.05/0.001 = 101850 aprox. 101000 ✓

---

## TEST 20: Potencia de bomba (MF_PUMPS opcion 1)

**Menu:** Bombas → Potencia bomba

**Entrada:**
- rho = 1000 kg/m3
- Q = 0.004 m3/s
- H = 10 m
- eta = 0.8

**Calculo:**
```
Ph = 1000 * 9.81 * 0.004 * 10 = 392.4 W
Pshaft = 392.4 / 0.8 = 490.5 W  ~  490 W
```

**Esperado en pantalla:**
```
Ph     = 392.4 W
Pshaft = 490.5 W  ~  490 W
```

---

## TEST 21: Perdida menor (MF_PIPES opcion 3)

**Menu:** Tuberias → Perdidas K

**Entrada:**
- Q tal que V = 2 m/s (usar Q = V*A con D = 0.1 m → Q = 2*PI*0.01/4 = 0.01571 m3/s)
- D = 0.1 m
- Ktot = 1
- rho = 1000 kg/m3

**Calculo:**
```
V = 2.0 m/s
hm = 1 * 2^2 / (2*9.81) = 4 / 19.62 = 0.2039 m  ~  0.204 m
dP = 1000 * 9.81 * 0.2039 = 2000 Pa
```

**Esperado en pantalla:**
```
hm  = 0.2039 m  ~  0.204 m
dP  = 2000 Pa
```

---

## TEST 22: Check cavitacion - sin riesgo (MF_PUMPS opcion 4)

**Menu:** Bombas → Check cavitacion

**Entrada:**
- NPSHa = 8.0 m
- NPSHr = 3.5 m

**Esperado:**
```
Margen = 4.5 m
No cavitacion (NPSHa >= NPSHr)
```

---

## TEST 23: Check cavitacion - con riesgo (MF_PUMPS opcion 4)

**Menu:** Bombas → Check cavitacion

**Entrada:**
- NPSHa = 2.0 m
- NPSHr = 3.5 m

**Esperado:**
```
Margen = -1.5 m
RIESGO CAVITACION (NPSHa < NPSHr)
```

---

---

## TEST 24: Velocidad del sonido (MF_COMP opcion 1)

**Menu:** Flujo Compresible → Velocidad sonido

**Entrada:**
- gamma = 1.4
- R = 287
- T = 288 K

**Calculo:**
```
a = sqrt(1.4 * 287 * 288) = sqrt(115718.4) = 340.17 m/s  ~  340 m/s
```

**Esperado en pantalla:**
```
a = 340.17 m/s  ~  340 m/s
```

---

## TEST 25: Numero Mach (MF_COMP opcion 2)

**Menu:** Flujo Compresible → Numero Mach

**Entrada:**
- V = 170 m/s
- gamma = 1.4, R = 287, T = 288 K

**Calculo:**
```
a = 340.17 m/s
M = 170 / 340.17 = 0.4997  ~  0.5
Regimen: SUBSONICO
```

**Esperado en pantalla:**
```
a = 340.17 m/s
M = 0.4997
Regimen: SUBSONICO
```

---

## TEST 26: Relaciones isentropicas M=2 (MF_COMP opcion 3)

**Menu:** Flujo Compresible → Relaciones isentropicas

**Entrada:**
- M = 2
- gamma = 1.4

**Calculo:**
```
T0/T     = 1 + 0.4/2 * 4 = 1 + 0.8 = 1.8
P0/P     = 1.8^(1.4/0.4) = 1.8^3.5 = 7.824
rho0/rho = 1.8^(1/0.4)   = 1.8^2.5 = 4.347
```

**Esperado en pantalla:**
```
T0/T     = 1.8
P0/P     = 7.824
rho0/rho = 4.347
```

---

## TEST 27: Area-Mach M=1 (MF_COMP opcion 5)

**Menu:** Flujo Compresible → Area-Mach

**Entrada:**
- M = 1
- gamma = 1.4

**Calculo:**
```
A/A* = 1  (garganta sonica)
```

**Esperado en pantalla:**
```
A/A* = 1
Nota: M=1, A=A* (garganta sonica)
```

---

## TEST 28: Choque normal M1=2 (MF_COMP opcion 9)

**Menu:** Flujo Compresible → Choque normal

**Entrada:**
- M1 = 2
- gamma = 1.4

**Calculo:**
```
M2sq = (1 + 0.2*4)/(1.4*4 - 0.2) = 1.8/5.4 = 0.3333
M2   = 0.5774  ~  0.577
P2/P1     = 1 + 2*1.4/2.4*(4-1) = 1 + 3.5 = 4.5
rho2/rho1 = 2.4*4 / (2 + 0.4*4) = 9.6/3.6 = 2.667
T2/T1     = 4.5 / 2.667 = 1.688
P02/P01   ~ 0.7209
```

**Esperado en pantalla:**
```
M2        = 0.5774
P2/P1     = 4.5
rho2/rho1 = 2.6667
T2/T1     = 1.688
P02/P01   = 0.7209
```

---

---

## TEST 29: Esfuerzo cortante Newton (MF_DIFF opcion 1 — modo du/dy)

**Menu:** Diferencial → Esfuerzo cortante Newton → Calcular tau

**Entrada:**
- mu = 0.001 Pa*s (agua a 20 C)
- du/dy = 100 1/s

**Calculo:**
```
tau = mu * (du/dy) = 0.001 * 100 = 0.1 Pa
```

**Esperado en pantalla:**
```
tau = 0.1 Pa
```

---

## TEST 30: Poiseuille tubo (MF_DIFF opcion 2)

**Menu:** Diferencial → Poiseuille tubo

**Entrada:**
- D = 0.01 m  (radio R = 0.005 m)
- L = 1.0 m
- deltaP = 400 Pa
- mu = 0.001 Pa*s

**Calculo:**
```
R  = 0.005 m
Q  = PI * R^4 * deltaP / (8 * mu * L)
   = PI * (0.005)^4 * 400 / (8 * 0.001 * 1)
   = PI * 6.25e-10 * 400 / 0.008
   = PI * 3.125e-5
   ~ 9.817e-5 m3/s  ~  9.82e-5 m3/s
Vavg = Q / (PI*R^2) = 9.817e-5 / (PI*2.5e-5) = 1.25 m/s
Vmax = 2 * Vavg = 2.50 m/s
tau_w = 4*mu*Vavg/R = 4*0.001*1.25/0.005 = 1.0 Pa
```

**Esperado en pantalla:**
```
Q    ~ 9.82e-5 m3/s
Vavg ~ 1.25 m/s
Vmax ~ 2.5 m/s
tau_w ~ 1.0 Pa
```

---

## TEST 31: Couette placas (MF_DIFF opcion 3)

**Menu:** Diferencial → Couette placas

**Entrada:**
- U = 1.0 m/s (velocidad placa superior)
- h = 0.005 m (separacion entre placas)
- mu = 0.001 Pa*s
- y = 0.0025 m (mitad del canal)

**Calculo:**
```
u(y) = U * y / h = 1.0 * 0.0025 / 0.005 = 0.5 m/s
tau  = mu * U / h = 0.001 * 1.0 / 0.005 = 0.2 Pa
```

**Esperado en pantalla:**
```
u(y) = 0.5 m/s
tau  = 0.2 Pa
```

---

## TEST 32: Campo lineal 2D — divergencia y vorticidad (MF_DIFF opcion 5)

**Menu:** Diferencial → Campo lineal 2D

**Entrada:**
- a = 1 (coef du/dx)
- b = 2 (coef du/dy)
- c = -1 (coef dv/dx, para flujo incompresible: dv/dy = -a = -1)
- d = -1 (coef dv/dy, debe ser -a para continuidad)
- x = 1.0, y = 1.0

**Campo:** u = a*x + b*y = 1+2 = 3;  v = c*x + d*y = -1-1 = -2
**Calculo:**
```
div = du/dx + dv/dy = a + d = 1 + (-1) = 0  (incompresible)
rotz = dv/dx - du/dy = c - b = -1 - 2 = -3  (rotacion)
```

**Esperado en pantalla:**
```
u    = 3.0 m/s
v    = -2.0 m/s
div  = 0.0  (incompresible)
rotz = -3.0 rad/s
```

---

## TEST 33: Vortice libre (MF_DIFF opcion 7)

**Menu:** Diferencial → Vortice libre

**Entrada:**
- C = 1.0 m2/s (constante de circulacion)
- r = 1.0 m

**Calculo:**
```
vtheta = C / r = 1.0 / 1.0 = 1.0 m/s
```

**Esperado en pantalla:**
```
vtheta = 1.0 m/s
```

---

---

## TEST 34: Agua a 20 C (MF_TABLES opcion 1)

**Menu:** Tablas → Agua por temperatura → 20 C

**Esperado en pantalla:**
```
rho = 998.2 kg/m3
mu  = 1.0E-3 Pa*s
nu  = 1.0E-6 m2/s
Valores aproximados
```

---

## TEST 35: Aire a 15 C (MF_TABLES opcion 2)

**Menu:** Tablas → Aire por temperatura → 15 C

**Esperado en pantalla:**
```
rho = 1.225 kg/m3
mu  = 1.79E-5 Pa*s
nu  = 1.46E-5 m2/s
Valores aproximados a 1 atm
```

---

## TEST 36: Presion vapor agua a 20 C (MF_TABLES opcion 3)

**Menu:** Tablas → Presion vapor agua → 20 C

**Calculo:**
```
Pv = 2339 Pa = 2.339 kPa
```

**Esperado en pantalla:**
```
Pv = 2339 Pa
Pv = 2.339 kPa
```

---

## TEST 37: Rugosidad acero comercial (MF_TABLES opcion 4)

**Menu:** Tablas → Rugosidad tuberias → Acero comercial

**Esperado en pantalla:**
```
eps = 4.5E-5 m
eps = 0.045 mm
Usar como estimacion
```

---

## TEST 38: Perdida menor entrada brusca (MF_TABLES opcion 5)

**Menu:** Tablas → Perdidas menores K → Entrada brusca

**Esperado en pantalla:**
```
K = 0.5
hm = K*V^2/(2*g)
Valor orientativo
```

---

## Notas sobre las pruebas

- Todos los calculos fueron verificados manualmente antes de la implementacion.
- La biseccion (Area-Mach, punto de operacion, diseno diametro, Q max cavitacion) converge en <= 50 iteraciones.
- El factor de Swamee-Jain difiere < 3% del factor de Colebrook en flujo turbulento desarrollado.
- MF_CAS no fue modificado y no forma parte del plan de pruebas activo.
- Los resultados del CAS dependen del motor simbolico de HP Prime; pueden variar en forma de representacion pero son equivalentes matematicamente.
- TEST 30: deltaP = 400 Pa (no 1000 Pa) para obtener Q ~ 9.82e-5 m3/s segun Hagen-Poiseuille.
- MF_TABLES proporciona valores tabulados fijos; los resultados son exactamente los valores almacenados.
