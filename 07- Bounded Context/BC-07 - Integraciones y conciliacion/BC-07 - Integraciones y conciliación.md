---
proyecto: Sistematizacion de Novedades Laborales
tipo: Bounded context
estado: borrador
date: 2026-04-06
tags:
  - sgpem
  - bounded-context
  - integraciones
  - conciliacion
---

# BC-07 - Integraciones y conciliación

> [!abstract] Proposito
> Este bounded context reune la entrada de eventos, resultados o evidencias externas y su aterrizaje dentro del dominio comun de novedades laborales. Su problema central no es resolver licencias, coberturas, movilidad, egresos u ocupaciones estables, sino traducir un ingreso externo al lenguaje canonico de SGPEM, correlacionarlo con la realidad interna, conciliar diferencias de entrada y derivarlo sin que la fuente externa deforme el modelo principal.

## 1. Por que es un bounded context separado

Cuando la integración externa se mezcla con la lógica central del dominio, el sistema termina subordinado al contrato tecnico disponible, al formato del proveedor o al circuito administrativo que emitió el dato. Separar este contexto permite que SGPEM conserve su propio lenguaje, haga aterrizar los ingresos externos en un catalogo comun y decida si lo recibido debe rechazarse, observarse, conciliarse, regularizarse o derivarse sin convertir la integracion en una categoría paralela de sistema.

## 2. Que incluye

- recepción de eventos, resultados o actos externos,
- validación técnica mínima del ingreso,
- correlación técnica con agente, cargo, plaza, caso o antecedente,
- control de duplicidad, reenvío e idempotencia,
- mapeo a tipos de novedad y familias canónicas,
- conciliación de diferencias entre fuente externa y trazabilidad interna,
- regularización mínima de desvíos de ingreso, correlación o reenvió,
- decisión de derivación a `[[BC-01 - Encuadre administrativo de la novedad]]` o a un bounded context funcional cuando el mapeo sea seguro,
- auditoria de ingresos, reenvios, rechazos y correcciones externas.

## 3. Que no incluye

- la resolución de negocio profunda de licencias, coberturas, movilidad, egreso u ocupación estable,
- la consolidación y cierre de periodo,
- la definición institucional del catalogo de novedades,
- la administración de sistemas externos,
- la reinterpretacion del caso ya resuelto dentro del contexto funcional,
- la regularizacion profunda de una novedad cuando el problema principal ya sea de negocio y no de ingreso.

## 4. Subproblemas internos

### 4.1 Traducción al lenguaje canonico

- convertir un hecho externo en una pieza legible dentro del dominio común,
- evitar que la fuente externa imponga su propia taxonomía como si fuera el modelo rector,
- distinguir entre lo que llega como dato externo y lo que el dominio puede reconocer como novedad laboral.

El problema aquí no es resolver la novedad, sino volverla comprensible dentro del lenguaje de SGPEM.

### 4.2 Correlación con la realidad interna

- vincular el ingreso externo con agente, plaza, cargo, prestacion, caso o antecedente segun corresponda,
- decidir si la referencia externa alcanza para aterrizar el ingreso sin inventar equivalencias forzadas,
- dejar el caso observado cuando la correlacion sea insuficiente o ambigua.

La correlacion no es una formalidad tecnica aislada: define si el ingreso externo puede hablar de algo real dentro del dominio.

### 4.3 Conciliacion semantica

- distinguir entre diferencia corregible de entrada y contradiccion que ya compromete el negocio,
- tratar duplicados, reenvios, correcciones o versiones de una misma huella externa sin multiplicar indebidamente casos,
- conciliar el ingreso externo con la trazabilidad interna cuando todavia no sea necesario reinterpretar la novedad de fondo.

Si la contradiccion obliga a redefinir licencias, coberturas, movilidad, egresos u ocupaciones estables, el problema ya no pertenece a `BC-07`.

### 4.4 Regularizacion minima de ingreso

- reparar faltantes o errores de entrada,
- completar trazabilidad externa cuando el propio circuito de origen aporta la informacion faltante,
- reabrir el procesamiento solo cuando el problema siga siendo de ingreso, correlacion o huella externa.

La `regularizacion` en `BC-07` es transversal y minima: corrige desvíos de entrada, correlacion y trazabilidad. Si el problema principal ya es validar una licencia, revisar una cobertura, fechar un egreso o decidir una ocupacion estable, la regularizacion debe salir al bounded context funcional correspondiente.

### 4.5 Derivacion y handoff

- derivar a `[[BC-01 - Encuadre administrativo de la novedad]]` cuando el ingreso externo ya es legible pero todavia no alcanza para una familia funcional inequívoca,
- derivar a un bounded context funcional cuando el aterrizaje semantico sea suficiente y no haya contradiccion bloqueante,
- preservar evidencia, referencia externa y trazabilidad sin resolver el negocio que corresponde a destino.

`BC-07` entrega casos traducidos y trazables; no entrega casos funcionalmente resueltos.

> [!note]
> En el primer corte tactico priorizado para este proyecto, la `designacion suplente` externa se normaliza en `BC-07 - Integraciones y conciliación` y pasa por `[[BC-01 - Encuadre administrativo de la novedad]]` antes de llegar a `[[BC-03 - Cobertura transitoria]]`, aun cuando la familia funcional ya resulte altamente inferible. Esta restriccion busca ordenar el ingreso inicial sin bloquear una futura derivacion directa para otros tipos de ingreso suficientemente maduros.

### 4.6 Confianza de origen y autoridad de destino

- una fuente externa puede tener alto peso de confianza inicial,
- esa confianza no reemplaza la autoridad operativa final del bounded context de destino,
- una fuente confiable puede habilitar una derivacion mas directa, pero nunca bypassear reglas de plaza, prestacion, causal o efectivizacion,
- cuando el origen sea estructurado pero semanticamente ambiguo, `BC-07` debe frenar la derivacion directa y devolver el caso a encuadre.

### 4.7 Ciclo de vida del ingreso externo

1. ingreso externo recibido,
2. ingreso externo vuelto legible en el lenguaje comun,
3. correlacion resuelta u observada,
4. conciliacion o regularizacion minima aplicada, cuando corresponda,
5. ingreso derivado,
6. trazabilidad de ingreso cerrada.

## 5. Conceptos principales

- evento externo,
- ingreso externo,
- huella externa,
- correlacion,
- confianza de origen,
- traduccion al lenguaje canonico,
- duplicado tecnico,
- reenvio corregido,
- mapeo canonico,
- conciliacion semantica,
- regularización mínima,
- observación de ingreso,
- trazabilidad de ingreso.

## 6. Eventos principales

- evento externo recibido,
- evento externo rechazado técnicamente,
- ingreso externo vuelto legible,
- correlacion resuelta,
- evento externo observado por contradiccion o ambiguedad,
- duplicado detectado,
- reenvío corregido detectado,
- evento externo conciliado,
- evento externo mapeado a novedad,
- regularizacion aplicada,
- evento externo derivado.

## 7. Decisiones principales

- si un ingreso externo alcanza para volverse legible dentro del catalogo comun,
- si el ingreso ya fue absorbido antes o si corresponde tratarlo como reenvio, correccion o nueva version,
- si la correlacion con agente, plaza, cargo, caso o antecedente es suficiente,
- a que tipo de novedad del catalogo comun y a que bounded context debe mapearse,
- si el ingreso puede derivarse directo al contexto funcional o debe pasar primero por `[[BC-01 - Encuadre administrativo de la novedad]]`,
- si la diferencia detectada es conciliable dentro del ingreso o si ya compromete el negocio del caso,
- si la regularizacion necesaria sigue siendo de entrada y trazabilidad o ya corresponde al contexto funcional de destino,
- que evidencia o trazabilidad debe preservarse para `[[BC-08 - Consolidacion y cierre]]`.

## 8. Reglas centrales de delimitacion

1. Toda integracion externa debe aterrizar en un tipo de novedad del catalogo comun.
2. **Ningun evento externo puede bypassar las reglas centrales del dominio.**
3. Este contexto traduce, correlaciona y concilia; no redefine el negocio principal de cada familia.
4. Todo ingreso externo relevante debe ser auditable, incluyendo rechazos, duplicados, reenvios y correcciones.
5. Si el problema principal es de ingreso, correlacion, duplicidad, reenvio o conciliacion semantica, pertenece aqui.
6. **`Duplicado` y `reenvio corregido` no son lo mismo y no deben colapsarse en una sola respuesta operativa.**
7. La derivacion directa solo es valida cuando el ingreso ya puede leerse en una familia funcional sin contradiccion bloqueante.
8. Si el ingreso externo es legible pero todavia ambiguo para encuadre, `BC-07` no debe forzar la decision de negocio: debe derivar a `[[BC-01 - Encuadre administrativo de la novedad]]`.
9. La regularizacion en `BC-07` se limita a desvíos de ingreso, correlacion o trazabilidad; no absorbe la regularizacion profunda del caso funcional.
10. **La fuente externa puede tener alto peso de confianza inicial sin reemplazar la autoridad operativa final del bounded context de destino.**
11. Si la contradiccion detectada ya exige reinterpretar el caso laboral, la resolucion debe salir del contexto de integracion.

## 9. Relación con otros bounded contexts

- entrega casos normalizados a `[[BC-01 - Encuadre administrativo de la novedad]]` cuando el mapeo aun requiere encuadre,
- puede derivar directamente a `[[BC-02 - Interrupciones temporales]]` en integraciones medicas claras y suficientemente correlacionadas,

- puede aportar evidencia o regularizacion minima a `[[BC-03 - Cobertura transitoria]]` cuando un caso ya abierto requiera trazabilidad externa adicional,
- puede derivar directamente a `[[BC-04 - Movilidad funcional]]`, `[[BC-05 - Egresos definitivos]]` o `[[BC-06 - Ocupacion estable de cargos]]` si el ingreso externo es inequívoco y completo,
- provee trazabilidad adicional a `[[BC-08 - Consolidacion y cierre]]`,
- no reemplaza la resolucion de negocio de `[[BC-02 - Interrupciones temporales]]`, `[[BC-03 - Cobertura transitoria]]`, `[[BC-04 - Movilidad funcional]]`, `[[BC-05 - Egresos definitivos]]` ni `[[BC-06 - Ocupacion estable de cargos]]`.

## 10. Puntos de interfaz relevantes

- modulo externo de licencias medicas,
- modulo externo de designaciones que puede entregar `designacion suficiente` para validar una cobertura sin reemplazar la `toma de posesion` local,
- resultados de concurso, adjudicación, designación o ascenso con fuente externa estructurada,
- actos administrativos digitalizados,
- procesos de conciliacion o regularizacion,
- necesidad de idempotencia e historial de reenvios,
- contradicciones entre fuente externa y realidad operativa,
- dependencia de calidad y completitud de datos externos,
- necesidad de dejar trazabilidad útil para criticidad y consolidación posterior.

## 11. Preguntas abiertas para investigación

- que ingresos externos deben poder convertirse en caso derivable y cuales deben quedar solo como evidencia o antecedente,
- donde termina exactamente la conciliacion de ingreso y donde empieza el encuadre o la resolucion funcional del caso,
- como debe resolverse una contradiccion entre evento externo y realidad operativa cuando ambos lados tienen trazabilidad fuerte,
- que patrones de ingreso justifican derivacion mas directa sin deformar la autoridad del contexto de destino,
- que trazabilidad minima debe preservar `BC-07` para que `[[BC-08 - Consolidacion y cierre]]` pueda explicar el cierre sin reabrir el negocio de fondo.
