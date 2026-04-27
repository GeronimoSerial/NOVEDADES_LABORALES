---
proyecto: Sistematizacion de Novedades Laborales
tipo: Bounded context
estado: vigente
date: 2026-04-06
tags:
  - sgpem
  - bounded-context
  - ocupacion-estable
  - titularizacion
  - interinatos
---

# BC-06 - Ocupación estable de cargos

> [!abstract] Proposito
> Este bounded context reune las novedades cuyo efecto principal es proveer de manera estable un cargo o plaza habilitada. Su problema central no es reemplazar temporalmente a otro docente ni explicar un movimiento funcional, sino distinguir con trazabilidad una ocupacion estable valida, su subtipo normativo, la vacante real que la habilita o la habilitacion estructural externa que la origina, la validacion rectora que la habilita y el momento en que esa ocupacion queda efectivizada mediante toma de posesion.

## 1. Por que es un bounded context separado

Según el Estatuto y la practica operativa, el interino no encaja en cobertura transitoria, cuyo problema rector es la suplencia o el reemplazo temporario. Tampoco toda ocupacion estable es la etapa final de un movimiento funcional: si el problema principal sigue siendo explicar un traslado, una reubicacion o un cambio de destino, la rectoria permanece en `[[BC-04 - Movilidad funcional]]`. El rasgo común de este contexto es otro: resolver una provisión estable sobre vacante real o cargo habilitado externamente, ya sea bajo carácter interino, titular o por continuidad o conversión con antecedente de suplencia cuando el régimen lo habilita. **En este contexto, `estable` no equivale necesariamente a `permanente`.**

La lectura transversal de plaza, estado y vacante debe mantenerse consistente con `[[Concepto transversal - Plaza, estado y vacante]]`.

## 2. Que incluye

- interinatos ordinarios sobre vacante real,
- reincorporaciones que restituyen al agente a una ocupacion estable de base,
- titularizacion por concurso de ingreso a la docencia u otro mecanismo equivalente,
- titularizacion por concurso de ascenso u otro mecanismo equivalente,
- continuidad o conversión con antecedente de suplencia cuando una vacante real y la norma habilitante lo permitan,
- adjudicaciones, designaciones o actos suficientes que produzcan una ocupación estable,
- cierres de revista interina que no rompen por si mismos el vinculo laboral general,
- regularizacion de aperturas estables mal soportadas o mal efectivizadas cuando el problema siga siendo la provision estable del cargo,
- otras ocupaciones estables equivalentes con base normativa suficiente.

## 3. Que no incluye

- coberturas transitorias,
- suplencias o reemplazos temporarios,
- la cobertura del cargo de origen cuando un movimiento se apoya en licencia por mayor jerarquia,
- licencias o interrupciones temporales,
- movimientos funcionales en cuanto tales,
- egresos definitivos,
- la administración estructural completa de plazas o POF como dominio autónomo,
- el modelado completo del concurso, la asamblea o el acto publico como proceso autónomo externo,
- la consolidación y cierre de periodo.

## 4. Subfamilias internas

### 4.1 Interinato ordinario

- es una ocupación estable no permanente sobre vacante real,
- requiere adjudicación, designación o acto suficiente según régimen,
- no debe confundirse con suplencia sobre ausencia temporaria,
- cuando la apertura sale por resolucion ministerial, su validacion rectora depende de `Direccion de nivel`,
- puede validarse antes, pero no queda efectivizado sin toma de posesión.

### 4.2 Titularizacion por ingreso

- expresa el acceso permanente al cargo por concurso de ingreso u otro mecanismo normativamente equivalente,
- Implica cambio de situación de revista y obligaciones posteriores de permanencia según régimen,
- cuando la apertura sale por resolucion ministerial, su validacion rectora depende de `Direccion de nivel`,
- la notificación o designación no alcanza por si sola para considerar efectivizada la ocupación,
- requiere toma de posesión y soporte suficiente del resultado habilitante.

### 4.3 Titularización por ascenso

- expresa el acceso permanente a un cargo superior o distinto por resultado de ascenso u otro mecanismo equivalente,
- no debe absorber como propio el movimiento funcional previo cuando ese movimiento sea el problema rector,
- cuando existe una decisión propia y separable de provisión estable sobre el cargo de destino, el caso pertenece aquí,
- cuando la apertura sale por resolucion ministerial, su validacion rectora depende de `Direccion de nivel`,
- requiere toma de posesión para quedar efectivizada.

### 4.4 Continuidad o conversión con antecedente de suplencia

- parte de una suplencia antecedente trazable, pero no toda suplencia antecedente produce continuidad o conversion,
- exige vacante real y habilitación normativa suficiente para abrir una ocupación estable,
- según nivel o régimen, puede mantener al ocupante actual o exigir nueva designación,
- no debe presumirse como pase automático desde `[[BC-03 - Cobertura transitoria]]`,
- cuando la apertura estable queda respaldada por resolucion ministerial, su validacion rectora depende de `Direccion de nivel`,
- exige nueva toma de posesión **porque cambia la base normativa de la prestacion y la trazabilidad administrativa del caso**.

### 4.5 Otras ocupaciones estables equivalentes

- permiten conservar una categoría abierta sin mezclar cualquier novedad con este contexto,
- solo deben incorporarse si comparten provisión estable sobre cargo o plaza habilitada,
- requieren subtipo identificable, vacante o habilitación trazable y soporte suficiente,
- no deben usarse para absorber movimientos, coberturas o cierres mal clasificados.

### 4.6 Validacion rectora y efectivizacion

| Subtipo                                               | Validacion rectora    | Efectivizacion        | Observaciones |
| ----------------------------------------------------- | --------------------- | --------------------- | ------------- |
| Interinato ordinario                                  | Direccion de nivel    | Toma de posesion local | La apertura puede quedar validada por resolucion ministerial antes del ejercicio efectivo y de la liquidacion |
| Titularizacion por ingreso                            | Direccion de nivel    | Toma de posesion local | Suele apoyarse en concurso, orden de merito, designacion y toma de posesion |
| Titularizacion por ascenso                            | Direccion de nivel    | Toma de posesion local | Debe distinguirse del movimiento funcional que la precede cuando exista |
| Continuidad o conversion con antecedente de suplencia | Direccion de nivel    | Toma de posesion local | La continuidad material no basta por si sola; debe existir apertura estable validada y nueva toma de posesion |
| Otras ocupaciones estables equivalentes               | Segun fuente habilitante | Toma de posesion local cuando corresponda | Incluye reincorporaciones y otros casos equivalentes cuando el problema rector sigue siendo la ocupacion estable |

### 4.7 Soporte mínimo por fuente habilitante

| Fuente                              | Soporte minimo relevante para BC-06                                                                                                                                                                                              | Observaciones distintivas                                                                                                                                                   |
| ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Concurso                            | Agente identificado, cargo o plaza involucrada, resultado u orden de merito trazable, constancia de designacion o notificacion y fecha prevista o efectiva de toma de posesion                                                   | Si la designacion no culmina en toma de posesion dentro del plazo aplicable, el nombramiento puede quedar sin efecto; puede proyectar permanencia obligatoria segun regimen |
| Asamblea o acto publico habilitante | Agente identificado, cargo o plaza ofrecida, listado o fuente habilitante, constancia de opcion o designacion y, cuando corresponda, acreditacion de DNI, cuaderno de actuacion profesional y certificado de aptitud psicofisica | En este contexto solo interesa cuando la asamblea adjudica una ocupacion estable; la asamblea general de suplencias ordinarias permanece fuera de `BC-06`                   |
| Adjudicacion                        | Agente identificado, cargo o plaza, fuente habilitante, constancia de adjudicacion y fecha prevista o efectiva de toma de posesion                                                                                               | Cuando el regimen lo exija, debe poder verificarse titulo registrado, aptitud psicofisica, declaracion jurada de incompatibilidad y documentacion excluyente del acto       |
| Acto administrativo                 | Agente identificado, cargo o plaza, subtipo estable, fecha efectiva o prevista, acto suficiente y referencia a vacante real o habilitacion estructural externa                                                                   | No presenta una diferencia fuerte respecto de la base comun, pero debe permitir reconstruir la causa habilitante de la provision estable                                    |

### 4.8 Soporte mínimo, soporte fuerte adicional y regla de efectivización

| Subtipo                                               | Soporte mínimo obligatorio                                                                                                                                               | Soporte fuerte adicional                                                                                                                                                     | Regla de efectivizacion                                                                                                                                        |
| ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Interinato ordinario                                  | Vacante real identificable, agente identificado, cargo o plaza, adjudicacion, designacion o acto suficiente y fecha prevista o efectiva de toma de posesion              | Referencia a resolución ministerial, orden de merito, constancia del acto publico o verificación de habilitación del agente cuando corresponda                               | Puede quedar validado antes por `Direccion de nivel`, pero solo queda efectivizado con toma de posesion local                                                  |
| Titularizacion por ingreso                            | Resultado de concurso o mecanismo equivalente, agente identificado, cargo o plaza, designacion o notificacion suficiente y fecha prevista o efectiva de toma de posesion | ~~Referencia a resolución ministerial, permanencia obligatoria, declaración jurada de incompatibilidad, aptitud psicofisica y titulo registrado cuando el régimen lo exija~~ | La titularizacion puede quedar validada antes por `Direccion de nivel`, pero no queda efectivizada sin toma de posesion local                                  |
| Titularizacion por ascenso                            | Resultado de ascenso u otro mecanismo equivalente, agente identificado, cargo o plaza de destino, designacion suficiente y fecha prevista o efectiva de toma de posesion | Referencia a resolución ministerial, concurso, orden de merito, incompatibilidades y cambio de situación de revista cuando corresponda                                       | La ocupacion estable del cargo de destino puede quedar validada antes por `Direccion de nivel`, pero se efectiviza con toma de posesion local                  |
| Continuidad o conversion con antecedente de suplencia | Suplencia antecedente trazable, vacante real, habilitacion normativa suficiente, agente identificado y nueva toma de posesion                                            | Verificacion de resolucion ministerial, titulo o habilitacion cuando la norma lo exija y constancia de si corresponde continuidad o nueva designacion                        | Nunca debe darse por efectivizada por simple continuidad material del ejercicio; requiere nueva toma de posesion                                               |
| Otras ocupaciones estables equivalentes               | Subtipo identificable, agente identificado, cargo o plaza, vacante real o habilitacion estructural externa y acto o respaldo suficiente                                  | Depende del subtipo; puede incluir resolucion ministerial, concurso, adjudicacion, titulo registrado o incompatibilidades                                                    | Se rigen por la regla general del contexto: validacion previa posible y efectivizacion solo con toma de posesion; incluye reincorporaciones cuando corresponda |

### 4.9 Regla general de toma de posesión
- toda ocupación estable puede quedar validada antes de quedar efectivizada,
- la toma de posesión se exige para interinatos, titularizaciones, reincorporaciones y otras provisiones estables equivalentes,
- en continuidad o conversión con antecedente de suplencia también se exige nueva toma de posesión, aunque el agente ya viniera ejerciendo en la misma plaza,
- la validacion por `Direccion de nivel` o por fuente habilitante suficiente no equivale por si sola a ocupacion estable efectivizada,
- la nueva toma de posesion deja constancia de que el agente ahora presta servicios bajo un nuevo encuadre legal y permite separar periodos,
- esa nueva toma de posesión evita absorber indebidamente la suplencia dentro de la ocupación estable, deja trazable el cambio de base normativa de la prestación y preserva la separabilidad entre `[[BC-03 - Cobertura transitoria]]` y `BC-06`,
- tambien fija el inicio del ejercicio efectivo y habilita la liquidacion correspondiente,
- sin toma de posesión no debe considerarse iniciado el ejercicio estable del cargo aunque exista adjudicación, designación o acto administrativo.

> [!NOTE] **Motivos por el cual es ideal su obligatoriedad**:
> La naturaleza jurídica de la prestación cambia. Mientras que en la suplencia el problema rector es la cobertura de un agente, en el interinato es la provisión estable de una vacante. 
> Permite separar períodos, y considerar efectivo el cambio jurídico de su situación de revista para su correspondiente liquidación.
### 4.10 Ciclo de vida de la ocupación estable

1. ocupación estable encuadrada,
2. base habilitante validada,
3. ocupación estable observada, cuando corresponda,
4. toma de posesión registrada,
5. ocupación estable efectivizada,
6. ocupación estable vigente,
7. ocupación estable regularizada.

### 4.11 Interfaces derivadas de la provisión estable

- si el problema principal sigue siendo una suplencia o un reemplazo temporario, la resolución pertenece a `[[BC-03 - Cobertura transitoria]]`,
- si el problema principal sigue siendo un traslado, una reubicacion, tareas pasivas u otro movimiento funcional, la rectoria permanece en `[[BC-04 - Movilidad funcional]]`,
- cuando un egreso definitivo deja o hace persistir una vacante apta para provisión estable, `[[BC-05 - Egresos definitivos]]` sigue siendo rector del cierre y `BC-06` recibe la vacante resultante o persistente,
- una vacante también puede originarse en creación o habilitación estructural externa sin que `BC-06` deba modelar el dominio estructural completo,
- si la ocupación estable se apoya en resultado externo, adjudicación o acto digitalizado, puede requerir normalizacion desde `[[BC-07 - Integraciones y conciliación]]`,
- la consolidación administrativa posterior del caso pertenece a `[[BC-08 - Consolidacion y cierre]]`.

## 5. Conceptos principales

- ocupación estable,
- ocupación estable no permanente,
- ocupación permanente,
- vacante real,
- habilitación estructural externa,
- provisión estable,
- interinato ordinario,
- titularizacion por ingreso,
- titularizacion por ascenso,
- continuidad o conversión con antecedente de suplencia,
- toma de posesión,
- base habilitante,
- adjudicacion valida,
- orden de merito,
- situación de revista,
- efectivizacion de la ocupación,
- ejercicio estable del cargo,
- regularización de la ocupación estable.

## 6. Eventos principales

- ocupación estable encuadrada,
- base habilitante validada,
- ocupación estable observada,
- toma de posesión registrada,
- ocupación estable efectivizada,
- continuidad o conversión reconocida,
- ocupacion estable vigente,
- ocupacion estable regularizada.

## 7. Decisiones principales

- si existe vacante real o habilitacion estructural externa ya trazable para abrir la ocupacion estable,
- si el caso corresponde a interinato ordinario, titularizacion por ingreso, titularizacion por ascenso u otra provision estable equivalente,
- si una `reincorporacion` devuelve al agente a una ocupacion estable de base o si en realidad se trata de un `reintegro` perteneciente a `[[BC-02 - Interrupciones temporales]]`,
- si una ocupacion estable derivada de una suplencia antecedente admite continuidad del mismo ocupante o exige nueva designacion segun regimen,
- si la ocupacion estable ya quedo validada por `Direccion de nivel` o por fuente habilitante suficiente,
- si el resultado externo, la adjudicacion o el acto administrativo son suficientes para habilitar el caso,
- que soporte adicional debe exigirse segun la fuente habilitante,
- si corresponde verificar titulo registrado o habilitacion especifica cuando la norma lo exija,
- desde cuando la ocupacion estable produce efectos administrativos y desde cuando queda efectivizada,
- si la toma de posesion local ya permite iniciar el ejercicio estable del cargo y habilitar la liquidacion,
- si una ausencia transitoria recae sobre el ocupante vigente del cargo y por lo tanto el reemplazo corresponde a `[[BC-03 - Cobertura transitoria]]` aunque la plaza siga siendo vacante en sentido estructural,
- si el caso debe observarse, regularizarse o derivarse por falta de evidencia o inconsistencia,
- si un `cese de interinato` cierra solo la revista interina por fecha limite del origen o si existe ademas otro problema rector fuera de este contexto,
- si la rectoria sigue en `[[BC-04 - Movilidad funcional]]` o si ya existe una decision propia de provision estable que pertenece a `BC-06`.

## 8. Reglas centrales de delimitacion

1. **No hay interinato sin vacante real.**
2. **Toda ocupación estable debe poder apoyarse en vacante real o en habilitación estructural externa ya trazada.**
3. **La suplencia no reemplaza la vacante de base ni la ocupación estable en si misma, ==sino la ausencia transitoria del ocupante vigente==; por eso puede existir suplencia sobre una plaza vacante con interino activo.**
4. **Toda ocupación estable debe apoyarse en un resultado, adjudicación o acto suficientemente valido.**
5. La ocupación estable puede quedar validada antes, pero **no queda efectivizada sin toma de posesion**.
6. **La continuidad o conversión con antecedente de suplencia no es automatica; depende del regimen aplicable y puede exigir nueva designación y/o toma de posesión.**
7. **La validacion por resolucion ministerial o por fuente habilitante suficiente no equivale por si sola a ocupacion estable efectivizada.**
8. **En los casos con antecedente de suplencia, la nueva toma de posesión es obligatoria porque cambia la base normativa de la prestación, la trazabilidad del cargo, el inicio del ejercicio efectivo y la liquidacion correspondiente.**
9. `Estable` no equivale necesariamente a `permanente`; el interinato integra este contexto aunque no sea titularizacion.
10. Cuando el problema principal es **`ocupar establemente un cargo`,** el caso pertenece aquí.
11. Si el problema principal sigue siendo `mover o reasignar`, la rectoria permanece en `[[BC-04 - Movilidad funcional]]` aunque luego aparezca una vacante o un destino estable.
12. Si un movimiento se apoya en licencia por mayor jerarquía, la cobertura del cargo de origen no pertenece a este contexto porque no hay provisión estable del cargo base.
13. La asamblea, el concurso o el acto publico pueden ser externos, pero su efecto laboral sobre el cargo debe ser comprensible dentro del dominio.
14. La consolidación administrativa del caso no pertenece aquí: debe salir a `[[BC-08 - Consolidacion y cierre]]`.
15. `Reincorporacion` pertenece aqui cuando restituye al agente a una ocupacion estable de base; no debe confundirse con `reintegro`, que sigue siendo cierre de una interrupcion temporal en `[[BC-02 - Interrupciones temporales]]`.
16. `Cese de Interinato` pertenece aqui cuando cierra el ciclo de vida de la revista interina por fecha limite del origen y no debe arrastrarse a `[[BC-05 - Egresos definitivos]]` salvo que exista un egreso definitivo rector separado.

## 9. Relación con otros bounded contexts

- recibe casos clasificados desde `[[BC-01 - Encuadre administrativo de la novedad]]`,
- puede recibir derivaciones desde `[[BC-03 - Cobertura transitoria]]` cuando una suplencia antecedente habilite continuidad o conversión a ocupación estable,
- puede recibir derivaciones desde `[[BC-04 - Movilidad funcional]]` cuando exista una decision propia y separable de provision estable sobre el cargo de destino,
- puede consumir vacantes resultantes o persistentes desde `[[BC-05 - Egresos definitivos]]` cuando el cierre definitivo deje una plaza apta para ocupacion estable o deje expresada una vacante de base ya existente,
- no absorbe el `reintegro` derivado de licencias u otras interrupciones, aunque pueda recibir `reincorporaciones` como retorno a ocupacion estable,
- puede consumir resultados externos normalizados desde `[[BC-07 - Integraciones y conciliación]]`,
- entrega sus resultados a `[[BC-08 - Consolidacion y cierre]]`,
- debe quedar claramente separado de `[[BC-03 - Cobertura transitoria]]` aun cuando exista antecedente de suplencia.

## 10. Puntos de interfaz relevantes

- vacante real o habilitación estructural externa como condición de apertura,
- validacion rectora por `Direccion de nivel` cuando la apertura estable sale por resolucion ministerial,
- concursos de ingreso y de ascenso como fuentes frecuentes de provisión estable,
- asambleas o actos públicos solo cuando efectivamente adjudican una ocupación estable,
- adjudicaciones o actos administrativos equivalentes como soporte principal del caso,
- distincion explicita entre `reincorporacion` y `reintegro`,
- toma de posesion local como requisito de efectivizacion del ejercicio estable y de la liquidacion,
- cierre de `cese de interinato` como clausura de la revista por fecha limite del origen y no como egreso definitivo natural,
- continuidad o conversion con antecedente de suplencia sin presuncion automatica,
- verificacion de titulo registrado, aptitud psicofisica y declaracion jurada cuando el regimen lo exija,
- impacto sobre la situacion de revista y la vigencia administrativa del cargo.

## 11. Preguntas abiertas para investigación

- que subtipos equivalentes conviene incorporar mas adelante con nombre propio dentro de `otras ocupaciones estables equivalentes`,
- que diferencias exactas de soporte conviene exigir entre mecanismos de adjudicacion con regimenes distintos,
- que automatizaciones seguras pueden derivar a `BC-06` desde `BC-03`, `BC-04` o `BC-05` sin deformar la decision de negocio.
