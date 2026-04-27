---
proyecto: Sistematizacion de Novedades Laborales
tipo: Escenarios BDD
estado: borrador
date: 2026-04-27
tags:
  - sgpem
  - bounded-context
  - integraciones
  - conciliacion
  - bdd
---

# Escenarios BDD - BC-07 - Integraciones y conciliacion

> [!abstract] Proposito
> Esta nota describe el comportamiento esperado de `[[BC-07 - Integraciones y conciliación]]` con lenguaje de negocio. Los escenarios cubren el ciclo de ingreso externo, traduccion, correlacion, duplicados, correcciones, observaciones y derivacion, usando `designacion suplente` como primer caso priorizado.

> [!tip]
> Estos escenarios aterrizan el nivel tactico de `[[Catalogo de decisiones y eventos - BC-07 - Integraciones y conciliacion]]`.

## 1. Como leer estos escenarios

- `BC-07` no valida coberturas ni registra tomas de posesión,
- en este primer corte la fuente se trata en forma agnostica con `tipo de fuente`,
- el umbral minimo de correlacion es `agente + cargo/plaza + fecha`,
- `duplicado tecnico` y `reenvio corregido` no significan lo mismo,
- la `designacion suplente` nueva pasa por `[[BC-01 - Encuadre administrativo de la novedad]]` antes de llegar a `[[BC-03 - Cobertura transitoria]]`,
- si la fuente externa contradice la realidad operativa local, la regla base es `observar y frenar`.

## 2. Escenarios de recepcion y traduccion

### Escenario: Recibir una designacion suplente externa con huella trazable

**Dado** un ingreso externo con `tipo de fuente` identificado
Y una `huella externa` utilizable
Y evidencia minima recibida
**Cuando** `BC-07 - Integraciones y conciliación` admite el ingreso
**Entonces** el caso queda registrado como `Evento externo recibido`

### Escenario: Volver legible una designacion suplente en lenguaje canonico

**Dado** un `Evento externo recibido`
Y datos suficientes de agente, cargo/plaza y fecha
Y evidencia externa que describe una designacion suplente
**Cuando** `BC-07 - Integraciones y conciliación` traduce el ingreso
**Entonces** el ingreso queda `vuelto legible`
Y la taxonomia externa no define por si sola el modelo rector

### Escenario: Rechazar tecnicamente un ingreso sin huella ni referencia utilizable

**Dado** un ingreso externo sin `huella externa`
Y sin referencia equivalente utilizable
**Cuando** `BC-07 - Integraciones y conciliación` revisa su admision tecnica
**Entonces** el ingreso queda `rechazado tecnicamente`
Y se emite `Evento externo rechazado tecnicamente`

## 3. Escenarios de correlacion

### Escenario: Resolver correlacion minima con agente, cargo/plaza y fecha

**Dado** un ingreso externo `vuelto legible`
Y coincidencia suficiente de `agente`, `cargo/plaza` y `fecha`
**Cuando** `BC-07 - Integraciones y conciliación` resuelve la correlacion
**Entonces** la correlacion queda resuelta
Y se emite `Correlacion resuelta`

### Escenario: Observar un ingreso cuando falta cargo/plaza correlacionable

**Dado** un ingreso externo `vuelto legible`
Y un `agente` correlacionable
Pero sin `cargo/plaza` suficientemente correlacionable
**Cuando** `BC-07 - Integraciones y conciliación` evalua la correlacion minima
**Entonces** el ingreso no puede seguir a derivacion
Y se emite `Evento externo observado por contradiccion o ambiguedad`

### Escenario: Observar un ingreso cuando la fecha externa contradice la realidad operativa

**Dado** un ingreso externo `vuelto legible`
Y `agente` y `cargo/plaza` correlacionables
Pero la `fecha` contradice en forma relevante la realidad operativa local
**Cuando** `BC-07 - Integraciones y conciliación` revisa la consistencia del ingreso
**Entonces** la regla base es `observar y frenar`
Y se emite `Evento externo observado por contradiccion o ambiguedad`

## 4. Escenarios de duplicados y correcciones

### Escenario: Detectar un duplicado tecnico exacto

**Dado** un ingreso externo ya absorbido previamente
Y que llega un nuevo ingreso con la misma `huella externa` o la misma identidad semantica sin diferencia relevante
**Cuando** `BC-07 - Integraciones y conciliación` compara el nuevo ingreso con el historial previo
**Entonces** el caso queda marcado como `duplicado tecnico`
Y se emite `Duplicado detectado`

### Escenario: Detectar un reenvio corregido de una designacion ya procesada

**Dado** un ingreso externo ya procesado previamente
Y que llega una nueva version con diferencia relevante de fecha, cargo/plaza, agente u otra evidencia sustantiva
**Cuando** `BC-07 - Integraciones y conciliación` revisa el nuevo ingreso
**Entonces** el caso queda identificado como `reenvio corregido`
Y se emite `Reenvio corregido detectado`
Y se emite `Ingreso reencolado para revision`

### Escenario: No autoactualizar una correccion externa ya procesada

**Dado** un `reenvio corregido` de una designacion ya procesada
**Cuando** `BC-07 - Integraciones y conciliación` decide su salida
**Entonces** el caso no debe autoactualizar el contexto funcional por si solo
Y debe quedar reencolado para nueva revision de encuadre

## 5. Escenarios de derivacion

### Escenario: Derivar una designacion suplente nueva a BC-01

**Dado** un ingreso externo `vuelto legible`
Y una `correlacion resuelta`
Y ausencia de contradiccion bloqueante
**Cuando** `BC-07 - Integraciones y conciliación` deriva el ingreso
**Entonces** se emite `Ingreso externo normalizado para encuadre`
Y el destino es `[[BC-01 - Encuadre administrativo de la novedad]]`
Y no se crea todavia una `cobertura validada` en `[[BC-03 - Cobertura transitoria]]`

### Escenario: No derivar directo a BC-03 una designacion suplente nueva en este primer corte

**Dado** un ingreso externo que ya puede leerse como `designacion suplente`
Y una `correlacion resuelta`
**Cuando** `BC-07 - Integraciones y conciliación` decide la salida del caso
**Entonces** la ruta base sigue siendo `[[BC-01 - Encuadre administrativo de la novedad]]`
Y la derivacion directa a `[[BC-03 - Cobertura transitoria]]` no se usa en este corte

### Escenario: Frenar la derivacion por contradiccion fuerte con realidad local

**Dado** un ingreso externo `vuelto legible`
Y una contradiccion fuerte con la realidad operativa local
**Cuando** `BC-07 - Integraciones y conciliación` revisa si puede derivarlo
**Entonces** el caso no debe derivarse
Y debe quedar `observado`
Y se emite `Evento externo observado por contradiccion o ambiguedad`

## 6. Escenarios de reencolado y continuidad

### Escenario: Reencolar una correccion externa para nueva revision de encuadre

**Dado** un `reenvio corregido` con diferencia relevante respecto del ingreso ya procesado
Y trazabilidad suficiente del ingreso previo
**Cuando** `BC-07 - Integraciones y conciliación` prepara la salida del caso
**Entonces** se emite `Ingreso reencolado para revision`
Y el nuevo tratamiento vuelve a circuito de encuadre o revision correspondiente

### Escenario: Mantener evidencia externa preservada al derivar a BC-01

**Dado** un ingreso externo listo para derivacion
**Cuando** `BC-07 - Integraciones y conciliación` lo entrega a `BC-01`
**Entonces** la `huella externa`, el `tipo de fuente` y la evidencia asociada deben preservarse
Y el receptor no debe recibir un caso funcionalmente resuelto sino un ingreso legible y trazable
