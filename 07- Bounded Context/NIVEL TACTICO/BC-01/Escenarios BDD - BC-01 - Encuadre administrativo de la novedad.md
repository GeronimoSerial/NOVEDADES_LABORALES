---
proyecto: Sistematizacion de Novedades Laborales
tipo: Escenarios BDD
estado: borrador
date: 2026-04-15
tags:
  - sgpem
  - bounded-context
  - encuadre
  - bdd
  - derivacion
---

# Escenarios BDD - BC-01 - Encuadre administrativo de la novedad

> [!abstract] Proposito
> Esta nota describe el comportamiento esperado de `[[BC-01 - Encuadre administrativo de la novedad]]` con lenguaje de negocio. Los escenarios cubren caminos normales, caminos alternativos, excepciones y resultados no felices de encuadre, para que la derivacion a los contextos funcionales ocurra con trazabilidad y sin mezclar responsabilidades.

## 1. Como leer estos escenarios

- la pregunta rectora es `que es este caso y a donde debe ir`,
- `BC-01` no resuelve el negocio profundo de la novedad: solo encuadra y deriva,
- `pendiente` expresa faltante simple de completitud,
- `observada` expresa contradiccion o duda relevante de encuadre,
- `rechazada` expresa imposibilidad terminal de encuadre,
- `solo relato sin respaldo` no alcanza para clasificar o derivar definitivamente,
- con origen confiable, datos minimos completos y sin contradiccion fuerte, un caso con solo relato puede quedar `pendiente`.

> [!tip]
> Estos escenarios aterrizan el nivel tactico de `[[Catalogo de decisiones y eventos - BC-01 - Encuadre administrativo de la novedad]]`.

## 2. Escenarios de recepcion y preclasificacion

### Escenario: Recibir una novedad con identificacion administrativa basica

**Dado** un ingreso de novedad con origen y actor que informa
Y una descripcion inicial del hecho
**Cuando** `BC-01 - Encuadre administrativo de la novedad` admite el ingreso
**Entonces** el caso queda como `recibida`
Y se emite `Novedad recibida`

### Escenario: Preclasificar una familia funcional por pregunta dominante

**Dado** una `novedad recibida`
Y una descripcion suficientemente legible del problema principal
Y datos base de agente y cargo/plaza o referencia equivalente
**Cuando** `BC-01 - Encuadre administrativo de la novedad` preclasifica el caso
**Entonces** la familia funcional queda marcada como tentativa
Y se emite `Novedad preclasificada`

### Escenario: No preclasificar cuando la pregunta dominante sigue ambigua

**Dado** una `novedad recibida`
Y una descripcion inicial que no permite distinguir con claridad la familia funcional
**Cuando** `BC-01 - Encuadre administrativo de la novedad` intenta preclasificar
**Entonces** el caso no debe forzar una familia tentativa
Y debe seguir en evaluacion de completitud y consistencia

## 3. Escenarios de completitud y consistencia

### Escenario: Dejar pendiente una novedad por faltante simple de completitud

**Dado** una `novedad recibida`
Y que falta un dato minimo no terminal, como fecha basica o establecimiento
**Cuando** `BC-01 - Encuadre administrativo de la novedad` evalua completitud minima
**Entonces** el caso queda `pendiente`
Y se emite `Novedad pendiente de completitud`

### Escenario: Observar una novedad por contradiccion relevante de encuadre

**Dado** una `novedad recibida` con datos minimos completos
Y evidencia que sugiere dos familias funcionales incompatibles para el mismo hecho
**Cuando** `BC-01 - Encuadre administrativo de la novedad` evalua consistencia de encuadre
**Entonces** el caso queda `observada`
Y se emite `Novedad observada por inconsistencia o duda de encuadre`

### Escenario: Tratar como pendiente un relato sin respaldo con origen confiable

**Dado** una `novedad recibida` con solo relato sin acto administrativo ni evento externo
Y un origen confiable
Y datos minimos completos
Y ausencia de contradiccion fuerte
**Cuando** `BC-01 - Encuadre administrativo de la novedad` evalua si puede avanzar
**Entonces** el caso no debe clasificarse ni derivarse definitivamente
Y el caso queda `pendiente`
Y se emite `Novedad pendiente de completitud`

### Escenario: Rechazar un relato sin respaldo con faltante critico

**Dado** una `novedad recibida` con solo relato sin respaldo
Y faltante critico de `agente` o `cargo/plaza`
**Cuando** `BC-01 - Encuadre administrativo de la novedad` evalua consistencia minima de admision
**Entonces** el caso queda `rechazada`
Y se emite `Novedad rechazada por imposibilidad de encuadre`

## 4. Escenarios de rechazo terminal

### Escenario: Rechazar una novedad fuera de dominio

**Dado** una `novedad recibida`
Y que el hecho no pertenece al dominio de novedades laborales
**Cuando** `BC-01 - Encuadre administrativo de la novedad` decide su admision funcional
**Entonces** el caso queda `rechazada`
Y se emite `Novedad rechazada por imposibilidad de encuadre`

### Escenario: Rechazar una novedad sin agente ni referencia equivalente valida

**Dado** una `novedad recibida`
Y ausencia de `agente` o de referencia equivalente utilizable
**Cuando** `BC-01 - Encuadre administrativo de la novedad` valida datos minimos de entrada
**Entonces** el caso queda `rechazada`
Y se emite `Novedad rechazada por imposibilidad de encuadre`

### Escenario: Rechazar una novedad sin cargo/plaza ni referencia funcional equivalente

**Dado** una `novedad recibida`
Y ausencia de `cargo/plaza` y de referencia funcional equivalente
**Cuando** `BC-01 - Encuadre administrativo de la novedad` valida datos minimos de entrada
**Entonces** el caso queda `rechazada`
Y se emite `Novedad rechazada por imposibilidad de encuadre`

### Escenario: Rechazar una novedad por contradiccion fuerte

**Dado** una `novedad recibida`
Y una contradiccion fuerte en datos esenciales de agente, cargo/plaza o fecha
**Cuando** `BC-01 - Encuadre administrativo de la novedad` evalua consistencia de encuadre
**Entonces** el caso queda `rechazada`
Y se emite `Novedad rechazada por imposibilidad de encuadre`

## 5. Escenarios de clasificacion y derivacion

### Escenario: Clasificar por pregunta dominante y derivar a interrupciones temporales

**Dado** una novedad con pregunta dominante de `interrumpir temporalmente`
Y completitud y consistencia suficientes
**Cuando** `BC-01 - Encuadre administrativo de la novedad` clasifica y deriva
**Entonces** se emite `Novedad clasificada`
Y se emite `Novedad derivada a contexto funcional`
Y se emite `Caso encuadrado para interrupcion temporal` hacia `[[BC-02 - Interrupciones temporales]]`

### Escenario: Clasificar por pregunta dominante y derivar a cobertura transitoria

**Dado** una novedad con pregunta dominante de `cubrir temporalmente`
Y completitud y consistencia suficientes
**Cuando** `BC-01 - Encuadre administrativo de la novedad` clasifica y deriva
**Entonces** se emite `Novedad clasificada`
Y se emite `Novedad derivada a contexto funcional`
Y se emite `Caso encuadrado para cobertura transitoria` hacia `[[BC-03 - Cobertura transitoria]]`

### Escenario: Clasificar por pregunta dominante y derivar a movilidad funcional

**Dado** una novedad con pregunta dominante de `mover o reasignar`
Y completitud y consistencia suficientes
**Cuando** `BC-01 - Encuadre administrativo de la novedad` clasifica y deriva
**Entonces** se emite `Novedad clasificada`
Y se emite `Novedad derivada a contexto funcional`
Y se emite `Caso encuadrado para movilidad funcional` hacia `[[BC-04 - Movilidad funcional]]`

### Escenario: Clasificar por pregunta dominante y derivar a egresos definitivos

**Dado** una novedad con pregunta dominante de `cerrar definitivamente`
Y completitud y consistencia suficientes
**Cuando** `BC-01 - Encuadre administrativo de la novedad` clasifica y deriva
**Entonces** se emite `Novedad clasificada`
Y se emite `Novedad derivada a contexto funcional`
Y se emite `Caso encuadrado para egreso definitivo` hacia `[[BC-05 - Egresos definitivos]]`

### Escenario: Clasificar por pregunta dominante y derivar a ocupacion estable

**Dado** una novedad con pregunta dominante de `ocupar establemente`
Y completitud y consistencia suficientes
**Cuando** `BC-01 - Encuadre administrativo de la novedad` clasifica y deriva
**Entonces** se emite `Novedad clasificada`
Y se emite `Novedad derivada a contexto funcional`
Y se emite `Caso encuadrado para ocupacion estable` hacia `[[BC-06 - Ocupacion estable de cargos]]`

### Escenario: No derivar cuando la familia sigue no inferible

**Dado** una novedad con datos parciales o ambiguos
Y sin pregunta dominante claramente inferible
**Cuando** `BC-01 - Encuadre administrativo de la novedad` revisa derivacion
**Entonces** el caso no debe derivarse por intuicion
Y el caso debe continuar como `pendiente` u `observada` segun corresponda

## 6. Escenarios de automatizacion y fallback

### Escenario: Derivar automaticamente cuando se cumple la regla base de automatizacion

**Dado** una novedad con origen confiable
Y familia inferible por pregunta dominante
Y datos minimos completos
Y evidencia valida
Y ausencia de contradicciones
**Cuando** `BC-01 - Encuadre administrativo de la novedad` aplica la regla de automatizacion
**Entonces** el caso puede clasificarse y derivarse automaticamente al contexto funcional correspondiente

### Escenario: Frenar automatizacion y pasar a revision humana por contradiccion

**Dado** una novedad inicialmente candidata a automatizacion
Y una contradiccion relevante detectada antes de derivar
**Cuando** `BC-01 - Encuadre administrativo de la novedad` reevalua la consistencia del caso
**Entonces** la automatizacion debe frenarse
Y el caso debe quedar `observada` para revision humana
Y se emite `Novedad observada por inconsistencia o duda de encuadre`

## 7. Escenarios de reingreso y flujo alternativo

### Escenario: Reingresar un caso pendiente con evidencia complementaria suficiente

**Dado** una novedad en estado `pendiente`
Y nueva evidencia que completa el faltante principal
**Cuando** `BC-01 - Encuadre administrativo de la novedad` reingresa y reevalua el caso
**Entonces** el caso vuelve al circuito de clasificacion y derivacion
Y conserva trazabilidad del estado previo

### Escenario: Reingresar un caso observado que mantiene la contradiccion

**Dado** una novedad en estado `observada`
Y evidencia complementaria que no resuelve la contradiccion de encuadre
**Cuando** `BC-01 - Encuadre administrativo de la novedad` reevalua el caso
**Entonces** el caso no debe derivarse
Y debe mantenerse `observada` o escalar a `rechazada` si la contradiccion se vuelve fuerte
