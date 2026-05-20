# FLUIDOS PRIME v1.0
## Herramientas de Mecánica de Fluidos para HP Prime G2

---

## Descripción

**FLUIDOS PRIME** es una aplicación modular para la HP Prime G2 diseñada como
calculadora avanzada y asistente de examen para cursos universitarios de
**Mecánica de Fluidos**. Toda la interfaz de usuario está en **español**.

---

## Requisitos

- HP Prime G2 (o HP Prime con firmware actualizado)
- HP Connectivity Kit (para importar los archivos)
- Todos los archivos `.hpprgm` deben estar instalados en la calculadora

---

## Archivos del proyecto

| Archivo               | Descripción                                      |
|-----------------------|--------------------------------------------------|
| `FLUIDOS.hpprgm`      | **Menú principal** — punto de entrada            |
| `MF_PROPS.hpprgm`     | Propiedades y números adimensionales             |
| `MF_MANO.hpprgm`      | Manómetros y presiones                           |
| `MF_HYDRO.hpprgm`     | Hidrostática                                     |
| `MF_BUOY.hpprgm`      | Flotabilidad y empuje                            |
| `MF_BERN.hpprgm`      | Ecuación de Bernoulli generalizada               |
| `MF_PIPES.hpprgm`     | Flujo incompresible en tuberías                  |
| `MF_PUMPS.hpprgm`     | Bombas y cavitación                              |
| `MF_COMP.hpprgm`      | Flujo compresible (isentrópico, A/A*, choque)    |
| `MF_CAS.hpprgm`       | Herramientas CAS simbólicas                      |
| `MF_UTILS.hpprgm`     | Convertidor de unidades y ayuda rápida           |
| `README.md`           | Este archivo                                     |
| `TEST_PLAN.md`        | Plan de pruebas con casos verificados            |
| `STRUCTURE.md`        | Descripción detallada de la arquitectura         |

---

## Instalación con HP Connectivity Kit

### Método 1: Copiar y pegar (recomendado para pocos archivos)

1. Abrir **HP Connectivity Kit** en la computadora.
2. Conectar la HP Prime G2 por USB.
3. En la calculadora aparecerá como dispositivo.
4. En Connectivity Kit, ir a **Programas**.
5. Para cada archivo `.hpprgm`:
   - Hacer clic en **Nuevo Programa** o **Crear Programa**.
   - Copiar el contenido del archivo `.hpprgm` al editor.
   - Guardar con el mismo nombre que el archivo (sin extensión).
   - Transferir a la calculadora.

### Método 2: Importar archivo directamente

1. En HP Connectivity Kit, menú **Archivo → Importar**.
2. Seleccionar el archivo `.hpprgm`.
3. El programa se importa automáticamente.
4. Repetir para todos los archivos.

### Método 3: Copiar desde la calculadora

1. En la calculadora, ir a **Apps → Programa** o **Herramientas → Programa**.
2. Crear un nuevo programa con el nombre indicado.
3. Copiar el código manualmente (poco práctico para archivos largos).

> **IMPORTANTE**: Todos los archivos deben instalarse para que el programa
> funcione correctamente. Si un módulo falta, el programa lo indicará con
> un mensaje de error.

---

## Uso

1. Ejecutar la función `FLUIDOS()` desde la **Vista del CAS** o desde
   **Herramientas → Programa** en la calculadora.
2. Navegar por el menú principal con las flechas y **ENTER**.
3. Presionar **ESC** para retroceder al menú anterior.
4. Seleccionar **"0. SALIR"** para salir del programa.

---

## Módulos implementados (Fase 1–3 completas)

### 1. Propiedades y Números Adimensionales (`MF_PROPS`)
- Número de Reynolds (Re = ρVD/μ o Re = VD/ν)
- Número de Mach (Ma = V/a, a = √(kRT))
- Número de Froude (Fr = V/√(gL))
- Peso específico (γ = ρg)
- Viscosidad cinemática (ν = μ/ρ)
- Presión dinámica (q = ½ρV²)
- Clasificación automática: laminar / transicional / turbulento
- Aviso de compresibilidad si Ma > 0.3

### 2. Manómetros y Presiones (`MF_MANO`)
- Conversión presión absoluta ↔ manométrica ↔ vacío
- Manómetro de tubo en U simple: ΔP = ρ·g·h·(ρ_m − ρ_f)
- Manómetro diferencial con fluido manométrico
- Manómetro inclinado: h = L·sin(θ)
- ΔP por columna de fluido: ΔP = ρgh
- Conversión de unidades de presión integrada (Pa, kPa, bar, atm, mmHg, psi)

### 3. Hidrostática (`MF_HYDRO`)
- Presión hidrostática: P = P₀ + ρgh
- Fuerza sobre placa vertical rectangular: F = ρg·h_c·A, centro de presión
- Fuerza sobre placa inclinada rectangular (ángulo θ con horizontal)
- Fuerza sobre superficie horizontal
- Excentricidad: e = I_G / (y_c·A)

### 4. Flotabilidad y Empuje (`MF_BUOY`)
- Fuerza de empuje: E = ρ_f·g·V_desplazado
- Análisis completo: flota / se hunde / neutro
- Fracción sumergida de cuerpo flotante: V_sub/V = ρ_cuerpo/ρ_fluido

### 5. Ecuación de Bernoulli Generalizada (`MF_BERN`)
- Hallar P₂ dado todo lo demás
- Hallar pérdidas de carga h_L
- Hallar altura de bomba h_bomba
- Potencia hidráulica: P_h = ρgQH
- Velocidad de salida (Torricelli)
- Desglose completo en metros de columna de fluido

### 6. Flujo en Tuberías (`MF_PIPES`)
- Velocidad media V = Q/A y caudal Q = V·A
- Reynolds en tubería
- Factor de fricción: f = 64/Re (laminar) o Swamee-Jain (turbulento)
- Pérdida mayor (Darcy-Weisbach): h_f = f·(L/D)·V²/2g
- Pérdidas menores: h_m = K·V²/2g
- Cálculo completo: Reynolds, f, h_f, h_m, h_L, ΔP

### 7. Bombas y Cavitación (`MF_PUMPS`)
- Potencia hidráulica y en eje
- Eficiencia η = P_h / P_eje
- NPSHa desde brida de succión o desde depósito
- Comparación NPSHa vs NPSHr con diagnóstico de cavitación
- Punto de operación bomba-sistema (curvas cuadráticas, bisección numérica)

### 8. Flujo Compresible (`MF_COMP`)
- Velocidad del sonido: a = √(kRT)
- Relaciones isentrópicas: T₀/T, P₀/P, ρ₀/ρ
- Relación Área-Mach: A/A* para M dado, y M para A/A* dado (ramas subsónica y supersónica, bisección numérica)
- Onda de choque normal: M₂, P₂/P₁, ρ₂/ρ₁, T₂/T₁, P₀₂/P₀₁
- Aviso si M₁ ≤ 1 (choque no físico)

### 9. Herramientas CAS (`MF_CAS`)
- Resolver ecuación simbólica para variable
- Simplificar expresión
- Derivar expresión (orden n)
- Integrar (indefinida y definida)
- Cinemática 2D: divergencia, vorticidad, aceleración material
- Caudal por integración de perfil u(r): Poiseuille, uniforme, personalizado
- Ecuaciones predefinidas de fluidos para despejar variables

### 10. Convertidor de Unidades (`MF_UNITS`)
- Presión: Pa, kPa, bar, atm, mmHg, psi
- Caudal: m³/s, L/s, L/min, m³/h, GPM
- Viscosidad dinámica: Pa·s, cP, P
- Longitud: m, cm, mm, in, ft
- Temperatura: K, °C, °F, °R
- Energía: J, kJ, Wh, kWh, kcal, BTU
- Potencia: W, kW, CV, HP

### 11. Ayuda Rápida (`MF_HELP`)
- Unidades SI del sistema
- Tipos de presión y conversión
- Regímenes de flujo
- Ecuación de Bernoulli
- Flujo compresible
- Cavitación
- Propiedades típicas de fluidos (agua, aire, mercurio)

---

## Funciones pendientes (fases futuras)

- Continuidad (ecuación de continuidad integral y diferencial)
- Cantidad de movimiento (fuerzas en codos, toberas)
- Perfil de Couette (flujo entre placas)
- Flujo en canales abiertos (tirante normal, curva de energía)
- Diagrama de Moody interactivo
- Tablas isentrópicas completas
- Tabla de propiedades del agua/aire en función de T

---

## Notas importantes

- **Unidades**: Todo en SI (Pa, kg, m, s, K). Usar el convertidor si es necesario.
- **Presiones**: Salvo indicación contraria, se acepta tanto manométrica como absoluta.
  La Bernoulli funciona con cualquiera siempre que se sea consistente.
- **Factor de fricción**: Se usa la ecuación de Swamee-Jain (explícita). Para mayor
  precisión en zona de transición usar Colebrook-White (iterativa, no incluida).
- **CAS**: El módulo CAS requiere que las variables estén definidas como texto.
  Los resultados son simbólicos; para evaluación numérica ingresar valores en las
  expresiones antes de llamar al CAS.
- **Bisección**: Los solucionadores de M (Area-Mach) y punto de operación usan
  bisección con 60–70 iteraciones. Convergencia garantizada si el rango es correcto.

---

## Autor y versión

- **Versión**: 1.0 (Mayo 2026)
- **Plataforma**: HP Prime G2 / HP PPL
- **Idioma de interfaz**: Español
- **Comentarios internos**: Inglés / Español
