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
| `MF_CONT.hpprgm`      | Continuidad y conservación de masa               |
| `MF_MOM.hpprgm`       | Cantidad de movimiento lineal                    |
| `MF_PIPES.hpprgm`     | Flujo incompresible en tuberías                  |
| `MF_PUMPS.hpprgm`     | Bombas y cavitación                              |
| `MF_COMP.hpprgm`      | Flujo compresible (isentrópico, A/A*, choque)    |
| `MF_DIFF.hpprgm`      | Flujo diferencial y viscoso (Poiseuille, Couette)|
| `MF_TABLES.hpprgm`    | Tablas de propiedades: agua, aire, Pv, eps, K    |
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

## Módulos implementados (Fase 1–3 + Fase 2 completas)

### 1. Propiedades y Números Adimensionales (`MF_PROPS`)
- Número de Reynolds (Re = ρVD/μ o Re = VD/ν)
- Número de Mach (Ma = V/a, a = √(kRT))
- Número de Froude (Fr = V/√(gL))
- Peso específico (γ = ρg)
- Viscosidad cinemática (ν = μ/ρ)
- Presión dinámica (q = ½ρV²)
- Clasificación automática: laminar / transicional / turbulento
- Aviso de compresibilidad si Ma > 0.3

### 2. Manómetros y Presiones (`MF_MANO`) — mejorado v2.0
- Presión hidrostática directa: P = P0 + rho*g*h
- Manómetro de tubo en U simple: DeltaP = (rho_m − rho_f)*g*dh
- Manómetro diferencial con dos fluidos
- Manómetro inclinado con dos fluidos: dh = L*sin(theta)
- Presión absoluta/manométrica/vacío con submenú
- Altura equivalente de columna de fluido: h = DeltaP/(rho*g)
- Ayuda integrada

### 3. Hidrostática (`MF_HYDRO`) — mejorado v2.0
- Fuerza sobre placa vertical: F = rho*g*yc*A (inputs: rho, g, A, yc)
- Fuerza sobre placa inclinada: F = rho*g*yc*A (inputs: rho, g, A, yc)
- Centro de presión placa vertical: yp = yc + Ixc/(yc*A)
- Centro de presión placa inclinada: yp = yc + Ixc*sin²(theta)/(yc*A)
- Compuerta rectangular: A, yc, F, yp desde b, h, y_top
- Fuerza sobre superficie horizontal: F = rho*g*h*A
- Ayuda integrada

### 4. Flotabilidad y Empuje (`MF_BUOY`) — mejorado v2.0
- Empuje Arquímedes: Fb = rho_fluid*g*Vdisp
- Análisis flota/hunde/neutro por comparación de densidades
- Fracción sumergida: frac = rho_obj/rho_fluid, con porcentajes
- Volumen sumergido desde masa: Vsub = m/rho_fluid
- Densidad del objeto desde masa y volumen: rho = m/V
- Carga máxima de flotación: m_payload = rho_fluid*Vmax − m_body
- Ayuda integrada

### 5. Ecuación de Bernoulli y Energía (`MF_BERN`) — mejorado v2.0
- Balance Bernoulli entre 2 puntos: E1 + hPump - hTurb - hL = E2 (comprueba equilibrio)
- Despejar P2 generalizado: P2 = rho*g*(E1 + hPump - hTurb - hL - V2²/2g - z2)
- Despejar V2 generalizado: V2 = sqrt(2*g*(E1 + hPump - hTurb - hL - P2/rho/g - z2))
- Despejar z2 generalizado: z2 = E1 + hPump - hTurb - hL - P2/rho/g - V2²/2g
- Tubo Pitot: V = sqrt(2*DeltaP/rho) o desde manómetro diferencial
- Venturi ideal: Q desde D1, D2, DeltaP, rho (fórmula exacta con areas)
- Torricelli: V = sqrt(2*g*h), Q = A*V opcional
- Orificio con coeficiente de descarga Cd: Q = Cd*A*sqrt(2*g*h)
- Alturas de energía: hp = P/rho/g, hv = V²/2g, H = hp + hv + z
- Ayuda integrada con fórmulas y unidades

### 6. Continuidad y Conservación de Masa (`MF_CONT`)
- Caudal volumétrico: Q = A·V (con conversiones L/s, m³/h)
- Caudal másico: ṁ = ρ·Q
- Continuidad 1 entrada → 1 salida: A₁V₁ = A₂V₂ (resolver cualquier incógnita)
- Continuidad 2 entradas → 1 salida: Q₁ + Q₂ = Q₃
- Continuidad 1 entrada → 2 salidas: Q₁ = Q₂ + Q₃
- Tasa de cambio de nivel en depósito: dh/dt = (Qin − Qout)/As
- Tiempo de llenado o vaciado de depósito
- Conversor de unidades de caudal (m³/s, L/s, m³/h, L/h, GPM, ft³/s)
- Ayuda integrada con principios y fórmulas

### 7. Cantidad de Movimiento Lineal (`MF_MOM`)
- Fuerza axial en tobera o difusor: Rx = P₁A₁ − P₂A₂ + ṁ(V₁ − V₂)
- Reaccion en codo 2D corregida para reportar la reaccion de anclaje/soporte estilo Tema 4
- Convenio del codo 2D: +x derecha, +y arriba, theta desde +x; Rx,Ry son soporte/anclaje sobre el codo
- Las fuerzas de presion usan presiones manometricas; si se ingresan absolutas, se convierten restando Patm
- Modo Bernoulli: calcula P1g cuando la salida descarga a atmosfera; TEST_PLAN incluye el caso Tema 4 problema 5
- Chorro contra placa fija: F = ρQV = ṁV
- Chorro desviado por ángulo θ: Fx = ṁV(1−cosθ), Fy = ṁV·sinθ
- Empuje tipo motor/cohete: T = ṁ(Ve − Vi) + (Pe − Pi)Ae, impulso específico
- Fuerza resultante 2D: magnitud, ángulo y fuerza de anclaje requerida
- Ayuda integrada con ecuación de cantidad de movimiento para volumen de control

### 8. Flujo en Tuberías (`MF_PIPES`) — mejorado v2.0
- V, Re, f: velocidad, Reynolds y factor de fricción en un paso
- Pérdida de tramo: hf y ΔP para una tubería directa
- Pérdidas menores K: hm = Ktot·V²/2g
- Sistema simple: hf + hm + hL + ΔP completo
- Sistema 2 tramos en serie: V1, V2, Re1, Re2, f1, f2, h1, h2, hm, hL, ΔP
- Diseño de diámetro: bisección para hallar D dado Q, L, Hav (DeltaZ + hL = Hav)
- K válvula de regulación: hvalve y Kvalve para equilibrar un sistema con bomba
- Ayuda de fórmulas integrada
- Factor de fricción: f = 64/Re (laminar, Re < 2300) o Swamee-Jain (turbulento)
- Aviso automático en zona de transición (2300 ≤ Re ≤ 4000)

### 9. Bombas y Cavitación (`MF_PUMPS`) — mejorado v2.0
- Potencia hidráulica Ph = ρgQH y en eje Pshaft = Ph/η
- Carga de bomba por Bernoulli generalizada
- NPSHa con cálculo completo de pérdidas en tubería de succión (hf + hm)
- Check de cavitación: margen = NPSHa − NPSHr, con diagnóstico
- Q máximo sin cavitación: bisección de NPSHa(Q) = NPSHr (40 iteraciones)
- Punto de operación simple: bisección de Hpump(Q) = Hsystem(Q)
  con Hpump = H0 − c·Q² y Hsystem = Hstatic + R·Q²
- Ayuda de fórmulas integrada
- MF_CAS está pendiente para fase final (no modificado)

### 10. Flujo Compresible (`MF_COMP`) — mejorado v2.0
- Velocidad del sonido: a = sqrt(gamma*R*T)
- Numero de Mach: M = V/a, con clasificacion subsonico/sonico/supersonico
- Relaciones isentropicas: T0/T, P0/P, rho0/rho (solo razones)
- Propiedades de remanso: T0, P0, rho0 desde condiciones estaticas y M
- Area-Mach: A/A* dado M
- Mach desde A/A*: biseccion rama subsonica o supersonica (50 iter.)
- Caudal masico tobera: mdot = A*P0/sqrt(T0)*sqrt(gamma/R)*M*(...)
- Flujo bloqueado: mdot* y P*/P0 para condicion choked (M=1)
- Choque normal: M2, P2/P1, rho2/rho1, T2/T1, P02/P01
- Ayuda de formulas integrada
- MF_CAS permanece pendiente para fase final (no modificado)

### 11. Flujo Diferencial y Viscoso (`MF_DIFF`) — nuevo v2.0
- Esfuerzo cortante Newton: tau = mu * du/dy (calcular tau o du/dy)
- Perfil de Poiseuille en tubo: Q, Vavg, Vmax, tau_w (Hagen-Poiseuille)
- Flujo de Couette entre placas: u(y) = U*y/h, tau = mu*U/h
- Poiseuille plano entre placas: Q, Vavg, Vmax, tau_w con gradiente de presion
- Campo de velocidades lineal 2D: u = ax+by, v = cx+dy — divergencia y vorticidad
- Deformacion 2D: tasa de deformacion lineal y angular (exx, eyy, exy)
- Vortice libre: vtheta = C/r, velocidad tangencial en radio r
- Ayuda integrada con formulas de flujo viscoso

### 12. Tablas de Propiedades (`MF_TABLES`) — nuevo v2.0
- Agua por temperatura (0–100 C): rho, mu, nu
- Aire por temperatura (0–40 C, 1 atm): rho, mu, nu
- Presion de vapor del agua (0–100 C): Pv en Pa y kPa
- Rugosidad absoluta de tuberias: vidrio, cobre, acero, hierro, hormigon
- Perdidas menores K: entradas, codos, valvulas
- Constantes utiles de referencia: g, R_air, k_air, rho_w, mu_w, Patm
- Ayuda de uso integrada
- MF_CAS permanece pendiente para fase final (no modificado)

### 13. Herramientas CAS (`MF_CAS`)
- Resolver ecuación simbólica para variable
- Simplificar expresión
- Derivar expresión (orden n)
- Integrar (indefinida y definida)
- Cinemática 2D: divergencia, vorticidad, aceleración material
- Caudal por integración de perfil u(r): Poiseuille, uniforme, personalizado
- Ecuaciones predefinidas de fluidos para despejar variables

### 14. Convertidor de Unidades (`MF_UNITS`)
- Presión: Pa, kPa, bar, atm, mmHg, psi
- Caudal: m³/s, L/s, L/min, m³/h, GPM
- Viscosidad dinámica: Pa·s, cP, P
- Longitud: m, cm, mm, in, ft
- Temperatura: K, °C, °F, °R
- Energía: J, kJ, Wh, kWh, kcal, BTU
- Potencia: W, kW, CV, HP

### 15. Ayuda Rápida (`MF_HELP`)
- Unidades SI del sistema
- Tipos de presión y conversión
- Regímenes de flujo
- Ecuación de Bernoulli
- Flujo compresible
- Cavitación
- Propiedades típicas de fluidos (agua, aire, mercurio)

---

## Funciones pendientes (fases futuras)

- MF_CAS: herramientas CAS simbólicas — pendiente para fase final (archivo presente pero no activo)
- CAS avanzado: resolver y simplificar ecuaciones de fluidos simbólicamente
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
