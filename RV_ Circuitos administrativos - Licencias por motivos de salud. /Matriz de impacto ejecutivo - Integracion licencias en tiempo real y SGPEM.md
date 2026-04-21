---
tipo: Analisis ejecutivo
tema: Integracion licencias salud - SGPEM
estado: vigente
date: 2026-03-31
tags:
  - cge
  - sgpem
  - licencias
  - impacto
  - matriz
---

# Matriz de impacto ejecutivo - Integración de licencias en tiempo real y SGPEM

> [!abstract] Objetivo
> Sintetizar en formato ejecutivo cómo impacta la integración del circuito de licencias por salud sobre el Proyecto de Planilla de Novedades (SGPEM), priorizando flujo, tiempos, gobernanza y riesgo operativo.

## 1) Contexto de decisión

- SGPEM ya definió que la unidad primaria es la **novedad individual por agente** y que la planilla es un **consolidado posterior**.
- El circuito de licencias por salud establece un flujo digital con actores formales (Agente, Gestión Escolar, Establecimiento, Contralor/Recono-cimientos) y hitos de validación/notificación.
- La integración cambia el régimen temporal: de información diferida (posterior) a información transaccional o casi en tiempo real.

## 2) Matriz de impacto priorizada

| # | Eje | Situación previa (información posterior) | Situación objetivo (tiempo real) | Impacto en SGPEM | Prioridad |
|---|---|---|---|---|---|
| 1 | Origen de licencia | Carga manual tardía en planilla/novedad | Alta desde sistema de licencias | Debe admitir origen externo con correlación de trámite | Alta |
| 2 | Validación documental | Verificación extemporánea | Regla temprana de certificado y control de plazo | Nuevos estados de tránsito antes de consolidar | Alta |
| 3 | Ventana de 24h | No modelada explícitamente | Condición de continuidad del trámite | Regla de cierre automático por inacción/falta de doc | Alta |
| 4 | Dictamen médico | Llega tarde al circuito de novedades | Dictamen impacta al recibir evento | Bloqueo/desbloqueo por criticidad en liquidación | Alta |
| 5 | Días reconocidos | Ajuste manual posterior | Dato oficial casi inmediato | Liquidación basada en días reconocidos, no sólo solicitados | Alta |
| 6 | Artículo otorgado | Comunicación final en lote | Sincronización al aprobar dictamen | Catálogo de artículos y trazabilidad normativa obligatoria | Alta |
| 7 | Notificación establecimiento | Parcial o desfasada | Notificación por cada hito | Motor de eventos y bandejas por actor | Media/Alta |
| 8 | Reintegro | Ya gestionado como novedad SGPEM | Se mantiene en SGPEM | Debe exigir licencia antecedente válida para cierre | Alta |
| 9 | Suplencias/reemplazos | Se disparan con información incompleta | Disparo condicionado a estado médico válido | Evita cobertura indebida sobre licencia rechazada | Alta |
| 10 | Corte 25-28 | Carga concentrada y retrabajo | Pre-cierre con datos vivos | Menor arrastre y menos correcciones de último momento | Alta |
| 11 | Auditoría | Trazabilidad fragmentada entre áreas | Trazabilidad unificada por evento | Bitácora interoperable y evidencia por actor | Media/Alta |
| 12 | Gobernanza | Competencias no siempre explícitas en SGPEM | Competencias normativas más claras en licencias | Ajustar reglas de aprobación/autoridad por nivel | Alta |
| 13 | Calidad de dato | Inconsistencias detectadas tarde | Control temprano y continuo | Menor tasa de observaciones críticas al cierre | Media/Alta |
| 14 | Riesgo salarial | Desvíos por fechas/estados tardíos | Corrección temprana con SLA | Disminuye riesgo de liquidación errónea | Alta |
| 15 | Continuidad operativa | Dependencia de carga mensual diferida | Operación continua mensual (24/7) | Consolida el cambio a modelo de novedad individual | Alta |

## 3) Efecto sobre el flujo SGPEM (lectura rápida)

1. **Antes:** SGPEM capturaba la licencia cuando el circuito externo ya había avanzado o cerrado.
2. **Ahora:** SGPEM recibe señales tempranas (inicio, validación, dictamen, resolución) y actualiza estado en curso.
3. **Resultado:** la planilla deja de ser “lugar de descubrimiento tardío” y pasa a ser “consolidado confiable de hechos ya trazados”.

## 4) Cambios mínimos indispensables

- Incorporar `origen_novedad` y `id_tramite_licencia_externo` en novedades de licencia.
- Modelar estados intermedios de licencia (pendiente doc, en revisión médica, aprobada, rechazada, cerrada por inacción).
- Definir reglas de criticidad para pre-cierre y cierre (25-28).
- Formalizar evento de reintegro como cierre operativo de interrupción en SGPEM.
- Integrar notificaciones por actor y tablero de pendientes por SLA.

## 5) Riesgos de no implementación

- Doble carga de una misma licencia en sistemas distintos.
- Inconsistencia entre días solicitados, dictaminados y liquidados.
- Reemplazos/suplencias activadas sobre estados no válidos.
- Mayor volumen de observaciones al final de período.
- Trazabilidad incompleta frente a auditoría.

## 6) Recomendación ejecutiva

> [!success] Recomendación
> Mantener el **escenario dual** (con/sin integración) como transición, pero priorizar la integración por eventos en tiempo real para licencias de salud y preservar en SGPEM el gobierno del reintegro, la consolidación y el control de liquidación por criticidad.

## 7) Fuentes utilizadas

- `RV_ Circuitos administrativos - Licencias por motivos de salud. /Circuito de licencias de Salud - Flujograma.pdf`
- `RV_ Circuitos administrativos - Licencias por motivos de salud. /Circuito de licencias médicas - Prosa.pdf`
- `RV_ Circuitos administrativos - Licencias por motivos de salud. /Presentación Proyecto de Solicitudes de Licencias Medicas.pdf`
- `RV_ Circuitos administrativos - Licencias por motivos de salud. /Proyecto de Decreto de Solicitud de Licencias Medicas .pdf`
- `RV_ Circuitos administrativos - Licencias por motivos de salud. /Proyecto de Resolución de Licencias Medicas.pdf`
- [[10 - Proyectos/Planillas de novedades/00 - Inicio]]
- [[10 - Proyectos/Planillas de novedades/01 - Reuniones/2026/2026-03-30 - Novedades laborales y licencias]]
- [[10 - Proyectos/Planillas de novedades/02 - Diseño funcional/SGPEM - Documento didactico integral]]
- [[10 - Proyectos/Planillas de novedades/02 - Diseño funcional/SGPEM - Marco base de novedades laborales]]
- [[10 - Proyectos/Planillas de novedades/02 - Diseño funcional/SGPEM - Sugerencias operativas]]
- [[10 - Proyectos/Planillas de novedades/03 - Diseño técnico/SGPEM - Plan técnico]]
