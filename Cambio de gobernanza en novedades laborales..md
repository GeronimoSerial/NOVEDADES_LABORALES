---
proyecto: Sistematización de Novedades Laborales
tipo: Sintesis funcional
estado: v0.1
date: 2026-04-01
tags:
  - sgpem
  - novedades-laborales
  - primaria
  - gobernanza
  - mermaid
---

# Tipos de novedades laborales en primaria

> [!abstract] Resumen ejecutivo
>El objetivo debe ser reducir progresivamente la cantidad de novedades laborales que la escuela necesita informar directamente. Para eso, primero conviene modernizar los flujos hoy analogicos, manuales o redundantes, absorber por integracion o por ordenamiento central los casos que ya no necesitan originacion escolar plena, y dejar al establecimiento en un rol mas acotado de deteccion, confirmacion o ejecucion. En primaria, esto implica empezar por las familias con mayor naturalidad administrativa y mayor posibilidad de salir del circuito local intensivo.

## 1. Criterio de lectura

Esta nota reordena las novedades laborales segun una pregunta de gobernanza:

**que novedades deben seguir siendo informadas por el establecimiento y cuales deberían migrar progresivamente a circuitos mas centralizados, integrados o asistidos?**

## 2. Mapa general por gobernanza

```mermaid
flowchart TD
    A[Novedades laborales] --> B[Central o casi central]
    A --> C[Mixta o transicional]
    A --> D[Local o inevitablemente local]

    B --> B1[Creacion y desafectacion de plaza]
    B --> B2[Baja definitiva]
    B --> B3[Traslado definitivo, reubicacion y permuta]
    B --> B4[Adscripcion, comision, tareas pasivas y afectacion]
    B --> B5[Licencias medicas integrables]
    B --> B6[Vacantes, interinatos y titularizacion]

    C --> C1[Reintegro]
    C --> C2[Suplencia]
    C --> C3[Traslado transitorio]
    C --> C4[Licencias extraordinarias]
    C --> C5[Licencias para personal suplente]
    C --> C6[Licencias por salud y maternidad sin integracion plena]

    D --> D1[Inasistencia injustificada]
    D --> D2[Paro]
    D --> D3[Inasistencia justificada y confirmacion local]
```

## 3. Cambio de paradigma

```mermaid
flowchart LR
    A[Esquema actual] --> A1[El establecimiento detecta]
    A1 --> A2[El establecimiento interpreta]
    A2 --> A3[El establecimiento informa muchas familias]
    A3 --> A4[La administracion ordena tarde]

    B[Esquema objetivo] --> B1[La administracion ordena antes]
    B1 --> B2[Integra o absorbe familias alineadas]
    B2 --> B3[SGPEM moderniza circuitos analogicos remanentes]
    B3 --> B4[El establecimiento confirma, ejecuta o completa excepciones]

    A4 -. migracion gradual .-> B1
```

## 4. Embudo de reducción de carga para el establecimiento

```mermaid
flowchart TD
    A[Universo total de novedades] --> B[1. Novedades que pueden salir del circuito escolar pleno]
    B --> B1[Creacion y desafectacion de plaza]
    B --> B2[Bajas definitivas]
    B --> B3[Movilidad con acto administrativo]
    B --> B4[Licencias medicas integrables]

    B --> C[2. Novedades que deben quedar en SGPEM pero con flujo simplificado]
    C --> C1[Licencias extraordinarias]
    C --> C2[Licencias para personal suplente]
    C --> C3[Licencias por salud y maternidad sin modulo aplicable]
    C --> C4[Reintegros]

    C --> D[3. Novedades que pueden seguir locales en una fase posterior]
    D --> D1[Inasistencia injustificada]
    D --> D2[Paro]
    D --> D3[Inasistencia justificada y confirmaciones operativas puntuales]
```

## 5. Familias principales y subfamilias relevantes

### 5.1 Estructura POF

- Creacion de plaza
- Desafectacion de plaza

### 5.2 Prestacion ordinaria

- Alta por designacion
- Baja por cese
- Reintegro
- Inasistencia justificada
- Inasistencia injustificada
- Paro

### 5.3 Cobertura y reemplazo

- Suplencia

### 5.4 Vacantes, interinatos y titularizacion

- Interinato sobre cargo vacante
- Pase de suplente a interino por vacancia definitiva
- Titularizacion por concurso de ingreso
- Titularizacion por concurso de ascenso

### 5.5 Movilidad funcional

- Traslado transitorio
- Traslado definitivo
- Adscripcion
- Comision de servicio
- Tareas pasivas
- Afectacion
- Permuta
- Reubicacion
- Mayor jerarquia como movimiento rector con efectos derivados sobre origen y destino

### 5.6 Licencias separadas en subfamilias

- Licencias medicas integrables por modulo externo
- Licencias por salud sin integracion plena
- Licencias por maternidad
- Licencias extraordinarias
- Licencias para personal suplente

> [!info]
> En esta nota, `licencias por salud y maternidad` se mantienen visibles como subfamilia diferenciada porque parte de esos casos puede convivir con integracion medica futura, mientras otra parte puede seguir requiriendo tratamiento propio dentro de SGPEM. A su vez, `licencias extraordinarias` y `licencias para personal suplente` deben quedar explicitamente fuera del supuesto de cobertura total del modulo de licencias medicas previsto.

## 6. Matriz sugerida por familia de novedad para primaria

| Familia de novedad                                     | Esquema actual dominante      | Objetivo ideal                                               | Sugerencia transicional                                                                       | Prioridad  |
| ------------------------------------------------------ | ----------------------------- | ------------------------------------------------------------ | --------------------------------------------------------------------------------------------- | ---------- |
| Creacion / desafectacion de plaza                      | Central                       | Min. ordena y deriva                                         | Mantener como caso ya alineado y usarlo como referencia                                       | Alta       |
| Adscripcion / comision / tareas pasivas / afectacion   | Mixto con peso central        | Central ordena primero                                       | Migrar temprano a bandeja central                                                             | Alta       |
| Traslado definitivo / reubicacion / permuta            | Mixto                         | Central ordena primero                                       | Reforzar origen administrativo y derivacion controlada                                        | Alta       |
| Vacantes / interinatos / titularizacion             | Mixto con acto externo o designacion administrativa | Ordenamiento central con confirmacion local obligatoria      | Tratar como provision de vacantes separada de suplencias                                      | Alta       |
| Licencias medicas integrables                          | Mixto con dependencia externa | Origen externo o central con ordenamiento temprano           | Integrar por eventos aprobados cuando el modulo este disponible                               | Alta       |
| Licencias por salud y maternidad sin integracion plena | Mixto                         | SGPEM ordena antes con circuito simplificado                 | Crear flujo propio simplificado dentro de SGPEM mientras no exista cobertura externa completa | Alta       |
| Licencias extraordinarias                              | Mixto o local segun caso      | Ordenamiento administrativo temprano con catalogo claro      | Estandarizar subtipos, respaldo minimo y derivacion asistida                                  | Media/Alta |
| Licencias para personal suplente                       | Local con alta ambiguedad     | SGPEM ordena sobre prestacion y condicion de suplencia       | Definir reglas especificas y evitar tratamiento residual manual                               | Alta       |
| Reintegro                                              | Local                         | Central ordena sobre antecedente valido                      | Mantener carga local asistida con validacion previa                                           | Media/Alta |
| Suplencia                                            | Mixto con peso local          | Central ordena sobre ausencia validada                       | Implementar preorden administrativo antes del reemplazo                                       | Media/Alta |
| Traslado transitorio                                   | Mixto y complejo              | Central ordena con doble control                             | Tratar como flujo especial con responsable bilateral                                          | Alta       |
| Baja definitiva                                        | Central                       | Central ordena sobre respaldo y efecto de plaza              | Mantener y formalizar trazabilidad completa                                                   | Alta       |
| Inasistencia injustificada                            | Inevitablemente local         | Registro local con ordenamiento administrativo posterior     | Mantener origen local y reforzar trazabilidad minima                                          | Media/Baja |
| Inasistencia justificada / paro                       | Fuertemente local             | Ordenamiento administrativo posterior o muestreo inteligente | Dejar para fase tardia del cambio                                                             | Media/Baja |


## 7. Matriz visual de prioridad y fase

```mermaid
flowchart TD
    F0[Fase 0<br/>lenguaje comun y catalogos] --> F1[Fase 1<br/>ordenamiento central en sombra]
    F1 --> F2[Fase 2<br/>cambio real en familias priorizadas]
    F2 --> F3[Fase 3<br/>derivacion estructurada al establecimiento]
    F3 --> F4[Fase 4<br/>expansion a casos complejos y locales]

    F2 --> A1[Creacion y desafectacion de plaza]
    F2 --> A2[Adscripcion, comision, tareas pasivas, afectacion]
    F2 --> A3[Traslado definitivo, reubicacion, permuta]
    F2 --> A4[Vacantes, interinatos y titularizacion]
    F2 --> A5[Licencias medicas integrables]
    F2 --> A6[Licencias por salud y maternidad sin integracion plena]
    F2 --> A7[Licencias para personal suplente]

    F4 --> B1[Suplencia]
    F4 --> B2[Traslado transitorio]
    F4 --> B3[Casos locales residuales]
    F4 --> B4[Excepciones territoriales]
```

## 8. Lectura ejecutiva

La reducción de carga para el establecimiento depende de tres movimientos simultáneos:

1. sacar del circuito escolar pleno las novedades que ya tienen naturalidad administrativa o acto central;
2. integrar las licencias medicas que puedan venir desde un modulo externo, sin convertir a ese modulo en el rector total de SGPEM;
3. modernizar dentro de SGPEM las familias que seguiran existiendo fuera de ese modulo, especialmente licencias no medicas, licencias para suplentes, reintegros y otros flujos todavia manuales.

## 9. Implicancia concreta para primaria

En primaria, el primer exito no deberia medirse por cantidad de pantallas nuevas ni por cantidad total de tipos cargables. Deberia medirse por una reduccion concreta de familias que la escuela necesita originar manualmente, interpretar sola o reconstruir al cierre.

Un objetivo razonable de primera etapa es este:

- que las novedades mas administrativas lleguen ya encuadradas;
- que las licencias medicas integrables se absorban por un circuito mas temprano;
- que las licencias no medicas remanentes se simplifiquen con catalogos, reglas y evidencia minima;
- y que el establecimiento quede cada vez menos expuesto a resolver por si mismo casos de alta complejidad normativa.

## 10. Referencias relacionadas

- [[10 - Proyectos/Planillas de novedades/03 - Diseño técnico/SGPEM - Plan técnico v5]]
- [[10 - Proyectos/Planillas de novedades/02 - Diseño funcional/SGPEM - Sugerencias para transición a ordenamiento administrativo central (Primario)]]
- [[10 - Proyectos/Planillas de novedades/02 - Diseño funcional/SGPEM - Marco base de novedades laborales]]
- [[10 - Proyectos/Planillas de novedades/02 - Diseño funcional/SGPEM - Paquete visual integral]]
- [[10 - Proyectos/Planillas de novedades/02 - Diseño funcional/SGPEM - Analisis de cambios del dia y matrices]]

## 11. Mini plan de accion por fases segun complejidad

El criterio recomendado es centralizar lo mas simple de absorber sin generar friccion operativa excesiva. Eso permite validar gobernanza, bandejas, derivaciones y trazabilidad con bajo riesgo antes de avanzar sobre familias mas sensibles.

### 11.1 Tabla compacta

| Fase   | Complejidad dominante   | Objetivo                                                                                | Familias sugeridas                                                                                                              |
| ------ | ----------------------- | --------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Fase 1 | Baja                    | Centralizar casos ya maduros administrativamente                                        | Creacion de plaza, desafectacion de plaza, baja definitiva, adscripcion, comision de servicio, tareas pasivas, afectacion       |
| Fase 2 | Baja/media              | Ampliar centralizacion a casos estructurados con acto definido                          | Traslado definitivo, reubicacion, permuta, licencias medicas integrables                                                        |
| Fase 3 | Media                   | Simplificar y centralizar asistidamente flujos aún analogicos o fragmentados            | Licencias extraordinarias, licencias por salud y maternidad sin integracion plena, licencias para personal suplente, reintegros |
| Fase 4 | Media/alta              | Avanzar sobre casos con mayor dependencia contextual                                    | Suplencia, traslado transitorio                                                                                                 |
| Fase 5 | Alta variabilidad local | Dejar para tratamiento local o selectivo los casos de menor retorno por centralizacion   | Inasistencia injustificada, inasistencia justificada, paro, confirmaciones operativas puntuales                                 |

### 11.2 Grafico por fases

```mermaid
flowchart LR
    F1[Fase 1<br/>Complejidad baja] --> F2[Fase 2<br/>Complejidad baja/media]
    F2 --> F3[Fase 3<br/>Complejidad media]
    F3 --> F4[Fase 4<br/>Complejidad media/alta]
    F4 --> F5[Fase 5<br/>Alta variabilidad local]

    F1 --> A1[Creacion y desafectacion de plaza]
    F1 --> A2[Baja definitiva]
    F1 --> A3[Adscripcion, comision, tareas pasivas, afectacion]

    F2 --> B1[Traslado definitivo]
    F2 --> B2[Reubicacion y permuta]
    F2 --> B3[Licencias medicas integrables]

    F3 --> C1[Licencias extraordinarias]
    F3 --> C2[Licencias por salud y maternidad sin integracion plena]
    F3 --> C3[Licencias para personal suplente]
    F3 --> C4[Reintegros]

    F4 --> D1[Suplencia]
    F4 --> D2[Traslado transitorio]
    F4 --> D3[Casos locales residuales]

    F5 --> E1[Inasistencia injustificada]
    F5 --> E2[Paro]
    F5 --> E3[Inasistencia justificada y confirmaciones]
```

### 11.3 Criterio ejecutivo de avance

Se debería pasar de una fase a otra solo si la anterior demuestra:

- reglas entendidas por los actores;
- menor carga real para el establecimiento;
- menor retrabajo manual;
- trazabilidad clara entre detección, ordenamiento y derivación;
- ausencia de aumento significativo de errores de liquidación.

### 12. Preguntas para decisiones ejecutivas
Analizar la complejidad de la reducción de carga manual para su implementación en POF.
- Si no se carga manualmente la información, el sistema actual permite una integración externa?
- Cómo impacta en la planta orgánica funcional el resultado positivo de una comisión de servicio, adscripción o tareas pasivas?
- [[Preguntas para continuar - Centralizacion de novedades laborales]]
