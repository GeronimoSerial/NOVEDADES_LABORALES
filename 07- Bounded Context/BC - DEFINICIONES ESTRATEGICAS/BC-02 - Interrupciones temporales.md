---
proyecto: Sistematizacion de Novedades Laborales
tipo: Bounded context
estado: borrador
date: 2026-04-06
tags:
  - sgpem
  - bounded-context
  - interrupciones
  - licencias
---

# BC-02 - Interrupciones temporales

> [!abstract] Propósito
> Este bounded context reune las novedades cuyo efecto principal es **suspender, alterar o pausar temporalmente un caso laboral sin convertirlo en egreso definitivo**. Su centro de gravedad es la temporalidad del caso: cuándo empieza la interrupción, bajo que condición se mantiene y cómo se cierra o reanuda. La autoridad operativa dentro de este contexto no es uniforme: depende del subtipo de interrupción.

## 1. Por qué es un bounded context separado

Licencias, inasistencias, paro y reintegros comparten una misma tensión de negocio: modifican temporalmente un estado sin cerrarlo en forma definitiva. Esa lógica es distinta de cubrir la ausencia, mover funcionalmente a una persona o cerrar un caso para siempre. La diferencia entre subtipos no obliga a romper el bounded context, pero si exige reglas internas distintas de autoridad, soporte y automatización.

## 2. Qué incluye

- licencias médicas integrables,
- licencias médicas no integradas absorbidas dentro de la misma subfamilia,
- licencias extraordinarias,
- licencias para personal suplente,
- inasistencias y paro,
- reintegros,
- observación y cierre de interrupciones temporales.

## 3. Qué no incluye

- la designación de suplencias o coberturas,
- la movilidad funcional,
- los egresos definitivos,
- la consolidación final del periodo,
- la traducción técnica de eventos externos.

## 4. Subpuntos iniciales

### 4.1 Licencias médicas

- recepción de aprobación externa,
- absorción de casos médicos que no entren por integración plena,
- datos mínimos de la interrupción,
- convivencia con el modulo medico integrable.

### 4.2 Licencias extraordinarias

- licencias extraordinarias,
- lectura central del encuadre,
- respaldo normativo o administrativo suficiente.

### 4.3 Licencias para personal suplente

- interrupciones sobre prestaciones de carácter suplente,
- lectura de la condición de suplencia al momento del caso,
- necesidad de validación final por Dirección de nivel.

### 4.4 Inasistencias y paro

- inasistencias,
- paro,
- eventos breves sin cierre definitivo,
- fuerte peso del registro y validación local.

### 4.5 Reintegros

- existencia de antecedente valido,
- fecha de retorno,
- cierre del caso transitorio,
- no debe confundirse con `reincorporacion`, que pertenece a `[[BC-06 - Ocupación estable de cargos]]` cuando restituye una ocupacion estable de base,
- ==caso de atención: sigue abierta la definición de si requiere o no una formalización administrativa adicional equivalente a toma de posesión.==

### 4.6 Autoridad operativa por subtipo

| Subtipo                          | Ultima palabra operativa | Observaciones                                                                                                                    |
| -------------------------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| Licencias médicas                | Integración externa      | La aprobación externa prevalece como habilitante; SGPEM sigue gobernando su correlación y tratamiento interno cuando corresponde |
| Licencias extraordinarias        | SGPEM central            | Se prioriza lectura central del encuadre en casos dudosos o con mas de una posible interpretación                                |
| Licencias para personal suplente | Dirección de nivel       | La condición de suplencia exige validación final especifica                                                                      |
| Inasistencias y paro             | Establecimiento          | Predomina el registro y validación local                                                                                         |
| Reintegros                       | ***Sistema*** central    | Debe controlar antecedente, vinculo y cierre correcto                                                                            |

### 4.7 Soporte mínimo y automatización por subtipo

| Subtipo                          | Soporte minimo relevante                                 | Puede preclasificarse o derivarse automaticamente desde BC-01                   |
| -------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------- |
| Licencias médicas                | Certificado medico o evento/aprobación externa confiable | Si, cuando el origen es confiable, el caso es consistente y el subtipo es claro |
| Licencias extraordinarias        | Acto administrativo                                      | Si, cuando el acto y el subtipo son inequívocos                                 |
| Licencias para personal suplente | Acto administrativo o constancia suficiente              | No por defecto                                                                  |
| Inasistencias y paro             | Constancia local                                         | Si, cuando el registro local es claro, completo y sin contradiccion             |
| Reintegros                       | Referencia a antecedente valido y fecha de retorno       | Si, cuando el antecedente es trazable y no hay contradiccion temporal           |

## 5. Conceptos principales

- interrupción temporal,
- subtipo de interrupción,
- antecedente válido,
- vigencia,
- causal de interrupción,
- aprobación externa,
- constancia local,
- reintegro,
- superposición invalida.

## 6. Eventos principales

- interrupción registrada,
- interrupción validada,
- interrupción observada,
- interrupción aplicada,
- reintegro registrado,
- interrupción cerrada.

## 7. Decisiones principales

- si el caso interrumpe realmente en forma temporal,
- qué subtipo de interrupción corresponde,
- cuándo comienza su vigencia,
- si requiere observación o evidencia adicional,
- cuándo un reintegro es válido,
- si la interrupción habilita o no acciones posteriores de cobertura,
- si el subtipo admite preclasificación o derivación automática desde `[[BC-01 - Encuadre administrativo de la novedad]]`.

## 8. Reglas centrales de delimitación

1. Una interrupción temporal **no equivale a un egreso definitivo**.
2. **Todo reintegro exige un antecedente** transitorio válido.
3. `Reintegro` no equivale a `reincorporacion`; el primero cierra una temporalidad previa y la segunda puede reabrir una ocupacion estable de base en `[[BC-06 - Ocupación estable de cargos]]`.
4. No se admiten solapamientos inválidos entre interrupciones incompatibles.
5. Este contexto decide la existencia, vigencia y cierre de la interrupción.
6. **Este contexto no resuelve por si mismo la cobertura transitoria derivada; solo produce la información habilitante para ello.**
7. La autoridad operativa dentro de este contexto **depende del subtipo** y no debe asumirse uniforme.
8. Las licencias medicas pueden apoyarse en integración externa sin que eso elimine el gobierno interno de SGPEM sobre correlación y trazabilidad.
9. Inasistencias y paro admiten mayor peso de registracion local que otros subtipos del mismo contexto.

## 9. Relación con otros bounded contexts

- recibe casos ya clasificados desde `[[BC-01 - Encuadre administrativo de la novedad]]`,
- puede habilitar casos en `[[BC-03 - Cobertura transitoria]]`,
- consume eventos externos normalizados desde `[[BC-07 - Integraciones y conciliacion]]`,
- entrega resultados a `[[BC-08 - Consolidacion y cierre]]`.

## 10. Puntos de interfaz relevantes

- módulo externo de licencias medicas,
- aprobaciones o eventos externos para licencias medicas,
- validaciones documentales o normativas según familia,
- registro local inevitable en inasistencias injustificadas,
- confirmaciones locales cuando correspondan,
- tratamiento del reintegro como novedad propia dentro de SGPEM.

## 11. Preguntas abiertas para investigación

- que subtipos concretos deben integrar `licencias extraordinarias`,
- qué diferencias operativas deben mantenerse entre licencias medicas integrables y no integradas,
- qué eventos deben habilitar cobertura y cuales no,
- si el reintegro exige o no una formalizacion administrativa adicional; por ahora, la fecha `hasta` del antecedente funciona como limite suficiente para requerir presencia,
- qué parte de este contexto debe centralizarse primero.
