---
proyecto: SGPEM
tipo: Reunión
fecha: 2026-02-06
estado: consolidado
fuente: Granola
tags:
  - reunion
  - cge
  - sgpem
  - inconsistencias
---

# Reuniones del 6 de febrero de 2026 - Implementación SGPEM

## Resumen Ejecutivo

Tres reuniones críticas sobre la implementación del **Sistema de Gestión de Planillas Escolares Mensuales (SGPEM)** del Consejo General de Educación de Corrientes. Estas discusiones validan y expanden el diseño técnico documentado en [[10 - Proyectos/Planillas de novedades/03 - Diseño técnico/SGPEM - Plan técnico|SGPEM - Plan técnico]], abordando desafíos operativos reales que impactan directamente en la arquitectura y funcionalidades del sistema.

### Temas Principales del Día

| Reunión                    | Horario | Tema Central                                        | Impacto en SGPEM                               |
| -------------------------- | ------- | --------------------------------------------------- | ---------------------------------------------- |
| Suspension reporting       | 12:10   | Digitalización de suplencias y validación de plazas | Módulo de validaciones (RN-020 a RN-025)       |
| Licencias y suplencias     | 13:25   | Sincronización POF-Gestión e inconsistencias        | Integraciones futuras (Sección 14)             |
| Gestión de inconsistencias | 14:38   | Crisis actual en POST y estrategia de resolución    | Nuevo módulo de inconsistencias no documentado |

### Contexto Técnico

**Estado del proyecto SGPEM:** En Fase 2 (Módulo Director) según Plan de Implementación (Sección 13)  
**Stack tecnológico:** Next.js 14, PostgreSQL con schema `planillas`, Prisma ORM, Better Auth  
**Piloto seleccionado:** Verón de Astrada (departamento pequeño para testing controlado)  

Las reuniones del día revelan que el sistema debe adaptarse para manejar **inconsistencias críticas** no previstas en el diseño original, particularmente en la sincronización entre POF y Gestión Educativa.

---

## 12:10 - Sistema Digital para Suplencias Docentes

### 💡 Propuesta Central

**Objetivo:** Digitalizar el proceso de planillas mensuales de suplencias para eliminar la carga administrativa actual de 1,270 escuelas enviando documentación física.

#### Datos del Problema Actual

| Métrica | Valor | Impacto |
|---------|-------|---------|
| Escuelas | 1,270 | Toda la provincia |
| Documentos mensuales | ~10 por escuela | 12,700 papeles/mes |
| Costo por envío | $60,000 ARS | Alto costo operativo |
| Tiempo de procesamiento | 1-2 meses | Planillas del mes vencido |

**Conexión con SGPEM:** Esta propuesta alinea directamente con el objetivo principal del sistema: *"Digitalizar la carga, validación y envío de planillas de novedades docentes"* (Sección 1, Resumen Ejecutivo).

### 🔧 Funcionalidades del Sistema

#### Validaciones y Controles (Relación con Sección 8 de Planillas.md)

Las validaciones automáticas discutidas se alinean con las **Reglas de Negocio** documentadas:

- ✅ **Validación contra POF** - Corresponde a RN-022: *"La plaza seleccionada no corresponde a esta escuela"*
- ✅ **Control de plazas disponibles** - RN-031: *"La plaza debe estar disponible (sin ocupante activo sin licencia)"*
- ✅ **Registro de motivo de salida** - Antes de cargar suplente, el director debe registrar el motivo (licencia, baja, etc.)

#### Casos "Bajo Observación" (Nuevo Concepto)

**Desafío identificado:** Las designaciones con inconsistencias no pueden bloquearse completamente sin paralizar toda la provincia.

**Solución propuesta:** Implementar un **estado intermedio** en el ciclo de vida de la planilla (Sección 5.4 de Planillas.md):

```
Estados actuales en SGPEM:
BORRADOR → GUARDADA_EN_BORRADORES → ENVIADA → APROBADA → CERRADA

Estado propuesto (nuevo):
BORRADOR → GUARDADA_EN_BORRADORES → [BAJO_OBSERVACION] → ENVIADA → APROBADA → CERRADA
```

**Características del estado "Bajo Observación":**
- Permite carga de designaciones con inconsistencias marcadas
- Bloquea liquidación hasta corrección
- Mantiene trazabilidad completa (Sección 7.1: RN-013)
- Visible para Personal Docente con prioridad de corrección

### 📊 Flujo de Trabajo Propuesto vs. Diseño SGPEM

**Flujo Actual (Documentado en Planillas.md):**
```
Asamblea de designación → Toma de posesión → Registro en sistema
                              ↓
                     Planilla digital (reemplaza envío físico)
                              ↓
                     Sincronización con POF y liquidaciones
```

**Adaptación requerida post-reunión:**

```
Asamblea de designación → Toma de posesión → Validación automática
                                                  ↓
                              ┌──────────────────┴──────────────────┐
                              ↓                                      ↓
                        [Sin inconsistencias]              [Con inconsistencias]
                              ↓                                      ↓
                    Estado: ENVIADA directamente           Estado: BAJO_OBSERVACION
                              ↓                                      ↓
                    APROBACIÓN automática              Corrección por Personal Docente
                              ↓                                      ↓
                    LIQUIDACIÓN                           Validación final → ENVIADA
```

### 🎯 Prueba Piloto

**Departamento seleccionado:** Verón de Astrada (coincide con Fase 4 del Plan de Implementación)

**Criterios de selección:**
- Tamaño manejable (pocas escuelas)
- Representativo de problemáticas provinciales
- Permite medir tiempos de resolución de inconsistencias

**Métricas a medir (Alineadas con Sección 13.3):**

| Métrica | Objetivo SGPEM | Medición en Piloto |
|---------|---------------|-------------------|
| Tiempo de carga | < 3s | Tiempo de resolución de inconsistencias |
| Errores de usuario | < 5% | % de designaciones bajo observación |
| Disponibilidad | 99.5% | Uptime del módulo de inconsistencias |

### ⚠️ Temas Pendientes y Decisiones Técnicas

| Tema | Descripción | Prioridad | Relación con Planillas.md |
|------|-------------|-----------|---------------------------|
| Norma legal | Formato compatible con expedientes ministeriales | Alta | Sección 15.3: Layout PDF |
| Almacenamiento | Capacidad para tomas de posesión escaneadas | Media | Cloudflare R2 / S3 (Sección 4.1) |
| Inconsistencias | Proceso para plazas que no coinciden con estado real | **Crítica** | **Nuevo requerimiento** |
| Toma de posesión digital | QR code para validación (requiere cambio normativo) | Alta | Sección 6.2: Flujo de toma |

---

## 13:25 - Licencias y Suplencias: Validación y Gestión

### 🚨 Problemas Críticos Identificados

#### Sincronización entre Sistemas (Impacto en Integraciones Futuras)

**Problema técnico:** POF y Gestión Educativa no sincronizan correctamente, lo que invalida las validaciones del SGPEM.

**Consecuencias en SGPEM:**
- Las validaciones automáticas (Sección 8) fallan por datos desactualizados
- RN-022 (*plaza pertenece a escuela*) no puede verificarse correctamente
- RN-031 (*plaza disponible*) muestra información inconsistente

**Sincronización nocturna:** Genera delays de 24hs en la información disponible para validaciones.

#### Inconsistencias Críticas en Escuelas

**Caso Real - Escuela 27 (Verón):**

| Problema | Descripción | Impacto en Validaciones SGPEM |
|----------|-------------|------------------------------|
| Plaza en grado cerrado | División cuarto B cerrada pero plaza 3003 asignada | RN-022 falla (plaza no corresponde) |
| Grados sin docente | Segundo A y B tienen matrícula pero sin docentes asignados | RN-031 alerta plaza vacante |
| Movimientos sin actualización | Personal puede mover docentes por resolución interna sin actualizar POF | Datos desactualizados en sistema |

**Implicancia:** El SGPEM no puede confiar únicamente en POF para validaciones. Se requiere un **módulo de inconsistencias** que permita:
1. Detectar discrepancias entre POF y realidad
2. Marcar casos para corrección
3. Permitir carga con observaciones (no bloqueo total)

#### Problemas de Datos

**DNI mal cargados:**
- Causan fallas en sincronización con RENAP
- Impactan la validación de identidad (Integración futura - Sección 14.2)
- Requieren validación previa antes de toma de posesión

**Modificaciones en POF rompen vínculos:**
- Trámites de traslado en curso pierden referencias
- Diseño actual del SGPEM asume estabilidad de IDs de plaza
- **Requerimiento nuevo:** Sistema debe manejar históricos de plazas

### 💡 Propuesta de Validación Digital

**Objetivo:** Permitir carga digital sin documentación física

**Beneficios esperados (Alineados con Sección 2.2):**
- ✅ Reducir papelerío (carga administrativa)
- ✅ Acelerar procesamiento (de 1-2 meses a inmediato)
- ✅ Permitir designaciones con observaciones vs. bloqueo total

**Desafío normativo:**
La toma de posesión digital requiere modificación normativa para tener validez legal. Formato propuesto:
- Formulario físico se archivará en establecimiento
- QR code para validación digital
- URL de verificación: `https://consejo.mec.gob.ar/verificar/{hash}` (Sección 15.3)

### 🎯 Estrategia de Implementación

#### Piloto en Escuelas de Verón

**Objetivos de testing:**
1. Probar validaciones completas vs. observaciones
2. **Medir tiempo necesario** para resolver inconsistencias (métrica clave)
3. Determinar viabilidad antes de expansión provincial

**Metodología:**
- No comunicar métricas inicialmente a cargadores (medición objetiva)
- Identificar a **Virginia Coronaria** como cargadora de referencia
- Documentar casos límite para ajustar reglas de negocio

#### Acceso Técnico Requerido (Para desarrollo SGPEM)

| Requerimiento | Propósito | Relación con Arquitectura (Sección 4) |
|---------------|-----------|--------------------------------------|
| Usuario sistema Gestión (solo primaria) | Verificar datos en tiempo real | Integración con schema `institucional` |
| Acceso a base de datos | Validaciones automáticas | PostgreSQL con Prisma ORM |
| Endpoint de plazas | Trabajar con información actualizada | API REST (Sección 9) |

### ⚖️ Decisión Pendiente: Estrategia de Validación

> **¿Permitir carga con observaciones o bloquear completamente?**
> 
> | Opción | Ventaja | Riesgo |
> |--------|---------|--------|
> | **Bloqueo total** | Mayor control y calidad de datos | Riesgo de paralizar toda la provincia |
> | **Observaciones** | Mantiene operatividad | Requiere seguimiento posterior |
> 
> **Recomendación técnica:** Implementar observaciones con workflow de corrección, alineado con el estado `BAJO_OBSERVACION` propuesto.

---

## 14:38 - Gestión de Inconsistencias en Traslados

### 🚨 Problema Actual: Crisis en el Sistema POST

**Situación crítica identificada:**

> **Todas las escuelas muestran inconsistencias en POST**, lo que genera una parálisis operativa.

**Impacto inmediato:**

| Síntoma | Causa | Efecto en SGPEM |
|---------|-------|-----------------|
| Directores no pueden cargar información | Bloqueo por inconsistencias | Sistema inhabilitado para uso |
| Cargadores tienen 1-2 meses de retraso | Procesamiento manual | Planillas del mes vencido |
| Validaciones estrictas | Sin margen para casos reales | Rechazo masivo de designaciones |

**Implicancia para SGPEM:** El sistema, tal como está diseñado, **no puede implementarse** sin resolver primero esta crisis de inconsistencias. Las validaciones automáticas (Sección 8) rechazarían la mayoría de las operaciones.

### 💡 Propuesta de Solución por Etapas

**Nuevo flujo de resolución (Pre-requisito para SGPEM):**

```
DÍA 1-5:    Director corrige inconsistencias detectadas
DÍA 5-10:   Personal docente arregla información reportada
CONTINUO:   Carga de novedades durante el mes (con SGPEM)
```

**Adaptación al ciclo de vida de planillas (Sección 5.4):**

```
Período mensual:
├─ Días 1-5:   Corrección de inconsistencias (estado: BORRADOR)
├─ Días 5-10:  Validación por Personal Docente (estado: BAJO_OBSERVACION)
├─ Día 10+:    Planilla operativa para carga normal
└─ Cierre:     Envío automático si no hay inconsistencias críticas
```

### 🔧 Módulo de Inconsistencias (Nuevo Requerimiento Funcional)

**Características requeridas (No documentadas en Planillas.md):**

1. **Vista por departamento** para cargadores
   - Agrupación geográfica (coincide con alcance de supervisores - Sección 11)
   - Indicadores de escuelas con inconsistencias

2. **Sistema de observaciones**
   - Permite carga bajo observación (nuevo estado)
   - Bloquea liquidación hasta corrección
   - Priorización por criticidad

3. **Validación automática** antes de liquidación
   - Checkpoint obligatorio en flujo APROBADA → CERRADA
   - Integración con liquidaciones del sistema legacy

### 🔄 Proceso de Licencias y Traslados

#### Desafío: Licencias Médicas

**Complejidad normativa:**
- Reconocimiento Médico debe validar en Gestión Educativa
- Cruzamiento automático de datos entre sistemas
- **Licencias complejas (27, 28):** Requieren validación manual por normativa especial

**Implicancia para SGPEM:**
- Necesita integración con Centro de Reconocimientos Médicos (Sección 14.3)
- Casos 27 y 28 deben marcarse para validación manual
- Workflow diferenciado según tipo de licencia

#### Desafío: Traslados Transitorios

**Situación compleja no contemplada en diseño original:**

```
Caso real:
- Docente titular en Escuela A
- Traslado transitorio a Escuela B como interino
- Resultado: titular/interino simultáneo en sistema
- Director no puede modificar porque es procedimiento válido
```

**Impacto en reglas de negocio (Sección 7.4):**
- RN-031 (*plaza disponible*) falla en estos casos
- RN-003 (*un agente por plaza*) no aplica a traslados transitorios
- **Requerimiento:** Nueva categoría "traslado transitorio" con reglas específicas

#### Casos Específicos Detectados

| Caso | Descripción | Solución Propuesta |
|------|-------------|-------------------|
| Múltiples cargos | Titular en escuela A, suplente en escuela B | Validación por CUIL en lugar de DNI único |
| Cambios de grado | Movimiento interno no informado | Workflow de actualización de POF desde escuela |
| Plazas vacantes con divisiones cerradas | Discrepancia POF-realidad | Marcado como inconsistencia crítica |

### 🎯 Próximos Pasos y Decisiones

#### Reunión Programada: Lunes con Cargadores

**Contexto:** Presentar propuesta de solución de inconsistencias antes de implementar SGPEM.

**Objetivos específicos:**
1. Validar propuesta de solución por etapas
2. **Medir tiempos de resolución** sin comunicar métricas (medición objetiva)
3. Identificar a **Virginia Coronaria** como referente técnico
4. Documentar casos límite para ajustar reglas

#### Implementación Piloto

**Estrategia:**
- **5 escuelas de Verón de Astrada** (departamento pequeño y controlable)
- Desarrollo de módulo de validación con reglas de negocio específicas
- Medición real de tiempos de resolución

**Métricas de éxito (Alineadas con Sección 13.3):**

| Indicador | Objetivo | Medición |
|-----------|----------|----------|
| Tiempo promedio de resolución | < 5 días | Tracking por inconsistencia |
| Escuelas sin inconsistencias | > 80% | Al finalizar período piloto |
| Directores operativos | 100% | Capacitados y sin bloqueos |

#### Posible Cambio Organizacional

> **Integración al equipo de Carlos Encina**
> 
> **Beneficios para SGPEM:**
> - ✅ Mejor acceso a información completa del sistema (todos los schemas)
> - ✅ Mayor poder de decisión desde posición central
> - ✅ Facilita integraciones con sistemas externos (POF, Gestión, RENAPER)
> 
> **Impacto:** Aceleraría decisiones técnicas y acceso a recursos necesarios para el desarrollo.

---

## 📋 Accionables Consolidados del Día

### Alta Prioridad (Bloqueantes para SGPEM)

1. [ ] **Desarrollar módulo de inconsistencias** con vista por departamento
   - *Relacionado con:* Sección 10.1 (Panel de Revisión CGE)
   - *Nuevo estado:* BAJO_OBSERVACION en ciclo de vida

2. [ ] **Definir reglas de negocio** para validaciones (con observaciones vs. bloqueo total)
   - *Relacionado con:* Sección 7 (Reglas de Negocio)
   - *Impacto:* RN-031, RN-022 necesitan ajustes

3. [ ] **Solicitar acceso técnico:** usuario Gestión + endpoint de plazas
   - *Relacionado con:* Sección 4.1 (Stack Tecnológico) y 14 (Integraciones)

4. [ ] **Reunión con cargadores** (lunes) - presentar propuesta de inconsistencias
   - *Métrica clave:* Medir tiempos de resolución

### Media Prioridad

5. [ ] **Norma legal** para validación de anexos de designación digital
   - *Relacionado con:* Sección 15.3 (Layout PDF con QR)

6. [ ] **Evaluar integración** al equipo de Carlos Encina
   - *Beneficio:* Facilitar integraciones técnicas

7. [ ] **Piloto en Verón:** 5 escuelas para validación de suplencias digitales
   - *Relacionado con:* Sección 13 (Fase 4: Piloto)

### Baja Prioridad

8. [ ] **Sincronización futura** con Reconocimiento Médico
   - *Relacionado con:* Sección 14.3 (Integraciones Futuras)

9. [ ] **Capacidad de almacenamiento** para tomas de posesión escaneadas
   - *Relacionado con:* Cloudflare R2 (Sección 4.1)

---

## 🔗 Conexiones Técnicas con Planillas.md

### Nuevos Requerimientos Identificados

Las reuniones del 6 de febrero revelan **funcionalidades no contempladas** en el diseño original del SGPEM:

1. **Estado "Bajo Observación"** - Nuevo estado en ciclo de vida de planillas
2. **Módulo de inconsistencias** - Vista para cargadores por departamento
3. **Manejo de traslados transitorios** - Nueva categoría con reglas específicas
4. **Validación de licencias médicas** - Workflow diferenciado (27, 28)

### Impacto en Arquitectura

| Componente | Estado Actual | Cambio Requerido |
|------------|---------------|------------------|
| Modelo de datos (Sección 5) | 9 tablas principales | Agregar tabla `inconsistencias` |
| Estados (Sección 5.4) | 5 estados | Agregar estado `BAJO_OBSERVACION` |
| Reglas de negocio (Sección 7) | 15 reglas documentadas | Agregar reglas para traslados transitorios |
| API REST (Sección 9) | 20+ endpoints | Agregar endpoints de inconsistencias |
| Roles (Sección 11) | 5 roles definidos | Agregar rol `cargador` con acceso por departamento |

### Reuniones Relacionadas

- **5 feb 2026:** Rural education content delivery strategy
- **Lunes próximo:** Reunión con cargadores de Verón de Astrada

### Decisiones Técnicas Pendientes

1. ¿Implementar módulo de inconsistencias dentro del schema `planillas` o schema separado?
2. ¿Cómo manejar históricos de plazas cuando POF se modifica?
3. ¿Priorizar corrección de inconsistencias antes del piloto SGPEM?

---

*Documento generado el 9 de febrero de 2026*  
*Contextualizado con: [[10 - Proyectos/Planillas de novedades/03 - Diseño técnico/SGPEM - Plan técnico|SGPEM - Plan técnico]] - Sistema de Gestión de Planillas Escolares Mensuales (SGPEM)*
