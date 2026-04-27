---
proyecto: Sistematizacion de Novedades Laborales
tipo: Bounded context
estado: borrador
date: 2026-04-06
tags:
  - sgpem
  - bounded-context
  - cobertura
  - suplencias
---

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
- como se regulariza un caso inconsistente todavia abierto,
- si el caso debe derivarse por cambio de base a `[[BC-06 - Ocupacion estable de cargos]]` cuando aparece una vacancia definitiva.

## 9. Reglas centrales de delimitacion

1. **No hay cobertura sin causante valido.**
2. En este nivel de diseno, **todo causante valido de cobertura proviene de `[[BC-02 - Interrupciones temporales]]` o de `[[BC-04 - Movilidad funcional]]`.**
3. **Toda cobertura debe poder explicarse por una causa habilitante trazable.**
4. **Toda cobertura debe poder explicar a que docente reemplazado responde o, de modo excepcional y justificado, que referencia funcional equivalente cubre.**
5. **La cobertura transitoria no cubre la vacante de base; reemplaza una prestacion temporalmente ausente vinculada a un docente reemplazado.**
6. **La toma de posesión es requisito de efectivizacion de la cobertura.**
7. La cobertura puede validarse antes, **pero no queda en ejercicio sin toma de posesión**.
8. **Toda cobertura validada debe contar con acto o designacion suficiente, provenga por integracion externa o por otra via trazable.**
9. La vigencia de la cobertura depende del subtipo y del causante que la habilita, y **comienza con la toma de posesion**.
10. **Si el causante desaparece, termina o cambia, la cobertura debe revisarse o cerrarse.**
11. **Si el ocupante vigente egresa en forma definitiva, la cobertura asociada debe cerrarse, revisarse o derivarse segun corresponda; la persistencia de la vacante de base no la sostiene por si sola.**
12. La cobertura transitoria no debe absorber interinatos, titularizaciones ni otras ocupaciones estables como figuras rectoras.
13. Cuando el causante es una licencia por mayor jerarquia, la cobertura del cargo de origen corresponde exclusivamente a suplencia mientras el agente ausente retenga ese cargo.
14. Si el cargo reemplazado queda vacante en forma definitiva y la norma habilita el pase, el caso puede derivarse por cambio de base a `[[BC-06 - Ocupacion estable de cargos]]` como regla de interfaz.
15. La regularización es excepcional, solo aplica a coberturas todavia abiertas pero inconsistentes y no debe usarse para ocultar coberturas huérfanas.

## 10. Relación con otros bounded contexts

- recibe habilitaciones o resultados desde `[[BC-02 - Interrupciones temporales]]`,
- puede recibir casos directos ya encuadrados desde `[[BC-01 - Encuadre administrativo de la novedad]]`,
- puede recibir derivaciones desde `[[BC-04 - Movilidad funcional]]` **cuando un caso de mayor jerarquía genere suplencia en el cargo de origen**; si existe licencia por mayor jerarquia, ese es el tratamiento obligatorio del cargo de origen,
- puede recibir revision, cierre o derivacion desde `[[BC-05 - Egresos definitivos]]` cuando un egreso definitivo recaiga sobre el ocupante vigente reemplazado o altere la trazabilidad de la cobertura,
- puede derivar excepcionalmente por cambio de base a `[[BC-06 - Ocupacion estable de cargos]]` cuando una suplencia deje de estar dominada por la logica temporal y deba transformarse en interinato u otra ocupación estable,
- entrega novedades consolidadas o observadas a `[[BC-08 - Consolidacion y cierre]]`,
- puede recibir desde `[[BC-07 - Integraciones y conciliación]]` una `designacion suficiente` proveniente de integracion externa para validar la cobertura, sin que eso reemplace la `toma de posesion`,
- puede requerir conciliación o regularización desde `[[BC-07 - Integraciones y conciliación]]`.

## 11. Puntos de interfaz relevantes

- reglas sobre ausencia validada,
- acto o designacion suficiente como condicion de validacion,
- designacion suficiente proveniente de integracion externa confiable como soporte posible de `cobertura validada`,
- licencia por mayor jerarquia como causante posible de suplencia en el cargo base,
- distincion entre vacante de base y ocupante vigente cuando existe interino activo,
- egreso definitivo del ocupante vigente como causal de cierre, revision o derivacion de la cobertura,
- toma de posesion local como requisito de efectivizacion de la cobertura, incluso cuando la designacion llegue por integracion externa,
- trazabilidad entre cobertura y caso causante,
- trazabilidad entre suplencia antecedente y eventual pase a ocupacion estable cuando exista vacancia definitiva,
- regularizaciones por cierres tardíos o inconsistentes.

## 12. Preguntas abiertas para investigación

- que otras causales institucionales, fuera de `BC-02` y `BC-04`, podrian habilitar cobertura,
- que soporte adicional debe exigirse en casos sin acto o designacion formal explicita,
- que reglas exactas deben impedir superposiciones o coberturas huerfanas,
- que excepciones de vigencia abierta deberian admitirse por normativa.
