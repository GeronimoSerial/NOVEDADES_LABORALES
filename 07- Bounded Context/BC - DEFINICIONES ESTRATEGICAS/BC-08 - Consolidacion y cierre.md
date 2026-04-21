---
proyecto: Sistematizacion de Novedades Laborales
tipo: Bounded context
estado: borrador
date: 2026-04-06
tags:
  - sgpem
  - bounded-context
  - consolidacion
  - cierre
---

# BC-08 - Consolidación y cierre

> [!abstract] Proposito
> Este bounded context reune la logica administrativa de criticidad, consolidacion, arrastre y cierre de periodo. Su trabajo no es crear o interpretar desde cero la novedad, sino decidir si un caso ya tratado por otros contextos puede consolidarse, debe observarse o debe bloquear el cierre.

## 1. Por que es un bounded context separado

El cierre 25-28 y la consolidación administrativa tienen reglas propias. Si se mezclan con el tratamiento del negocio base, todo el dominio queda sesgado por la urgencia del cierre. Separarlo permite que la novedad viva durante el mes y que el cierre solo decida sobre casos ya trazados.

## 2. Qué incluye

- criticidad del caso,
- clasificación consolidable o no consolidable,
- observaciones de cierre,
- arrastre controlado,
- bloqueo de cierre cuando corresponda,
- consolidación por periodo,
- salida administrativa derivada a planilla.

## 3. Que no incluye

- la clasificacion inicial de la novedad,
- la resolucion profunda de interrupciones, coberturas, movilidad o egresos,
- la gestion completa de liquidacion,
- la administracion de plazas o POF.

## 4. Subpuntos iniciales

### 4.1 Criticidad

- critica,
- media,
- no critica.

### 4.2 Estado de cierre

- consolidable,
- observable,
- bloqueante,
- arrastrable con politica explicita.

### 4.3 Salida administrativa

- consolidacion por periodo,
- generacion de planilla derivada,
- auditoria de decisiones de cierre.

## 5. Conceptos principales

- criticidad,
- consolidable,
- arrastre,
- bloqueo de cierre,
- consolidacion,
- salida derivada.

## 6. Eventos principales

- caso clasificado por criticidad,
- caso marcado como consolidable,
- caso observado para cierre,
- caso arrastrado,
- cierre bloqueado,
- consolidacion generada,
- planilla derivada emitida.

## 7. Decisiones principales

- si un caso bloquea o no el cierre,
- si puede consolidarse,
- si debe arrastrarse con evidencia,
- si la criticidad cambio por nueva informacion,
- cuando el periodo puede cerrarse en forma explicable.

## 8. Reglas centrales de delimitacion

1. La planilla es salida derivada y no fuente primaria del dominio.
2. Una novedad critica no puede desaparecer del cierre por omisión.
3. Los casos no criticos solo pueden arrastrarse con politica explicita.
4. Este contexto decide sobre consolidacion y cierre; no reinterpreta desde cero el negocio del caso.
5. Cuando el problema principal es `consolidar, arrastrar o bloquear`, pertenece aquí.

## 9. Relacion con otros bounded contexts

- recibe resultados desde `[[BC-02 - Interrupciones temporales]]`,
- recibe resultados desde `[[BC-03 - Cobertura transitoria]]`,
- recibe resultados desde `[[BC-04 - Movilidad funcional]]`,
- recibe resultados desde `[[BC-05 - Egresos definitivos]]`,
- recibe resultados desde `[[BC-06 - Ocupacion estable de cargos]]`,
- consume evidencia complementaria desde `[[BC-07 - Integraciones y conciliacion]]` si hace falta trazabilidad adicional,
- no reemplaza el encuadre inicial de `[[BC-01 - Encuadre administrativo de la novedad]]`.

## 10. Puntos de interfaz relevantes

- ==política de cierre 25-28,
- criterios de criticidad,
- observaciones y arrastres,
- necesidad de explicabilidad administrativa del cierre.

## 11. Preguntas abiertas para investigación

- que casos deben considerarse críticos desde el primer corte,
- que evidencia mínima debe permitirse para arrastre no critico,
- como se mide un cierre exitoso sin volver a la lógica *planillocentrica*,
- que decisiones deben automatizarse y cuales deben quedar bajo control humano.
