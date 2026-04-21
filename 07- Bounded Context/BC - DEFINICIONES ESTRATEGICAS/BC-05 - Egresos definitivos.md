---
proyecto: Sistematizacion de Novedades Laborales
tipo: Bounded context
estado: vigente
date: 2026-04-06
tags:
  - sgpem
  - bounded-context
  - egresos
  - bajas
---

# BC-05 - Egresos definitivos

> [!abstract] Proposito
> Este bounded context reune las novedades cuyo efecto principal es cerrar definitivamente un caso laboral. Su problema central no es pausar, mover ni cubrir, sino explicar con trazabilidad una desvinculacion definitiva, distinguir su causal normativa, fijar su fecha efectiva y expresar sus efectos inmediatos sobre la plaza, los casos derivados y, cuando corresponda, la proyeccion residual del egreso para consulta futura sin volverla el eje del bounded context.

## 1. Por que es un bounded context separado

Renuncia aceptada, jubilacion, cese por fallecimiento, cesantia, exoneracion y otras causales legales equivalentes no comparten la misma lógica que una licencia, una suplencia o un traslado. El rasgo común aqui es el cierre irreversible del caso laboral. En este contexto, `baja` o `cese` funcionan como rótulos administrativos generales, pero la decisión de negocio debe poder distinguir la causal normativa real del egreso y su efecto inmediato.

## 2. Que incluye

- renuncia aceptada,
- renuncia para acogerse a jubilacion como subtipo propio de renuncia aceptada y estado previo al cierre jubilatorio definitivo del vinculo laboral,
- jubilacion,
- retiro voluntario,
- cese por fallecimiento,
- sanciones expulsivas como `cesantia` y `exoneracion`,
- otros egresos definitivos equivalentes con base legal suficiente,
- expresión del efecto inmediato del egreso sobre la plaza,
- resolución o derivación de efectos dependientes que no pueden quedar abiertos.

## 3. Que no incluye

- interrupciones temporales,
- coberturas transitorias en si mismas,
- movilidad funcional,
- ocupación estable de cargos,
- consolidación y cierre de periodo,
- liquidación previsional, pensión, seguro, obra social u otros beneficios externos como proceso autónomo,
- administración estructural completa de plazas o POF como dominio autónomo.

Este contexto no administra por si mismo la ocupación posterior del cargo, pero si debe poder expresar si el egreso genera `vacante resultante`, `vacante persistente`, `cambio de caracter de vacante` o incluso `cierre de plaza` cuando eso sea una consecuencia inmediata y trazable del cierre definitivo. Tambien debe distinguir el cierre del vinculo laboral del `ocupante vigente` respecto del estado posterior de la `vacante de base`, porque ambos efectos pueden no coincidir en la misma novedad.

La lectura transversal de plaza, estado y vacante debe mantenerse consistente con `[[Concepto transversal - Plaza, estado y vacante]]`.

## 4. Subfamilias internas

### 4.1 Renuncia aceptada

- la renuncia solo produce egreso cuando queda aceptada por autoridad competente,
- mientras no exista aceptación, el caso puede quedar presentado pero no aplicado como egreso,
- la permanencia transitoria del agente hasta la aceptación o el reemplazo forma parte del problema del subtipo,
- `renuncia para acogerse a jubilacion` no debe reabsorberse como jubilacion pura: sigue siendo una variante de renuncia aceptada,
- dentro de SGPEM representa un estado previo y trazable hacia la jubilacion o el retiro, sin agotar por si mismo el cierre jubilatorio definitivo del vinculo,
- por eso conviene tratarla como puente hacia `jubilacion` o `retiro`, sin perder su rectoria propia como renuncia aceptada.

### 4.2 Jubilación

- el cese puede quedar determinado por el acceso al beneficio previsional o por la configuración de las condiciones exigidas por el regimen aplicable,
- el problema aquí no es la renuncia como acto de voluntad, sino el egreso por situación previsional,
- cuando el cierre jubilatorio depende de decreto, la `jubilacion` representa el ultimo estado con vinculo laboral del agente en este sistema,
- el resultado administrativo puede proyectarse como `retiro`, sin que este contexto deba modelar en detalle la liquidación del beneficio.

### 4.3 Retiro voluntario

- constituye un subtipo propio de egreso definitivo con regimen especial,
- no debe diluirse dentro de `jubilacion` ni dentro de `renuncia aceptada`, aunque pueda guardar proximidad con ambas familias,
- su problema rector sigue siendo el cierre definitivo del vinculo laboral con causal y fecha trazables,
- puede proyectar efectos previsionales o administrativos posteriores sin que este bounded context deba absorberlos.

### 4.4 Cese por fallecimiento

- el fallecimiento actúa como causal directa de cierre definitivo,
- extingue la relación laboral del agente desde una fecha trazable,
- puede dejar proyectado un beneficio economico directo a familiares, pero ese beneficio no se liquida dentro de este bounded context.

### 4.5 Sanciones expulsivas

- incluyen `cesantia` y `exoneracion`,
- su problema central es el cierre definitivo por sanción máxima con acto suficiente,
- extinguen derechos y deberes del agente en el vinculo laboral según la causal aplicada,
- no deben confundirse con otras causales de egreso definitivo no disciplinarias.

### 4.6 Otros egresos definitivos equivalentes

- agotamiento del maximo legal de licencias por enfermedad: el caso cierra por agotamiento del margen normativo admisible,
- perdida irreversible de condiciones psicofisicas: el egreso se apoya en determinación medica o junta suficiente,
- incompatibilidad legal: el cierre definitivo se produce por quedar el agente alcanzado por una incompatibilidad no subsanable en el marco aplicable,
- ocultamiento de impedimentos de ingreso: el egreso se apoya en la detección posterior de un impedimento relevante preexistente,
- otros cierres equivalentes pueden incorporarse si comparten cierre definitivo con base legal suficiente.

### 4.7 Regla general de autoridad operativa

- el egreso definitivo requiere autoridad competente o causal legal autosuficiente según el subtipo,
- este contexto no necesita una autoridad unica para todos los subtipos, pero si una regla común de trazabilidad fuerte entre causal, fecha efectiva y respaldo suficiente,
- cuando la causal depende de acto administrativo, el egreso no debe considerarse aplicado sin ese respaldo,
- cuando la causal produce efecto directo por configuración legal o hecho trazable, el contexto debe poder registrar la fecha efectiva aunque el circuito administrativo complementario llegue después.

### 4.8 Soporte minimo, efectivizacion y proyeccion residual

| Subtipo | Soporte minimo obligatorio | Soporte fuerte adicional | Regla de efectivizacion | Proyeccion residual relevante |
| ------- | -------------------------- | ------------------------ | ----------------------- | ----------------------------- |
| Renuncia aceptada | Agente identificado, cargo o plaza afectada, fecha de presentacion, fecha prevista o efectiva y constancia de aceptacion | Referencia al reemplazo o constancia de permanencia transitoria cuando corresponda | La renuncia puede presentarse antes, pero solo produce egreso al quedar aceptada | La variante para acogerse a jubilacion conserva una transicion protegida sin dejar de ser renuncia |
| Jubilacion | Agente identificado, cargo o plaza afectada, fecha efectiva y respaldo previsional o administrativo suficiente | Referencia al tramite previsional o acto complementario cuando exista | El egreso puede quedar determinado por configuracion legal previsional; `BC-05` debe poder registrar la fecha efectiva del cierre | Proyecta cambio de situacion a `retiro` sin modelar aqui la liquidacion del beneficio |
| Retiro voluntario | Agente identificado, cargo o plaza afectada, causal de retiro, fecha efectiva y respaldo normativo o administrativo suficiente | Referencia al regimen especial aplicable o al tramite complementario cuando exista | El egreso se aplica cuando el retiro voluntario queda suficientemente respaldado y con fecha efectiva trazable | Puede proyectar efectos previsionales o administrativos posteriores sin modelarlos aqui |
| Cese por fallecimiento | Agente identificado, cargo o plaza afectada y fecha cierta de fallecimiento con constancia suficiente | Referencia a actuaciones administrativas complementarias | Produce cierre definitivo desde la fecha trazable del fallecimiento | Puede dejar beneficio economico directo a familiares como efecto externo derivado |
| Sanciones expulsivas | Agente identificado, cargo o plaza afectada, subtipo sancionatorio, fecha efectiva y acto sancionatorio suficiente | Referencia al sumario o antecedente disciplinario | El egreso se aplica con el acto sancionatorio firme o suficiente segun regimen | Extingue sin transicion protectoria equivalente los derechos y deberes del vinculo laboral |
| Otros egresos definitivos equivalentes | Agente identificado, cargo o plaza afectada, causal legal trazable, fecha efectiva y respaldo suficiente | Dictamen medico, junta, acto o constancia segun causal | Se aplican cuando la causal equivalente queda suficientemente acreditada | Depende de la causal; puede haber cierres con transicion o sin ella, pero debe quedar explicitado |

### 4.9 Efectos inmediatos sobre plaza y casos derivados

- un egreso definitivo puede generar `vacante resultante` como efecto inmediato sobre la plaza afectada,
- tambien puede dejar expresada una `vacante persistente` cuando el cierre recae sobre el ocupante vigente de una plaza que ya era vacante en sentido estructural,
- en otros casos puede producir `cambio de caracter de vacante`; por ejemplo, una vacante puede dejar de revistar bajo un carácter especial como `ley ruffino`,
- en algunos casos tambien puede corresponder `cierre de plaza` como consecuencia inmediata y trazable del egreso,
- el egreso definitivo recae sobre el vinculo laboral del ocupante vigente; la vacante de base que resulte o persista despues del cierre debe quedar expresada por separado,
- si el egreso definitivo afecta a un ocupante de una plaza ya vacante en sentido estructural, `BC-05` cierra ese vinculo pero no debe confundirlo con una nueva ocupacion estable de la vacante de base,
- si existen suplencias, reemplazos u otros casos dependientes del ocupante saliente, `BC-05` debe exigir su cierre, revisión o derivación explicita, porque la sola persistencia de la vacante no sostiene esa cobertura,
- cuando el efecto posterior principal sea una nueva ocupación estable sobre cargo vacante, la resolución debe salir a `[[BC-06 - Ocupacion estable de cargos]]`,
- cuando el efecto posterior principal sea revisar, cerrar o transformar una cobertura transitoria existente, la resolución debe salir a `[[BC-03 - Cobertura transitoria]]`.

### 4.10 Puente con el catalogo administrativo inicial

| Codigo o familia | Lectura funcional dentro de `BC-05` | Observaciones |
| ---------------- | ----------------------------------- | ------------- |
| 72 - Renuncia por Jubilacion | Renuncia aceptada con puente a jubilacion o retiro | No agota por si sola el cierre jubilatorio definitivo del vinculo cuando ese cierre depende de decreto |
| 73 - Fallecimiento | Cese por fallecimiento | Hecho trazable con cierre definitivo directo |
| 76 - Renuncia por Motivos Particulares | Renuncia aceptada | Requiere aceptacion cuando ese sea el regimen aplicable |
| 79 - Retiro Voluntario | Retiro voluntario | Subtipo propio dentro de `BC-05` |
| 80 - Cesantia | Sancion expulsiva | No debe mezclarse con causales protectorias o naturales |
| Exoneracion | Sancion expulsiva | Comparte familia con `cesantia`, con semantica sancionatoria propia |
| Jubilacion | Jubilacion | No debe confundirse con la renuncia para acogerse a jubilacion |

### 4.11 Ciclo de vida del egreso

1. egreso definitivo encuadrado,
2. causal de egreso validada,
3. egreso definitivo observado, cuando corresponda,
4. egreso definitivo aplicado,
5. efecto inmediato sobre plaza registrado,
6. casos derivados resueltos o derivados,
7. cierre definitivo completado.

## 5. Conceptos principales

- egreso definitivo,
- baja,
- cese,
- causal normativa de egreso,
- renuncia aceptada,
- renuncia para acogerse a jubilacion,
- jubilacion,
- retiro,
- retiro voluntario,
- cese por fallecimiento,
- cesantia,
- exoneracion,
- causal equivalente de egreso,
- fecha efectiva,
- evidencia de cierre,
- proyeccion residual del egreso,
- vacante resultante,
- vacante persistente,
- cambio de caracter de vacante,
- cierre de plaza,
- efectos dependientes,
- cierre irreversible del caso.

## 6. Eventos principales

- egreso definitivo encuadrado,
- causal de egreso validada,
- egreso definitivo observado,
- egreso definitivo aplicado,
- vacante resultante registrada,
- cambio de caracter de vacante registrado,
- cierre de plaza registrado,
- casos derivados regularizados o derivados,
- cierre definitivo completado.

## 7. Decisiones principales

- si el caso pertenece realmente a egreso definitivo,
- si corresponde tratarlo como renuncia aceptada, jubilacion, retiro voluntario, cese por fallecimiento, sancion expulsiva u otra causal equivalente,
- si `baja` o `cese` están siendo usados solo como rotulo administrativo general o si la causal normativa ya quedo identificada,
- desde cuando produce efecto el egreso según el subtipo,
- si la evidencia mínima alcanza para aplicar el cierre o si el caso debe observarse,
- si existe una proyeccion residual relevante del egreso que convenga dejar explicitada para consulta futura sin convertirla en problema rector del contexto,
- que efecto inmediato produce sobre la plaza: vacante resultante, vacante persistente, cambio de caracter de vacante o cierre,
- si el egreso recae sobre el ocupante vigente de una plaza que ya era vacante en sentido estructural y que estado posterior deja esa base,
- si una cobertura dependiente pierde su causante por el egreso del ocupante vigente aunque la vacante de base permanezca abierta,
- que casos dependientes deben cerrarse, revisarse o derivarse a otros bounded contexts,
- si el caso debe regularizarse por inconsistencia de fecha, causal o soporte.

## 8. Reglas centrales de delimitacion

1. **`Baja` o `cese` funcionan como rótulos administrativos generales; la decisión de negocio debe poder identificar la causal normativa real del egreso.**
2. **No hay egreso definitivo sin causal y fecha efectivas suficientemente trazables.**
3. La renuncia no produce efecto definitivo por su sola presentación; requiere aceptación cuando ese sea el régimen aplicable.
4. **`Renuncia para acogerse a jubilacion` sigue siendo una variante de renuncia aceptada, funciona como puente hacia `jubilacion` o `retiro` y no debe reabsorberse como jubilacion pura.**
5. **Cuando el cierre jubilatorio depende de decreto, `jubilacion` representa el ultimo estado con vinculo laboral del agente en este sistema.**
6. No todos los subtipos de egreso se efectivizan del mismo modo; algunos dependen de aceptación o acto, y otros de configuración legal o hecho trazable.
7. **Este contexto resuelve el cierre definitivo del caso y sus efectos inmediatos sobre plaza y dependencias, pero no la ocupación posterior del cargo ni la liquidación externa de beneficios.**
8. La `proyeccion residual del egreso` puede expresarse cuando la causal preserve una transicion protegida del agente o proyecte un beneficio externo a terceros, pero no debe desplazar el problema rector del cierre definitivo.
9. Cuando el problema principal es `cerrar definitivamente`, el caso pertenece aquí aunque luego habilite decisiones en otros contexts.
10. **El egreso definitivo cierra el vinculo del ocupante vigente; la vacante de base que resulte o persista debe expresarse aparte y no confundirse con la ocupacion posterior del cargo.**
11. **Si una suplencia o cobertura dependia del ocupante saliente, no puede sostenerse por la sola persistencia de la vacante; debe cerrarse, revisarse o derivarse a `[[BC-03 - Cobertura transitoria]]`.**
12. **Si el efecto posterior principal es ocupar establemente un cargo vacante, la resolucion debe salir a `[[BC-06 - Ocupacion estable de cargos]]`.**
13. `Retiro voluntario` integra este bounded context como subtipo propio y no debe diluirse dentro de `jubilacion` ni de `renuncia aceptada`.
14. `Cesantia` y `exoneracion` no deben mezclarse con causales naturales o protectorias de egreso, aunque todas integren el mismo bounded context.

## 9. Relación con otros bounded contexts

- recibe casos ya clasificados desde `[[BC-01 - Encuadre administrativo de la novedad]]`,
- puede requerir evidencia o conciliacion desde `[[BC-07 - Integraciones y conciliación]]` cuando la causal o la fecha provengan de fuentes externas,
- puede exigir revision, cierre o derivacion de coberturas existentes hacia `[[BC-03 - Cobertura transitoria]]`,
- puede habilitar derivaciones a `[[BC-06 - Ocupacion estable de cargos]]` cuando el egreso deje una vacante apta para ocupacion estable,
- entrega sus resultados a `[[BC-08 - Consolidacion y cierre]]`,
- no debe mezclarse con `[[BC-02 - Interrupciones temporales]]` ni con `[[BC-04 - Movilidad funcional]]`.

## 10. Puntos de interfaz relevantes

- aceptacion de renuncia y permanencia transitoria asociada,
- configuración previsional suficiente para jubilación,
- regimen especial y soporte suficiente para `retiro voluntario`,
- cese por fallecimiento como causal directa con constancia trazable,
- actos sancionatorios para `cesantia` y `exoneracion`,
- dictamen medico, junta o soporte equivalente para causales legales especiales,
- distincion entre egreso del ocupante vigente y estado posterior de la vacante de base,
- vacante resultante, vacante persistente, cambio de carácter de vacante y cierre de plaza como efectos inmediatos del egreso,
- trazabilidad de suplencias, reemplazos u otros casos dependientes,
- proyección residual del egreso cuando exista transición protegida del agente o beneficio externo a familiares.

## 11. Preguntas abiertas para investigación

- que otras causales equivalentes conviene incorporar mas adelante al catalogo inicial con nombre propio,
- en que casos exactos el egreso genera `cambio de caracter de vacante` y en cuales `cierre de plaza`,
- que automatizaciones seguras pueden derivar desde `BC-05` hacia `BC-03` o `BC-06` sin deformar la decision de negocio.
