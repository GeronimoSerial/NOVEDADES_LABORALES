---
proyecto: Sistematizacion de Novedades Laborales
tipo: Matriz comparativa
estado: borrador
date: 2026-04-06
tags:
  - sgpem
  - bounded-context
  - matriz
  - novedades-laborales
---

# Matriz comparativa - Bounded contexts de novedades laborales

> [!abstract] Proposito
> Esta nota resume y compara los ocho bounded contexts definidos para el dominio de novedades laborales. Su objetivo es facilitar lectura rapida, evitar solapamientos y servir como puerta de entrada para profundizar cada contexto por separado.

## 1. Como leer esta matriz

La matriz compara los contextos segun siete preguntas simples:

1. cual es su objetivo principal,
2. cual es su foco de decision,
3. con que tipo de caso suele empezar,
4. con que tipo de resultado suele terminar,
5. con que otros contextos se relaciona mas,
6. con que otro contexto podria confundirse,
7. donde estan hoy sus principales dudas o hotspots.

## 2. Matriz comparativa

| Bounded context                                   | Objetivo principal                                               | Foco de decision                                                   | Entrada tipica                                                                   | Salida tipica                                                 | Se relaciona mas con              | No confundir con                                        | Hotspots iniciales                                                              |
| ------------------------------------------------- | ---------------------------------------------------------------- | ------------------------------------------------------------------ | -------------------------------------------------------------------------------- | ------------------------------------------------------------- | --------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------------- |
| [[BC-01 - Encuadre administrativo de la novedad]] | Recibir, clasificar y derivar la novedad                         | A que familia pertenece el caso y a donde debe ir                  | Caso recibido desde escuela, area central o integracion                          | Novedad clasificada, observada o derivada                     | BC-02, BC-03, BC-04, BC-05, BC-06 | No confundir con la resolucion profunda del negocio     | autoridad de encuadre, datos minimos, observacion vs rechazo                    |
| [[BC-02 - Interrupciones temporales]]             | Tratar pausas o alteraciones temporales sin cierre definitivo    | Si existe interrupcion valida, su vigencia, su subtipo y su cierre | Licencia, inasistencia, paro o reintegro                                         | Interrupcion aplicada, cerrada o habilitante para otra accion | BC-01, BC-03, BC-06, BC-07        | No confundir con cobertura ni con egreso definitivo     | licencias medicas vs extraordinarias, reintegro, autoridad por subtipo, habilitacion de cobertura |
| [[BC-03 - Cobertura transitoria]]                 | Tratar suplencias y reemplazos temporarios                       | Si corresponde validar, efectivizar, sostener o cerrar una cobertura | Ausencia valida u otra causal de reemplazo temporario                          | Cobertura validada, efectivizada, finalizada o regularizada   | BC-02, BC-01, BC-04, BC-07, BC-08 | No confundir con la licencia ni con ocupacion estable   | causantes validos, toma de posesion, vacante de base vs ocupante vigente, continuidad/conversion con antecedente de suplencia y suplencias derivadas de mayor jerarquia |
| [[BC-04 - Movilidad funcional]]                   | Tratar movimientos de asignacion o destino funcional             | Si el caso es un movimiento, con que subtipo, vigencia y forma de efectivizacion | Traslado transitorio, traslado definitivo, adscripción, comisión, tareas pasivas, permuta, reubicacion o mayor jerarquia | Movimiento validado, efectivizado, finalizado o regularizado | BC-01, BC-03, BC-06, BC-07, BC-08 | No confundir con interrupcion temporal ni con cobertura | separacion entre subtipos normativos, regla general de toma de posesion, doble control en TT e interfaces derivadas del movimiento |
| [[BC-05 - Egresos definitivos]]                   | Tratar cierres definitivos del caso laboral                      | Si el caso cierra definitivamente, con que causal normativa y con que efecto inmediato sobre la plaza | Renuncia aceptada, jubilacion, cese por fallecimiento, cesantia, exoneracion u otra causal legal equivalente | Egreso aplicado con vacancia, cambio de caracter o cierre de plaza explicitados, y dependencias derivadas resueltas u observadas | BC-01, BC-03, BC-06, BC-07, BC-08 | No confundir con interrupcion temporal ni con ocupacion posterior del cargo | familias normativas de egreso, heterogeneidad de efectivizacion, proyeccion residual del egreso e interfaces sobre plaza |
| [[BC-06 - Ocupación estable de cargos]]           | Tratar provisiones estables de cargos                            | Si el caso corresponde a interinato, titularizacion o continuidad/conversion valida sobre vacante real | Vacante real o habilitacion estructural externa ya trazada, adjudicacion valida, resultado de concurso, acto suficiente o antecedente de suplencia con norma habilitante | Ocupacion estable validada, efectivizada, vigente u observada; la toma de posesion local marca la efectivizacion | BC-01, BC-03, BC-04, BC-05, BC-07, BC-08 | No confundir con suplencia ni con movilidad             | estable vs permanente, vacante real, vacante de base vs ocupante vigente, separacion entre validacion rectora y efectivizacion local, nueva toma de posesion y continuidad/conversion con antecedente de suplencia |
| [[BC-07 - Integraciones y conciliación]]          | Traducir ingresos externos al modelo comun y resolver su conciliacion tecnica | Si el ingreso externo tiene correlacion valida, es idempotente, puede mapearse en forma segura y a que contexto debe derivarse | Licencia aprobada, adjudicacion o acto digitalizado, movimiento externo estructurado, reenvio o regularizacion de ingreso | Evento rechazado, observado, conciliado, regularizado o derivado a BC-01 o a un contexto funcional | BC-01, BC-02, BC-03, BC-04, BC-05, BC-06, BC-08 | No confundir con la logica principal del negocio        | confianza por fuente, idempotencia vs reenvio corregido, regla de derivacion directa y contradicciones entre fuente externa y realidad operativa |
| [[BC-08 - Consolidacion y cierre]]                | Decidir criticidad, consolidabilidad, arrastre y salida derivada | Si el caso consolida, bloquea o se arrastra                        | Casos ya tratados por otros contexts                                             | Caso consolidado, observado, arrastrado o bloqueante          | BC-02, BC-03, BC-04, BC-05, BC-06, BC-07 | No confundir con el tratamiento base de cada novedad    | criterios de criticidad, arrastre, explicabilidad del cierre                    |

## 3. Lectura transversal por tipo de problema

> [!info]
> `Vacante` se mantiene como concepto transversal central y no como bounded context propio dentro de novedades laborales. `BC-04` y `BC-05` pueden generar, dejar expresada o transformar una vacante; `BC-06` puede consumirla para provisión estable; `BC-03` no resuelve la vacante de base, sino el reemplazo del ocupante vigente. Ver `[[Concepto transversal - Plaza, estado y vacante]]`.

### 3.1 Contextos de entrada

- [[BC-01 - Encuadre administrativo de la novedad]]
- [[BC-07 - Integraciones y conciliación]]

Su problema principal es hacer entrar un caso al dominio sin mezclar todavía toda su complejidad de negocio.

### 3.2 Contextos de negocio especifico

- [[BC-02 - Interrupciones temporales]]
- [[BC-03 - Cobertura transitoria]]
- [[BC-04 - Movilidad funcional]]
- [[BC-05 - Egresos definitivos]]
- [[BC-06 - Ocupación estable de cargos]]

Su problema principal es resolver familias con reglas de negocio propias.

### 3.3 Contexto de salida administrativa

- [[BC-08 - Consolidación y cierre]]

Su problema principal es decidir como entra cada caso al cierre del periodo sin volver a mezclarlo con la lógica base del negocio.

## 4. Lectura transversal por nivel de complejidad

| Nivel | Contextos | Motivo |
|---|---|---|
| Complejidad inicial baja/media | BC-01, BC-07 | Organizan entrada, mapeo y derivacion |
| Complejidad media/alta | BC-02, BC-03 | Combinan temporalidad, causalidad y relaciones entre casos |
| Complejidad alta | BC-04, BC-05, BC-06 | Requieren fuerte trazabilidad de movimiento, cierre u ocupacion estable |
| Complejidad administrativa transversal | BC-08 | Condensa criterio de criticidad, arrastre y consolidacion |

## 5. Principales zonas de confusion a vigilar

1. mezclar `interrupcion temporal` con `cobertura transitoria`;
2. mezclar `movilidad funcional` con `interrupcion` o `egreso`;
3. mezclar `suplencia` con `interinato` o con `ocupacion estable de cargos`;
4. usar `mayor jerarquia` sin distinguir movimiento rector de efectos derivados en origen y destino;
5. dejar que `integraciones` definan el modelo de negocio;
6. usar `consolidacion y cierre` para reinterpretar lo que no se resolvio antes;
7. hacer que `encuadre administrativo` intente resolver logica especifica que no le corresponde.

## 6. Orden recomendado para profundizar investigacion

La secuencia propuesta coincide con la numeracion actual de los bounded contexts para que el orden de lectura y trabajo no quede ambiguo.

1. [[BC-01 - Encuadre administrativo de la novedad]]
2. [[BC-02 - Interrupciones temporales]]
3. [[BC-03 - Cobertura transitoria]]
4. [[BC-04 - Movilidad funcional]]
5. [[BC-05 - Egresos definitivos]]
6. [[BC-06 - Ocupación estable de cargos]]
7. [[BC-07 - Integraciones y conciliación]]
8. [[BC-08 - Consolidacion y cierre]]

## 7. Siguiente uso recomendado

Esta nota puede usarse como:

1. indice de lectura para entrar a cada bounded context,
2. base para una sesion de event storming mas acotada,
3. apoyo para detectar que preguntas pertenecen a cada contexto y cuales son transversales,
4. companion de `[[Matriz de interfaces - Bounded contexts de novedades laborales]]` cuando el trabajo pase de comparacion general a refinamiento de handoffs semanticos.
