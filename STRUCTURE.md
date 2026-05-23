# FLUIDOS PRIME v1.0 — Estructura del Proyecto

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
├── MF_CAS.hpprgm         ← Módulo: Herramientas CAS
├── MF_UTILS.hpprgm       ← Módulo: Unidades + Ayuda
├── README.md             ← Instrucciones de instalación y uso
├── TEST_PLAN.md          ← Plan de pruebas con valores esperados
└── STRUCTURE.md          ← Este archivo
```

---

## Arquitectura general

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
├── MF_MANO()     ← Módulo independiente
│   ├── MF_MANO_PC()    Conversión Abs/Man/Vacío
│   ├── MF_MANO_SIM()   Manómetro simple U
│   ├── MF_MANO_DIF()   Manómetro diferencial
│   ├── MF_MANO_INC()   Manómetro inclinado
│   └── MF_MANO_DP()    ΔP = ρgh directo
│
├── MF_HYDRO()    ← Módulo independiente
│   ├── MF_HYDRO_P()    Presión hidrostática
│   ├── MF_HYDRO_FV()   Fuerza en placa vertical
│   ├── MF_HYDRO_FI()   Fuerza en placa inclinada
│   └── MF_HYDRO_FH()   Fuerza en superficie horizontal
│
├── MF_BUOY()     ← Módulo independiente
│   ├── MF_BUOY_FE()    Fuerza de empuje
│   ├── MF_BUOY_FL()    Análisis de flotabilidad
│   └── MF_BUOY_FR()    Fracción sumergida
│
├── MF_BERN()     ← Módulo independiente
│   ├── MF_BERN_P2()    Hallar P₂
│   ├── MF_BERN_HL()    Hallar h_L
│   ├── MF_BERN_HB()    Hallar h_bomba
│   ├── MF_BERN_POT()   Potencia hidráulica
│   └── MF_BERN_V2()    Velocidad de salida
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
│   ├── MF_MOM_BEND()    Reaccion en codo 2D
│   ├── MF_MOM_PLATE()   Chorro contra placa fija
│   ├── MF_MOM_DEF()     Chorro desviado por angulo
│   ├── MF_MOM_THRUST()  Empuje motor/cohete
│   ├── MF_MOM_2D()      Fuerza resultante 2D
│   └── MF_MOM_HELP()    Ayuda cantidad de movimiento
│
├── MF_PIPES()    ← Módulo independiente
│   ├── MF_PIPES_V()    Velocidad media / caudal
│   ├── MF_PIPES_RE()   Reynolds en tubería
│   ├── MF_PIPES_FF()   Factor de fricción
│   ├── MF_PIPES_HF()   Pérdida mayor (Darcy-Weisbach)
│   ├── MF_PIPES_HM()   Pérdidas menores
│   └── MF_PIPES_TOT()  Cálculo completo
│
├── MF_PUMPS()    ← Módulo independiente
│   ├── MF_PUMPS_POT()  Potencia de bomba
│   ├── MF_PUMPS_ETA()  Eficiencia
│   ├── MF_PUMPS_NPSHA() NPSHa disponible
│   ├── MF_PUMPS_CAVI() Evaluación cavitación
│   └── MF_PUMPS_OP()   Punto de operación bomba-sistema
│
├── MF_COMP()     ← Módulo independiente
│   ├── aAstar_()       (interna) A/A* = f(M,k)
│   ├── solveMach_()    (interna) bisección M dado A/A*
│   ├── MF_COMP_SONIDO() Velocidad del sonido
│   ├── MF_COMP_IS()    Relaciones isentrópicas
│   ├── MF_COMP_AREA()  Relación Área-Mach
│   └── MF_COMP_SHOCK() Onda de choque normal
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
