---
proyecto: Sistematizacion de Novedades Laborales
tipo: Bounded context
estado: vigente
date: 2026-04-06
tags:
  - sgpem
  - bounded-context
  - movilidad
  - traslados
---
# BC-04 - Movilidad funcional

> [!abstract] Proposito
> Este bounded context reune las novedades cuyo efecto principal es mover, reasignar o redirigir funcionalmente un caso laboral. El problema central aqui no es pausar temporalmente ni cubrir una ausencia, sino explicar con trazabilidad un cambio de asignacion, destino o encuadre funcional, distinguiendo entre movimiento validado y movimiento efectivizado cuando el ejercicio en destino exige toma de posesion.

## 1. Por que es un bounded context separado

Traslados, adscripciones, comisiones, tareas pasivas, permutas, reubicaciones, casos de mayor jerarquia y otros movimientos funcionales equivalentes comparten una semántica distinta de las licencias o suplencias. Su complejidad esta en explicar el movimiento, su vigencia, su origen y su destino funcional, y en algunos subtipos su efectivizacion mediante toma de posesión.

## 2. Qué incluye

- traslado transitorio,
- traslado definitivo,
- adscripción,
- comisión de servicio,
- tareas pasivas,
- permuta,
- reubicación,
- movimiento por mayor jerarquia,
- afectacion,
- ==otros movimientos funcionales equivalentes a confirmar.==

## 3. Qué no incluye

- licencias o interrupciones temporales puras,
- coberturas transitorias,
- la suplencia o el interinato como figuras de ocupacion del cargo,
- egresos definitivos,
- cierre y consolidación del periodo,
- traducción técnica de integraciones externas.

## 4. Subfamilias internas

### 4.1 Traslado transitorio

- cambio temporal de destino funcional con retorno esperable,
- requiere doble trazabilidad entre origen y destino,
- mantiene la referencia al cargo de origen mientras el agente ejerce en destino,
- es uno de los subtipos mas sensibles a control bilateral y consistencia operativa.

### 4.2 Traslado definitivo

- cambio definitivo de destino funcional,
- exige trazabilidad completa entre situacion de origen, destino y fecha efectiva,
- su problema central no es ocupar una vacante como interino, sino explicar el movimiento del agente hacia el nuevo destino.

### 4.3 Adscripción y comisión de servicio

- son movimientos de asignacion institucional con respaldo administrativo suficiente,
- comparten la necesidad de explicar origen, destino, vigencia y motivo funcional,
- la ultima palabra operativa queda en `SGPEM central`, aunque la ejecucion pueda requerir confirmacion local.

### 4.4 Tareas pasivas

- implican una adecuacion funcional o reasignacion de tareas dentro de un encuadre administrativo especifico,
- su problema principal no es la ausencia ni el reemplazo, sino el nuevo modo de prestacion o ubicacion funcional del agente,
- requieren trazabilidad clara entre cargo base, encuadre resultante y vigencia.

### 4.5 Permuta

- es un intercambio de destino entre agentes bajo una figura normativa propia,
- no debe confundirse con traslado hacia vacante,
- exige consistencia bilateral entre ambas referencias involucradas.

### 4.6 Reubicación

- es una respuesta administrativa a una situación de disponibilidad o supresion del cargo de origen,
- consume normalmente el estado previo de `pase a disponibilidad`,
- no debe modelarse como traslado ordinario,
- requiere explicar tanto la perdida del destino original como el nuevo destino asignado.

### 4.7 Mayor jerarquia

- es un movimiento funcional en el que el agente pasa a desempeñarse en un cargo de jerarquia superior,
- debe explicarse por cargo base, cargo de destino, vigencia y respaldo suficiente,
- incluye tanto la apertura como el cierre del movimiento rector, incluso cuando el catalogo use rotulos de `interinato`,
- puede generar efectos derivados sobre el cargo de origen o de destino, pero esos efectos no cambian su pertenencia a `BC-04`.

### 4.8 Afectacion

- es un movimiento funcional transitorio con trazabilidad propia de origen, destino y vigencia,
- `23 - Afectacion` y `83 - Cese de Afectacion` quedan dentro de este contexto como apertura y cierre del mismo movimiento,
- no requiere salir del bounded context para sostener su pertenencia estrategica.

### 4.9 Autoridad operativa por subtipo

| Subtipo                      | Ultima palabra operativa | Observaciones |
| ---------------------------- | ------------------------ | ------------- |
| Traslado transitorio         | Modelo mixto bilateral   | Requiere ordenamiento central y confirmacion coordinada entre origen y destino |
| Traslado definitivo          | SGPEM central            | El establecimiento aporta contexto, pero el ordenamiento del movimiento es central |
| Adscripción / comisión       | SGPEM central            | Se prioriza ordenamiento administrativo temprano con respaldo suficiente |
| Tareas pasivas               | SGPEM central            | No debe quedar librado a lectura puramente local |
| Permuta                      | SGPEM central            | La validacion debe asegurar consistencia entre ambas partes del intercambio |
| Reubicación                  | SGPEM central            | Debe respetar la logica especifica de disponibilidad y nuevo destino |
| Mayor jerarquia              | SGPEM central            | Se sostiene como movimiento rector y exige consistencia entre origen, destino y cierre |
| Afectacion                   | SGPEM central            | Se trata como movilidad funcional transitoria con su cierre propio |

### 4.10 Soporte mínimo y efectivizacion

| Subtipo                | Soporte minimo obligatorio | Soporte fuerte adicional | Regla de efectivizacion |
| ---------------------- | -------------------------- | ------------------------ | ----------------------- |
| Traslado transitorio   | Agente identificado, cargo o plaza de origen, cargo o plaza de destino, fecha desde, fecha hasta, causal y norma legal o acto suficiente | Confirmacion coordinada entre origen y destino | El movimiento puede validarse antes, pero requiere toma de posesion para iniciar ejercicio efectivo en destino |
| Traslado definitivo    | Agente identificado, origen funcional, destino funcional, fecha efectiva, causal y acto o respaldo suficiente | Evidencia de destino habilitado o vacante segun corresponda | El movimiento puede validarse antes, pero para `traslado definitivo` la toma de posesion queda exigida para iniciar ejercicio efectivo en destino |
| Adscripción / comisión | Agente identificado, origen, destino, vigencia, motivo funcional y acto o disposicion suficiente | Referencia al area, dependencia o autoridad que impulsa el movimiento | Se rige por la regla general del contexto: si el subtipo habilita inicio de ejercicio efectivo en destino, requiere toma de posesion |
| Tareas pasivas         | Agente identificado, cargo base o referencia equivalente, nuevo encuadre funcional, vigencia y respaldo suficiente | Soporte medico, laboral o administrativo cuando corresponda | Se rige por la regla general del contexto: si el subtipo habilita inicio de ejercicio efectivo en destino, requiere toma de posesion |
| Permuta                | Agentes identificados, destinos o cargos involucrados, fecha efectiva y acto o respaldo suficiente del intercambio | Constancia de acuerdo y validacion bilateral | Se rige por la regla general del contexto: si la permuta habilita inicio de ejercicio efectivo en nuevo destino, requiere toma de posesion |
| Reubicación            | Agente identificado, situacion de disponibilidad o supresion trazable, nuevo destino, fecha efectiva y acto suficiente | Referencia al cargo o destino suprimido y al nuevo encuadre resultante | Se rige por la regla general del contexto: si la reubicacion habilita inicio de ejercicio efectivo en nuevo destino, requiere toma de posesion |
| Mayor jerarquia        | Agente identificado, cargo base, cargo de destino, fecha desde, causal y acto o respaldo suficiente | Referencia al movimiento rector y a sus efectos administrativos relevantes | Se rige por la regla general del contexto: si el subtipo habilita inicio de ejercicio efectivo en destino, requiere toma de posesion |
| Afectacion             | Agente identificado, origen funcional, destino funcional, vigencia y acto o respaldo suficiente | Referencia al motivo funcional y al cierre esperado del movimiento | Se rige por la regla general del contexto: si el subtipo habilita inicio de ejercicio efectivo en destino, requiere toma de posesion |

### 4.11 Regla general de toma de posesion

- los movimientos funcionales pueden quedar validados antes de quedar efectivizados,
- la toma de posesion se exige cuando el subtipo habilita inicio de ejercicio efectivo en destino,
- `traslado transitorio` y `traslado definitivo` quedan expresamente alcanzados por esa regla,
- la falta de toma de posesion impide considerar vigente el ejercicio en destino aunque exista acto administrativo.

### 4.12 Ciclo de vida del movimiento

1. movimiento funcional solicitado,
2. movimiento funcional validado,
3. toma de posesion registrada, cuando corresponda,
4. movimiento funcional efectivizado,
5. movimiento funcional vigente,
6. movimiento funcional finalizado,
7. movimiento funcional regularizado.

### 4.13 Interfaces derivadas del movimiento

- un movimiento funcional puede generar efectos derivados sobre cargos, destinos o formas de cobertura sin dejar de pertenecer por eso a `BC-04`,
- un movimiento tambien puede generar, dejar expresada o transformar una `vacante resultante` o `vacante persistente` sin que por eso la vacante se vuelva el problema rector del caso,
- si el efecto derivado principal es una suplencia o reemplazo temporario, la resolucion corresponde a `[[BC-03 - Cobertura transitoria]]`,
- si el efecto derivado principal es ocupar establemente un cargo vacante, la resolucion corresponde a `[[BC-06 - Ocupacion estable de cargos]]`,
- la lectura transversal de plaza, estado y vacante debe mantenerse consistente con `[[Concepto transversal - Plaza, estado y vacante]]`,
- `mayor jerarquia` es un ejemplo claro de este patron, pero no el unico subtipo que puede producir interfaces hacia otros bounded contexts.

## 5. Conceptos principales

- origen funcional,
- destino funcional,
- cargo base,
- cargo de destino,
- movimiento transitorio,
- movimiento definitivo,
- traslado transitorio,
- traslado definitivo,
- adscripción,
- comisión de servicio,
- tareas pasivas,
- permuta,
- reubicación,
- afectacion,
- disponibilidad,
- movimiento validado,
- movimiento efectivizado,
- toma de posesion,
- mayor jerarquia,
- licencia por mayor jerarquia,
- vacante resultante,
- vacante persistente,
- derivacion a cobertura,
- derivacion a ocupacion estable,
- vigencia del movimiento,
- retorno o cierre del movimiento.

## 6. Eventos principales

- movimiento funcional solicitado,
- movimiento funcional validado,
- movimiento funcional observado,
- toma de posesion registrada,
- movimiento funcional efectivizado,
- movimiento funcional finalizado,
- movimiento funcional regularizado.

## 7. Decisiones principales

- si el caso pertenece realmente a movilidad funcional,
- si corresponde tratarlo como traslado transitorio, traslado definitivo, adscripción/comisión, tareas pasivas, permuta, reubicación, afectacion o mayor jerarquia,
- como deben explicarse origen, destino, vigencia y causal del movimiento,
- si la evidencia minima alcanza para validar el subtipo correcto,
- desde cuando produce efectos el movimiento validado y desde cuando comienza el ejercicio efectivo,
- si el subtipo queda alcanzado por la regla general de toma de posesion,
- si el movimiento genera, deja expresada o transforma una vacante resultante o persistente y como debe quedar trazada sobre la plaza,
- si el movimiento genera efectos derivados que deben salir a `[[BC-03 - Cobertura transitoria]]` o `[[BC-06 - Ocupacion estable de cargos]]`,
- si el caso debe observarse, regularizarse o cerrarse por inconsistencia,
- si un movimiento genera efectos derivados que deban resolverse fuera de este contexto sin perder su rectoria aqui.

## 8. Reglas centrales de delimitacion

1. **Toda movilidad debe poder explicar origen y destino funcional.**
2. **Debe quedar explicito el subtipo normativo del movimiento; `permuta` y `reubicacion` no deben absorberse como simples traslados.**
3. La movilidad no equivale por si sola a licencia, cobertura, ocupación estable ni egreso.
4. **Este contexto resuelve la trazabilidad del movimiento, su vigencia y, cuando corresponda, su efectivizacion.**
5. La toma de posesión se exige cuando el subtipo habilita inicio de ejercicio efectivo en destino; `traslado transitorio` y `traslado definitivo` quedan expresamente alcanzados por esa regla.
6. Un movimiento puede quedar validado antes de quedar efectivizado.
7. Cuando el problema principal es `mover o reasignar`, el caso pertenece aquí aunque produzca efectos colaterales en otros contextos.
8. **Cuando el efecto derivado principal es una suplencia o reemplazo temporario, la resolución debe salir a `[[BC-03 - Cobertura transitoria]]`.**
9. **Cuando el efecto derivado principal es una ocupación estable de cargo vacante, la resolución debe salir a `[[BC-06 - Ocupacion estable de cargos]]`.**
10. **La vacante que resulte o persista por efecto del movimiento es un concepto transversal de plaza y no convierte por si sola al caso en un bounded context distinto.**
11. `Adscripción`, `comisión` y `tareas pasivas` no deben reabsorberse como licencias ni como interrupciones temporales puras.
12. Los subtipos de movilidad funcional pueden generar efectos derivados de cobertura u ocupacion estable que salgan por interfaz sin cambiar por eso su pertenencia a `BC-04`; `mayor jerarquia` es un ejemplo de ese patron.
13. `Pase a Disponibilidad` pertenece aqui cuando su problema rector es sostener la trazabilidad del agente hasta su reubicacion u otra salida funcional.
14. `Afectacion` y `cese de afectacion` pertenecen aqui como apertura y cierre de una movilidad funcional transitoria.

## 9. Relación con otros bounded contexts

- recibe casos ya clasificados desde `[[BC-01 - Encuadre administrativo de la novedad]]`,
- deriva a `[[BC-03 - Cobertura transitoria]]` cuando el efecto derivado del movimiento sea una suplencia o reemplazo temporario,
- deriva a `[[BC-06 - Ocupacion estable de cargos]]` cuando el efecto derivado del movimiento sea ocupar establemente un cargo vacante,
- cuando un movimiento se apoya en licencia por mayor jerarquía, el cargo de origen debe tratarse en `[[BC-03 - Cobertura transitoria]]` como suplencia y no como interinato,
- puede generar efectos colaterales que luego impacten en `[[BC-08 - Consolidacion y cierre]]`,
- puede requerir evidencia o conciliación desde `[[BC-07 - Integraciones y conciliación]]`,
- en algunos casos puede convivir con `[[BC-02 - Interrupciones temporales]]`, pero no debe mezclarse con ese contexto.

## 10. Puntos de interfaz relevantes

- necesidad de doble control en `traslado transitorio`,
- consistencia bilateral en `permuta`,
- trazabilidad de disponibilidad y nuevo destino en `reubicacion`,
- cierre de `mayor jerarquia` y de `afectacion` como cierres del movimiento rector,
- trazabilidad de vacante resultante o persistente cuando el movimiento impacta el estado de la plaza,
- toma de posesion segun regla general de inicio de ejercicio efectivo en destino,
- actos administrativos o respaldo suficiente como soporte principal del movimiento,
- interfaces derivadas del movimiento hacia cobertura u ocupacion estable; `mayor jerarquia` es un ejemplo particularmente visible.

## 11. Preguntas abiertas para investigación

- que variantes normativas internas de `mayor jerarquia` conviene distinguir mas adelante,
- que variantes exactas de `adscripción`, `comisión`, `tareas pasivas` y `afectacion` requieren una regla mas fuerte de efectivizacion,
- que datos minimos adicionales deben exigirse para `permuta` y otros movimientos con referencia bilateral,
- como debe sostenerse el cierre o retorno de movimientos transitorios institucionales sin perder trazabilidad,
- que diferencias operativas finas conviene distinguir mas adelante entre `afectacion` y otros movimientos institucionales transitorios.
