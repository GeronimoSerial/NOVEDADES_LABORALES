---
proyecto: Sistematizacion de Novedades Laborales
tipo: Matriz de interfaces
estado: vigente
date: 2026-04-09
tags:
  - sgpem
  - bounded-context
  - cobertura
  - suplencias
  - interfaces
  - handoff
  - novedades-laborales
---

# Matriz de interfaces - Bounded contexts de novedades laborales

> [!abstract] Proposito
> Esta nota baja un primer nivel de detalle sobre las interfaces entre bounded contexts del dominio de novedades laborales. No define contratos tecnicos ni payloads definitivos. Define handoffs semanticos: cuando un contexto debe entregar un caso a otro, con que precondiciones minimas, con que datos minimos y con que riesgo principal de confusion.

## 1. Alcance

Esta matriz sirve para refinar bordes entre contexts sin convertir la etapa de diseño en una discusión prematura de eventos técnicos, tablas o APIs.

Queda explicitamente fuera de esta nota:

- el modelado de comandos o endpoints,
- el payload tecnico exacto de eventos,
- la administracion estructural completa de POF,
- la logica interna exhaustiva de cada subtipo normativo,
- la liquidacion posterior.

## 2. Reglas de lectura

1. La interfaz se define por la `pregunta dominante` del caso y no por el nombre administrativo del codigo.
2. El contexto emisor no entrega un caso `resuelto por el receptor`; entrega un caso `semantica y administrativamente listo` para que el receptor tome su propia decision.
3. `Datos minimos` significa umbral semantico de handoff, no contrato tecnico final.
4. Si falta informacion minima para el handoff, el caso no debe saltar de contexto por intuicion.
5. `BC-07` puede acelerar el aterrizaje de un caso, pero no bypassar las reglas centrales del contexto de destino.
6. `BC-08` recibe resultados de contexts funcionales ya tratados; no reemplaza su negocio base.

## 3. Campos semánticos recurrentes

Estos campos aparecen una y otra vez en varias interfaces. Conviene tratarlos como vocabulario canónico transversal.

| Campo semantico | Uso transversal |
| --- | --- |
| Agente | Identifica a quien afecta la novedad |
| Cargo o plaza | Referencia funcional minima del caso |
| Ocupante vigente | Permite distinguir reemplazo del ocupante respecto de vacante estructural |
| Vacante de base | Permite separar estructura de plaza respecto de la prestacion actual |
| Subtipo normativo | Evita derivaciones ambiguas |
| Causante | Explica por que otra accion queda habilitada |
| Fecha desde / hasta / efectiva | Ordena vigencia, cierre y efectivizacion |
| Acto, soporte o evidencia | Permite validar o al menos admitir el handoff |
| Origen y destino funcional | Son criticos en movilidad |
| Toma de posesion | Marca efectivizacion en coberturas, movimientos y ocupaciones estables |
| Efecto sobre plaza | Expresa vacante resultante, persistente, cambio de caracter o cierre |

## 4. Matriz prioritaria de interfaces

| Origen | Destino | Disparador de handoff | Precondiciones minimas | Datos minimos del handoff | Evento saliente semantico | Riesgo principal de confusion |
| --- | --- | --- | --- | --- | --- | --- |
| [[BC-01 - Encuadre administrativo de la novedad]] | [[BC-02 - Interrupciones temporales]] | La pregunta dominante del caso pasa a ser `interrumpir temporalmente` una prestacion sin cierre definitivo | Familia inferible, datos minimos de ingreso completos, sin contradiccion bloqueante | agente, cargo/plaza, subtipo tentativo, fecha basica, origen, evidencia minima, descripcion del hecho | caso encuadrado para interrupcion temporal | confundir interrupcion con cobertura o con movilidad |
| [[BC-01 - Encuadre administrativo de la novedad]] | [[BC-03 - Cobertura transitoria]] | La pregunta dominante del caso pasa a ser `cubrir temporalmente` una ausencia o un reemplazo | Reemplazo como problema rector, referencia funcional suficiente, familia inferible; si proviene de `designacion suplente` externa, `docente reemplazado` identificable y sin contradiccion bloqueante | agente propuesto si existe, docente reemplazado o referencia equivalente, cargo/plaza, fecha desde, origen, soporte inicial y, si proviene de `BC-07`, referencia de designacion, establecimiento, fecha hasta cuando exista, toma de posesion declarada y observaciones documentales de origen | caso encuadrado para cobertura transitoria | mezclar suplencia con interinato u ocupacion estable |
| [[BC-01 - Encuadre administrativo de la novedad]] | [[BC-04 - Movilidad funcional]] | La pregunta dominante del caso pasa a ser `mover o reasignar funcionalmente` | Movimiento reconocible, origen y destino identificables o al menos inferibles, soporte suficiente | agente, origen funcional, destino funcional, subtipo tentativo, fecha desde, acto o respaldo, origen del caso | caso encuadrado para movilidad funcional | absorber movilidad como licencia o como egreso |
| [[BC-01 - Encuadre administrativo de la novedad]] | [[BC-05 - Egresos definitivos]] | La pregunta dominante del caso pasa a ser `cerrar definitivamente` el vinculo laboral | Causal de egreso reconocible, fecha efectiva o trazable, soporte suficiente | agente, cargo/plaza, causal tentativa, fecha efectiva o prevista, soporte, origen del caso | caso encuadrado para egreso definitivo | usar baja o cese como rotulo sin causal normativa real |
| [[BC-01 - Encuadre administrativo de la novedad]] | [[BC-06 - Ocupación estable de cargos]] | La pregunta dominante del caso pasa a ser `ocupar establemente` un cargo | Lectura clara de ocupacion estable, vacante o habilitacion estructural trazable, soporte suficiente, sin confundir validacion con efectivizacion | agente, cargo/plaza, vacante real o habilitacion, subtipo tentativo, fuente habilitante, fecha prevista | caso encuadrado para ocupacion estable | mezclar interinato con suplencia o con movilidad de destino |
| [[BC-02 - Interrupciones temporales]] | [[BC-03 - Cobertura transitoria]] | Una interrupcion validada habilita reemplazo temporario | Interrupcion valida, vigencia clara, causal habilitante para cobertura, referencia del reemplazado | agente interrumpido, cargo/plaza, subtipo de interrupcion, fecha desde/hasta, causante valido, referencia del ocupante vigente | cobertura habilitada por interrupcion valida | asumir que toda interrupcion habilita cobertura |
| [[BC-03 - Cobertura transitoria]] | [[BC-06 - Ocupación estable de cargos]] | La cobertura pierde su base temporal y aparece una vacante real con norma habilitante para provision estable | Vacante real o persistente apta, trazabilidad de la suplencia antecedente, regla normativa de continuidad o conversion y validacion rectora identificable cuando corresponda | agente ocupante actual, plaza/cargo, causante original, antecedente de suplencia, vacante apta, soporte normativo, validacion rectora cuando corresponda, nueva toma de posesion requerida | caso derivable a ocupacion estable desde cobertura antecedente | convertir una suplencia en interinato por mera continuidad material |
| [[BC-04 - Movilidad funcional]] | [[BC-03 - Cobertura transitoria]] | Un movimiento funcional genera como efecto derivado una suplencia o reemplazo en origen o destino | Movimiento rector validado, efecto derivado identificable, referencia del cargo afectado | agente movido, subtipo de movimiento, cargo base o destino afectado, fecha desde, acto del movimiento, necesidad de cobertura derivada | cobertura derivada de movimiento funcional | perder la rectoria de movilidad y modelar todo como suplencia |
| [[BC-04 - Movilidad funcional]] | [[BC-06 - Ocupación estable de cargos]] | El movimiento deja de ser solo desplazamiento y aparece una decision propia de provision estable sobre cargo habilitado | Movimiento validado, vacante o cargo habilitado identificable, acto o resultado que funda la ocupacion estable, separabilidad de la decision y validacion rectora identificable cuando corresponda | agente, cargo de destino, subtipo de movimiento, acto base, vacante o habilitacion, fecha prevista o efectiva, validacion rectora cuando corresponda, toma de posesion si corresponde | caso derivable a ocupacion estable desde movilidad | absorber la provision estable dentro del movimiento rector |
| [[BC-05 - Egresos definitivos]] | [[BC-03 - Cobertura transitoria]] | El egreso definitivo del ocupante vigente deja sin causante o sin trazabilidad una cobertura existente | Egreso aplicado, cobertura dependiente identificada, impacto sobre ocupante vigente explicitado | agente saliente, cargo/plaza, causal y fecha del egreso, cobertura dependiente, ocupante vigente afectado, estado posterior de la vacante de base | revision o cierre de cobertura por egreso definitivo | sostener una suplencia solo porque la plaza sigue vacante |
| [[BC-05 - Egresos definitivos]] | [[BC-06 - Ocupación estable de cargos]] | El egreso deja una vacante resultante o persistente apta para provision estable | Egreso aplicado, efecto sobre plaza trazado, vacante apta identificada, sin confundir con cierre del caso previo | cargo/plaza, agente saliente, causal y fecha del egreso, efecto sobre plaza, vacante apta, restricciones o caracter de vacante | vacante derivable a ocupacion estable tras egreso | mezclar cierre del caso saliente con apertura automatica de una nueva provision |
| [[BC-07 - Integraciones y conciliación]] | [[BC-01 - Encuadre administrativo de la novedad]] | El ingreso externo ya es legible y debe pasar por encuadre comun antes del contexto funcional | Ingreso vuelto legible, correlacion suficiente, sin contradiccion bloqueante; en este corte, una `designacion suplente` nueva pasa siempre por `BC-01` | fuente externa, huella externa, agente correlacionado, cargo/plaza, fecha desde o efectiva, tipo de hecho interpretado, soporte informado, evidencia preservada y trazabilidad de si es ingreso nuevo, duplicado o reenvio corregido | ingreso externo normalizado para encuadre | dejar que la taxonomia externa defina el bounded context |
| [[BC-07 - Integraciones y conciliación]] | [[BC-02 - Interrupciones temporales]] | El ingreso externo describe en forma inequivoca una interrupcion temporal | Mapeo canonico seguro, correlacion suficiente, sin contradiccion bloqueante | fuente externa, agente, cargo/plaza, subtipo, vigencia, soporte externo, huella externa | interrupcion externa derivada en forma directa | confiar en el origen y saltarse validaciones internas |
| [[BC-07 - Integraciones y conciliación]] | [[BC-03 - Cobertura transitoria]] | Un caso de cobertura ya abierto requiere evidencia externa adicional o regularizacion minima de trazabilidad | Cobertura existente identificada, problema principal todavia acotado a ingreso o correlacion, sin reinterpretacion profunda del negocio | huella externa, caso de cobertura relacionado, agente, cargo/plaza, evidencia adicional, historial de reenvios o correcciones | evidencia externa conciliada para cobertura existente | desplazar a BC-07 una regularizacion que ya es negocio propio de cobertura |
| [[BC-07 - Integraciones y conciliación]] | [[BC-04 - Movilidad funcional]] | El ingreso externo describe en forma inequivoca un movimiento funcional | Mapeo seguro, origen y destino correlacionables, acto o soporte suficiente | fuente externa, agente, origen, destino, subtipo, vigencia, soporte, huella externa | movimiento externo derivado en forma directa | aceptar movimiento sin trazabilidad real de origen y destino |
| [[BC-07 - Integraciones y conciliación]] | [[BC-05 - Egresos definitivos]] | El ingreso externo describe en forma inequivoca un cierre definitivo | Mapeo seguro, causal suficientemente trazable, fecha efectiva correlacionada | fuente externa, agente, cargo/plaza, causal, fecha efectiva, soporte, huella externa | egreso externo derivado en forma directa | absorber como definitivo un hecho todavia no firme |
| [[BC-07 - Integraciones y conciliación]] | [[BC-06 - Ocupación estable de cargos]] | El ingreso externo describe en forma inequivoca una provision estable | Mapeo seguro, vacante o habilitacion correlacionada, acto suficiente y sin confundir validacion con efectivizacion | fuente externa, agente, cargo/plaza, subtipo estable, vacante o habilitacion, fecha prevista, soporte, huella externa | ocupacion estable derivada en forma directa | convertir adjudicacion o concurso en ocupacion efectivizada sin toma de posesion |
| [[BC-02 - Interrupciones temporales]] | [[BC-08 - Consolidacion y cierre]] | La interrupcion ya fue resuelta, observada o cerrada para el periodo | Caso funcionalmente tratado, criticidad evaluable, trazabilidad minima preservada | caso, agente, cargo/plaza, resultado del tratamiento, vigencia, observaciones, evidencia relevante | resultado de interrupcion listo para consolidacion | reabrir el negocio base en cierre |
| [[BC-03 - Cobertura transitoria]] | [[BC-08 - Consolidacion y cierre]] | La cobertura ya fue validada, efectivizada, finalizada u observada | Caso funcionalmente tratado, toma de posesion y vigencia trazadas cuando corresponda | caso, agente, cargo/plaza, causante, estado de cobertura, toma de posesion, observaciones, evidencia relevante | resultado de cobertura listo para consolidacion | usar cierre administrativo para corregir cobertura mal modelada |
| [[BC-04 - Movilidad funcional]] | [[BC-08 - Consolidacion y cierre]] | El movimiento ya fue validado, efectivizado, finalizado u observado | Origen y destino trazados, estado del movimiento claro, evidencia preservada | caso, agente, origen, destino, subtipo, estado, fechas, toma de posesion si aplica, observaciones | resultado de movilidad listo para consolidacion | usar BC-08 para decidir subtipo de movimiento |
| [[BC-05 - Egresos definitivos]] | [[BC-08 - Consolidacion y cierre]] | El egreso ya fue aplicado u observado con sus efectos inmediatos trazados | Causal, fecha y efecto sobre plaza suficientemente expresados | caso, agente, cargo/plaza, causal, fecha efectiva, efecto sobre plaza, observaciones, evidencia relevante | resultado de egreso listo para consolidacion | esconder faltantes de causal o fecha dentro del cierre de periodo |
| [[BC-06 - Ocupación estable de cargos]] | [[BC-08 - Consolidacion y cierre]] | La ocupacion estable ya fue validada, efectivizada, observada o regularizada | Base habilitante clara, vacante trazada, toma de posesion registrada si corresponde | caso, agente, cargo/plaza, subtipo estable, vacante o habilitacion, toma de posesion, estado, observaciones | resultado de ocupacion estable listo para consolidacion | consolidar ocupaciones no efectivizadas |
| [[BC-07 - Integraciones y conciliación]] | [[BC-08 - Consolidacion y cierre]] | Se requiere trazabilidad complementaria de ingreso para explicar el cierre | Caso funcional ya resuelto y necesidad administrativa de explicabilidad adicional | huella externa, fuente, correlacion, historial de reenvios, evidencia asociada | trazabilidad externa preservada para consolidacion | dejar que el historial de integracion reemplace el resultado del caso funcional |

## 5. Reglas refinadas por interfaz critica

### 5.1 BC-02 -> BC-03

- No toda interrupcion habilita cobertura.
- La pregunta correcta no es `hubo ausencia`, sino `hubo ausencia habilitante para reemplazo`.
- El causante debe quedar trazado como antecedente vivo de la cobertura.
- Si desaparece el causante, la cobertura debe revisarse o cerrarse.

### 5.2 BC-03 -> BC-06

- Una suplencia antecedente no se transforma en interinato por sola continuidad material.
- Debe existir vacante real o vacante persistente apta para provision estable.
- Debe existir norma o regla habilitante para continuidad o conversion.
- Cuando la apertura estable sale por resolucion ministerial, la validacion rectora corresponde a `Direccion de nivel`.
- La nueva ocupacion estable exige nueva toma de posesion local en este nivel de diseno.

### 5.3 BC-04 -> BC-03

- El movimiento sigue siendo el caso rector.
- La suplencia derivada solo absorbe el efecto de reemplazo.
- `Mayor jerarquia` es el caso mas visible, pero no el unico.

### 5.4 BC-04 -> BC-06

- No toda llegada a destino es ocupacion estable.
- La movilidad explica desplazamiento; `BC-06` solo entra cuando hay una decision separable de provision estable.
- La vacante o habilitacion estructural debe estar trazada, no inferida por costumbre operativa.

### 5.5 BC-05 -> BC-03

- El egreso definitivo del ocupante vigente puede destruir el sustento de una cobertura activa.
- La persistencia de la vacante de base no mantiene por si sola la suplencia.
- Debe quedar explicitado si la cobertura se cierra, se revisa o se deriva.

### 5.6 BC-05 -> BC-06

- El egreso no abre por si solo una nueva ocupacion estable.
- Primero debe quedar expresado el efecto sobre plaza: vacante resultante, persistente, cambio de caracter o cierre.
- Solo despues puede evaluarse si la vacante queda apta para provision estable.

### 5.7 BC-07 -> destino funcional

- La confianza de origen no reemplaza la decision del receptor.
- Si hay ambiguedad semantica, `BC-07` debe frenar y enviar a `BC-01`.
- En este corte, una `designacion suplente` nueva no deriva directo a `BC-03`: pasa siempre por `BC-01`.
- La integracion directa solo vale cuando el mapping canonico es suficientemente seguro.

### 5.8 BC-07 -> BC-03

- `BC-07` puede completar trazabilidad de una cobertura abierta sin volverse duenio de la cobertura.
- Si la correccion requerida redefine causante, vigencia o cierre de la cobertura, el problema vuelve a `BC-03`.
- La regularizacion minima de ingreso no debe ocultar coberturas huerfanas o mal sostenidas.

## 6. Datos minimos sugeridos por familia de handoff

### 6.1 Para handoffs hacia interrupciones temporales

- agente,
- cargo o plaza,
- subtipo de interrupcion,
- fecha desde y, si existe, hasta,
- soporte minimo,
- origen del caso.

### 6.2 Para handoffs hacia cobertura transitoria

- agente reemplazante, si ya existe,
- docente reemplazado o referencia funcional equivalente,
- cargo o plaza,
- causante valido,
- fecha desde,
- si el caso proviene de integracion externa, referencia de designacion, establecimiento, fecha hasta cuando exista, toma de posesion declarada y observaciones documentales de origen,
- evidencia de toma de posesion cuando ya exista.

### 6.3 Para handoffs hacia movilidad funcional

- agente,
- origen funcional,
- destino funcional,
- subtipo de movimiento,
- vigencia,
- acto o soporte suficiente,
- necesidad de toma de posesion cuando corresponda.

### 6.4 Para handoffs hacia egresos definitivos

- agente,
- cargo o plaza afectada,
- causal normativa,
- fecha efectiva,
- soporte o acto suficiente,
- efecto inmediato sobre plaza si ya es conocido.

### 6.5 Para handoffs hacia ocupacion estable de cargos

- agente,
- cargo o plaza,
- subtipo estable,
- vacante real o habilitacion estructural externa,
- fuente habilitante,
- validacion rectora cuando corresponda,
- fecha prevista o efectiva,
- toma de posesion local cuando ya exista o deba exigirse.

### 6.6 Para handoffs hacia consolidacion y cierre

- identificador del caso,
- resultado del tratamiento,
- observaciones pendientes,
- evidencia minima preservada,
- trazabilidad suficiente para explicar consolidacion, arrastre o bloqueo.

## 7. Zonas de atencion inmediata

1. `Cese de...` no siempre significa `BC-05`; muchas veces es cierre de cobertura o de movilidad.
2. `Mayor jerarquia` debe leerse como movimiento rector con posibles efectos derivados.
3. `Interino` y `suplente` no deben compartir automaticamente el mismo bounded context aunque usen circuito administrativo parecido.
4. `Vacante de base` y `ocupante vigente` deben viajar explicitamente en interfaces sensibles.
5. `Toma de posesion` debe leerse como umbral de efectivizacion y no como simple dato accesorio.
6. `Afectacion` y `cese de afectacion` se leen como movilidad funcional transitoria dentro de `[[BC-04 - Movilidad funcional]]`.

## 8. Uso recomendado

- usar esta nota junto con `[[01 - Mapa y clasificacion]]`,
- usar `[[01 - Mapa y clasificacion]]` para validar mapeo de motivos administrativos y familias funcionales contra cada contexto,
- usarla como base para una siguiente nota de `eventos canonicos` o `contratos semanticos por handoff`,
- validarla contra el catalogo real de motivos de ingreso y egreso antes de bajar a diseno tecnico.

# BC-03 - Cobertura transitoria

> [!abstract] Proposito
> Este bounded context reune las novedades cuyo sentido principal es cubrir temporalmente una ausencia o sostener un reemplazo habilitado por un causante transitorio. Su problema central no es la interrupcion original, sino decidir si corresponde una suplencia, con que trazabilidad debe sostenerse y como se efectiviza, mantiene, cierra o regulariza sin perder el vinculo con el causante.

## 1. Por que es un bounded context separado

Aunque muchas veces se mezclan con licencias, las suplencias responden a otra lógica. Su pregunta principal no es `que interrumpio el caso`, sino `si existe una causa habilitante suficiente para reemplazar`, `a quien se reemplaza`, `desde cuando puede efectivizarse` y `hasta cuando debe sostenerse`.

## 2. Qué incluye

- suplencia por licencia o interrupción validada,
- suplencia derivada de mayor jerarquia,
- referencia al causante o motivo habilitante,
- validacion administrativa de la cobertura,
- efectivizacion de la cobertura por toma de posesión,
- vigencia y cierre de la cobertura,
- regularizacion excepcional de coberturas inconsistentes.

## 3. Qué no incluye

- la definición profunda de la licencia o interrupción original,
- la ocupación estable de cargos,
- la movilidad funcional,
- los egresos definitivos,
- la consolidación y criticidad final,
- la integración técnica con sistemas externos.

## 4. Lenguaje ubicuo base

- `cobertura transitoria` es el genero funcional de este bounded context; `suplencia` es su figura mas visible,
- `causante` es el hecho habilitante vivo que permite abrir, sostener o revisar una cobertura,
- en este nivel de diseno, los causantes validos provienen de `[[BC-02 - Interrupciones temporales]]` o de `[[BC-04 - Movilidad funcional]]`,
- `docente reemplazado` es el termino principal del contexto cuando la persona reemplazada puede individualizarse con claridad,
- `ocupante vigente` es un concepto transversal relacionado, útil para consistencia con plaza, vacante e interfaces, pero no reemplaza al termino rector propio de este contexto,
- ==`referencia funcional equivalente` es una excepción restringida y solo puede usarse cuando no puede individualizarse con suficiente claridad al docente reemplazado pero si la prestación a cubrir, con justificación explicita,==
- `cobertura solicitada` puede existir aun sin reemplazante identificado,
- `cobertura validada` es la cobertura que ya cuenta con acto o designación suficiente, venga por integración externa o por otra vía trazable,
- `cobertura efectivizada` es el hito que ocurre con la toma de posesión,
- `cobertura vigente` es el estado sostenido que comienza con la toma de posesión,
- `cobertura observada` es el resultado lateral de una validación fallida y no un estado central del ciclo de vida,
- `cobertura finalizada` es el cierre ordinario de la cobertura,
- `regularizacion` es excepcional y acotada a coberturas todavía abiertas pero inconsistentes,
- la cobertura transitoria reemplaza al docente reemplazado o, en forma excepcional, a una referencia funcional equivalente; no cubre la vacante de base,
- cuando la logica temporal deja de dominar y aparece una provision estable habilitada, la salida correcta es una `derivacion por cambio de base` hacia `[[BC-06 - Ocupacion estable de cargos]]`.

> [!tip]
> Para el nivel tactico de este bounded context, ver `[[Catalogo de decisiones y eventos - BC-03 - Cobertura transitoria]]`.

## 5. Subpuntos iniciales

### 5.1 Suplencia por licencia o interrupcion validada

- nace desde un causante ya validado en `[[BC-02 - Interrupciones temporales]]`,
- requiere identificar con claridad al docente reemplazado o, de manera excepcional y justificada, la referencia funcional equivalente,
- la ultima palabra operativa queda en `SGPEM central`.

### 5.2 Suplencia derivada de mayor jerarquia

- nace desde un caso rector de `[[BC-04 - Movilidad funcional]]`,
- su hecho habilitante debe nombrarse aqui como `causante por mayor jerarquia`,
- el movimiento sigue explicandose en `BC-04` y la suplencia solo absorbe el efecto de reemplazo,
- cuando el movimiento se apoya en licencia por mayor jerarquía, la cobertura del cargo de origen es necesariamente suplencia y no interinato,
- la ultima palabra operativa hereda la rectoria de `BC-04`.

### 5.3 Autoridad operativa por subtipo

| Subtipo                                        | Ultima palabra operativa                    | Observaciones                                                                      |
| ---------------------------------------------- | ------------------------------------------- | ---------------------------------------------------------------------------------- |
| Suplencia por licencia o interrupcion validada | SGPEM central                               | El causante puede venir de `BC-02`, pero la decision de cobertura se resuelve aqui |
| Suplencia derivada de mayor jerarquia          | Hereda de `[[BC-04 - Movilidad funcional]]` | `BC-04` sigue siendo el contexto rector del movimiento                             |

### 5.4 Soporte mínimo y efectivizacion

| Subtipo                                        | Soporte minimo obligatorio                                                                                                        | Soporte fuerte adicional                    | Regla de efectivizacion                                                               |
| ---------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- | ------------------------------------------------------------------------------------- |
| Suplencia por licencia o interrupcion validada | Causante validado, docente reemplazado o referencia funcional equivalente justificada, fecha desde y toma de posesion             | Acto o designacion suficiente cuando exista | La cobertura puede validarse antes, pero solo queda efectivizada con toma de posesion |
| Suplencia derivada de mayor jerarquia          | Caso rector valido en `BC-04`, docente reemplazado o referencia funcional equivalente justificada, fecha desde y toma de posesion | Acto administrativo del movimiento rector   | La cobertura derivada no queda efectivizada sin toma de posesion                      |

Una `designacion suficiente` proveniente de una `integracion externa` confiable, como un modulo de designaciones, puede alcanzar para dejar una cobertura `validada`, pero no para considerarla `efectivizada` ni `vigente` sin `toma de posesion` local trazable.

### 5.5 Ciclo de vida de la cobertura

1. cobertura solicitada,
2. cobertura validada,
3. toma de posesión registrada,
4. cobertura efectivizada,
5. cobertura vigente,
6. cobertura finalizada,
7. cobertura regularizada.

==La `regularizacion` no es una subfamilia principal de cobertura, sino una etapa excepcional para corregir una cobertura todavia abierta pero inconsistente.==

==`Cobertura efectivizada` nombra el hito de inicio del ejercicio efectivo; `cobertura vigente` nombra el estado sostenido que empieza con esa toma de posesión.==

## 6. Conceptos principales

- cobertura transitoria,
- suplencia,
- cobertura solicitada,
- causante,
- causante por mayor jerarquia,
- docente reemplazado,
- referencia funcional equivalente,
- cobertura validada,
- designacion suficiente,
- toma de posesión,
- cobertura efectivizada,
- vigencia de cobertura,
- cobertura vigente,
- referencia al caso origen,
- cierre de cobertura,
- inconsistencia de cobertura.

## 7. Eventos principales

- cobertura solicitada,
- cobertura validada,
- cobertura observada por validacion fallida,
- toma de posesion registrada,
- cobertura efectivizada,
- cobertura vigente,
- cobertura finalizada,
- cobertura regularizada.

## 8. Decisiones principales

- si existe causante suficiente y trazable,
- si el causante proviene de `[[BC-02 - Interrupciones temporales]]` o de `[[BC-04 - Movilidad funcional]]`,
- si una cobertura corresponde o no,
- si corresponde una suplencia y no una ocupación estable de cargo,
- si la cobertura solicitada puede existir aun sin reemplazante identificado,
- si el docente reemplazado quedo identificado con claridad,
- si corresponde admitir la excepcion de referencia funcional equivalente con justificacion explicita,
- si la cobertura puede validarse con acto o designacion suficiente y evidencia disponible,
- desde cuando y hasta cuando debe mantenerse segun el subtipo y el causante,
- si la toma de posesión ya permite efectivizar la cobertura e iniciar su vigencia,
- si la cobertura reemplaza al docente reemplazado aunque la plaza siga siendo vacante en sentido estructural,
- si un egreso definitivo del ocupante vigente obliga a cerrar, revisar o derivar la cobertura aunque la plaza siga siendo vacante en sentido estructural,
- si la cobertura quedó desligada indebidamente de su motivo,
- como se regulariza un caso inconsistente todavía abierto,
- si el caso debe derivarse por cambio de base a `[[BC-06 - Ocupacion estable de cargos]]` cuando aparece una vacancia definitiva.

## 9. Reglas centrales de delimitacion

1. **No hay cobertura sin causante válido.**
2. En este nivel de diseño, **todo causante valido de cobertura proviene de `[[BC-02 - Interrupciones temporales]]` o de `[[BC-04 - Movilidad funcional]]`.**
3. **Toda cobertura debe poder explicarse por una causa habilitante trazable.**
4. **Toda cobertura debe poder explicar a que docente reemplazado responde o, de modo excepcional y justificado, que referencia funcional equivalente cubre.**
5. **La cobertura transitoria no cubre la vacante de base; reemplaza una prestación temporalmente ausente vinculada a un docente reemplazado.**
6. **La toma de posesión es requisito de efectivizacion de la cobertura.**
7. La cobertura puede validarse antes, **pero no queda en ejercicio sin toma de posesión**.
8. **Toda cobertura validada debe contar con acto o designación suficiente, provenga por integración externa o por otra vía trazable.**
9. La vigencia de la cobertura depende del subtipo y del causante que la habilita, y **comienza con la toma de posesión**.
10. **Si el causante desaparece, termina o cambia, la cobertura debe revisarse o cerrarse.**
11. **Si el ocupante vigente egresa en forma definitiva, la cobertura asociada debe cerrarse, revisarse o derivarse según corresponda; la persistencia de la vacante de base no la sostiene por si sola.**
12. La cobertura transitoria no debe absorber interinatos, titularizaciones ni otras ocupaciones estables como figuras rectoras.
13. Cuando el causante es una licencia por mayor jerarquía, la cobertura del cargo de origen corresponde exclusivamente a suplencia mientras el agente ausente retenga ese cargo.
14. Si el cargo reemplazado queda vacante en forma definitiva y la norma habilita el pase, el caso puede derivarse por cambio de base a `[[BC-06 - Ocupacion estable de cargos]]` como regla de interfaz.
15. La regularización es excepcional, solo aplica a coberturas todavía abiertas pero inconsistentes y no debe usarse para ocultar coberturas huérfanas.

## 10. Relación con otros bounded contexts

- recibe habilitaciones o resultados desde `[[BC-02 - Interrupciones temporales]]`,
- puede recibir casos directos ya encuadrados desde `[[BC-01 - Encuadre administrativo de la novedad]]`,
- puede recibir derivaciones desde `[[BC-04 - Movilidad funcional]]` **cuando un caso de mayor jerarquía genere suplencia en el cargo de origen**; si existe licencia por mayor jerarquía, ese es el tratamiento obligatorio del cargo de origen,
- puede recibir revisión, cierre o derivación desde `[[BC-05 - Egresos definitivos]]` cuando un egreso definitivo recaiga sobre el ocupante vigente reemplazado o altere la trazabilidad de la cobertura,
- puede derivar excepcionalmente por cambio de base a `[[BC-06 - Ocupacion estable de cargos]]` cuando una suplencia deje de estar dominada por la logica temporal y deba transformarse en interinato u otra ocupación estable,
- entrega novedades consolidadas o observadas a `[[BC-08 - Consolidacion y cierre]]`,
- puede recibir desde `[[BC-07 - Integraciones y conciliación]]` una `designacion suficiente` proveniente de integración externa para validar la cobertura, sin que eso reemplace la `toma de posesion`,
- puede requerir conciliación o regularización desde `[[BC-07 - Integraciones y conciliación]]`.

## 11. Puntos de interfaz relevantes

- reglas sobre ausencia validada,
- acto o designación suficiente como condición de validación,
- designación suficiente proveniente de integración externa confiable como soporte posible de `cobertura validada`,
- licencia por mayor jerarquía como causante posible de suplencia en el cargo base,
- distinción entre vacante de base y ocupante vigente cuando existe interino activo,
- egreso definitivo del ocupante vigente como causal de cierre, revisión o derivación de la cobertura,
- toma de posesión local como requisito de efectivizacion de la cobertura, incluso cuando la designación llegue por integración externa,
- trazabilidad entre cobertura y caso causante,
- trazabilidad entre suplencia antecedente y eventual pase a ocupacion estable cuando exista vacancia definitiva,
- regularizaciones por cierres tardíos o inconsistentes.

## 12. Preguntas abiertas para investigación

- que otras causales institucionales, fuera de `BC-02` y `BC-04`, podrian habilitar cobertura,
- que soporte adicional debe exigirse en casos sin acto o designacion formal explicita,
- que reglas exactas deben impedir superposiciones o coberturas huerfanas,
- que excepciones de vigencia abierta deberian admitirse por normativa.
