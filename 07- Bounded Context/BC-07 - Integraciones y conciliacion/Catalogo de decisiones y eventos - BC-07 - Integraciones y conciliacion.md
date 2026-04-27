---
proyecto: Sistematizacion de Novedades Laborales
tipo: Catalogo tactico
estado: borrador
date: 2026-04-27
tags:
  - sgpem
  - bounded-context
  - integraciones
  - conciliacion
  - decisiones
  - eventos
---

# Catalogo de decisiones y eventos - BC-07 - Integraciones y conciliacion

> [!abstract] Proposito
> Este catalogo baja a nivel tactico el comportamiento de `[[BC-07 - Integraciones y conciliación]]`. Ordena las decisiones propias del ingreso externo, sus eventos y sus reglas de borde, sin absorber la resolucion de negocio que corresponde a `[[BC-01 - Encuadre administrativo de la novedad]]`, `[[BC-03 - Cobertura transitoria]]` u otros bounded contexts funcionales.

> [!tip]
> Para ver estos comportamientos como casos de negocio, consultar `[[Escenarios BDD - BC-07 - Integraciones y conciliacion]]`.

## 1. Alcance

- la unidad semantica central es el `ingreso externo` vuelto legible dentro del dominio comun,
- este primer corte valida el contexto con un caso prioritario: `designacion suplente`,
- el catalogo cubre recepcion, traduccion, correlacion, deteccion de duplicados, deteccion de reenvios corregidos, observacion y derivacion,
- en este corte, la salida normal de una `designacion suplente` nueva es `[[BC-01 - Encuadre administrativo de la novedad]]`, no `[[BC-03 - Cobertura transitoria]]` en forma directa,
- si una correccion externa llega despues de que el ingreso ya fue procesado, la regla base es `observar y reencolar`, no autoactualizar.

## 2. Unidad semantica central

- `ingreso externo`,
- `tipo de fuente`,
- `huella externa`,
- `traduccion al lenguaje canonico`,
- `designacion suplente normalizada`,
- `correlacion minima`,
- `duplicado tecnico`,
- `reenvio corregido`,
- `observacion de ingreso`,
- `evidencia externa preservada`,
- `ingreso normalizado para encuadre`,
- `ingreso reencolado para revision`.

## 3. Tipos de ingreso priorizados en este corte

> [!info]
> Este primer corte no intenta cubrir todo el universo de integraciones. Fija un patron reusable y lo prueba con `designacion suplente` como primer tipo priorizado.

| Tipo de ingreso externo | Significado dentro de `BC-07 - Integraciones y conciliación` | Datos minimos esperados | Lo que no decide aqui |
| --- | --- | --- | --- |
| `designacion suplente` nueva | ingreso externo legible que puede iniciar un circuito de encuadre hacia cobertura | tipo de fuente, huella externa, agente, cargo/plaza, fecha desde o efectiva, soporte o evidencia externa | si la cobertura corresponde, si queda validada y si puede efectivizarse |
| `designacion suplente` duplicada | reenvio identico de un ingreso ya absorbido | misma huella externa o misma identidad semantica del ingreso ya procesado | reabrir el caso por el solo hecho del reenvio |
| `designacion suplente` corregida | nueva version de un ingreso previo con diferencia relevante de datos | referencia al ingreso previo, huella externa o version, campos corregidos, evidencia asociada | modificar automaticamente el caso funcional ya tratado |

## 4. Decisiones propias

### 4.1 Admitir ingreso externo recibido

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si el ingreso externo puede registrarse para ser procesado dentro de `BC-07` |
| Precondiciones | origen identificable y huella externa o referencia equivalente utilizable |
| Datos minimos | tipo de fuente, huella externa o referencia equivalente, evidencia basica recibida |
| Reglas | todo ingreso externo debe poder registrarse para evitar perdida de trazabilidad, aun antes de quedar vuelto legible |
| Resultado | el ingreso externo queda recibido para traduccion y correlacion |
| Evento emitido | `Evento externo recibido` |

### 4.2 Rechazar técnicamente ingreso externo

| Campo               | Contenido                                                                                                                                                                                                |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Pregunta de negocio | Si el ingreso debe rechazarse antes de cualquier lectura semántica                                                                                                                                       |
| Precondiciones      | falla tecnica terminal identificable                                                                                                                                                                     |
| Datos minimos       | evidencia del fallo, tipo de fuente, referencia disponible                                                                                                                                               |
| Reglas              | corresponde solo para fallas terminales de integridad minima, no para dudas de negocio; si el ingreso puede leerse pero contradice la realidad operativa, corresponde `observacion` y no rechazo tecnico |
| Resultado           | el ingreso queda rechazado tecnicamente                                                                                                                                                                  |
| Evento emitido      | `Evento externo rechazado tecnicamente`                                                                                                                                                                  |

### 4.3 Volver legible una designación suplente

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si el ingreso externo puede traducirse al lenguaje canonico como `designacion suplente` |
| Precondiciones | ingreso externo recibido y evidencia suficiente para lectura semantica basica |
| Datos minimos | tipo de fuente, huella externa, agente propuesto, cargo/plaza, fecha desde o efectiva, soporte o evidencia externa |
| Reglas | en este corte la fuente se modela en forma agnostica con `tipo de fuente`; la traduccion no valida todavia cobertura ni toma de posesion; el resultado debe ser legible para encuadre sin imponer la taxonomia externa como modelo rector |
| Resultado | el ingreso queda vuelto legible como `designacion suplente` o debe pasar a observacion |
| Evento emitido | `Ingreso externo vuelto legible` |

### 4.4 Resolver correlación mínima

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si el ingreso externo ya habla de algo suficientemente correlacionable dentro del dominio |
| Precondiciones | ingreso vuelto legible |
| Datos minimos | agente, cargo/plaza, fecha desde o efectiva, huella externa, evidencia preservada |
| Reglas | el umbral minimo de este corte exige `agente + cargo/plaza + fecha`; si falta alguno de esos tres elementos o existe ambiguedad fuerte, el caso no debe seguir |
| Resultado | correlacion resuelta o ingreso observado por ambiguedad o contradiccion |
| Eventos emitidos | `Correlacion resuelta` o `Evento externo observado por contradiccion o ambiguedad` |

### 4.5 Detectar duplicado tecnico

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si el ingreso externo ya fue absorbido antes sin diferencia relevante |
| Precondiciones | ingreso recibido o vuelto legible, correlacion suficiente cuando haga falta |
| Datos minimos | huella externa, tipo de fuente, agente, cargo/plaza, fecha, version si existe |
| Reglas | `duplicado tecnico` y `reenvio corregido` no son lo mismo; si el ingreso no agrega diferencia relevante, no debe reabrir el flujo |
| Resultado | el ingreso queda marcado como duplicado tecnico |
| Evento emitido | `Duplicado detectado` |

### 4.6 Detectar reenvio corregido

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si el ingreso externo representa una nueva version corregida de algo ya procesado |
| Precondiciones | existe referencia previa trazable y diferencia relevante identificable |
| Datos minimos | huella externa o version, referencia al ingreso previo, campos corregidos, evidencia comparativa |
| Reglas | en este corte, una correccion externa de una designacion ya procesada no autoactualiza el caso funcional; la regla base es `observar y reencolar` para nueva revision de encuadre |
| Resultado | el ingreso queda identificado como reenvio corregido y reencolado para revision |
| Eventos emitidos | `Reenvio corregido detectado`, `Ingreso reencolado para revision` |

### 4.7 Observar contradicción o ambigüedad del ingreso

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si el ingreso externo debe frenarse por contradiccion con la realidad operativa o por ambiguedad semantica |
| Precondiciones | traduccion o correlacion en evaluacion |
| Datos minimos | evidencia externa, datos correlacionados, contradiccion o ambiguedad identificada |
| Reglas | cuando la fuente externa contradice la realidad operativa local, la regla base es `observar y frenar`; no corresponde derivar con observacion silenciosa ni dejar que la fuente externa prevalezca por si sola |
| Resultado | el ingreso queda observado hasta revision posterior |
| Evento emitido | `Evento externo observado por contradiccion o ambiguedad` |

### 4.8 Derivar ingreso normalizado a `BC-01 - Encuadre administrativo de la novedad`

| Campo | Contenido |
| --- | --- |
| Pregunta de negocio | Si el ingreso externo ya puede entregarse a encuadre administrativo sin perder trazabilidad |
| Precondiciones | ingreso vuelto legible, correlacion resuelta, sin contradiccion bloqueante |
| Datos minimos | tipo de fuente, huella externa, agente, cargo/plaza, fecha desde o efectiva, tipo de hecho interpretado, evidencia preservada |
| Reglas | en este corte, la `designacion suplente` nueva pasa siempre por `BC-01`, aun cuando la familia funcional parezca altamente inferible; `BC-07` entrega un ingreso legible, no una cobertura resuelta |
| Resultado | el ingreso queda normalizado para encuadre |
| Evento emitido | `Ingreso externo normalizado para encuadre` |

## 5. Eventos propios

| Evento | Significado semantico | Decision que lo emite | Estado o hito que inaugura |
| --- | --- | --- | --- |
| `Evento externo recibido` | El ingreso externo entro a `BC-07` para ser procesado | Admitir ingreso externo recibido | inicio de trazabilidad de integracion |
| `Evento externo rechazado tecnicamente` | El ingreso no pudo admitirse por falla tecnica terminal | Rechazar tecnicamente ingreso externo | rechazo tecnico |
| `Ingreso externo vuelto legible` | El ingreso ya puede leerse en lenguaje canonico | Volver legible una designacion suplente | legibilidad del ingreso |
| `Correlacion resuelta` | El ingreso ya fue vinculado con suficiente seguridad al dominio | Resolver correlacion minima | correlacion resuelta |
| `Duplicado detectado` | El ingreso no agrega novedad respecto de uno ya absorbido | Detectar duplicado tecnico | cierre por duplicado |
| `Reenvio corregido detectado` | El ingreso representa una version corregida del caso previo | Detectar reenvio corregido | nueva revision requerida |
| `Evento externo observado por contradiccion o ambiguedad` | El ingreso no puede seguir por contradiccion o ambiguedad relevante | Resolver correlacion minima u Observar contradiccion o ambiguedad del ingreso | estado observado |
| `Ingreso externo normalizado para encuadre` | El ingreso ya puede pasar a `BC-01` para su encuadre formal | Derivar ingreso normalizado a `BC-01 - Encuadre administrativo de la novedad` | salida a encuadre |
| `Ingreso reencolado para revision` | Un reenvio corregido vuelve a circuito para nueva lectura | Detectar reenvio corregido | reingreso controlado |

## 6. Eventos salientes de interfaz

| Evento saliente | Destino | Significado semantico | Decision que lo emite |
| --- | --- | --- | --- |
| `Ingreso externo normalizado para encuadre` | [[BC-01 - Encuadre administrativo de la novedad]] | La designacion suplente externa ya es legible y correlacionable para su lectura administrativa | Derivar ingreso normalizado a `BC-01 - Encuadre administrativo de la novedad` |
| `Ingreso reencolado para revision` | [[BC-01 - Encuadre administrativo de la novedad]] | Una correccion externa vuelve a circuito para nueva evaluacion | Detectar reenvio corregido |

## 7. Estados centrales y resultados

### 7.1 Estados centrales

1. `recibido`
2. `legible`
3. `correlacionado`
4. `observado`
5. `derivado`

### 7.2 Resultados laterales

1. `rechazado tecnicamente`
2. `duplicado`
3. `reencolado para revision`

## 8. Transiciones minimas

| Situacion inicial | Decision | Resultado |
| --- | --- | --- |
| ingreso externo identificable | Admitir ingreso externo recibido | `recibido` |
| `recibido` con falla terminal | Rechazar tecnicamente ingreso externo | `rechazado tecnicamente` |
| `recibido` | Volver legible una designacion suplente | `legible` |
| `legible` con correlacion suficiente | Resolver correlacion minima | `correlacionado` |
| `legible` con contradiccion o ambiguedad | Resolver correlacion minima | `observado` |
| ingreso correlacionable ya absorbido | Detectar duplicado tecnico | `duplicado` |
| ingreso con diferencia relevante sobre otro previo | Detectar reenvio corregido | `reencolado para revision` |
| `correlacionado` sin contradiccion bloqueante | Derivar ingreso normalizado a `BC-01 - Encuadre administrativo de la novedad` | `derivado` + `Ingreso externo normalizado para encuadre` |

## 9. Reglas de borde

1. `BC-07 - Integraciones y conciliación` traduce, correlaciona y deriva; no valida coberturas ni registra tomas de posesion.
2. En este primer corte, la fuente externa se modela en forma agnostica con `tipo de fuente` y no se ata a un modulo concreto.
3. El umbral minimo de correlacion es `agente + cargo/plaza + fecha`.
4. Si falta alguno de esos elementos o existe contradiccion fuerte, corresponde `observar y frenar`.
5. `Duplicado tecnico` y `reenvio corregido` deben distinguirse expresamente.
6. La `designacion suplente` nueva pasa siempre por `BC-01 - Encuadre administrativo de la novedad` antes de llegar a `BC-03 - Cobertura transitoria`.
7. Una correccion externa de un ingreso ya procesado no autoactualiza el caso funcional; se observa y reencola para revision.
8. `BC-07` preserva evidencia, huella externa y trazabilidad para que el contexto receptor pueda decidir su propio negocio.
9. Si el ingreso externo no puede leerse con seguridad como `designacion suplente`, no debe forzarse una familia funcional por intuición.

## 10. Pendientes residuales

- precisar si algunos ingresos maduros podrán derivar en futuros cortes en forma directa a `BC-03 - Cobertura transitoria`,
- cerrar taxonomia fina de `tipo de fuente` y de `soporte suficiente` sin volverla dependiente de un proveedor puntual,
- decidir en que condiciones una evidencia externa tardia puede ir directo a un caso ya abierto de cobertura sin pasar nuevamente por encuadre.
