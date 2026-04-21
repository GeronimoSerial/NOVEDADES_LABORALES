---
proyecto: Sistematizacion de Novedades Laborales
tipo: Catalogo tactico
estado: borrador
date: 2026-04-14
tags:
  - sgpem
  - bounded-context
  - cobertura
  - decisiones
  - eventos
---

# Catalogo de decisiones y eventos - BC-03 - Cobertura transitoria

> [!abstract] Proposito
> Este catálogo baja a nivel táctico el comportamiento de `[[BC-03 - Cobertura transitoria]]`. Ordena las decisiones propias del contexto, los eventos que emite y sus principales reglas de borde, sin absorber responsabilidades que pertenecen a `[[BC-02 - Interrupciones temporales]]`, `[[BC-04 - Movilidad funcional]]`, `[[BC-05 - Egresos definitivos]]` o `[[BC-06 - Ocupacion estable de cargos]]`.

> [!tip]
> Para ver estos comportamientos expresados como casos de negocio, consultar `[[Escenarios BDD - BC-03 - Cobertura transitoria]]`.

## 1. Alcance

- la unidad semantica central es la `cobertura transitoria`,
- `suplencia` es la figura visible mas frecuente dentro de ese genero,
- el catalogo describe decisiones propias, eventos propios y eventos salientes de interfaz,
- no redefine la validacion del causante externo ni la provision estable del cargo.

## 2. Unidad semantica central

- `cobertura transitoria`,
- `causante` como antecedente externo habilitante ya validado fuera de este contexto,
- `docente reemplazado` como termino rector del contexto,
- `referencia funcional equivalente` como excepcion restringida y justificada,
- `acto o designacion suficiente` como condicion de validacion,
- `toma de posesion` como umbral de efectivizacion,
- `vigencia` como estado sostenido desde la toma de posesion,
- `regularizacion` como correccion excepcional de una cobertura todavia abierta pero inconsistente.

## 3. Antecedentes externos habilitantes

> [!info]
> Estos antecedentes no son eventos propios de `BC-03 - Cobertura transitoria`. Son hechos externos que este contexto debe recibir, referenciar y respetar para decidir sobre la cobertura.

| Antecedente externo habilitante | Contexto rector                       | Significado dentro de `BC-03 - Cobertura transitoria`                                                 | Datos minimos que deben llegar                                                              | Lo que no decide aqui                                                  |
| ------------------------------- | ------------------------------------- | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| Interrupcion habilitante        | [[BC-02 - Interrupciones temporales]] | Habilita la apertura, mantenimiento o cierre de una cobertura asociada a una ausencia temporal        | agente afectado, cargo/plaza, subtipo de interrupción, vigencia, referencia del caso origen | si la interrupcion existe, su subtipo y su validez                     |
| Causante por mayor jerarquia    | [[BC-04 - Movilidad funcional]]       | Habilita la suplencia en el cargo de origen cuando el movimiento rector genera necesidad de reemplazo | agente movido, cargo base, cargo de destino, vigencia, referencia del movimiento origen     | si el movimiento existe, su subtipo y su efectivizacion como movilidad |

> [!note]
> `BC-05 - Egresos definitivos` no habilita la apertura de una cobertura. Puede, en cambio, forzar revision, cierre o salida por interfaz de una cobertura ya existente cuando afecta al docente reemplazado o rompe la trazabilidad del caso.

## 4. Decisiones propias

### 4.1 Admitir solicitud de cobertura

| Campo               | Contenido                                                                                                                                                   |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Pregunta de negocio | Si el caso puede nacer como `cobertura solicitada` dentro de este bounded context                                                                           |
| Precondiciones      | pregunta dominante de reemplazo temporal, cargo/plaza identificable, fecha desde trazable, antecedente externo habilitante identificable                    |
| Datos minimos       | cargo/plaza, fecha desde, antecedente externo, docente reemplazado si ya existe, agente reemplazante si ya existe                                           |
| Reglas              | la solicitud puede existir aun sin reemplazante identificado; la falta de reemplazante no bloquea la solicitud, pero la falta de antecedente habilitante si |
| Resultado           | la cobertura queda admitida como caso en tratamiento                                                                                                        |
| Evento emitido      | `Cobertura solicitada`                                                                                                                                      |

### 4.2 Validar cobertura

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si la cobertura ya cuenta con sustento suficiente para ser tratada como `cobertura validada` |
| Precondiciones | cobertura solicitada, antecedente externo trazado, docente reemplazado identificado o excepcion justificada de referencia funcional equivalente |
| Datos minimos | antecedente externo, docente reemplazado o referencia funcional equivalente justificada, fecha desde, acto o designacion suficiente |
| Reglas | la validacion exige acto o designacion suficiente, provenga por integracion externa o por otra via trazable; una `designacion suficiente` proveniente de un modulo externo de designaciones puede dejar la cobertura `validada`; la referencia funcional equivalente solo vale como excepcion restringida y justificada; validar no equivale a efectivizar |
| Resultado | la cobertura queda validada o resulta observada por validacion fallida |
| Eventos emitidos | `Cobertura validada` o `Cobertura observada por validacion fallida` |

### 4.3 Efectivizar cobertura

| Campo               | Contenido                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Pregunta de negocio | Si la cobertura validada ya puede iniciar ejercicio efectivo                                                                                      |
| Precondiciones      | cobertura validada y evidencia suficiente para registrar la toma de posesion                                                                       |
| Datos minimos       | referencia de la cobertura validada, fecha de toma de posesion, evidencia de la toma de posesion, agente reemplazante, cargo/plaza               |
| Reglas              | la toma de posesion es el umbral de efectivizacion; sin toma de posesion no hay ejercicio efectivo; una cobertura validada por `designacion suficiente` recibida por integracion externa sigue necesitando confirmacion local trazable de toma de posesion; la vigencia comienza con esa toma de posesion |
| Resultado           | la cobertura pasa a ejercicio efectivo y entra en vigencia                                                                                        |
| Eventos emitidos    | `Toma de posesion registrada`, `Cobertura efectivizada`, `Cobertura vigente`                                                                      |

### 4.4 Sostener cobertura vigente

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si la cobertura debe seguir vigente segun el subtipo y el antecedente habilitante |
| Precondiciones | cobertura vigente, antecedente externo todavia compatible, trazabilidad del docente reemplazado preservada |
| Datos minimos | vigencia del antecedente, situacion del docente reemplazado, fecha hasta si existe, estado actual de la cobertura |
| Reglas | la cobertura no se sostiene por la sola persistencia de la vacante de base; debe seguir existiendo base temporal suficiente |
| Resultado | la cobertura permanece vigente o pasa a revision por cambio del antecedente |
| Evento emitido | no agrega un evento canonico nuevo mientras solo se sostenga la misma vigencia |

### 4.5 Revisar cobertura por cambio del antecedente o por egreso del docente reemplazado

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si la cobertura todavia se sostiene cuando cambia el antecedente habilitante o cuando un egreso definitivo afecta al docente reemplazado |
| Precondiciones | cobertura vigente o validada, cambio del antecedente externo o hecho relevante proveniente de `[[BC-05 - Egresos definitivos]]` |
| Datos minimos | referencia de la cobertura, antecedente previo, hecho nuevo, impacto sobre el docente reemplazado y, cuando haga falta precision transversal, sobre el ocupante vigente, fecha del cambio |
| Reglas | la revision no crea un nuevo estado central; desemboca en continuidad, finalizacion o salida por interfaz; un egreso definitivo puede destruir el sustento de la cobertura aunque la vacante de base persista |
| Resultado | la cobertura se sostiene, se finaliza o el caso se deriva por cambio de base |
| Evento emitido | no tiene evento canonico propio; produce uno de los resultados finales del catalogo |

### 4.6 Finalizar cobertura

| Campo               | Contenido                                                                                                                                   |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| Pregunta de negocio | Si corresponde el cierre ordinario de la cobertura                                                                                          |
| Precondiciones      | cobertura vigente o validada con cierre semántico suficientemente trazable                                                                  |
| Datos minimos       | referencia de la cobertura, motivo de finalizacion, fecha efectiva, situacion del antecedente habilitante                                   |
| Reglas              | la finalizacion cierra la cobertura como problema de reemplazo temporal; no debe confundirse con egreso definitivo ni con ocupacion estable |
| Resultado           | la cobertura queda cerrada en su propio bounded context                                                                                     |
| Evento emitido      | `Cobertura finalizada`                                                                                                                      |

### 4.7 Regularizar cobertura abierta inconsistente

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si una cobertura todavia abierta pero inconsistente puede corregirse sin rehacer toda la semantica del caso |
| Precondiciones | cobertura abierta, inconsistencia identificada, posibilidad de recomponer trazabilidad minima |
| Datos minimos | referencia de la cobertura, inconsistencia detectada, evidencia correctiva, fecha o tramo a corregir |
| Reglas | la regularizacion es excepcional y acotada; no debe usarse para ocultar coberturas huerfanas, sin causante o ya finalizadas |
| Resultado | la cobertura queda regularizada o debe salir a revision mas profunda |
| Evento emitido | `Cobertura regularizada` |

### 4.8 Derivar caso a `BC-06 - Ocupacion estable de cargos` por cambio de base

| Campo               | Contenido                                                                                                                                                                         |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Pregunta de negocio | Si la lógica temporal deja de dominar y aparece una decisión separable de provisión estable                                                                                       |
| Precondiciones      | cobertura antecedente trazable, vacante real o persistente apta, regla o norma habilitante, separabilidad respecto de la lógica temporal                                          |
| Datos minimos       | referencia de la cobertura antecedente, docente reemplazante actual, plaza/cargo, antecedente externo original, vacante apta, soporte normativo                                   |
| Reglas              | no hay conversión automática de la cobertura; no alcanza la continuidad material; el nuevo caso estable exige nueva toma de posesión en `[[BC-06 - Ocupacion estable de cargos]]` |
| Resultado           | el caso sale por interfaz hacia provision estable                                                                                                                                 |
| Evento emitido      | `Caso derivado a BC-06 - Ocupacion estable de cargos por cambio de base`                                                                                                          |

## 5. Eventos propios

| Evento | Significado semantico | Decision que lo emite | Estado o hito que inaugura |
| --- | --- | --- | --- |
| `Cobertura solicitada` | El caso ya fue admitido como problema de cobertura temporal | Admitir solicitud de cobertura | inicio del tratamiento propio |
| `Cobertura validada` | La cobertura ya cuenta con acto o designacion suficiente | Validar cobertura | cobertura validada |
| `Cobertura observada por validacion fallida` | La cobertura no pudo validarse con la evidencia disponible | Validar cobertura | resultado lateral de observacion |
| `Toma de posesion registrada` | Quedo registrada la toma de posesion del reemplazante | Efectivizar cobertura | hito previo a la vigencia |
| `Cobertura efectivizada` | La cobertura ya paso a ejercicio efectivo | Efectivizar cobertura | inicio del ejercicio efectivo |
| `Cobertura vigente` | La cobertura entro en estado sostenido de vigencia | Efectivizar cobertura | cobertura vigente |
| `Cobertura finalizada` | La cobertura quedo cerrada como problema temporal | Finalizar cobertura | cierre ordinario |
| `Cobertura regularizada` | La cobertura abierta inconsistente fue corregida | Regularizar cobertura abierta inconsistente | recomposicion excepcional |

## 6. Evento saliente de interfaz

| Evento saliente | Destino | Significado semantico | Decision que lo emite |
| --- | --- | --- | --- |
| `Caso derivado a BC-06 - Ocupacion estable de cargos por cambio de base` | [[BC-06 - Ocupacion estable de cargos]] | El caso deja de estar dominado por la logica temporal y pasa a una decision separable de provision estable | Derivar caso a `BC-06 - Ocupacion estable de cargos` por cambio de base |

## 7. Estados centrales y hitos decisivos

### 7.1 Estados centrales

1. `cobertura solicitada`
2. `cobertura validada`
3. `cobertura vigente`
4. `cobertura finalizada`

### 7.2 Hitos decisivos

1. `toma de posesion registrada`
2. `cobertura efectivizada`
3. `cobertura regularizada`

### 7.3 Resultados laterales

1. `cobertura observada por validacion fallida`
2. `caso derivado a BC-06 - Ocupacion estable de cargos por cambio de base`

## 8. Transiciones minimas

| Situacion inicial | Decision | Resultado |
| --- | --- | --- |
| caso encuadrado como cobertura | Admitir solicitud de cobertura | `cobertura solicitada` |
| `cobertura solicitada` | Validar cobertura | `cobertura validada` |
| `cobertura solicitada` | Validar cobertura | `cobertura observada por validacion fallida` |
| `cobertura validada` | Efectivizar cobertura | `toma de posesion registrada` -> `cobertura efectivizada` -> `cobertura vigente` |
| `cobertura vigente` | Sostener cobertura vigente | sigue `cobertura vigente` |
| `cobertura vigente` o `cobertura validada` | Revisar cobertura por cambio del antecedente o por egreso del docente reemplazado | continuidad, `cobertura finalizada` o salida por interfaz |
| `cobertura vigente` o `cobertura validada` | Finalizar cobertura | `cobertura finalizada` |
| cobertura abierta inconsistente | Regularizar cobertura abierta inconsistente | `cobertura regularizada` |
| cobertura antecedente con cambio de base | Derivar caso a `BC-06 - Ocupacion estable de cargos` por cambio de base | `Caso derivado a BC-06 - Ocupacion estable de cargos por cambio de base` |

## 9. Reglas de borde

1. `BC-03 - Cobertura transitoria` no valida el antecedente externo; lo recibe y lo referencia.
2. No hay `cobertura solicitada` si no existe al menos un antecedente externo habilitante identificable.
3. `Docente reemplazado` es el termino rector del contexto; `ocupante vigente` se usa como precisión transversal cuando hace falta leer plaza y vacante.
4. `Referencia funcional equivalente` solo puede admitirse como excepción restringida y justificada.
5. `Cobertura validada` exige acto o designación suficiente, por integración externa o por otra via trazable.
6. `Cobertura validada` no equivale a `cobertura vigente`.
7. Una `designacion suficiente` proveniente de integracion externa puede validar la cobertura, pero no efectivizarla por si sola.
8. La `toma de posesion` es el umbral de efectivizacion y el inicio de la vigencia.
9. La cobertura no cubre la vacante de base y no se sostiene por la sola persistencia de esa vacante.
10. Si el antecedente cambia, desaparece o queda roto por un egreso definitivo del docente reemplazado, la cobertura debe revisarse o cerrarse; `ocupante vigente` queda como precision transversal cuando haga falta leer plaza y vacante.
11. El `cambio de base` no convierte automaticamente la cobertura en ocupacion estable; solo habilita un nuevo caso por interfaz.
12. La regularizacion no debe usarse para reabrir coberturas ya finalizadas ni para ocultar coberturas sin causante.

## 10. Pendientes residuales

- precisar que variaciones normativas podrían ampliar mas adelante el conjunto de antecedentes externos habilitantes,
- cerrar reglas mas finas de superposición invalida entre coberturas,
- definir con mayor detalle cuando una revisión por egreso definitivo debe terminar en cierre y cuando en salida por interfaz.
