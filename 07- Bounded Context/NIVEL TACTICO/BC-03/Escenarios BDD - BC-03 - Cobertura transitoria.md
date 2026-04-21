---
proyecto: Sistematizacion de Novedades Laborales
tipo: Escenarios BDD
estado: borrador
date: 2026-04-14
tags:
  - sgpem
  - bounded-context
  - cobertura
  - bdd
  - suplencias
---

# Escenarios BDD - BC-03 - Cobertura transitoria

> [!abstract] Proposito
> Esta nota expresa el comportamiento esperado de `[[BC-03 - Cobertura transitoria]]` en un lenguaje cercano al negocio. Los escenarios describen como nace, se valida, se efectiviza, se sostiene, se cierra, se regulariza o se deriva una cobertura transitoria sin absorber decisiones que pertenecen a otros bounded contexts.

## 1. Como leer estos escenarios

- el termino rector del contexto es `docente reemplazado`,
- `ocupante vigente` solo aparece cuando hace falta una precision transversal sobre plaza o vacante,
- `cobertura observada` no es un estado central: es el resultado lateral de una validacion fallida,
- `cobertura validada`, `cobertura efectivizada` y `cobertura vigente` nombran momentos distintos,
- `regularizacion` solo aplica a coberturas todavia abiertas pero inconsistentes,
- `cambio de base` no convierte automaticamente la cobertura en ocupacion estable: solo habilita una derivacion a `[[BC-06 - Ocupacion estable de cargos]]`.

> [!tip]
> Estos escenarios bajan a comportamiento el lenguaje ubicuo de `[[BC-03 - Cobertura transitoria]]` y el nivel tactico de `[[Catalogo de decisiones y eventos - BC-03 - Cobertura transitoria]]`.

## 2. Escenarios de admision

### Escenario: Admitir una cobertura solicitada por una interrupcion habilitante

**Dado** un caso de `[[BC-02 - Interrupciones temporales]]` que ya reconoció una interrupción habilitante
Y un `docente reemplazado` claramente identificable
Y una fecha desde trazable para la cobertura
**Cuando** `BC-03 - Cobertura transitoria` admite la solicitud
**Entonces** la cobertura queda en estado `cobertura solicitada`
Y se emite `Cobertura solicitada`

### Escenario: Admitir una cobertura solicitada por causante de mayor jerarquia

**Dado** un caso rector de `[[BC-04 - Movilidad funcional]]` que genero un `causante por mayor jerarquia`
Y un cargo de origen que necesita reemplazo temporal
Y un `docente reemplazado` identificable en ese cargo
**Cuando** `BC-03 - Cobertura transitoria` admite la solicitud
**Entonces** la cobertura queda en estado `cobertura solicitada`
Y el movimiento sigue siendo explicado por `[[BC-04 - Movilidad funcional]]`
Y se emite `Cobertura solicitada`

### Escenario: Admitir una cobertura solicitada aunque todavía no exista reemplazante identificado

**Dado** un `causante` habilitante ya trazado
Y un `docente reemplazado` identificado
Y una necesidad de cobertura temporal ya reconocible
**Cuando** `BC-03 - Cobertura transitoria` admite la solicitud
**Entonces** la cobertura puede quedar como `cobertura solicitada`
Y la falta de reemplazante identificado no impide iniciar el caso

### Escenario: No admitir una cobertura cuando no existe causante identificable

**Dado** una necesidad aparente de reemplazo
Y que no existe un `causante` identificable proveniente de `[[BC-02 - Interrupciones temporales]]` o `[[BC-04 - Movilidad funcional]]`
**Cuando** `BC-03 - Cobertura transitoria` revisa la apertura del caso
**Entonces** la cobertura no puede ser admitida como `cobertura solicitada`
Y el caso debe permanecer fuera del ciclo propio de cobertura transitoria

## 3. Escenarios de validacion

### Escenario: Validar una cobertura con acto o designacion suficiente

**Dado** una `cobertura solicitada`
Y un `causante` externo ya trazado
Y un `docente reemplazado` identificado con claridad
Y un `acto o designacion suficiente`
**Cuando** `BC-03 - Cobertura transitoria` valida la cobertura
**Entonces** la cobertura queda en estado `cobertura validada`
Y se emite `Cobertura validada`

### Escenario: Validar una cobertura con designacion suficiente recibida por integracion externa

**Dado** una `cobertura solicitada`
Y un `causante` externo ya trazado
Y un `docente reemplazado` identificado con claridad
Y una `designacion suficiente` recibida desde un modulo externo de designaciones por una via trazable
**Cuando** `BC-03 - Cobertura transitoria` valida la cobertura
**Entonces** la cobertura queda en estado `cobertura validada`
Y la via de ingreso no cambia el significado de `cobertura validada`
Y se emite `Cobertura validada`

### Escenario: Validar excepcionalmente una cobertura con referencia funcional equivalente justificada

Dado una `cobertura solicitada`
Y un `causante` externo ya trazado
Y que no puede individualizarse con suficiente claridad al `docente reemplazado`
Y una `referencia funcional equivalente` explicitamente justificada
Y un `acto o designacion suficiente`
Cuando `BC-03 - Cobertura transitoria` valida la cobertura
Entonces la cobertura queda en estado `cobertura validada`
Y la `referencia funcional equivalente` queda registrada como excepcion restringida
Y se emite `Cobertura validada`

### Escenario: Observar una cobertura por falta de acto o designacion suficiente

Dado una `cobertura solicitada`
Y un `causante` externo ya trazado
Y un `docente reemplazado` identificado con claridad
Pero no existe `acto o designacion suficiente`
Cuando `BC-03 - Cobertura transitoria` intenta validar la cobertura
Entonces la cobertura no alcanza el estado `cobertura validada`
Y el resultado es `Cobertura observada por validacion fallida`

### Escenario: Observar una cobertura cuando la referencia funcional equivalente no esta justificada

Dado una `cobertura solicitada`
Y un `causante` externo ya trazado
Y que no puede identificarse con claridad al `docente reemplazado`
Y que tampoco existe una justificacion suficiente para usar `referencia funcional equivalente`
Cuando `BC-03 - Cobertura transitoria` intenta validar la cobertura
Entonces la cobertura no alcanza el estado `cobertura validada`
Y el resultado es `Cobertura observada por validacion fallida`

## 4. Escenarios de efectivizacion y vigencia

### Escenario: Efectivizar una cobertura validada al registrarse la toma de posesion

Dado una `cobertura validada`
Y una evidencia suficiente para registrar la `toma de posesion`
Cuando `BC-03 - Cobertura transitoria` efectiviza la cobertura
Entonces queda registrada la `toma de posesion`
Y se emite `Toma de posesion registrada`
Y se emite `Cobertura efectivizada`
Y la cobertura pasa a `cobertura vigente`
Y se emite `Cobertura vigente`

### Escenario: Efectivizar una cobertura validada por designacion externa cuando la escuela confirma la toma de posesion

Dado una `cobertura validada` a partir de una `designacion suficiente` proveniente de integracion externa
Y una confirmacion local de `toma de posesion` mediante codigo o QR vinculable a esa designacion
Cuando `BC-03 - Cobertura transitoria` registra la `toma de posesion`
Entonces la cobertura queda `efectivizada`
Y la cobertura pasa a `cobertura vigente`

### Escenario: No efectivizar una cobertura validada por designacion externa si el codigo o QR no puede vincularse con la toma de posesion

Dado una `cobertura validada` a partir de una `designacion suficiente` proveniente de integracion externa
Y una confirmacion local informada con codigo o QR
Pero ese codigo o QR no puede vincularse de manera trazable con la designacion validada
Cuando `BC-03 - Cobertura transitoria` revisa si corresponde registrar la `toma de posesion`
Entonces la cobertura no puede quedar `efectivizada`
Y la cobertura todavia no puede pasar a `cobertura vigente`

### Escenario: No efectivizar una cobertura validada sin evidencia de toma de posesion

Dado una `cobertura validada`
Pero no existe evidencia suficiente para registrar la `toma de posesion`
Cuando `BC-03 - Cobertura transitoria` revisa si corresponde iniciar el ejercicio efectivo
Entonces la cobertura no puede quedar `efectivizada`
Y la cobertura todavia no puede pasar a `cobertura vigente`

### Escenario: Mantener una cobertura vigente mientras el causante sigue activo

Dado una `cobertura vigente`
Y un `causante` que sigue habilitando el reemplazo
Y una trazabilidad preservada con el `docente reemplazado`
Cuando `BC-03 - Cobertura transitoria` revisa la continuidad del caso
Entonces la cobertura debe seguir como `cobertura vigente`
Y la sola persistencia de la vacante de base no es la razon de esa continuidad

## 5. Escenarios de revision y cierre

### Escenario: Finalizar una cobertura cuando termina la interrupcion que la sostenia

Dado una `cobertura vigente`
Y un `causante` proveniente de `[[BC-02 - Interrupciones temporales]]`
Y que la interrupcion ya no sostiene el reemplazo
Cuando `BC-03 - Cobertura transitoria` revisa la continuidad del caso
Entonces la cobertura debe cerrarse como `cobertura finalizada`
Y se emite `Cobertura finalizada`

### Escenario: Revisar y cerrar una cobertura cuando un egreso definitivo afecta al docente reemplazado

Dado una `cobertura vigente`
Y un `docente reemplazado` cuya situacion cambia por un hecho proveniente de `[[BC-05 - Egresos definitivos]]`
Y que ese hecho rompe el sustento temporal de la cobertura
Cuando `BC-03 - Cobertura transitoria` revisa el caso
Entonces la cobertura no puede sostenerse solo porque la vacante de base siga existiendo
Y la cobertura debe cerrarse como `cobertura finalizada`
Y se emite `Cobertura finalizada`

### Escenario: Revisar una cobertura validada cuando cambia el antecedente externo antes de la toma de posesion

Dado una `cobertura validada`
Y que todavia no se registro la `toma de posesion`
Y que el `causante` cambia, desaparece o deja de ser compatible con el reemplazo
Cuando `BC-03 - Cobertura transitoria` revisa el caso
Entonces la cobertura no puede avanzar a `cobertura efectivizada`
Y el caso debe resolverse por cierre o por nueva decision segun el antecedente vigente

## 6. Escenarios de regularizacion

### Escenario: Regularizar una cobertura abierta inconsistente con trazabilidad recuperable

Dado una cobertura todavia abierta
Y una inconsistencia que no destruye por completo su trazabilidad
Y evidencia suficiente para recomponer lo que falta
Cuando `BC-03 - Cobertura transitoria` regulariza la cobertura
Entonces la cobertura queda `regularizada`
Y se emite `Cobertura regularizada`

### Escenario: No regularizar una cobertura ya finalizada

Dado una `cobertura finalizada`
Y una inconsistencia detectada despues del cierre
Cuando `BC-03 - Cobertura transitoria` revisa si corresponde una `regularizacion`
Entonces la cobertura no debe reabrirse por `regularizacion`
Y el caso requiere otro tratamiento fuera de esta excepcion

### Escenario: No regularizar una cobertura abierta que quedo sin causante trazable

Dado una cobertura todavia abierta
Pero ya no existe `causante` trazable que la sostenga
Cuando `BC-03 - Cobertura transitoria` revisa si corresponde una `regularizacion`
Entonces la `regularizacion` no puede usarse para ocultar una cobertura huerfana
Y el caso debe pasar por revision o cierre segun corresponda

## 7. Escenarios de interfaz

### Escenario: Derivar un caso a ocupacion estable por cambio de base

Dado una cobertura antecedente trazable
Y una vacante real o persistente apta para provision estable
Y una regla habilitante para salir de la logica puramente temporal
Cuando `BC-03 - Cobertura transitoria` decide el `cambio de base`
Entonces se emite `Caso derivado a BC-06 - Ocupacion estable de cargos por cambio de base`
Y el nuevo caso pasa a `[[BC-06 - Ocupacion estable de cargos]]`
Y la cobertura transitoria no se convierte automaticamente en ocupacion estable

### Escenario: No derivar a ocupacion estable por mera continuidad material de la suplencia

Dado una cobertura vigente
Y el mismo agente prestando servicios de manera continua
Pero sin vacante apta o sin regla habilitante para provision estable
Cuando `BC-03 - Cobertura transitoria` revisa si corresponde un `cambio de base`
Entonces la continuidad material por si sola no alcanza para derivar el caso
Y la cobertura debe seguir tratandose como problema de reemplazo temporal o cerrarse si ya no tiene sustento
