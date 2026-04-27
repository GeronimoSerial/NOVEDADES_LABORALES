---
proyecto: Sistematizacion de Novedades Laborales
tipo: Catalogo tactico
estado: borrador
date: 2026-04-15
tags:
  - sgpem
  - bounded-context
  - encuadre
  - decisiones
  - eventos
---

> [!abstract] Proposito
> Este catalogo baja a nivel tactico el comportamiento de `[[BC-01 - Encuadre administrativo de la novedad]]`. Ordena las decisiones propias de encuadre, los eventos que emite y sus reglas de borde, sin absorber decisiones de negocio que pertenecen a `[[BC-02 - Interrupciones temporales]]`, `[[BC-03 - Cobertura transitoria]]`, `[[BC-04 - Movilidad funcional]]`, `[[BC-05 - Egresos definitivos]]` o `[[BC-06 - Ocupacion estable de cargos]]`.

> [!tip]
> Para ver estos comportamientos como casos de negocio, consultar `[[Escenarios BDD - BC-01 - Encuadre administrativo de la novedad]]`.

## 1. Alcance

- la unidad semántica central es la `novedad` en su etapa de entrada,
- el problema rector es `encuadrar` y `derivar`, no resolver el negocio profundo,
- el catalogo describe admision, preclasificacion, evaluacion de completitud, observacion, rechazo, clasificacion y derivacion,
- la salida esperada es un caso semanticamente listo para que otro contexto tome su propia decision.

## 2. Unidad semantica central

- `caso recibido`,
- `origen de la novedad`,
- `pregunta dominante del caso`,
- `familia funcional`,
- `datos minimos iniciales`,
- `evidencia minima de admision`,
- `pendiente`,
- `observada`,
- `clasificada`,
- `derivada`,
- `rechazada`.

## 3. Antecedentes de entrada

> [!info]
> Estos antecedentes no definen por si solos la resolucion del caso. Solo habilitan o condicionan el encuadre inicial.

| Antecedente de entrada | Significado dentro de `BC-01 - Encuadre administrativo de la novedad` | Datos minimos esperados | Lo que no decide aqui |
| --- | --- | --- | --- |
| Ingreso local o central | Permite iniciar el caso con lectura administrativa basica | origen, actor que informa, agente o referencia equivalente, cargo/plaza o referencia equivalente, fecha basica, motivo inicial | la resolucion funcional profunda |
| Ingreso por integracion externa | Puede habilitar preclasificacion o derivacion automatica si es confiable y consistente | huella externa, fuente, correlacion de agente y cargo/plaza, fecha basica, evidencia externa valida | la decision final del contexto funcional de destino |
| Reingreso por conciliacion o regularizacion | Reabre lectura de encuadre para casos que vuelven con nueva evidencia | referencia del caso previo, evidencia complementaria, motivo de reingreso, estado previo | rehacer historia funcional fuera de las reglas de cada contexto |

## 4. Decisiones propias

### 4.1 Admitir novedad recibida

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si el caso puede ingresar al dominio como `caso recibido` |
| Precondiciones | ingreso identificable y vinculable a un actor/origen |
| Datos minimos | origen, actor que informa, descripcion inicial |
| Reglas | toda novedad debe poder registrarse al menos como ingreso inicial para evitar perdida de trazabilidad |
| Resultado | la novedad queda recibida para encuadre |
| Evento emitido | `Novedad recibida` |

### 4.2 Preclasificar familia funcional tentativa

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si el caso permite una lectura temprana de familia por `pregunta dominante` |
| Precondiciones | caso recibido y descripcion inicial legible |
| Datos minimos | motivo o descripcion inicial, agente o referencia equivalente, cargo/plaza o referencia equivalente, fecha basica |
| Reglas | la preclasificacion es tentativa y no habilita por si sola una derivacion automatica |
| Resultado | el caso queda con familia tentativa o sigue sin preclasificacion segura |
| Evento emitido | `Novedad preclasificada` cuando aplica |

### 4.3 Evaluar completitud minima

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si el caso tiene piso minimo para clasificar y eventualmente derivar |
| Precondiciones | caso recibido o preclasificado |
| Datos minimos | origen, actor que informa, agente o referencia equivalente, establecimiento/unidad, fecha basica, motivo inicial, cargo/plaza o referencia equivalente |
| Reglas | faltante simple de completitud no elimina el caso: lo deja `pendiente` |
| Resultado | completitud suficiente o caso `pendiente` |
| Evento emitido | `Novedad pendiente de completitud` cuando aplica |

### 4.4 Evaluar consistencia de encuadre

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si hay contradicciones o ambiguedades que impiden una clasificacion confiable |
| Precondiciones | completitud minima alcanzada o evidencia complementaria en evaluacion |
| Datos minimos | relato del caso, evidencia disponible, cruces basicos de agente/cargo/plaza/fecha |
| Reglas | duda relevante o contradiccion de encuadre lleva a `observada`; contradiccion fuerte puede llevar a `rechazada` |
| Resultado | caso consistente, `observada` o `rechazada` segun gravedad |
| Evento emitido | `Novedad observada por inconsistencia o duda de encuadre` cuando aplica |

### 4.5 Clasificar familia funcional confirmada

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | A que familia pertenece el caso segun su pregunta dominante |
| Precondiciones | caso no rechazado, con completitud y consistencia suficientes |
| Datos minimos | pregunta dominante explicitada, evidencia minima valida, datos base completos |
| Reglas | la clasificacion se decide por pregunta dominante y no por rotulo administrativo aislado |
| Resultado | caso `clasificada` para una familia funcional |
| Evento emitido | `Novedad clasificada` |

### 4.6 Derivar al bounded context funcional

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si el caso ya puede enviarse al contexto que lo resolvera |
| Precondiciones | novedad clasificada y umbral de handoff alcanzado |
| Datos minimos | familia destino, datos minimos del handoff, evidencia minima preservada, trazabilidad del origen |
| Reglas | el emisor entrega un caso semanticamente listo, no un caso ya resuelto por el receptor |
| Resultado | caso `derivada` a `BC-02`, `BC-03`, `BC-04`, `BC-05` o `BC-06` |
| Evento emitido | `Novedad derivada a contexto funcional` |

### 4.7 Rechazar por imposibilidad de encuadre

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si el caso no puede admitirse ni sostenerse dentro de las reglas minimas de entrada |
| Precondiciones | causal terminal identificada |
| Datos minimos | causal de rechazo trazable y evidencia de respaldo |
| Reglas | rechazo terminal, como minimo, para `fuera de dominio`, `sin agente`, `sin cargo/plaza` o `contradiccion fuerte` |
| Resultado | caso `rechazada` |
| Evento emitido | `Novedad rechazada por imposibilidad de encuadre` |

### 4.8 Reingresar caso con evidencia complementaria

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si un caso `pendiente` u `observada` puede retomar su curso con nueva evidencia |
| Precondiciones | caso previo trazable y evidencia complementaria valida |
| Datos minimos | referencia del caso previo, evidencia agregada, estado anterior, motivo de reingreso |
| Reglas | el reingreso no borra historia; agrega trazabilidad y habilita nueva evaluacion de completitud/consistencia |
| Resultado | el caso vuelve a evaluacion y puede terminar clasificado, seguir pendiente, observado o rechazado |
| Evento emitido | `Novedad recibida` (nuevo ingreso vinculado) |

## 5. Eventos propios

| Evento | Significado semantico | Decision que lo emite | Estado o hito que inaugura |
| --- | --- | --- | --- |
| `Novedad recibida` | El caso ingreso a BC-01 para encuadre administrativo inicial | Admitir novedad recibida | inicio de tratamiento en entrada |
| `Novedad preclasificada` | Existe familia tentativa por lectura temprana | Preclasificar familia funcional tentativa | preclasificacion |
| `Novedad pendiente de completitud` | Faltan datos o evidencia simple para avanzar | Evaluar completitud minima | estado pendiente |
| `Novedad observada por inconsistencia o duda de encuadre` | El caso tiene ambiguedad o contradiccion relevante de encuadre | Evaluar consistencia de encuadre | estado observado |
| `Novedad clasificada` | La familia funcional quedo confirmada | Clasificar familia funcional confirmada | estado clasificada |
| `Novedad derivada a contexto funcional` | El caso fue enviado a un contexto funcional de destino | Derivar al bounded context funcional | estado derivada |
| `Novedad rechazada por imposibilidad de encuadre` | El caso no puede admitirse por causal terminal | Rechazar por imposibilidad de encuadre | estado rechazada |

## 6. Eventos salientes de interfaz

| Evento saliente | Destino | Significado semantico | Decision que lo emite |
| --- | --- | --- | --- |
| `Caso encuadrado para interrupcion temporal` | [[BC-02 - Interrupciones temporales]] | La pregunta dominante pasa a `interrumpir temporalmente` | Derivar al bounded context funcional |
| `Caso encuadrado para cobertura transitoria` | [[BC-03 - Cobertura transitoria]] | La pregunta dominante pasa a `cubrir temporalmente` | Derivar al bounded context funcional |
| `Caso encuadrado para movilidad funcional` | [[BC-04 - Movilidad funcional]] | La pregunta dominante pasa a `mover o reasignar` | Derivar al bounded context funcional |
| `Caso encuadrado para egreso definitivo` | [[BC-05 - Egresos definitivos]] | La pregunta dominante pasa a `cerrar definitivamente` | Derivar al bounded context funcional |
| `Caso encuadrado para ocupacion estable` | [[BC-06 - Ocupacion estable de cargos]] | La pregunta dominante pasa a `ocupar establemente` | Derivar al bounded context funcional |

## 7. Estados centrales y resultados

### 7.1 Estados centrales

1. `recibida`
2. `preclasificada`
3. `pendiente`
4. `observada`
5. `clasificada`
6. `derivada`
7. `rechazada`

### 7.2 Resultados esperados

1. `derivada` a contexto funcional correcto
2. `pendiente` por faltante simple
3. `observada` por duda/contradiccion relevante
4. `rechazada` por causal terminal

## 8. Transiciones minimas

| Situacion inicial | Decision | Resultado |
| --- | --- | --- |
| ingreso identificable | Admitir novedad recibida | `recibida` |
| `recibida` | Preclasificar familia funcional tentativa | `preclasificada` (si aplica) |
| `recibida` o `preclasificada` | Evaluar completitud minima | sigue evaluacion o `pendiente` |
| `recibida` o `preclasificada` | Evaluar consistencia de encuadre | sigue evaluacion, `observada` o `rechazada` |
| caso no rechazado con piso suficiente | Clasificar familia funcional confirmada | `clasificada` |
| `clasificada` | Derivar al bounded context funcional | `derivada` + evento saliente segun destino |
| cualquier estado evaluable con causal terminal | Rechazar por imposibilidad de encuadre | `rechazada` |
| `pendiente` u `observada` con nueva evidencia | Reingresar caso con evidencia complementaria | vuelve a evaluacion de encuadre |

## 9. Reglas de borde

1. `BC-01 - Encuadre administrativo de la novedad` clasifica y deriva; no resuelve el negocio funcional profundo.
2. No hay derivacion sin familia funcional inferible por `pregunta dominante`.
3. Si faltan datos minimos o evidencia simple, el caso queda `pendiente`.
4. Si hay inconsistencia o ambiguedad relevante, el caso queda `observada`.
5. El rechazo es terminal, como minimo, para `fuera de dominio`, `sin agente`, `sin cargo/plaza` o `contradiccion fuerte`.
6. Un `solo relato sin respaldo` no alcanza para clasificar o derivar definitivamente.
7. Si ese relato proviene de origen confiable, con datos minimos completos y sin contradiccion fuerte, el caso puede quedar `pendiente`.
8. Si ademas hay faltante critico o contradiccion fuerte, corresponde `rechazada`.
9. La preclasificacion automatica solo vale con origen confiable, datos minimos completos, evidencia valida y ausencia de contradicciones.
10. Ante duda semantica en ingreso externo, el caso no debe saltar por intuicion: debe pasar por encuadre explicito en BC-01.
11. El reingreso por evidencia complementaria agrega trazabilidad; no borra historia del estado previo.

## 10. Pendientes residuales

- precisar reglas finas por subtipo para automatizacion segura en interrupciones temporales,
- definir umbrales operativos de `contradiccion fuerte` por familia,
- cerrar criterios de autoridad final por familia cuando hay conflicto entre origen confiable y evidencia local,
- revisar si algunos rechazos terminales deben derivar a un circuito de excepcion administrativo separado.
