---
proyecto: SGPEM
tipo: ADR
estado: propuesta
fecha: 2026-03-31
horizonte: 30 dias
audiencia: mixta interareas
tags:
  - adr
  - sgpem
  - ejecutivo
  - licencias
  - decision
  - v3
---

# ADR-002 - Version ejecutiva para decision SGPEM v3 y licencias

> [!abstract] Proposito
> Presentar una version ejecutiva unificada para toma de decisiones sobre SGPEM v3, incorporando las variantes analizadas en la fecha y la implementacion de licencias, con foco en decisiones de comite, informacion faltante y plan de accion de 30 dias.

> [!important] Alcance de esta ADR
> Este documento no reemplaza el diseno tecnico detallado. Define marco decisional, criterios de priorizacion, faltantes criticos y linea de ejecucion institucional para reducir riesgo operativo y salarial.

---

## 1) Estado de situacion consolidado

### 1.1 Cambio rector confirmado

- SGPEM adopta como unidad primaria la **novedad laboral por agente**.
- La planilla se mantiene como **consolidado administrativo por periodo**.
- El cierre 25-28 pasa de logica de deteccion tardia a logica de decision con criticidad.

### 1.2 Situacion actual de madurez

- Existe definicion tecnica v3 con arquitectura objetivo y catalogos canonicos.
- Existe analisis integrado del impacto funcional y matrices operativas.
- Persisten brechas de gobierno operativo: responsables formales, fechas cerradas y acuerdos de dependencia externa.

### 1.3 Riesgo institucional actual

- Si no se define decision ejecutiva en el corto plazo, se sostiene:
  - retrabajo de fin de periodo,
  - divergencia entre estado medico, estado laboral y consolidado,
  - mayor exposicion a error de liquidacion,
  - menor trazabilidad para auditoria.

---

## 2) Decisiones ejecutivas requeridas (comite)

| ID | Decision | Estado propuesto | Impacto |
|---|---|---|---|
| D-01 | Estrategia de integracion de licencias (A/B/C) | Resolver en comite | Alto |
| D-02 | Politica operativa de cierre 25-28 por criticidad | Resolver en comite | Alto |
| D-03 | Politica de eventos tardios post-corte | Resolver en comite | Alto |
| D-04 | Tratamiento de licencias art. 27/28 | Resolver en comite | Alto |
| D-05 | Estrategia de despliegue territorial | Resolver en comite | Alto |
| D-06 | Esquema de gobernanza por rol y SLA | Resolver en comite | Medio/Alto |

---

## 3) Variantes analizadas para integracion de licencias

## 3.1 Resumen de alternativas

- **Variante A - Sin integracion externa:** captura interna completa del ciclo.
- **Variante B - Integracion full por eventos:** dependencia total de eventos externos.
- **Variante C - Eventos + fallback polling:** eventos como canal principal con contingencia para conciliacion y recuperacion.

## 3.2 Matriz comparativa ejecutiva

| Criterio | Variante A | Variante B | Variante C |
|---|---|---|---|
| Oportunidad del dato | Baja/Media | Alta | Alta |
| Resiliencia operativa | Alta | Media/Baja | Alta |
| Dependencia externa | Baja | Alta | Media |
| Carga manual interna | Alta | Baja | Media/Baja |
| Riesgo de latencia | Alto | Medio | Bajo/Medio |
| Trazabilidad de eventos | Media | Alta | Alta |
| Continuidad en contingencia | Media | Baja | Alta |
| Complejidad de implementacion | Media | Media | Media/Alta |
| Adecuacion institucional (30 dias) | Media | Media | Alta |

## 3.3 Recomendacion ejecutiva

> [!success] Recomendacion para comite
> Adoptar **Variante C** como estrategia institucional objetivo, con implementacion por piloto territorial y gobierno por criticidad. Esta alternativa ofrece el mejor equilibrio entre oportunidad de dato, resiliencia operativa y continuidad ante fallas de integracion.

---

## 4) Lineamiento ejecutivo para implementacion de licencias

## 4.1 Criterio general

- SGPEM consume eventos del circuito de licencias para reflejar impacto laboral y de liquidacion.
- SGPEM no replica tramite medico completo; gobierna correlacion, estados, reintegro y consolidacion.

## 4.2 Reglas minimas institucionales

- Toda licencia integrada debe registrar origen, correlacion externa e idempotencia.
- Todo cambio de estado debe dejar evidencia auditable.
- Toda decision de cierre debe responder a criticidad definida.

## 4.3 Tratamiento de art. 27/28 (enfoque mixto)

- **Desde ahora (minimo obligatorio):** control reforzado, validacion medica explicita y trazabilidad diferenciada.
- **Por fases (maduracion):** automatizacion progresiva de validacion, monitoreo dedicado y reglas de excepcion refinadas.

---

## 5) Politica ejecutiva de cierre 25-28 (sesgo balanceado)

## 5.1 Principio

- Casos **criticos**: bloquean consolidacion/cierre.
- Casos **no criticos**: admiten arrastre controlado con evidencia.

## 5.2 Matriz simplificada de decision

| Caso | Criticidad | Decision ejecutiva |
|---|---|---|
| Licencia sin dictamen en caso critico | Critica | Bloquear cierre |
| Pendiente documental no critico | No critica | Arrastre controlado |
| Licencia rechazada con reemplazo activo | Critica | Regularizar y bloquear |
| Evento tardio sin impacto salarial alto | No critica | Arrastre controlado |
| Evento tardio con impacto salarial alto | Critica | Ajuste extraordinario |

---

## 6) Politica de eventos tardios post-corte (hibrida)

### 6.1 Criterio institucional

- Regla por defecto: **arrastre controlado** al siguiente periodo.
- Excepcion: **ajuste extraordinario** cuando exista impacto salarial alto o riesgo legal/auditable.

### 6.2 Criterios de activacion de ajuste extraordinario

- desvio salarial potencial significativo,
- afectacion de derechos del agente,
- incompatibilidad normativa,
- riesgo de auditoria alta.

---

## 7) Matriz de informacion faltante para decidir (R/A/V)

> [!warning] Lectura de semaforo
> **Rojo:** bloquea decision o salida del piloto. **Amarillo:** no bloquea inicio, pero limita calidad/escala. **Verde:** disponible y utilizable.

| Item de informacion | Estado | Impacto si falta | Rol responsable | Fecha objetivo recomendada |
|---|---|---|---|---|
| Confirmacion de ventana operativa del sistema externo de licencias | Rojo | Alto | cge_admin + integraciones | +5 dias |
| Definicion formal de responsables por subproceso SGPEM | Rojo | Alto | coordinacion proyecto | +3 dias |
| Matriz oficial de origen de novedades por tipo | Rojo | Alto | coordinacion funcional | +3 dias |
| Criterios cuantitativos de criticidad para cierre 25-28 | Rojo | Alto | cge_revisor + tecnico funcional | +4 dias |
| Politica formal para eventos tardios de alto impacto salarial | Rojo | Alto | conduccion CGE + liquidacion | +4 dias |
| Catalogo canonico final de estados (licencia y novedad) validado interareas | Amarillo | Medio/Alto | equipo funcional + tecnico | +7 dias |
| Protocolo de contingencia (fallo de eventos y uso de polling) | Amarillo | Medio/Alto | integraciones + operacion | +7 dias |
| Protocolo operativo especifico para art. 27/28 | Amarillo | Medio/Alto | gestion medica + funcional | +10 dias |
| Definicion de tablero ejecutivo con SLA institucional | Amarillo | Medio | PM/Coordinacion | +10 dias |
| Definicion de estructura de trazabilidad y evidencia auditora | Verde | Bajo | equipo tecnico | Disponible |
| Definicion de arquitectura objetivo v3 | Verde | Bajo | equipo tecnico | Disponible |
| Definicion de enfoque dual por fases | Verde | Bajo | comite proyecto | Disponible |

---

## 8) KPI ejecutivos de portada (seguimiento obligatorio)

| KPI | Definicion operativa | Meta 30 dias | Rol dueno |
|---|---|---|---|
| Pendientes criticos | Casos criticos abiertos por periodo | Reduccion sostenida semanal | cge_revisor |
| Latencia de eventos | Tiempo entre evento externo y estado aplicado en SGPEM | Estabilizar en umbral operativo acordado | integraciones |
| % fallback polling | Proporcion de casos resueltos por contingencia | Tendencia descendente | integraciones + operacion |
| Bloqueos de cierre | Cantidad de cierres bloqueados por criticidad | Reduccion con mayor madurez de datos | cge_revisor + liquidacion |
| Riesgo salarial | Indice de exposicion a desvio por estado/fecha | Mantener en rango controlado | liquidacion + coordinacion funcional |
| Trazabilidad completa | % de casos con correlacion, estado y evidencia auditora completa | Aumento continuo hasta cobertura plena | tecnico funcional + auditor |

> [!info] Nota metodologica
> Durante la fase de piloto territorial, las metas se expresan inicialmente como tendencia y umbral operativo acordado por comite, para luego formalizar valores numericos por KPI al cierre de la segunda semana del piloto.

---

## 9) Plan de accion ejecutivo (30 dias, por rol)

| Semana | Hito | Resultado esperado | Rol lider | Dependencias |
|---|---|---|---|---|
| 1 | Cierre de decisiones D-01 a D-06 | Marco de gobierno validado | conduccion CGE | Comite interareas |
| 1 | Matriz oficial de origen de novedades | Regla unica de origen por tipo | coordinacion funcional | Reuniones tecnicas y normativas |
| 1 | Definicion de criticidad 25-28 y eventos tardios | Regla de cierre publicada | cge_revisor + liquidacion | Acuerdo institucional |
| 2 | Preparacion de piloto territorial | Entorno y protocolo operativo listos | cge_admin + tecnico | Integraciones + soporte |
| 2 | Activacion Variante C en modo controlado | Eventos + fallback operativos | integraciones | Sistema externo licencias |
| 3 | Monitoreo de KPIs y ajuste de reglas | Informe ejecutivo de desvio/ajuste | PM/Coordinacion | Datos de operacion real |
| 3 | Implementacion minima reforzada art. 27/28 | Control diferenciado en produccion piloto | gestion medica + funcional | Validacion normativa |
| 4 | Evaluacion de salida a escalado | Dictamen para ampliar cobertura | comite interareas | Evidencia piloto |

---

## 10) Riesgos ejecutivos y mitigaciones prioritarias

| Riesgo | Nivel | Mitigacion ejecutiva | Rol de control |
|---|---|---|---|
| Caida o inestabilidad de integracion externa | Alto | fallback polling + conciliacion diaria + escalamiento | integraciones |
| Bloqueo excesivo en cierre por mala calibracion de criticidad | Alto | matriz balanceada + revision semanal de umbrales | cge_revisor |
| Divergencia entre estado medico y laboral | Alto | correlacion obligatoria + auditoria de enlace | tecnico funcional + auditor |
| Falta de responsables claros por subproceso | Alto | asignacion formal por rol en comite | coordinacion proyecto |
| Adopcion incompleta del esquema nuevo | Medio/Alto | protocolo institucional + capacitacion operativa focalizada | coordinacion funcional |

---

## 11) Recomendacion institucional final

> [!success] Recomendacion integral
> Para el horizonte de 30 dias, se recomienda aprobar la transicion operativa de SGPEM bajo **Variante C (eventos + fallback polling)**, con **piloto territorial**, politica de cierre **balanceada por criticidad**, tratamiento **hibrido de eventos tardios** y control **mixto reforzado para art. 27/28**. La decision debe condicionarse al cierre de faltantes en estado rojo y al seguimiento semanal de los KPI ejecutivos definidos.

---

## 12) Anexo - Lista breve de decisiones a firmar en comite

- [ ] Aprobar estrategia de integracion Variante C para piloto territorial.
- [ ] Aprobar matriz de criticidad de cierre 25-28.
- [ ] Aprobar politica hibrida de eventos tardios.
- [ ] Aprobar protocolo minimo reforzado para art. 27/28.
- [ ] Aprobar matriz de roles y SLA de seguimiento.
- [ ] Aprobar KPI ejecutivos obligatorios y calendario de reporte.

---

## 13) Fuentes de referencia

- [[10 - Proyectos/Planillas de novedades/03 - Diseño técnico/SGPEM - Plan técnico v3]]
- [[10 - Proyectos/Planillas de novedades/02 - Diseño funcional/SGPEM - Analisis de cambios del dia y matrices]]
- [[10 - Proyectos/Planillas de novedades/01 - Reuniones/2026/2026-03-30 - Novedades laborales y licencias]]
- [[10 - Proyectos/Planillas de novedades/01 - Reuniones/2026/2026 - Cronología SGPEM consolidada]]
- [[10 - Proyectos/Planillas de novedades/02 - Diseño funcional/Licencias - Guia practica del nuevo circuito]]
- [[10 - Proyectos/Planillas de novedades/05 - Decisiones/ADR-001 - Estructura del proyecto SGPEM en Obsidian]]
