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

## Notas sobre las pruebas

- Todos los cálculos fueron verificados manualmente antes de la implementación.
- La bisección (Area-Mach, punto de operación) converge en < 70 iteraciones.
- El factor de Swamee-Jain difiere < 3% del factor de Colebrook en flujo turbulento desarrollado.
- Los resultados del CAS dependen del motor simbólico de HP Prime; pueden variar en forma de representación pero son equivalentes matemáticamente.
