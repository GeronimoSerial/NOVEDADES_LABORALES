---
proyecto: SGPEM
tipo: Diseño funcional
estado: borrador
date: 2026-03-30
tags:
  - proyecto
  - sgpem
  - novedades-laborales
  - planilla
  - marco-base
---

# SGPEM - Marco base de novedades laborales

> [!abstract] Objetivo
> Consolidar un marco funcional amplio para el proyecto de Planilla de Novedades, usando la normativa y procedimientos existentes como base de conocimiento (ABC), pero sin limitar el diseño a secundaria/superior.

---

## 1) Propósito y alcance de este documento

Este documento cumple tres funciones en simultáneo:

1. **Base documental:** organiza lo que ya existe en manuales y resoluciones relevadas.
2. **Marco de diseño:** define reglas comunes para cualquier nivel/modalidad, no solo secundaria/superior.
3. **Puente de implementación:** ofrece una estructura operativa para combinar con el análisis de Planilla de Novedades actual y guiar decisiones futuras.

Este texto **no reemplaza** normativa vigente. Se usa para:

- reducir ambigüedad funcional,
- alinear criterios entre equipos,
- y dejar trazabilidad de supuestos, riesgos y decisiones.

---

## 2) Corpus documental analizado

Documentos ubicados en `OneDrive_2026-03-30/Novedades laborales/`:

- `M-PD01 Manual de procedimientos (versión definitiva).pdf`.
- `Manual Basico Estab.pdf`.
- `ME-R-3138-14_-_DDJJ.pdf`.
- `Decreto 3484-14.pdf`.
- `res 114-10.pdf`.
- `Resolucion N 2141-2015 Designaciones.pdf`.

### 2.1 Estado de legibilidad (OCR/texto)

| Documento | Estado de lectura | Nivel de confianza analítica |
|---|---|---|
| M-PD01 Manual de procedimientos | Texto legible completo | Alto |
| Manual Básico Estab | Texto legible completo | Alto |
| ME-R-3138-14_-_DDJJ | Texto parcial (OCR ruidoso) | Medio |
| Decreto 3484-14 | Sin texto extraíble | Bajo |
| res 114-10 | Sin texto extraíble | Bajo |
| Resolución 2141-2015 Designaciones | Sin texto extraíble | Bajo |

> [!warning] Límite actual
> Para Decreto 3484-14, Res. 114-10 y Res. 2141-2015 no hubo lectura literal por falta de OCR. Este marco se apoya en referencias indirectas encontradas en los manuales legibles.

---

## 3) Criterio rector para SGPEM (acordado)

### 3.1 Lo existente se usa como ABC, no como frontera

Se adopta una regla de diseño explícita:

- La experiencia y documentación de secundaria/superior se usa como **punto de partida validado**.
- El nuevo marco de Planilla de Novedades se diseña como **modelo generalizable** para otros niveles/modalidades.
- Donde una regla sea específica de un nivel, se modela como **parametrizable** (no hardcodeada como única opción).

### 3.2 Consecuencia práctica

La pregunta funcional deja de ser “esto existe en secundaria/superior” y pasa a ser:

`¿Esto es una regla transversal del dominio o una variante de configuración por nivel?`

---

## 4) Síntesis funcional del material de base

### 4.1 Aporte principal de M-PD01

Del manual se desprenden pilares valiosos para SGPEM:

- formalización del circuito administrativo de designaciones,
- estandarización documental (formularios y numeraciones),
- trazabilidad del trámite,
- reglas de responsabilidades por actor,
- control de errores/observaciones/devoluciones.

También introduce una separación importante entre:

- movimientos tratados por instrumentos de designación específicos,
- y movimientos de novedades laborales en planillas mensuales.

### 4.2 Aporte principal del Manual Básico

Aporta estructura conceptual para el dominio:

- definición de plaza, estado de plaza y prestación de servicio,
- catálogo de motivos de ingreso/egreso,
- relación entre operación real, dato administrativo y validación.

Para SGPEM, este documento sirve como base de:

- taxonomía de eventos laborales,
- consistencia de estados,
- y reglas de no contradicción temporal.

### 4.3 Aporte principal de Res. 3138/14 (DDJJ)

Con OCR parcial, se identifican elementos de valor:

- enfoque de registro formal en sistema,
- obligatoriedad y actualización de DDJJ,
- control de calidad de datos para soportar procesos posteriores.

Para SGPEM, esto refuerza el principio de que la planilla no debe vivir aislada, sino sobre un dato base confiable.

---

## 5) Principios funcionales del nuevo marco de novedades

1. **Separación entre hecho y trámite:** una novedad laboral es un hecho; su tramitación puede variar por canal.
2. **Trazabilidad completa:** cada novedad debe registrar origen, actor, fecha efectiva, evidencia y estado.
3. **Configurabilidad por nivel/modalidad:** reglas específicas se parametrizan.
4. **Validaciones preventivas:** detectar antes de liquidar.
5. **No bloqueo ciego:** cuando haya observación, debe existir flujo de corrección.
6. **Compatibilidad normativa progresiva:** permitir evolución por etapas sin romper operación.
7. **Auditoría por diseño:** todo cambio crítico deja huella.

---

## 6) Arquitectura funcional propuesta (agnóstica por nivel)

### 6.1 Capas

- **Capa A - Núcleo común (transversal):**
  - identidad del agente,
  - plaza/cargo,
  - período,
  - tipo de novedad,
  - fechas,
  - estado del trámite,
  - impacto en liquidación.

- **Capa B - Reglas de dominio:**
  - solapamientos,
  - compatibilidades,
  - coherencia estado plaza/agente,
  - ventanas de corte.

- **Capa C - Reglas por nivel/modalidad:**
  - documentación específica,
  - circuito de aprobación,
  - artículos/referencias particulares,
  - excepciones permitidas.

- **Capa D - Operación y control:**
  - tablero de observaciones,
  - métricas,
  - alertas de riesgo,
  - cierre de período.

### 6.2 Resultado esperado

Una Planilla de Novedades que soporte:

- alta diversidad de casos,
- baja ambigüedad operativa,
- y crecimiento progresivo sin rediseño total.

---

## 7) Matriz maestra de novedades (base para combinación)

> [!info]
> Esta matriz es deliberadamente amplia. Debe usarse como plantilla de consolidación y refinarse por jurisdicción/nivel con configuración.

| Tipo de novedad | Finalidad | Canal sugerido | Soporte mínimo | Validaciones clave | Impacto liquidación | Criticidad |
|---|---|---|---|---|---|---|
| Alta por designación | Incorporar prestación | Circuito de designación o equivalente digital | Norma/acto + identificación plaza + fecha toma | Plaza válida, disponibilidad, no duplicidad activa | Alta | Alta |
| Baja por cese/renuncia/jubilación/fallecimiento | Cierre de prestación | Planilla + circuito de baja | Motivo codificado + fecha efectiva + evidencia | No dejar superposición posterior, cierre de suplencias derivadas | Alta | Alta |
| Licencia simple | Interrupción temporal | Planilla | Artículo/motivo + período | Fechas válidas, no solapamiento inválido | Media/Alta | Media |
| Licencia compleja | Interrupción con validación especial | Planilla + validación externa | Documento médico + dictamen/validación | Estado de validación previo a cierre de período | Alta | Alta |
| Reintegro | Reanudación | Planilla | Fecha de reintegro + referencia a licencia previa | Continuidad temporal, cierre de licencia | Alta | Alta |
| Reemplazo/suplencia | Cobertura temporal | Planilla + designación según norma | Identificación reemplazado + período | Un suplente activo por plaza/intervalo | Alta | Alta |
| Traslado transitorio | Movilidad temporal | Planilla + flujo específico | Origen/destino + fechas + norma | Coherencia doble estado titular/interino transitorio | Alta | Alta |
| Adscripción/comisión/tarea pasiva | Cambio funcional temporal | Planilla | Tipo + acto administrativo + vigencia | Trazabilidad de ida/vuelta | Media | Media |
| Inasistencia justificada/injustificada | Registro puntual | Planilla | Fecha + motivo | No conflicto con licencia vigente | Media | Media |
| Paro | Registro masivo por fecha | Planilla | Fecha + alcance | Consistencia por agente/plaza | Media | Media |

---

## 8) Reglas transversales de validación (mínimo funcional)

### 8.1 Reglas temporales

- `fecha_inicio <= fecha_fin` cuando aplique.
- Una novedad no puede empezar fuera del período sin regla explícita de arrastre.
- No se permite doble estado incompatible en la misma ventana temporal.

### 8.2 Reglas de identidad y contexto

- Agente identificable por clave única (DNI/CUIL/legajo según diseño).
- Plaza/cargo debe existir y pertenecer al ámbito válido.
- Escuela/dependencia de la novedad debe ser consistente con el contexto operativo.

### 8.3 Reglas de integridad del estado

- No puede coexistir en la misma plaza una combinación incompatible de ocupación activa.
- Toda baja debe resolver dependencias (suplencias, reemplazos encadenados, pendientes).
- Reintegro solo procede si existe antecedente de interrupción válido.

### 8.4 Reglas de documentación

- Cada tipo de novedad define su set mínimo de respaldo.
- Lo faltante no desaparece: pasa a estado `BAJO_OBSERVACION` o equivalente.
- Debe existir evidencia de quién observó, qué faltó y cuándo se corrigió.

### 8.5 Reglas de liquidación

- Bloquear cierre/liquidación ante inconsistencias críticas.
- Permitir continuidad con observaciones no críticas bajo política definida.
- Toda decisión de excepción debe quedar auditada.

---

## 9) Modelo de estados recomendado para planilla y novedad

### 9.1 Estado de planilla

Flujo sugerido:

`BORRADOR -> EN_REVISION_INTERNA -> ENVIADA -> BAJO_OBSERVACION (si aplica) -> APROBADA -> CERRADA`

### 9.2 Estado de novedad

Flujo sugerido:

`DETECTADA -> REGISTRADA -> VALIDADA -> APLICADA -> CERRADA`

Estados alternos:

- `OBSERVADA`
- `RECHAZADA`
- `ANULADA`

Este doble esquema (planilla + novedad) evita mezclar estado documental con estado del hecho laboral.

---

## 10) Riesgos relevantes y mitigación

| Riesgo | Probabilidad | Impacto | Mitigación propuesta |
|---|---|---|---|
| Dependencia de reglas históricas de un único nivel | Alta | Alto | Parametrizar por nivel/modalidad desde inicio |
| Datos maestros desactualizados (plaza/agente) | Alta | Alto | Validación previa + cola de corrección |
| Falta de OCR en normas clave | Alta | Medio/Alto | Revisión legal literal de PDFs faltantes |
| Bloqueos operativos por validaciones rígidas | Media | Alto | Estado de observación con circuito de resolución |
| Impacto salarial por errores de fecha/estado | Media | Alto | Controles de cierre y alertas tempranas |

---

## 11) Decisiones funcionales recomendadas para SGPEM

1. **Conservar** la lógica de estandarización documental y trazabilidad del ABC existente.
2. **Generalizar** el modelo para múltiples niveles, con perfiles de regla configurables.
3. **Separar** explícitamente reglas de negocio vs reglas normativas específicas.
4. **Implementar** un tablero de observaciones como parte nativa del proceso.
5. **Adoptar** una matriz única de novedades con columnas de criticidad e impacto liquidación.
6. **Priorizar** primero consistencia operativa y auditabilidad antes que automatización total.

---

## 12) Hoja de ruta sugerida para combinar con el análisis de Planilla

### Etapa 1 - Consolidación (corto plazo)

- Mapear todos los tipos de novedad actualmente usados.
- Clasificar cada uno en: transversal, específico por nivel o excepcional.
- Aplicar la matriz maestra de este documento como plantilla de normalización.

### Etapa 2 - Normalización (corto/medio)

- Definir catálogo único de tipos y motivos.
- Definir catálogo único de estados y transiciones permitidas.
- Definir mínimos documentales por tipo de novedad.

### Etapa 3 - Control operativo (medio)

- Activar estado `BAJO_OBSERVACION` con SLA de corrección.
- Activar indicadores de calidad de carga y tiempo de resolución.
- Activar pre-cierre con verificación de inconsistencias críticas.

### Etapa 4 - Escalado (medio/largo)

- Incorporar reglas por nivel como configuración versionada.
- Integrar validaciones externas (DDJJ, médicos, padrón, etc.).
- Estandarizar reportes ejecutivos para toma de decisión.

---

## 13) Pendientes documentales para cerrar versión 1.0

- Recuperar texto literal (OCR o transcripción) de:
  - `Decreto 3484-14.pdf`
  - `res 114-10.pdf`
  - `Resolucion N 2141-2015 Designaciones.pdf`
- Confirmar con área legal/operativa qué reglas son obligatorias hoy y cuáles son prácticas históricas.
- Cerrar taxonomía final de novedades para piloto inicial.

---

## 14) Cierre

Este marco permite avanzar sin quedar atados a una sola implementación histórica.

La premisa de trabajo para SGPEM queda definida así:

**Aprovechar al máximo la base existente, pero diseñar el sistema para la realidad completa de Planilla de Novedades, con foco en trazabilidad, consistencia y escalabilidad.**

---

## Referencias internas recomendadas

- [[10 - Proyectos/Planillas de novedades/Planillas de novedades/00 - Inicio|SGPEM - Inicio]]
- [[10 - Proyectos/Planillas de novedades/Planillas de novedades/01 - Reuniones/2026/2026-03-30 - Novedades laborales y licencias|Reunión 2026-03-30 - Novedades laborales y licencias]]
- [[10 - Proyectos/Planillas de novedades/Planillas de novedades/02 - Diseño funcional/SGPEM - Sugerencias operativas|SGPEM - Sugerencias operativas]]
- [[10 - Proyectos/Planillas de novedades/Planillas de novedades/03 - Diseño técnico/SGPEM - Plan técnico|SGPEM - Plan técnico]]
