---
proyecto: Sistematizacion de Novedades Laborales
tipo: Concepto transversal
estado: borrador
date: 2026-04-08
aliases:
  - Concepto transversal - Plaza, estado y vacante
tags:
  - sgpem
  - bounded-context
  - concepto-transversal
  - plaza
  - vacante
  - pof
---

# Concepto transversal - Plaza, estado y vacante

> [!abstract] Proposito
> Esta nota fija un lenguaje comun para tratar `plaza`, `estado de plaza` y `vacante` dentro del dominio de novedades laborales sin convertirlos en un bounded context autonomo. Su objetivo es evitar que los bounded contexts de novedades absorban indebidamente la administracion estructural de POF y, al mismo tiempo, asegurar que respeten la entidad normativa de la plaza y sus estados.

## 1. Que es y que no es esta nota

- no propone un bounded context nuevo,
- no reemplaza la administracion estructural de plazas ni la POF,
- no redefine el sistema maestro externo,
- si fija conceptos transversales que todos los bounded contexts de novedades deben leer de la misma manera.

## 2. Criterio general

En este recorte, `vacante` no es una familia de novedad autonoma. Es un concepto transversal central ligado a la `plaza`, a su `estado` y a su `caracter`. Por eso algunos bounded contexts pueden generar, dejar expresada, transformar o consumir una vacante, pero ninguno de ellos debe absorber por si solo la administracion estructural de plazas.

## 3. Conceptos minimos comunes

### 3.1 Plaza

- es la posicion laboral formal sobre la que recaen ocupacion, estado y trazabilidad administrativa,
- no es un dato accesorio del agente,
- su lectura depende del dominio POF aunque las novedades laborales la impacten.

### 3.2 Estado de plaza

- expresa la condicion normativa u operativa de la plaza,
- condiciona operaciones permitidas, bloqueadas o derivables,
- debe consultarse como referencia estructural aunque no se modele aqui toda la administracion de estados.

### 3.3 Vacante de base

- expresa que la plaza carece de ocupacion estable definitiva o que se encuentra disponible segun el regimen aplicable,
- no equivale automaticamente a ausencia temporaria,
- no equivale por si sola a suplencia,
- puede coexistir con un `ocupante vigente` no definitivo, por ejemplo un interino activo.

### 3.4 Ocupante vigente

- es quien ejerce efectivamente la prestacion actual sobre la plaza en un momento dado,
- puede coincidir o no con la condicion estructural de la vacante de base,
- sus ausencias, egresos o movimientos pueden disparar novedades distintas sin alterar por si solos la lectura estructural completa de la plaza.

### 3.5 Vacante resultante

- es la vacante que aparece como efecto inmediato y trazable de una novedad,
- suele quedar expresada por `BC-04` o `BC-05` cuando el movimiento o el egreso dejan disponible la plaza o alteran su caracter.

### 3.6 Vacante persistente

- es una vacante de base que ya existia y que permanece despues de otra novedad sobre el ocupante vigente,
- sirve para no confundir el cierre o cambio del ocupante con la aparicion de una vacante nueva,
- es especialmente util cuando egresa un interino o cuando cae una cobertura sobre una plaza ya vacante en sentido estructural.

### 3.7 Cambio de caracter de vacante

- expresa que la plaza sigue vacante, pero bajo otra calificacion o restriccion relevante,
- no equivale necesariamente a una nueva vacante,
- debe quedar trazado cuando sea efecto inmediato del caso.

### 3.8 Vacante apta para provision estable

- es una vacante que, ademas de existir como base, se encuentra habilitada para una ocupacion estable segun el regimen y la evidencia disponible,
- no toda vacante estructural queda inmediatamente apta para ser provista por `BC-06`.

## 4. Regla de lectura por bounded context

### 4.1 BC-03 - Cobertura transitoria

- resuelve reemplazos temporarios,
- reemplaza al `ocupante vigente` o a la prestacion temporalmente ausente,
- no resuelve la `vacante de base`.

### 4.2 BC-04 - Movilidad funcional

- resuelve el movimiento como problema rector,
- puede generar o dejar expresada una `vacante resultante` o `vacante persistente`,
- no se convierte por eso en contexto de administracion de vacantes.

### 4.3 BC-05 - Egresos definitivos

- resuelve el cierre definitivo del vinculo laboral,
- puede expresar `vacante resultante`, `vacante persistente`, `cambio de caracter de vacante` o `cierre de plaza`,
- no resuelve por si mismo la ocupacion posterior del cargo.

### 4.4 BC-06 - Ocupacion estable de cargos

- consume una vacante real o habilitacion estructural externa para abrir una ocupacion estable,
- puede recibir vacantes resultantes o persistentes desde otros contexts,
- no administra por si mismo la estructura general de plazas.

## 5. Reglas transversales de consistencia

1. `Vacante` no debe modelarse como bounded context propio dentro de este mapa de novedades laborales.
2. Ningun bounded context de novedades debe absorber la administracion estructural completa de plazas o POF.
3. Toda novedad que impacte una plaza debe poder distinguir entre `vacante de base` y `ocupante vigente`.
4. Una cobertura transitoria no se sostiene por la sola persistencia de la vacante de base si pierde su causante u ocupante reemplazado.
5. Una vacante resultante o persistente puede ser insumo para `BC-06`, pero no obliga por si sola a abrir una ocupacion estable.
6. Cuando una novedad cambie el estado o el caracter de la vacante, ese efecto debe quedar expresado aunque la administracion estructural viva fuera de este recorte.

## 6. Dependencia externa a respetar

- la POF o el sistema maestro de plazas sigue siendo la fuente estructural principal,
- SGPEM debe leer y respetar esa estructura para no aplicar novedades incompatibles,
- el dominio de novedades laborales puede producir efectos sobre plaza y vacante, pero no reemplaza la gobernanza estructural externa.

## 7. Uso recomendado

- usar esta nota como referencia comun al redactar o corregir bounded contexts,
- enlazarla cuando aparezcan dudas sobre `vacante`, `estado de plaza`, `ocupante vigente` o `caracter de vacante`,
- mantener aqui el vocabulario transversal y dejar los subtipos normativos especificos en cada bounded context.
