# FLUIDOS PRIME v2.0 — Estructura del Proyecto

## Árbol de archivos

```
HP_PRIME_FLUID_MECHANICS/
├── FLUIDOS.hpprgm        ← Punto de entrada principal
├── MF_PROPS.hpprgm       ← Módulo: Propiedades y Números Adim.
├── MF_MANO.hpprgm        ← Módulo: Manómetros y Presiones
├── MF_HYDRO.hpprgm       ← Módulo: Hidrostática
├── MF_BUOY.hpprgm        ← Módulo: Flotabilidad y Empuje
├── MF_BERN.hpprgm        ← Módulo: Ecuación de Bernoulli
├── MF_CONT.hpprgm        ← Módulo: Continuidad y Conservación de Masa
├── MF_MOM.hpprgm         ← Módulo: Cantidad de Movimiento Lineal
├── MF_PIPES.hpprgm       ← Módulo: Flujo en Tuberías
├── MF_PUMPS.hpprgm       ← Módulo: Bombas y Cavitación
├── MF_COMP.hpprgm        ← Módulo: Flujo Compresible
├── MF_DIFF.hpprgm        ← Módulo: Flujo Diferencial y Viscoso
├── MF_TABLES.hpprgm      ← Módulo: Tablas de Propiedades
├── MF_CAS.hpprgm         ← Módulo: Herramientas CAS
├── MF_UTILS.hpprgm       ← Módulo: Unidades + Ayuda
├── README.md             ← Instrucciones de instalación y uso
├── TEST_PLAN.md          ← Plan de pruebas con valores esperados
└── STRUCTURE.md          ← Este archivo
```

---

## Arquitectura general

Regla de angulos: las funciones que usan SIN, COS o ATAN protegen sus
calculos del modo global `HAngle`; guardan el modo anterior, usan radianes
internamente y restauran el modo original.

```
FLUIDOS()         ← Menú principal (FLUIDOS.hpprgm)
├── MF_PROPS()    ← Módulo independiente
│   ├── MF_RE()         Reynolds
│   ├── MF_MA()         Mach
│   ├── MF_FR()         Froude
│   ├── MF_PE()         Peso específico
│   ├── MF_VC()         Viscosidad cinemática
│   └── MF_PD()         Presión dinámica
│
├── MF_MANO()     ← Módulo independiente (v2.0)
│   ├── fmt_()              Helper formato numérico
│   ├── MF_MANO_HIDRO()     Opcion 1: P = P0 + rho*g*h
│   ├── MF_MANO_SIM()       Opcion 2: manometro U simple
│   ├── MF_MANO_DIF()       Opcion 3: manometro diferencial
│   ├── MF_MANO_INC()       Opcion 4: manometro inclinado (dos fluidos)
│   ├── MF_MANO_ABS()       Opcion 5: presion absoluta/manometrica/vacio
│   ├── MF_MANO_HEQV()      Opcion 6: altura equivalente h = DeltaP/(rho*g)
│   └── MF_MANO_HELP()      Opcion 7: ayuda formulas
│
├── MF_HYDRO()    ← Módulo independiente (v2.0)
│   ├── fmt_()              Helper formato numérico
│   ├── MF_HYDRO_FV()       Opcion 1: fuerza placa vertical F = rho*g*yc*A
│   ├── MF_HYDRO_FI()       Opcion 2: fuerza placa inclinada F = rho*g*yc*A
│   ├── MF_HYDRO_CPV()      Opcion 3: centro de presion placa vertical
│   ├── MF_HYDRO_CPI()      Opcion 4: centro de presion placa inclinada
│   ├── MF_HYDRO_RECT()     Opcion 5: compuerta rectangular (A, yc, F, yp)
│   ├── MF_HYDRO_FH()       Opcion 6: fuerza superficie horizontal
│   └── MF_HYDRO_HELP()     Opcion 7: ayuda formulas
│
├── MF_BUOY()     ← Módulo independiente (v2.0)
│   ├── fmt_()              Helper formato numérico
│   ├── MF_BUOY_FB()        Opcion 1: empuje Arquimedes Fb = rho*g*Vdisp
│   ├── MF_BUOY_FL()        Opcion 2: flota/hunde/neutro por densidades
│   ├── MF_BUOY_FRAC()      Opcion 3: fraccion sumergida = rho_obj/rho_fluid
│   ├── MF_BUOY_VSUB()      Opcion 4: volumen sumergido Vsub = m/rho_fluid
│   ├── MF_BUOY_RHOOBJ()    Opcion 5: densidad objeto rho = m/V
│   ├── MF_BUOY_CMAX()      Opcion 6: carga maxima de flotacion
│   └── MF_BUOY_HELP()      Opcion 7: ayuda formulas
│
├── MF_BERN()     ← Módulo independiente (v2.0)
│   ├── fmt_()              Helper formato numérico
│   ├── MF_BERN_BAL()       Opcion 1: balance Bernoulli entre 2 puntos (comprueba equilibrio)
│   ├── MF_BERN_P2()        Opcion 2: despejar P2 generalizado
│   ├── MF_BERN_V2G()       Opcion 3: despejar V2 generalizado
│   ├── MF_BERN_Z2()        Opcion 4: despejar z2 generalizado
│   ├── MF_BERN_PITOT()     Opcion 5: tubo Pitot (desde DeltaP o manometro)
│   ├── MF_BERN_VENT()      Opcion 6: Venturi ideal (Q desde D1, D2, DeltaP, rho)
│   ├── MF_BERN_TORR()      Opcion 7: Torricelli V = sqrt(2*g*h), Q = A*V opcional
│   ├── MF_BERN_ORIC()      Opcion 8: orificio con Cd: Q = Cd*A*sqrt(2*g*h)
│   ├── MF_BERN_HEAD()      Opcion 9: alturas de energia hp, hv, H
│   └── MF_BERN_HELP()      Opcion 10: ayuda formulas Bernoulli
│
├── MF_CONT()     ← Módulo independiente
│   ├── MF_CONT_Q()      Caudal volumetrico Q = A*V
│   ├── MF_CONT_MDOT()   Caudal masico mdot = rho*Q
│   ├── MF_CONT_C11()    Continuidad 1 entrada - 1 salida
│   ├── MF_CONT_C21()    Continuidad 2 entradas - 1 salida
│   ├── MF_CONT_C12()    Continuidad 1 entrada - 2 salidas
│   ├── MF_CONT_DHT()    Tasa de cambio de nivel en deposito
│   ├── MF_CONT_TFILL()  Tiempo de llenado o vaciado
│   ├── MF_CONT_CONV()   Conversor de unidades de caudal
│   └── MF_CONT_HELP()   Ayuda de continuidad
│
├── MF_MOM()      ← Módulo independiente
│   ├── angulo_()        (interna) angulo desde componentes Fx, Fy
│   ├── MF_MOM_NOZ()     Fuerza axial en tobera o difusor
│   ├── MF_MOM_BEND()    Reaccion en codo 2D estilo Tema 4
│   ├── MF_MOM_PLATE()   Chorro contra placa fija
│   ├── MF_MOM_DEF()     Chorro desviado por angulo
│   ├── MF_MOM_THRUST()  Empuje motor/cohete
│   ├── MF_MOM_2D()      Fuerza resultante 2D
│   └── MF_MOM_HELP()    Ayuda cantidad de movimiento
│
├── MF_PIPES()    ← Módulo independiente (v2.0)
│   ├── fmt_()          Helper formato numérico
│   ├── regimenStr()    Clasificación régimen por Re
│   ├── calcFF()        Factor de fricción Darcy (laminar/Swamee-Jain)
│   ├── calcHL_()       Helper: hL total de un tramo (interno)
│   ├── MF_PIPES_VRF()  Opcion 1: V, Re, f
│   ├── MF_PIPES_TRAMO() Opcion 2: Perdida de tramo (hf, dP)
│   ├── MF_PIPES_K()    Opcion 3: Perdidas menores K
│   ├── MF_PIPES_SIM()  Opcion 4: Sistema simple completo
│   ├── MF_PIPES_2T()   Opcion 5: Sistema 2 tramos en serie
│   ├── MF_PIPES_DIAM() Opcion 6: Diseño de diámetro (bisección)
│   ├── MF_PIPES_KVAL() Opcion 7: K válvula de regulación
│   ├── MF_PIPES_FD()   Opcion 8: Factor f Darcy y Colebrook
│   ├── MF_PIPES_LOSSES() Opcion 9: Perdidas tuberia
│   ├── MF_PL_FG()      Con f Darcy dado
│   ├── MF_PL_COLE()    Con f calculado por Colebrook
│   ├── MF_PL_MIN()     Solo perdidas menores
│   └── MF_PIPES_HELP() Opcion 10: Ayuda fórmulas
│
├── MF_PUMPS()    ← Módulo independiente (v2.0)
│   ├── fmt_()          Helper formato numérico
│   ├── calcFFp_()      Factor de fricción (local, evita conflicto)
│   ├── calcNPSHa_()    Helper: NPSHa dado Q (interno para bisección)
│   ├── MF_PUMPS_POT()  Opcion 1: Potencia bomba (Ph, Pshaft)
│   ├── MF_PUMPS_BERN() Opcion 2: Carga bomba Bernoulli
│   ├── MF_PUMPS_NPSHA() Opcion 3: NPSHa con pérdidas de tubería
│   ├── MF_PUMPS_CAVI() Opcion 4: Check cavitación (margen)
│   ├── MF_PUMPS_QMAX() Opcion 5: Q max sin cavitación (bisección)
│   ├── MF_PUMPS_OP()   Opcion 6: Punto de operación simple (bisección)
│   └── MF_PUMPS_HELP() Opcion 7: Ayuda fórmulas
│
├── MF_COMP()     ← Módulo independiente (v2.0)
│   ├── fmt_()          Helper formato numérico
│   ├── aAstar_(M,k)    (interna) A/A* = f(M, gamma)
│   ├── solveMach_()    (interna) bisección M dado A/A* (50 iter.)
│   ├── MF_COMP_SONIDO() Opcion 1: a = sqrt(gamma*R*T)
│   ├── MF_COMP_MACH()  Opcion 2: M = V/a, clasificacion regimen
│   ├── MF_COMP_IS()    Opcion 3: razones T0/T, P0/P, rho0/rho
│   ├── MF_COMP_REMAN() Opcion 4: T0, P0, rho0 desde estaticas
│   ├── MF_COMP_ASTAR() Opcion 5: A/A* dado M
│   ├── MF_COMP_MINV()  Opcion 6: M desde A/A* (biseccion subsonica o supersonica)
│   ├── MF_COMP_MDOT()  Opcion 7: caudal masico isentropico en tobera
│   ├── MF_COMP_CHOKE() Opcion 8: flujo bloqueado mdot* y P*/P0
│   ├── MF_COMP_SHOCK() Opcion 9: choque normal (M2, P2/P1, rho2/rho1, T2/T1, P02/P01)
│   └── MF_COMP_HELP()  Opcion 10: ayuda formulas
│
├── MF_DIFF()     ← Módulo independiente (v2.0)
│   ├── fmt_()          Helper formato numérico
│   ├── MF_DIFF_TAU()   Opcion 1: tau = mu*(du/dy), modo calculo tau o du/dy
│   ├── MF_DIFF_PUIS()  Opcion 2: Poiseuille tubo (Q, Vavg, Vmax, tau_w)
│   ├── MF_DIFF_COUETTE() Opcion 3: Couette placas (u(y), tau)
│   ├── MF_DIFF_PUIS2() Opcion 4: Poiseuille plano (Q, Vavg, Vmax, tau_w)
│   ├── MF_DIFF_CAMP()  Opcion 5: Campo lineal 2D (div, rotz)
│   ├── MF_DIFF_DEF()   Opcion 6: Deformacion 2D (exx, eyy, exy)
│   ├── MF_DIFF_VORT()  Opcion 7: Vortice libre (vtheta = C/r)
│   └── MF_DIFF_HELP()  Opcion 8: Ayuda formulas
│
├── MF_TABLES()   ← Módulo independiente (v2.0)
│   ├── fmt_()              Helper formato numérico
│   ├── MF_TABLES_AGUA()    Opcion 1: propiedades agua por T (0-100 C)
│   ├── MF_TABLES_AIRE()    Opcion 2: propiedades aire por T (0-40 C, 1 atm)
│   ├── MF_TABLES_PV()      Opcion 3: presion vapor agua por T
│   ├── MF_TABLES_EPS()     Opcion 4: rugosidad absoluta tuberias
│   ├── MF_TABLES_K()       Opcion 5: coeficiente K perdidas menores
│   ├── MF_TABLES_CST()     Opcion 6: constantes utiles de referencia
│   └── MF_TABLES_HELP()    Opcion 7: ayuda uso tablas
│
├── MF_CAS()      ← Módulo independiente
│   ├── MF_CAS_SOLV()   Resolver ecuación simbólica
│   ├── MF_CAS_SIMP()   Simplificar expresión
│   ├── MF_CAS_DIFF()   Derivar expresión
│   ├── MF_CAS_INTG()   Integrar expresión
│   ├── MF_CAS_KIN()    Cinemática 2D
│   ├── MF_CAS_FLOW()   Caudal por integración
│   └── MF_CAS_EQSOLV() Ecuaciones predefinidas
│
└── MF_UNITS()    ─┐  ← Módulo independiente (MF_UTILS.hpprgm)
   MF_HELP()      ─┘
    ├── MF_U_PRES()     Conversión presión
    ├── MF_U_CAUD()     Conversión caudal
    ├── MF_U_VISC()     Conversión viscosidad
    ├── MF_U_LONG()     Conversión longitud
    ├── MF_U_TEMP()     Conversión temperatura
    ├── MF_U_ENER()     Conversión energía/potencia
    ├── MF_HELP_UNID()  Ayuda: Unidades SI
    ├── MF_HELP_PRES()  Ayuda: Tipos de presión
    ├── MF_HELP_RE()    Ayuda: Régimen de flujo
    ├── MF_HELP_BERN()  Ayuda: Bernoulli
    ├── MF_HELP_COMP()  Ayuda: Flujo compresible
    ├── MF_HELP_CAVI()  Ayuda: Cavitación
    └── MF_HELP_PROPF() Ayuda: Propiedades típicas
```

---

## Patrón de navegación

Cada módulo usa el mismo patrón de control de flujo:

```
EXPORT MF_MODULO()
BEGIN
  LOCAL c;
  WHILE 1 DO
    CHOOSE(c, "TITULO", "Op1", "Op2", ..., "0. Volver");
    IF c == 0 OR c == N THEN RETURN; END;  // ESC o Volver
    IF c == 1 THEN sub1(); END;
    ...
  END;
END;
```

Cada sub-función:
```
sub1()
BEGIN
  // 1. Solicitar datos con INPUT
  IF NOT INPUT(...) THEN RETURN; END;  // ESC cancela
  // 2. Validar datos
  IF dato <= 0 THEN MSGBOX("Error: ..."); RETURN; END;
  // 3. Calcular
  resultado := formula;
  // 4. Mostrar con MSGBOX
  MSGBOX("resultado = " + STRING(resultado));
END;
```

---

## Principios de diseño

1. **Modularidad**: Cada archivo es un módulo independiente que puede usarse
   por separado sin el menú principal.

2. **Robustez**: Toda función valida sus entradas antes de calcular.
   Los errores se muestran con MSGBOX y retornan al menú, nunca bloquean.

3. **IFERR en menú principal**: Las llamadas a módulos en FLUIDOS.hpprgm
   están envueltas en IFERR para manejar módulos no instalados.

4. **Sin variables globales**: Todo estado es local a cada función.
   No hay efectos secundarios entre módulos.

5. **Consistencia de interfaz**: CHOOSE para menús, INPUT para datos,
   MSGBOX para resultados y errores. Patrón uniforme en todos los módulos.

6. **CAS como complemento**: El módulo CAS (MF_CAS) es auxiliar.
   Los cálculos numéricos se hacen directamente en HP PPL para mayor velocidad.

7. **Internacionalización**: Todos los textos de usuario en español.
   Comentarios de código en inglés/español según conveniencia.

---

## Convenciones de nomenclatura

| Prefijo   | Significado                                   |
|-----------|-----------------------------------------------|
| `MF_`     | Función del módulo principal (EXPORT)         |
| `MF_XX_`  | Subfunción del módulo XX (no EXPORT por defecto) |
| `fmt_`    | Helper de formato numérico (local al archivo) |
| `calc`    | Helper de cálculo interno                     |
| `_`       | Función interna (no exportada, sufijo)        |

---

## Compatibilidad

- **Hardware**: HP Prime G2 (serie 2AP18AA)
- **Firmware**: Cualquier versión reciente (2019+)
- **Formato**: HP PPL estándar, sin extensiones propietarias
- **CAS**: Usa `CAS("expresion")` estándar de HP PPL para llamadas simbólicas
- **Connectivity Kit**: Compatible con HP Connectivity Kit para Windows/Mac
