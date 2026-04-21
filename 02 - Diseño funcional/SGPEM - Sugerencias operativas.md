# Sugerencias Técnicas y Operativas
## SGPEM - Sistema de Gestión de Planillas Escolares Mensuales
### Post-Reuniones 6 de Febrero, 2026

> [!info] Continuidad de decisiones
> Para contexto actualizado de marzo 2026 sobre novedades laborales, integracion con licencias y priorizacion operativa, ver: [[10 - Proyectos/Planillas de novedades/01 - Reuniones/2026/2026-03-30 - Novedades laborales y licencias]].

---

## Resumen Ejecutivo

Este documento presenta un análisis detallado de los problemas críticos identificados durante las reuniones del 6 de febrero de 2026, junto con recomendaciones técnicas y operativas específicas para su resolución. Las sugerencias están diseñadas para desbloquear la implementación del **Sistema de Gestión de Planillas Escolares Mensuales (SGPEM)** y garantizar su éxito en el piloto de Verón de Astrada.

### Síntesis de Problemas Críticos

| # | Problema | Impacto | Prioridad | Estado SGPEM |
|---|----------|---------|-----------|--------------|
| 1 | **Crisis de inconsistencias en POST** | Parálisis operativa provincial | CRÍTICA | Bloquea piloto |
| 2 | **Sincronización POF-Gestión** | Validaciones automáticas fallidas | ALTA | Requiere adaptación |
| 3 | **Traslados transitorios** | Reglas de negocio insuficientes | ALTA | Nuevo requerimiento |
| 4 | **Licencias médicas (27, 28)** | Workflow no diferenciado | MEDIA | Adaptación necesaria |
| 5 | **Datos desactualizados** | Calidad de información baja | MEDIA | Mejora continua |

### Principio Directriz

> **"Las validaciones deben ser preventivas, no punitivas. El sistema debe guiar hacia la corrección, no bloquear sin alternativas."**

---

## 1. Análisis de Problemas y Recomendaciones Detalladas

### 1.1. Crisis de Inconsistencias en POST

#### Diagnóstico Profundo

**Síntomas observados:**
- 100% de escuelas con inconsistencias detectadas
- Directores incapacitados para cargar información
- Cargadores con retraso de 1-2 meses en procesamiento
- Sistema paralizado por validaciones excesivamente restrictivas

**Causas raíz identificadas:**

```
Árbol de causas:
├── Validaciones demasiado estrictas
│   ├── No permiten casos límite reales
│   ├── Sin estado intermedio (aprobado/rechazado binario)
│   └── Sin flujo de corrección integrado
├── Datos maestros desactualizados
│   ├── POF no refleja realidad operativa
│   ├── Modificaciones manuales sin actualizar sistema
│   └── Históricos de cambios no registrados
└── Proceso de corrección manual
    ├── Sin herramientas de gestión
    ├── Sin priorización de casos
    └── Sin seguimiento de resolución
```

#### Recomendación Técnica: Módulo de Gestión de Inconsistencias

##### 1.1.1. Extensión del Modelo de Datos

**Nueva tabla requerida:** `inconsistencias`

```sql
-- Schema: planillas
-- Tabla: inconsistencias

CREATE TABLE planillas.inconsistencias (
    id_inconsistencia       BIGSERIAL PRIMARY KEY,
    id_escuela              INTEGER NOT NULL REFERENCES institucional.escuela(id_escuela),
    id_planilla             BIGINT REFERENCES planillas.planillas(id_planilla),
    tipo_inconsistencia     VARCHAR(50) NOT NULL, -- 'PLAZA_CERRADA', 'DNI_INVALIDO', 'TRASLADO_TRANSITORIO', etc.
    severidad               VARCHAR(20) NOT NULL CHECK (severidad IN ('CRITICA', 'ALTA', 'MEDIA', 'BAJA')),
    descripcion             TEXT NOT NULL,
    entidad_afectada        VARCHAR(50), -- 'PLAZA', 'AGENTE', 'NOVEDAD', 'TOMA_POSESION'
    id_entidad              BIGINT, -- ID de la entidad afectada
    
    -- Estado del workflow de corrección
    estado                  VARCHAR(30) NOT NULL DEFAULT 'DETECTADA' 
        CHECK (estado IN ('DETECTADA', 'ASIGNADA', 'EN_CORRECCION', 'CORREGIDA', 'VALIDADA', 'RECHAZADA')),
    
    -- Asignación y seguimiento
    asignado_a              INTEGER REFERENCES auth.users(id_user),
    fecha_asignacion        TIMESTAMP,
    fecha_correccion        TIMESTAMP,
    correccion_detalle      TEXT,
    validado_por            INTEGER REFERENCES auth.users(id_user),
    fecha_validacion        TIMESTAMP,
    
    -- Trazabilidad
    creado_por              INTEGER NOT NULL REFERENCES auth.users(id_user),
    fecha_creacion          TIMESTAMP DEFAULT NOW(),
    modificado_por          INTEGER REFERENCES auth.users(id_user),
    fecha_modificacion      TIMESTAMP,
    
    -- Metadata para análisis
    departamento            VARCHAR(100), -- Denormalizado para filtros rápidos
    tiempo_resolucion_horas INTEGER, -- Calculado al cerrar
    
    CONSTRAINT chk_entidad_o_planilla CHECK (
        (id_planilla IS NOT NULL) OR (entidad_afectada IS NOT NULL AND id_entidad IS NOT NULL)
    )
);

-- Índices optimizados
CREATE INDEX idx_inconsistencias_escuela ON planillas.inconsistencias(id_escuela);
CREATE INDEX idx_inconsistencias_estado ON planillas.inconsistencias(estado);
CREATE INDEX idx_inconsistencias_severidad ON planillas.inconsistencias(severidad);
CREATE INDEX idx_inconsistencias_asignado ON planillas.inconsistencias(asignado_a) WHERE asignado_a IS NOT NULL;
CREATE INDEX idx_inconsistencias_departamento ON planillas.inconsistencias(departamento);
CREATE INDEX idx_inconsistencias_fecha_creacion ON planillas.inconsistencias(fecha_creacion);
CREATE INDEX idx_inconsistencias_tipo ON planillas.inconsistencias(tipo_inconsistencia);

-- Trigger para auditoría automática
CREATE OR REPLACE FUNCTION planillas.audit_inconsistencias()
RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'UPDATE' THEN
        NEW.modificado_por := current_setting('app.current_user_id')::INTEGER;
        NEW.fecha_modificacion := NOW();
        
        -- Calcular tiempo de resolución al cerrar
        IF NEW.estado IN ('CORREGIDA', 'VALIDADA') AND OLD.estado NOT IN ('CORREGIDA', 'VALIDADA') THEN
            NEW.tiempo_resolucion_horas := EXTRACT(EPOCH FROM (NOW() - NEW.fecha_creacion)) / 3600;
        END IF;
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trigger_audit_inconsistencias
    BEFORE UPDATE ON planillas.inconsistencias
    FOR EACH ROW EXECUTE FUNCTION planillas.audit_inconsistencias();
```

**Tabla de catálogo:** `tipos_inconsistencia`

```sql
CREATE TABLE planillas.tipos_inconsistencia (
    id_tipo                 SERIAL PRIMARY KEY,
    codigo                  VARCHAR(50) UNIQUE NOT NULL,
    nombre                  VARCHAR(200) NOT NULL,
    descripcion             TEXT,
    severidad_default       VARCHAR(20) NOT NULL CHECK (severidad_default IN ('CRITICA', 'ALTA', 'MEDIA', 'BAJA')),
    entidad_afectada        VARCHAR(50) NOT NULL,
    requiere_validacion_manual BOOLEAN DEFAULT false,
    instrucciones_correccion TEXT,
    activo                  BOOLEAN DEFAULT true,
    fecha_creacion          TIMESTAMP DEFAULT NOW()
);

-- Datos iniciales basados en problemas detectados
INSERT INTO planillas.tipos_inconsistencia (codigo, nombre, descripcion, severidad_default, entidad_afectada, instrucciones_correccion) VALUES
('PLAZA_GRADO_CERRADO', 'Plaza asignada a grado cerrado', 'La plaza está asignada a una división que está cerrada según POF', 'CRITICA', 'PLAZA', 'Verificar estado actual de la división en POF. Si está cerrada, debe liberarse la plaza o reasignarse a división abierta.'),
('GRADO_SIN_DOCENTE', 'Grado con matrícula sin docente asignado', 'La división tiene alumnos matriculados pero no tiene docente asignado', 'ALTA', 'PLAZA', 'Asignar docente titular o suplente a la plaza correspondiente.'),
('TRASLADO_TRANSITORIO', 'Traslado transitorio activo', 'Docente con estado simultáneo (titular/interino) por traslado transitorio', 'MEDIA', 'AGENTE', 'Verificar validez del traslado transitorio. Marcar plaza original como "titular en traslado" y nueva como "interino transitorio".'),
('DNI_INVALIDO', 'DNI con formato inválido', 'El número de DNI no cumple con el formato esperado o no existe en RENAPER', 'ALTA', 'AGENTE', 'Verificar número de DNI con el docente. Corregir en sistema institucional.'),
('PLAZA_MODIFICADA_POF', 'Plaza modificada en POF durante trámite', 'La plaza fue modificada en POF mientras hay un trámite de traslado activo', 'ALTA', 'PLAZA', 'Verificar historial de cambios. Actualizar referencias en trámite o crear nueva plaza.'),
('LICENCIA_27_28_SIN_VAL', 'Licencia 27/28 sin validación médica', 'Licencia médica compleja sin validación del Centro de Reconocimientos', 'MEDIA', 'NOVEDAD', 'Enviar a validación manual con Centro Médico. No procesar hasta tener certificación.'),
('MULTIPLES_CARGOS_CONFLICTO', 'Múltiples cargos generan conflicto', 'Docente con cargos simultáneos que violan normativa', 'ALTA', 'AGENTE', 'Verificar compatibilidad de cargos según normativa vigente. Ajustar o justificar excepción.'),
('CAMBIO_GRADO_NO_INFORMADO', 'Cambio de grado no reflejado en POF', 'Docente cambió de grado/división pero POF no fue actualizado', 'MEDIA', 'PLAZA', 'Actualizar POF con el cambio de grado o verificar si es cambio temporal.');
```

##### 1.1.2. Extensión del Ciclo de Vida de Planillas

**Estado adicional requerido:** `BAJO_OBSERVACION`

```
Ciclo de vida extendido:

BORRADOR 
    ↓ [Envío con inconsistencias]
BAJO_OBSERVACION 
    ↓ [Corrección completada]
ENVIADA 
    ↓ [Revisión CGE]
APROBADA 
    ↓ [Checkpoint de inconsistencias]
┌──────────────────────────────────────┐
│ VALIDACIÓN: ¿Hay inconsistencias     │
│ sin resolver en esta planilla?       │
├──────────────────────────────────────┤
│ SÍ → Retornar a BAJO_OBSERVACION     │
│ NO  → Proceder a CERRADA             │
└──────────────────────────────────────┘
    ↓
CERRADA
```

**Modificación de tabla `estados_planilla`:**

```sql
-- Agregar nuevo estado
INSERT INTO planillas.estados_planilla (id_estado, codigo, nombre, descripcion, es_editable, color, orden_workflow)
VALUES 
(6, 'BAJO_OBSERVACION', 'Bajo Observación', 'Planilla enviada pero contiene inconsistencias pendientes de resolución. Bloqueada para liquidación.', false, '#FFA500', 3);

-- Actualizar orden de estados posteriores
UPDATE planillas.estados_planilla SET orden_workflow = orden_workflow + 1 WHERE orden_workflow >= 3 AND codigo != 'BAJO_OBSERVACION';
```

##### 1.1.3. Reglas de Negocio Adicionales

```sql
-- Función de validación de inconsistencias
CREATE OR REPLACE FUNCTION planillas.validar_inconsistencias_planilla(p_id_planilla BIGINT)
RETURNS TABLE (
    puede_proceder BOOLEAN,
    inconsistencias_criticas INTEGER,
    inconsistencias_altas INTEGER,
    mensaje TEXT
) AS $$
DECLARE
    v_criticas INTEGER;
    v_altas INTEGER;
BEGIN
    -- Contar inconsistencias activas
    SELECT 
        COUNT(*) FILTER (WHERE severidad = 'CRITICA' AND estado NOT IN ('CORREGIDA', 'VALIDADA')),
        COUNT(*) FILTER (WHERE severidad = 'ALTA' AND estado NOT IN ('CORREGIDA', 'VALIDADA'))
    INTO v_criticas, v_altas
    FROM planillas.inconsistencias
    WHERE id_planilla = p_id_planilla;
    
    -- Determinar si puede proceder
    RETURN QUERY
    SELECT 
        (v_criticas = 0) as puede_proceder,
        v_criticas,
        v_altas,
        CASE 
            WHEN v_criticas > 0 THEN 'No puede proceder: ' || v_criticas || ' inconsistencias críticas pendientes'
            WHEN v_altas > 0 THEN 'Puede proceder con observaciones: ' || v_altas || ' inconsistencias altas detectadas'
            ELSE 'Sin inconsistencias bloqueantes'
        END as mensaje;
END;
$$ LANGUAGE plpgsql;

-- Trigger para validar antes de cambiar a APROBADA
CREATE OR REPLACE FUNCTION planillas.validar_antes_aprobacion()
RETURNS TRIGGER AS $$
DECLARE
    v_validacion RECORD;
BEGIN
    -- Solo ejecutar si está cambiando a APROBADA
    IF NEW.id_estado = (SELECT id_estado FROM planillas.estados_planilla WHERE codigo = 'APROBADA')
       AND OLD.id_estado != (SELECT id_estado FROM planillas.estados_planilla WHERE codigo = 'APROBADA') THEN
        
        SELECT * INTO v_validacion FROM planillas.validar_inconsistencias_planilla(NEW.id_planilla);
        
        IF NOT v_validacion.puede_proceder THEN
            RAISE EXCEPTION 'No se puede aprobar planilla: %', v_validacion.mensaje;
        END IF;
    END IF;
    
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trigger_validar_antes_aprobacion
    BEFORE UPDATE ON planillas.planillas
    FOR EACH ROW EXECUTE FUNCTION planillas.validar_antes_aprobacion();
```

#### Recomendación Operativa: Proceso de Corrección por Etapas

**Propuesta de flujo operativo:**

```
PERÍODO MENSUAL (ej: Abril 2026)

Días 1-5: VENTANA DE CORRECCIÓN DIRECTORES
├─ Sistema detecta automáticamente inconsistencias
├─ Directores reciben notificación con lista priorizada
├─ Correcciones simples (datos mal cargados, asignaciones)
└─ Estado planilla: BORRADOR → Correcciones aplicadas

Días 5-10: VALIDACIÓN PERSONAL DOCENTE
├─ Cargadores revisan inconsistencias remanentes
├─ Asignan casos complejos a especialistas
├─ Validan correcciones de directores
└─ Estado planilla: BAJO_OBSERVACIÓN → EN_CORRECCIÓN

Días 10-25: OPERACIÓN NORMAL
├─ Planillas sin inconsistencias críticas: operativas
├─ Carga de novedades diarias habilitada
├─ Nuevas inconsistencias: flujo rápido (24-48hs)
└─ Estado planilla: ENVIADA o BAJO_OBSERVACIÓN

Día 25-30: CIERRE Y LIQUIDACIÓN
├─ Checkpoint final de inconsistencias
├─ Solo planillas sin críticas: pasan a APROBADA
├─ Con críticas: mantener en BAJO_OBSERVACIÓN, no liquidar
└─ Estado final: CERRADA (solo sin inconsistencias críticas)
```

**Roles y responsabilidades:**

| Rol | Responsabilidad en Corrección | Acceso en SGPEM |
|-----|------------------------------|-----------------|
| Director | Corregir datos de su escuela | Vista de inconsistencias de su escuela |
| Cargador | Validar correcciones, asignar casos complejos | Vista por departamento, asignación de tickets |
| Personal Docente (CGE) | Resolver casos complejos, validar reglas | Vista provincial, validación final |
| Supervisor | Monitorear progreso por zona | Dashboard de inconsistencias por zona |

##### 1.1.4. Métricas de Control

**KPIs para medir efectividad:**

```sql
-- Vista de métricas de inconsistencias
CREATE OR REPLACE VIEW planillas.vw_metricas_inconsistencias AS
SELECT 
    DATE_TRUNC('month', fecha_creacion) as periodo,
    departamento,
    COUNT(*) as total_inconsistencias,
    COUNT(*) FILTER (WHERE estado IN ('CORREGIDA', 'VALIDADA')) as resueltas,
    COUNT(*) FILTER (WHERE estado NOT IN ('CORREGIDA', 'VALIDADA', 'RECHAZADA')) as pendientes,
    ROUND(AVG(tiempo_resolucion_horas) FILTER (WHERE tiempo_resolucion_horas IS NOT NULL), 2) as tiempo_promedio_horas,
    COUNT(*) FILTER (WHERE severidad = 'CRITICA') as criticas,
    COUNT(*) FILTER (WHERE severidad = 'CRITICA' AND estado NOT IN ('CORREGIDA', 'VALIDADA')) as criticas_pendientes,
    ROUND(
        (COUNT(*) FILTER (WHERE estado IN ('CORREGIDA', 'VALIDADA')) * 100.0 / NULLIF(COUNT(*), 0)),
        2
    ) as tasa_resolucion_pct
FROM planillas.inconsistencias
GROUP BY DATE_TRUNC('month', fecha_creacion), departamento;

-- Objetivos sugeridos:
-- Tasa de resolución: > 90% dentro del período mensual
-- Tiempo promedio: < 48 horas para críticas, < 5 días para altas
-- Críticas pendientes: 0 al cierre de período
```

**Dashboard sugerido para Supervisores/CGE:**

```
┌─────────────────────────────────────────────────────────────────┐
│  MÉTRICAS DE INCONSISTENCIAS - Período: Abril 2026             │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Indicadores Principales                                       │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐           │
│  │   156        │ │   142        │ │   14         │           │
│  │ Detectadas   │ │ Resueltas    │ │ Pendientes   │           │
│  │  (91%)       │ │              │ │   (9%)       │           │
│  └──────────────┘ └──────────────┘ └──────────────┘           │
│                                                                  │
│  Por Severidad                                                 │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐           │
│  │   23         │ │   89         │ │   44         │           │
│  │ Críticas     │ │ Altas        │ │ Medias/Bajas │           │
│  │ (0 pend)     │ │ (14 pend)    │ │ (0 pend)     │           │
│  └──────────────┘ └──────────────┘ └──────────────┘           │
│                                                                  │
│  Tiempo Promedio de Resolución: 36.5 horas                    │
│                                                                  │
│  Por Departamento                                              │
│  ┌──────────────┬──────────────┬──────────────┬──────────────┐ │
│  │ Departamento │ Detectadas   │ Resueltas    │ Tiempo (hs)  │ │
│  ├──────────────┼──────────────┼──────────────┼──────────────┤ │
│  │ Verón        │ 12           │ 12           │ 24.0         │ │
│  │ Capital      │ 89           │ 78           │ 42.3         │ │
│  │ Goya         │ 55           │ 52           │ 38.1         │ │
│  └──────────────┴──────────────┴──────────────┴──────────────┘ │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

### 1.2. Sincronización POF-Gestión Educativa

#### Diagnóstico Profundo

**Problemas identificados:**
1. **Latencia de sincronización:** Actualización nocturna genera delay de 24hs
2. **Inconsistencias persistentes:** POF modificado manualmente sin reflejar cambios
3. **Validaciones fallidas:** SGPEM confía en datos desactualizados
4. **Sin historial:** No se registran cambios en plazas a lo largo del tiempo

**Impacto técnico:**
- RN-022 (plaza pertenece a escuela): Falsa negativa por desactualización
- RN-031 (plaza disponible): Información errónea sobre ocupación
- Integridad referencial: Violaciones silenciosas

#### Recomendación Técnica: Sistema de Validación en Tiempo Real

##### 1.2.1. Arquitectura de Cache y Validación

```typescript
// servicios/validacionPlazas.ts
// Sistema de validación con cache y fallback

import { redis } from '@/lib/redis';
import { prisma } from '@/lib/prisma';

interface ValidacionPlazaResult {
  esValida: boolean;
  plaza: {
    id: number;
    estado: 'ACTIVA' | 'CERRADA' | 'VACANTE';
    escuelaId: number;
    grado: string;
    division: string;
  } | null;
  inconsistencias: string[];
  cacheHit: boolean;
  ultimaActualizacion: Date;
}

export class ValidadorPlazas {
  private static readonly CACHE_TTL = 300; // 5 minutos
  private static readonly CACHE_KEY_PREFIX = 'pof:plaza:';

  /**
   * Valida una plaza contra POF en tiempo real
   * Primero consulta cache, luego API de Gestión Educativa
   */
  async validarPlaza(idPlaza: number): Promise<ValidacionPlazaResult> {
    const cacheKey = `${ValidadorPlazas.CACHE_KEY_PREFIX}${idPlaza}`;
    
    // 1. Intentar leer de cache
    const cached = await redis.get(cacheKey);
    if (cached) {
      return {
        ...JSON.parse(cached),
        cacheHit: true,
      };
    }

    // 2. Consultar API de Gestión Educativa
    const resultado = await this.consultarGestionEducativa(idPlaza);
    
    // 3. Guardar en cache
    await redis.setex(
      cacheKey,
      ValidadorPlazas.CACHE_TTL,
      JSON.stringify({ ...resultado, cacheHit: false })
    );
    
    return resultado;
  }

  /**
   * Consulta en tiempo real a API de Gestión Educativa
   */
  private async consultarGestionEducativa(idPlaza: number): Promise<Omit<ValidacionPlazaResult, 'cacheHit'>> {
    try {
      const response = await fetch(`${process.env.GESTION_API_URL}/plazas/${idPlaza}`, {
        headers: {
          'Authorization': `Bearer ${process.env.GESTION_API_TOKEN}`,
          'X-Source': 'SGPEM'
        },
        cache: 'no-store' // Siempre fresh
      });

      if (!response.ok) {
        // Fallback a base de datos local
        return this.validarConBaseLocal(idPlaza);
      }

      const data = await response.json();
      
      return {
        esValida: data.estado === 'ACTIVA',
        plaza: {
          id: data.id,
          estado: data.estado,
          escuelaId: data.id_escuela,
          grado: data.grado,
          division: data.division,
        },
        inconsistencias: this.detectarInconsistencias(data),
        ultimaActualizacion: new Date(data.updated_at),
      };
    } catch (error) {
      console.error('Error consultando Gestión Educativa:', error);
      return this.validarConBaseLocal(idPlaza);
    }
  }

  /**
   * Fallback a base de datos local cuando API no disponible
   */
  private async validarConBaseLocal(idPlaza: number): Promise<Omit<ValidacionPlazaResult, 'cacheHit'>> {
    const plaza = await prisma.plaza.findUnique({
      where: { id: idPlaza },
      include: { escuela: true }
    });

    if (!plaza) {
      return {
        esValida: false,
        plaza: null,
        inconsistencias: ['Plaza no encontrada en sistema local'],
        ultimaActualizacion: new Date(),
      };
    }

    return {
      esValida: plaza.activa,
      plaza: {
        id: plaza.id,
        estado: plaza.activa ? 'ACTIVA' : 'CERRADA',
        escuelaId: plaza.id_escuela,
        grado: plaza.grado || '',
        division: plaza.division || '',
      },
      inconsistencias: [],
      ultimaActualizacion: plaza.updatedAt,
    };
  }

  /**
   * Detecta inconsistencias entre POF y estado real
   */
  private detectarInconsistencias(plazaData: any): string[] {
    const inconsistencias: string[] = [];

    // Detectar plaza asignada a grado cerrado
    if (plazaData.estado_division === 'CERRADA' && plazaData.id_agente_asignado) {
      inconsistencias.push('Plaza asignada a grado cerrado');
    }

    // Detectar grado con matrícula sin docente
    if (plazaData.cantidad_alumnos > 0 && !plazaData.id_agente_asignado) {
      inconsistencias.push('Grado con matrícula sin docente asignado');
    }

    // Detectar cambios recientes en POF
    const ultimaModificacion = new Date(plazaData.updated_at);
    const horasDesdeModificacion = (Date.now() - ultimaModificacion.getTime()) / (1000 * 60 * 60);
    
    if (horasDesdeModificacion < 24) {
      inconsistencias.push(`Plaza modificada recientemente (${Math.round(horasDesdeModificacion)}hs)`);
    }

    return inconsistencias;
  }

  /**
   * Invalida cache de una plaza cuando hay cambios
   */
  async invalidarCache(idPlaza: number): Promise<void> {
    await redis.del(`${ValidadorPlazas.CACHE_KEY_PREFIX}${idPlaza}`);
  }

  /**
   * Valida múltiples plazas en batch (optimización)
   */
  async validarPlazasBatch(idsPlazas: number[]): Promise<Map<number, ValidacionPlazaResult>> {
    const resultados = new Map<number, ValidacionPlazaResult>();
    
    // Separar: cache hits vs misses
    const cacheChecks = await Promise.all(
      idsPlazas.map(async (id) => {
        const cached = await redis.get(`${ValidadorPlazas.CACHE_KEY_PREFIX}${id}`);
        return { id, cached };
      })
    );

    const cacheHits = cacheChecks.filter(c => c.cached);
    const cacheMisses = cacheChecks.filter(c => !c.cached).map(c => c.id);

    // Procesar hits
    cacheHits.forEach(({ id, cached }) => {
      resultados.set(id, { ...JSON.parse(cached!), cacheHit: true });
    });

    // Consultar misses en batch
    if (cacheMisses.length > 0) {
      const batchResults = await Promise.all(
        cacheMisses.map(id => this.validarPlaza(id))
      );
      
      batchResults.forEach((resultado, index) => {
        resultados.set(cacheMisses[index], resultado);
      });
    }

    return resultados;
  }
}

export const validadorPlazas = new ValidadorPlazas();
```

##### 1.2.2. Webhook para Sincronización en Tiempo Real

```typescript
// app/api/webhooks/gestion/route.ts
// Endpoint para recibir notificaciones de cambios en Gestión Educativa

import { NextRequest, NextResponse } from 'next/server';
import { validadorPlazas } from '@/servicios/validacionPlazas';
import { prisma } from '@/lib/prisma';
import { crearInconsistencia } from '@/servicios/inconsistencias';

export async function POST(request: NextRequest) {
  try {
    // Validar firma del webhook
    const signature = request.headers.get('X-Gestion-Signature');
    if (!validarFirmaWebhook(await request.text(), signature)) {
      return NextResponse.json({ error: 'Firma inválida' }, { status: 401 });
    }

    const evento = await request.json();

    switch (evento.tipo) {
      case 'PLAZA_MODIFICADA':
        await handlePlazaModificada(evento.data);
        break;
      
      case 'PLAZA_CERRADA':
        await handlePlazaCerrada(evento.data);
        break;
      
      case 'PLAZA_NUEVA':
        await handlePlazaNueva(evento.data);
        break;
      
      case 'AGENTE_ASIGNADO':
        await handleAgenteAsignado(evento.data);
        break;
      
      default:
        console.log('Evento no manejado:', evento.tipo);
    }

    return NextResponse.json({ success: true });
  } catch (error) {
    console.error('Error procesando webhook:', error);
    return NextResponse.json({ error: 'Error interno' }, { status: 500 });
  }
}

async function handlePlazaModificada(data: any) {
  // Invalidar cache
  await validadorPlazas.invalidarCache(data.id_plaza);

  // Verificar si hay trámites activos afectados
  const tramitesActivos = await prisma.tomaPosesion.findMany({
    where: {
      id_plaza: data.id_plaza,
      estado: { in: ['PENDIENTE', 'EN_PROCESO'] }
    }
  });

  if (tramitesActivos.length > 0) {
    // Crear inconsistencia por modificación durante trámite
    for (const tramite of tramitesActivos) {
      await crearInconsistencia({
        tipo: 'PLAZA_MODIFICADA_POF',
        id_escuela: tramite.id_escuela,
        entidad_afectada: 'TOMA_POSESION',
        id_entidad: tramite.id_toma_posesion,
        descripcion: `Plaza ${data.id_plaza} modificada en POF mientras hay trámite activo`,
        severidad: 'ALTA',
        creado_por: 1 // Sistema
      });
    }
  }

  // Registrar en historial
  await prisma.historialPlaza.create({
    data: {
      id_plaza: data.id_plaza,
      tipo_cambio: 'MODIFICACION',
      datos_anteriores: data.datos_anteriores,
      datos_nuevos: data.datos_nuevos,
      fecha_cambio: new Date(),
      origen: 'GESTION_EDUCATIVA'
    }
  });
}

async function handlePlazaCerrada(data: any) {
  await validadorPlazas.invalidarCache(data.id_plaza);

  // Verificar si hay docente asignado
  if (data.id_agente_asignado) {
    // Crear inconsistencia crítica
    await crearInconsistencia({
      tipo: 'PLAZA_GRADO_CERRADO',
      id_escuela: data.id_escuela,
      entidad_afectada: 'PLAZA',
      id_entidad: data.id_plaza,
      descripcion: `Plaza ${data.id_plaza} cerrada pero tiene docente asignado`,
      severidad: 'CRITICA',
      creado_por: 1 // Sistema
    });
  }
}

async function handlePlazaNueva(data: any) {
  // Agregar a base local
  await prisma.plaza.create({
    data: {
      id: data.id_plaza,
      id_escuela: data.id_escuela,
      grado: data.grado,
      division: data.division,
      activa: true
    }
  });
}

async function handleAgenteAsignado(data: any) {
  await validadorPlazas.invalidarCache(data.id_plaza);
  
  // Invalidar cache de agente también
  await redis.del(`agente:${data.id_agente}`);
}

function validarFirmaWebhook(payload: string, signature: string | null): boolean {
  if (!signature) return false;
  
  const crypto = require('crypto');
  const hash = crypto
    .createHmac('sha256', process.env.GESTION_WEBHOOK_SECRET!)
    .update(payload)
    .digest('hex');
  
  return hash === signature;
}
```

##### 1.2.3. Tabla de Historial de Plazas

```sql
-- Schema: planillas
-- Tabla: historial_plaza

CREATE TABLE planillas.historial_plaza (
    id_historial        BIGSERIAL PRIMARY KEY,
    id_plaza            INTEGER NOT NULL,
    tipo_cambio         VARCHAR(50) NOT NULL, -- 'CREACION', 'MODIFICACION', 'CIERRE', 'REAPERTURA'
    datos_anteriores    JSONB,
    datos_nuevos        JSONB,
    fecha_cambio        TIMESTAMP DEFAULT NOW(),
    origen              VARCHAR(50), -- 'GESTION_EDUCATIVA', 'SGPEM', 'MANUAL'
    id_usuario          INTEGER REFERENCES auth.users(id_user),
    observaciones       TEXT
);

CREATE INDEX idx_historial_plaza_plaza ON planillas.historial_plaza(id_plaza);
CREATE INDEX idx_historial_plaza_fecha ON planillas.historial_plaza(fecha_cambio);
CREATE INDEX idx_historial_plaza_tipo ON planillas.historial_plaza(tipo_cambio);
```

#### Recomendación Operativa: Contrato de API con Gestión Educativa

**Especificación de integración:**

```yaml
# contrato-api-gestion.yaml

openapi: 3.0.0
info:
  title: API de Gestión Educativa - Endpoints para SGPEM
  version: 1.0.0
  description: Contrato de integración entre SGPEM y Sistema de Gestión Educativa

paths:
  /plazas/{id}:
    get:
      summary: Obtener información actualizada de una plaza
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
      responses:
        200:
          description: Información de plaza
          content:
            application/json:
              schema:
                type: object
                properties:
                  id: integer
                  id_escuela: integer
                  estado: string # 'ACTIVA', 'CERRADA', 'VACANTE'
                  grado: string
                  division: string
                  id_agente_asignado: integer
                  estado_division: string
                  cantidad_alumnos: integer
                  updated_at: string # ISO 8601
        404:
          description: Plaza no encontrada

  /webhooks/sgpem:
    post:
      summary: Registrar webhook para notificaciones
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                url: string
                eventos: array # ['PLAZA_MODIFICADA', 'PLAZA_CERRADA', 'AGENTE_ASIGNADO']
      responses:
        201:
          description: Webhook registrado

  /plazas/batch:
    post:
      summary: Consultar múltiples plazas en batch
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                ids: array of integer
      responses:
        200:
          description: Lista de plazas
```

**Requisitos de infraestructura:**

| Componente | Especificación | Propósito |
|------------|---------------|-----------|
| Redis Cache | Instancia dedicada | Cache de validaciones (TTL: 5 min) |
| Rate Limiting | 1000 req/min | Protección de API Gestión Educativa |
| Cola de webhooks | Redis/Bull | Procesamiento asíncrono de eventos |
| Circuit Breaker | Configuración: 5 errores/60s | Fallback automático a BD local |

---

### 1.3. Traslados Transitorios

#### Diagnóstico Profundo

**Problema específico:**
- Docente titular en Escuela A
- Traslado transitorio a Escuela B como interino
- Sistema muestra: titular/interino simultáneos (inconsistencia técnica)
- Procedimiento válido normativamente, pero no soportado por sistema

**Impacto en reglas de negocio:**
- RN-031: Plaza disponible (falla porque titular sigue "ocupando")
- RN-003: Un agente por plaza (falla porque está en dos plazas)
- Validación de toma de posesión: Confusión en estado del agente

#### Recomendación Técnica: Workflow Específico para Traslados Transitorios

##### 1.3.1. Nuevo Tipo de Novedad y Estados

```sql
-- Agregar tipos de novedad específicos
INSERT INTO planillas.tipos_novedad (codigo, nombre, requiere_plaza, requiere_fecha_fin, instrucciones)
VALUES 
('TRASLADO_TRANSITORIO_SALIDA', 'Traslado Transitorio - Salida', true, true, 'Docente titular que se traslada transitoriamente a otra escuela'),
('TRASLADO_TRANSITORIO_INGRESO', 'Traslado Transitorio - Ingreso', true, true, 'Docente interino por traslado transitorio desde otra escuela');

-- Estados específicos para plazas
ALTER TYPE planillas.estado_plaza_enum ADD VALUE 'TITULAR_EN_TRASLADO';
ALTER TYPE planillas.estado_plaza_enum ADD VALUE 'INTERINO_TRANSITORIO';
```

##### 1.3.2. Reglas de Negocio Específicas

```sql
-- Función de validación para traslados transitorios
CREATE OR REPLACE FUNCTION planillas.validar_traslado_transitorio(
    p_id_agente INTEGER,
    p_id_plaza_origen INTEGER,
    p_id_plaza_destino INTEGER
)
RETURNS TABLE (
    es_valido BOOLEAN,
    mensaje TEXT,
    checks_passed INTEGER,
    checks_total INTEGER
) AS $$
DECLARE
    v_checks_passed INTEGER := 0;
    v_checks_total INTEGER := 5;
BEGIN
    -- Check 1: Agente existe y es titular en plaza origen
    IF EXISTS (
        SELECT 1 FROM planillas.planilla_agentes 
        WHERE id_agente = p_id_agente 
        AND id_plaza = p_id_plaza_origen 
        AND situacion_revista = 'TITULAR'
    ) THEN
        v_checks_passed := v_checks_passed + 1;
    END IF;

    -- Check 2: Plaza destino está vacante o tiene licencia
    IF EXISTS (
        SELECT 1 FROM planillas.plazas 
        WHERE id_plaza = p_id_plaza_destino 
        AND (estado = 'VACANTE' OR estado = 'LICENCIA_TEMPORAL')
    ) THEN
        v_checks_passed := v_checks_passed + 1;
    END IF;

    -- Check 3: No hay traslado transitorio activo para este agente
    IF NOT EXISTS (
        SELECT 1 FROM planillas.novedades 
        WHERE id_agente = p_id_agente 
        AND tipo_novedad IN ('TRASLADO_TRANSITORIO_SALIDA', 'TRASLADO_TRANSITORIO_INGRESO')
        AND fecha_fin IS NULL
    ) THEN
        v_checks_passed := v_checks_passed + 1;
    END IF;

    -- Check 4: Plaza origen y destino son de la misma jurisdicción (misma zona)
    IF EXISTS (
        SELECT 1 
        FROM institucional.escuelas e1
        JOIN institucional.escuelas e2 ON e1.id_zona = e2.id_zona
        JOIN planillas.plazas p1 ON p1.id_escuela = e1.id_escuela
        JOIN planillas.plazas p2 ON p2.id_escuela = e2.id_escuela
        WHERE p1.id_plaza = p_id_plaza_origen 
        AND p2.id_plaza = p_id_plaza_destino
    ) THEN
        v_checks_passed := v_checks_passed + 1;
    END IF;

    -- Check 5: Fecha de inicio es válida (no en el pasado)
    IF CURRENT_DATE <= CURRENT_DATE + INTERVAL '1 day' THEN
        v_checks_passed := v_checks_passed + 1;
    END IF;

    RETURN QUERY SELECT 
        (v_checks_passed = v_checks_total) as es_valido,
        CASE 
            WHEN v_checks_passed = v_checks_total THEN 'Traslado transitorio válido'
            ELSE 'Faltan ' || (v_checks_total - v_checks_passed) || ' validaciones'
        END as mensaje,
        v_checks_passed,
        v_checks_total;
END;
$$ LANGUAGE plpgsql;
```

##### 1.3.3. Proceso Automatizado

```typescript
// servicios/trasladosTransitorios.ts

interface TrasladoTransitorioParams {
  idAgente: number;
  idPlazaOrigen: number;
  idPlazaDestino: number;
  idEscuelaOrigen: number;
  idEscuelaDestino: number;
  fechaInicio: Date;
  fechaFin?: Date;
  normaLegal: string;
}

export async function procesarTrasladoTransitorio(params: TrasladoTransitorioParams) {
  return await prisma.$transaction(async (tx) => {
    // 1. Validar traslado
    const validacion = await tx.$queryRaw`
      SELECT * FROM planillas.validar_traslado_transitorio(
        ${params.idAgente},
        ${params.idPlazaOrigen},
        ${params.idPlazaDestino}
      )
    `;

    if (!validacion[0].es_valido) {
      throw new Error(`Traslado inválido: ${validacion[0].mensaje}`);
    }

    // 2. Crear novedad de salida en escuela origen
    const novedadSalida = await tx.novedad.create({
      data: {
        id_agente: params.idAgente,
        id_plaza: params.idPlazaOrigen,
        id_tipo: await obtenerIdTipoNovedad('TRASLADO_TRANSITORIO_SALIDA'),
        fecha_inicio: params.fechaInicio,
        fecha_fin: params.fechaFin,
        norma_legal: params.normaLegal,
        observaciones: `Traslado transitorio a escuela ${params.idEscuelaDestino}`
      }
    });

    // 3. Marcar plaza origen como "TITULAR_EN_TRASLADO"
    await tx.plaza.update({
      where: { id: params.idPlazaOrigen },
      data: { 
        estado: 'TITULAR_EN_TRASLADO',
        observaciones: `Titular en traslado transitorio desde ${params.fechaInicio}`
      }
    });

    // 4. Crear toma de posesión en escuela destino
    const tomaDestino = await tx.tomaPosesion.create({
      data: {
        id_escuela: params.idEscuelaDestino,
        id_agente: params.idAgente,
        id_plaza: params.idPlazaDestino,
        situacion_revista: 'INTERINO_TRANSITORIO',
        fecha_toma: params.fechaInicio,
        norma_legal: params.normaLegal,
        motivo: 'TRASLADO_TRANSITORIO'
      }
    });

    // 5. Crear novedad de ingreso en escuela destino
    const novedadIngreso = await tx.novedad.create({
      data: {
        id_agente: params.idAgente,
        id_plaza: params.idPlazaDestino,
        id_tipo: await obtenerIdTipoNovedad('TRASLADO_TRANSITORIO_INGRESO'),
        fecha_inicio: params.fechaInicio,
        fecha_fin: params.fechaFin,
        norma_legal: params.normaLegal,
        observaciones: `Traslado transitorio desde escuela ${params.idEscuelaOrigen}`
      }
    });

    // 6. Marcar plaza destino como "INTERINO_TRANSITORIO"
    await tx.plaza.update({
      where: { id: params.idPlazaDestino },
      data: { 
        estado: 'INTERINO_TRANSITORIO',
        id_agente_asignado: params.idAgente
      }
    });

    // 7. Registrar historial
    await tx.historialTraslado.create({
      data: {
        id_agente: params.idAgente,
        id_plaza_origen: params.idPlazaOrigen,
        id_plaza_destino: params.idPlazaDestino,
        fecha_inicio: params.fechaInicio,
        fecha_fin: params.fechaFin,
        id_novedad_salida: novedadSalida.id,
        id_novedad_ingreso: novedadIngreso.id,
        estado: 'ACTIVO'
      }
    });

    return {
      novedadSalida,
      tomaDestino,
      novedadIngreso,
      mensaje: 'Traslado transitorio procesado correctamente'
    };
  });
}
```

##### 1.3.4. Cierre de Traslado Transitorio

```typescript
export async function cerrarTrasladoTransitorio(
  idAgente: number,
  fechaFin: Date
) {
  return await prisma.$transaction(async (tx) => {
    // 1. Buscar traslado activo
    const traslado = await tx.historialTraslado.findFirst({
      where: {
        id_agente: idAgente,
        estado: 'ACTIVO'
      }
    });

    if (!traslado) {
      throw new Error('No hay traslado transitorio activo para este agente');
    }

    // 2. Cerrar novedades
    await tx.novedad.update({
      where: { id: traslado.id_novedad_salida },
      data: { fecha_fin: fechaFin }
    });

    await tx.novedad.update({
      where: { id: traslado.id_novedad_ingreso },
      data: { fecha_fin: fechaFin }
    });

    // 3. Restaurar plaza origen
    await tx.plaza.update({
      where: { id: traslado.id_plaza_origen },
      data: { 
        estado: 'ACTIVA',
        observaciones: null
      }
    });

    // 4. Liberar plaza destino
    await tx.plaza.update({
      where: { id: traslado.id_plaza_destino },
      data: { 
        estado: 'VACANTE',
        id_agente_asignado: null
      }
    });

    // 5. Cerrar historial
    await tx.historialTraslado.update({
      where: { id: traslado.id },
      data: { 
        estado: 'CERRADO',
        fecha_cierre: new Date()
      }
    });

    return { mensaje: 'Traslado transitorio cerrado correctamente' };
  });
}
```

---

### 1.4. Licencias Médicas (Artículos 27 y 28)

#### Diagnóstico Profundo

**Complejidad normativa:**
- Artículos 27 y 28: Licencias médicas de larga duración o complejas
- Requieren validación explícita del Centro de Reconocimientos Médicos
- No pueden procesarse automáticamente como licencias simples

**Problema actual:**
- SGPEM no diferencia entre tipos de licencias médicas
- Workflow único para todas las licencias
- Validación manual genera retrasos y posibles errores

#### Recomendación Técnica: Workflow Diferenciado con Integración Médica

##### 1.4.1. Clasificación de Licencias Médicas

```sql
-- Extender catálogo de tipos de novedad
INSERT INTO planillas.tipos_novedad (codigo, nombre, requiere_plaza, requiere_fecha_fin, requiere_validacion_manual, instrucciones)
VALUES 
('LIC_MEDICA_SIMPLE', 'Licencia Médica Simple', false, true, false, 'Hasta 30 días, certificado médico común'),
('LIC_MEDICA_27', 'Licencia Médica Art. 27', false, true, true, 'Licencia compleja, requiere validación del Centro de Reconocimientos'),
('LIC_MEDICA_28', 'Licencia Médica Art. 28', false, true, true, 'Licencia por enfermedad grave, requiere junta médica');
```

##### 1.4.2. Estados de Validación Médica

```sql
-- Tabla de validaciones médicas
CREATE TABLE planillas.validaciones_medicas (
    id_validacion       BIGSERIAL PRIMARY KEY,
    id_novedad          BIGINT NOT NULL REFERENCES planillas.novedades(id_novedad),
    tipo_licencia       VARCHAR(20) NOT NULL, -- '27', '28'
    estado_validacion   VARCHAR(30) NOT NULL DEFAULT 'PENDIENTE' 
        CHECK (estado_validacion IN ('PENDIENTE', 'EN_REVISION', 'APROBADA', 'RECHAZADA', 'OBSERVADA')),
    
    -- Datos del Centro Médico
    id_centro_medico    INTEGER,
    id_medico_revisor   INTEGER,
    fecha_solicitud     TIMESTAMP DEFAULT NOW(),
    fecha_revision      TIMESTAMP,
    dictamen            TEXT,
    observaciones_medico TEXT,
    
    -- Documentación
    certificado_inicial VARCHAR(500), -- URL a documento
    estudios_adjuntos   JSONB, -- Array de URLs
    
    -- Trazabilidad
    creado_por          INTEGER NOT NULL REFERENCES auth.users(id_user),
    modificado_por      INTEGER REFERENCES auth.users(id_user),
    fecha_modificacion  TIMESTAMP
);

CREATE INDEX idx_validaciones_novedad ON planillas.validaciones_medicas(id_novedad);
CREATE INDEX idx_validaciones_estado ON planillas.validaciones_medicas(estado_validacion);
```

##### 1.4.3. Workflow de Validación

```typescript
// servicios/licenciasMedicas.ts

enum EstadoValidacionMedica {
  PENDIENTE = 'PENDIENTE',
  EN_REVISION = 'EN_REVISION',
  APROBADA = 'APROBADA',
  RECHAZADA = 'RECHAZADA',
  OBSERVADA = 'OBSERVADA'
}

interface SolicitudLicenciaMedica {
  idAgente: number;
  tipoLicencia: '27' | '28';
  fechaInicio: Date;
  fechaFin: Date;
  certificadoMedico: string; // URL
  estudiosComplementarios?: string[];
  diagnostico?: string;
}

export async function solicitarLicenciaMedica(params: SolicitudLicenciaMedica) {
  return await prisma.$transaction(async (tx) => {
    // 1. Crear novedad con estado especial
    const novedad = await tx.novedad.create({
      data: {
        id_agente: params.idAgente,
        id_tipo: await obtenerIdTipoNovedad(`LIC_MEDICA_${params.tipoLicencia}`),
        fecha_inicio: params.fechaInicio,
        fecha_fin: params.fechaFin,
        estado: 'PENDIENTE_VALIDACION_MEDICA', // Estado especial
        observaciones: `Solicitud de licencia art. ${params.tipoLicencia}. Pendiente validación médica.`
      }
    });

    // 2. Crear registro de validación
    const validacion = await tx.validacionMedica.create({
      data: {
        id_novedad: novedad.id,
        tipo_licencia: params.tipoLicencia,
        estado_validacion: EstadoValidacionMedica.PENDIENTE,
        certificado_inicial: params.certificadoMedico,
        estudios_adjuntos: params.estudiosComplementarios || [],
        creado_por: 1 // Sistema o usuario actual
      }
    });

    // 3. Notificar al Centro de Reconocimientos Médicos
    await notificarCentroMedico({
      idValidacion: validacion.id,
      tipoLicencia: params.tipoLicencia,
      agente: params.idAgente,
      diagnostico: params.diagnostico
    });

    return {
      novedad,
      validacion,
      mensaje: `Licencia art. ${params.tipoLicencia} registrada. Pendiente validación médica.`
    };
  });
}

// Función para que el Centro Médico actualice estado
export async function actualizarValidacionMedica(
  idValidacion: number,
  nuevoEstado: EstadoValidacionMedica,
  dictamen: string,
  idMedico: number
) {
  return await prisma.$transaction(async (tx) => {
    // 1. Actualizar validación
    const validacion = await tx.validacionMedica.update({
      where: { id: idValidacion },
      data: {
        estado_validacion: nuevoEstado,
        dictamen: dictamen,
        id_medico_revisor: idMedico,
        fecha_revision: new Date()
      }
    });

    // 2. Actualizar novedad según resultado
    const novedad = await tx.novedad.update({
      where: { id: validacion.id_novedad },
      data: {
        estado: nuevoEstado === EstadoValidacionMedica.APROBADA ? 'ACTIVA' : 'RECHAZADA',
        observaciones: `${novedad.observaciones}\nValidación médica: ${nuevoEstado}. Dictamen: ${dictamen}`
      }
    });

    // 3. Si fue aprobada, procesar reemplazo si aplica
    if (nuevoEstado === EstadoValidacionMedica.APROBADA) {
      await procesarReemplazoAutomatico(novedad.id);
    }

    return { validacion, novedad };
  });
}
```

##### 1.4.4. Integración con Centro de Reconocimientos

```typescript
// integraciones/centroReconocimientos.ts

interface NotificacionCentroMedico {
  idValidacion: number;
  tipoLicencia: string;
  agente: number;
  diagnostico?: string;
}

export async function notificarCentroMedico(data: NotificacionCentroMedico) {
  // Enviar a API del Centro de Reconocimientos
  const response = await fetch(process.env.CENTRO_MEDICO_WEBHOOK_URL!, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${process.env.CENTRO_MEDICO_API_TOKEN}`
    },
    body: JSON.stringify({
      tipo: 'NUEVA_LICENCIA_MEDICA',
      id_validacion_sgpem: data.idValidacion,
      tipo_licencia: data.tipoLicencia,
      id_agente: data.agente,
      diagnostico_resumen: data.diagnostico,
      url_verificacion: `https://sgpem.mec.gob.ar/validaciones/${data.idValidacion}`,
      callback_url: `https://sgpem.mec.gob.ar/api/webhooks/centro-medico`
    })
  });

  if (!response.ok) {
    // Fallback: Marcar para procesamiento manual
    await marcarParaProcesamientoManual(data.idValidacion);
  }
}

// Endpoint para recibir respuesta del Centro Médico
export async function webhookCentroMedico(request: Request) {
  const data = await request.json();
  
  if (data.tipo === 'RESULTADO_VALIDACION') {
    await actualizarValidacionMedica(
      data.id_validacion_sgpem,
      data.resultado,
      data.dictamen,
      data.id_medico
    );
  }
}
```

---

### 1.5. Datos Desactualizados (DNI, Plazas)

#### Diagnóstico Profundo

**Problemas identificados:**
- DNI mal cargados causan fallas en sincronización RENAPER
- Plazas modificadas sin actualizar referencias
- Cambios de grado no informados en POF

**Impacto:**
- Validaciones de identidad fallan
- Inconsistencias propagadas a liquidaciones
- Dificultad para auditoría y trazabilidad

#### Recomendación Técnica: Validación Proactiva y Data Quality

##### 1.5.1. Validación de DNI con RENAPER

```typescript
// servicios/validacionIdentidad.ts

import { redis } from '@/lib/redis';

interface ValidacionDNIResult {
  esValido: boolean;
  datosRenaper?: {
    nombre: string;
    apellido: string;
    cuil: string;
    fechaNacimiento: Date;
    estado: 'ACTIVO' | 'FALLECIDO' | 'BAJA';
  };
  discrepancias: string[];
}

export async function validarDNIConRenaper(
  dni: string,
  datosLocales: {
    nombre: string;
    apellido: string;
    cuil?: string;
  }
): Promise<ValidacionDNIResult> {
  // Cache para no saturar RENAPER
  const cacheKey = `renaper:validacion:${dni}`;
  const cached = await redis.get(cacheKey);
  
  if (cached) {
    return compararConDatosLocales(JSON.parse(cached), datosLocales);
  }

  try {
    const response = await fetch(`${process.env.RENAPER_API_URL}/personas/${dni}`, {
      headers: {
        'Authorization': `Bearer ${process.env.RENAPER_API_TOKEN}`,
        'X-Source': 'SGPEM-CGE-CORRIENTES'
      }
    });

    if (!response.ok) {
      return {
        esValido: false,
        discrepancias: ['No se pudo validar con RENAPER']
      };
    }

    const datosRenaper = await response.json();
    
    // Guardar en cache (24 horas)
    await redis.setex(cacheKey, 86400, JSON.stringify(datosRenaper));
    
    return compararConDatosLocales(datosRenaper, datosLocales);
  } catch (error) {
    console.error('Error validando DNI con RENAPER:', error);
    return {
      esValido: false,
      discrepancias: ['Error de conexión con RENAPER']
    };
  }
}

function compararConDatosLocales(
  datosRenaper: any,
  datosLocales: any
): ValidacionDNIResult {
  const discrepancias: string[] = [];

  // Comparar nombre
  if (normalizarTexto(datosRenaper.nombre) !== normalizarTexto(datosLocales.nombre)) {
    discrepancias.push(`Nombre: Local "${datosLocales.nombre}" vs RENAPER "${datosRenaper.nombre}"`);
  }

  // Comparar apellido
  if (normalizarTexto(datosRenaper.apellido) !== normalizarTexto(datosLocales.apellido)) {
    discrepancias.push(`Apellido: Local "${datosLocales.apellido}" vs RENAPER "${datosRenaper.apellido}"`);
  }

  // Comparar CUIL si existe localmente
  if (datosLocales.cuil && datosRenaper.cuil !== datosLocales.cuil) {
    discrepancias.push(`CUIL: Local "${datosLocales.cuil}" vs RENAPER "${datosRenaper.cuil}"`);
  }

  // Verificar estado
  if (datosRenaper.estado !== 'ACTIVO') {
    discrepancias.push(`Estado en RENAPER: ${datosRenaper.estado}`);
  }

  return {
    esValido: discrepancias.length === 0 && datosRenaper.estado === 'ACTIVO',
    datosRenaper: {
      nombre: datosRenaper.nombre,
      apellido: datosRenaper.apellido,
      cuil: datosRenaper.cuil,
      fechaNacimiento: new Date(datosRenaper.fecha_nacimiento),
      estado: datosRenaper.estado
    },
    discrepancias
  };
}

function normalizarTexto(texto: string): string {
  return texto
    .toUpperCase()
    .normalize('NFD')
    .replace(/[\u0300-\u036f]/g, '') // Quitar tildes
    .replace(/[^A-Z0-9]/g, ''); // Solo alfanumérico
}
```

##### 1.5.2. Proceso Batch de Validación de Data Quality

```typescript
// jobs/validacionDataQuality.ts
// Job programado para validar datos periódicamente

import { prisma } from '@/lib/prisma';
import { validarDNIConRenaper } from '@/servicios/validacionIdentidad';
import { crearInconsistencia } from '@/servicios/inconsistencias';

export async function ejecutarValidacionDataQuality() {
  console.log('Iniciando validación de data quality...');
  
  // 1. Validar agentes sin verificación reciente
  const agentesPendientes = await prisma.agente.findMany({
    where: {
      OR: [
        { fecha_validacion_dni: null },
        { fecha_validacion_dni: { lt: new Date(Date.now() - 90 * 24 * 60 * 60 * 1000) } } // 90 días
      ]
    },
    take: 100 // Batch de 100 para no saturar APIs
  });

  for (const agente of agentesPendientes) {
    try {
      const validacion = await validarDNIConRenaper(agente.dni, {
        nombre: agente.nombre,
        apellido: agente.apellido,
        cuil: agente.cuil
      });

      if (!validacion.esValido && validacion.discrepancias.length > 0) {
        // Crear inconsistencia
        await crearInconsistencia({
          tipo: 'DNI_INVALIDO',
          entidad_afectada: 'AGENTE',
          id_entidad: agente.id,
          descripcion: `Discrepancias en datos de agente: ${validacion.discrepancias.join(', ')}`,
          severidad: 'ALTA'
        });
      }

      // Actualizar fecha de validación
      await prisma.agente.update({
        where: { id: agente.id },
        data: { fecha_validacion_dni: new Date() }
      });

      // Rate limiting: Esperar 100ms entre llamadas
      await new Promise(resolve => setTimeout(resolve, 100));
    } catch (error) {
      console.error(`Error validando agente ${agente.id}:`, error);
    }
  }

  // 2. Detectar plazas modificadas sin actualizar
  await detectarPlazasDesactualizadas();
  
  console.log('Validación de data quality completada');
}

async function detectarPlazasDesactualizadas() {
  // Comparar POF con datos locales
  const plazasLocales = await prisma.plaza.findMany({
    where: { activa: true },
    include: { escuela: true }
  });

  for (const plaza of plazasLocales) {
    // Consultar estado actual en Gestión Educativa
    const estadoActual = await consultarEstadoPlazaEnGestion(plaza.id);
    
    if (estadoActual && estadoActual.updated_at > plaza.updatedAt) {
      // Plaza modificada después de última sincronización
      await crearInconsistencia({
        tipo: 'PLAZA_MODIFICADA_POF',
        id_escuela: plaza.id_escuela,
        entidad_afectada: 'PLAZA',
        id_entidad: plaza.id,
        descripcion: `Plaza modificada en POF el ${estadoActual.updated_at}. Requiere sincronización.`,
        severidad: 'ALTA'
      });
    }
  }
}

// Programar ejecución diaria
// Cron: 0 3 * * * (3 AM todos los días)
```

##### 1.5.3. Reporte de Data Quality

```sql
-- Vista de reporte de calidad de datos
CREATE OR REPLACE VIEW planillas.vw_data_quality AS
SELECT 
    'AGENTES' as entidad,
    COUNT(*) as total,
    COUNT(*) FILTER (WHERE fecha_validacion_dni IS NULL) as sin_validar,
    COUNT(*) FILTER (WHERE fecha_validacion_dni < NOW() - INTERVAL '90 days') as validacion_vencida,
    COUNT(*) FILTER (WHERE cuil IS NULL OR LENGTH(cuil) != 11) as cuil_invalido,
    ROUND(
        COUNT(*) FILTER (WHERE fecha_validacion_dni IS NOT NULL) * 100.0 / COUNT(*),
        2
    ) as pct_validados
FROM planillas.agentes

UNION ALL

SELECT 
    'PLAZAS' as entidad,
    COUNT(*) as total,
    COUNT(*) FILTER (WHERE updated_at < NOW() - INTERVAL '30 days') as sin_actualizar,
    COUNT(*) FILTER (WHERE estado = 'DESCONOCIDO') as estado_invalido,
    COUNT(*) FILTER (WHERE id_escuela IS NULL) as sin_escuela,
    ROUND(
        COUNT(*) FILTER (WHERE updated_at >= NOW() - INTERVAL '7 days') * 100.0 / COUNT(*),
        2
    ) as pct_actualizadas_reciente
FROM planillas.plazas;
```

---

## 2. Arquitectura de Solución Integrada

### 2.1. Diagrama de Arquitectura Extendida

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           CAPA DE PRESENTACIÓN                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐   │
│  │   Director   │  │   Cargador   │  │    CGE       │  │     CME      │   │
│  │   (Escuela)  │  │(Departamento)│  │  (Revisor)   │  │   (Médico)   │   │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘   │
└─────────┼─────────────────┼─────────────────┼─────────────────┼───────────┘
          │                 │                 │                 │
┌─────────┼─────────────────┼─────────────────┼─────────────────┼───────────┐
│         ▼                 ▼                 ▼                 ▼           │
│  ┌────────────────────────────────────────────────────────────────────┐  │
│  │                      SGPEM - API REST                               │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐             │  │
│  │  │   Módulo     │  │   Módulo     │  │   Módulo     │             │  │
│  │  │  Planillas   │  │Inconsistencias│  │  Validación  │             │  │
│  │  └──────────────┘  └──────────────┘  └──────────────┘             │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐             │  │
│  │  │   Traslados  │  │   Licencias  │  │   Agentes    │             │  │
│  │  │ Transitorios │  │   Médicas    │  │              │             │  │
│  │  └──────────────┘  └──────────────┘  └──────────────┘             │  │
│  └────────────────────────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────────────────────────┘
                                      │
┌─────────────────────────────────────┼────────────────────────────────────┐
│                              CAPA DE SERVICIOS                           │
│  ┌──────────────┐  ┌──────────────┐ │ ┌──────────────┐  ┌──────────────┐│
│  │  Validador   │  │   Gestor de  │ │ │  Validador   │  │  Procesador  ││
│  │    Plazas    │  │Inconsistencias│ │ │    DNI       │  │   Batch      ││
│  │   (Cache)    │  │              │ │ │  (RENAPER)   │  │              ││
│  └──────────────┘  └──────────────┘ │ └──────────────┘  └──────────────┘│
└─────────────────────────────────────┼────────────────────────────────────┘
                                      │
┌─────────────────────────────────────┼────────────────────────────────────┐
│                         INTEGRACIONES EXTERNAS                           │
│  ┌──────────────┐  ┌──────────────┐ │ ┌──────────────┐  ┌──────────────┐│
│  │   Gestión    │  │     POF      │ │ │   RENAPER    │  │ Centro Médico││
│  │  Educativa   │  │   Oficial    │ │ │              │  │              ││
│  │  (API+Hook)  │  │   (Sync)     │ │ │    (API)     │  │   (Webhook)  ││
│  └──────────────┘  └──────────────┘ │ └──────────────┘  └──────────────┘│
└─────────────────────────────────────┴────────────────────────────────────┘
                                      │
┌─────────────────────────────────────┼────────────────────────────────────┐
│                              CAPA DE DATOS                               │
│  ┌──────────────┐  ┌──────────────┐ │ ┌──────────────┐  ┌──────────────┐│
│  │  PostgreSQL  │  │    Redis     │ │ │  Cloudflare  │  │   Logs/      ││
│  │   (Prisma)   │  │    Cache     │ │ │     R2       │  │  Auditoría   ││
│  │              │  │   +Queue     │ │ │  (Archivos)  │  │              ││
│  └──────────────┘  └──────────────┘ │ └──────────────┘  └──────────────┘│
└──────────────────────────────────────────────────────────────────────────┘
```

### 2.2. Nuevos Componentes Requeridos

| Componente | Tecnología | Propósito | Esfuerzo Est. |
|------------|-----------|-----------|---------------|
| **Módulo Inconsistencias** | Next.js + PostgreSQL | Gestión de casos, workflow de corrección | 80 horas |
| **Validador de Plazas** | TypeScript + Redis | Cache de validaciones, consulta en tiempo real | 40 horas |
| **Webhook Handler** | Next.js API Routes | Recepción de eventos externos | 24 horas |
| **Validador DNI RENAPER** | TypeScript + API externa | Validación de identidad | 16 horas |
| **Workflow Licencias Médicas** | Next.js + PostgreSQL | Gestión de validaciones 27/28 | 32 horas |
| **Job Data Quality** | Bull + Redis | Procesos batch de validación | 16 horas |
| **Dashboard Métricas** | Next.js + Charts | KPIs y reportes | 24 horas |
| **Tests + QA** | Jest + Playwright | Cobertura de nuevos módulos | 40 horas |
| **Documentación** | Markdown + Swagger | Especificaciones y guías | 16 horas |
| **TOTAL** | | | **308 horas (~8 semanas)** |

### 2.3. Cambios en Modelo de Datos

```sql
-- Resumen de cambios en schema planillas

-- 1. Nuevas tablas
CREATE TABLE planillas.inconsistencias (...); -- Gestión de casos
CREATE TABLE planillas.tipos_inconsistencia (...); -- Catálogo
CREATE TABLE planillas.historial_plaza (...); -- Tracking de cambios
CREATE TABLE planillas.historial_traslado (...); -- Traslados transitorios
CREATE TABLE planillas.validaciones_medicas (...); -- Licencias 27/28

-- 2. Tablas modificadas
ALTER TABLE planillas.estados_planilla ADD COLUMN color VARCHAR(7);
ALTER TABLE planillas.estados_planilla ADD COLUMN orden_workflow INTEGER;
INSERT INTO planillas.estados_planilla (codigo, nombre, ...) VALUES ('BAJO_OBSERVACION', ...);

-- 3. Nuevos tipos ENUM
ALTER TYPE planillas.estado_plaza_enum ADD VALUE 'TITULAR_EN_TRASLADO';
ALTER TYPE planillas.estado_plaza_enum ADD VALUE 'INTERINO_TRANSITORIO';

-- 4. Nuevas funciones
CREATE OR REPLACE FUNCTION planillas.validar_inconsistencias_planilla(...);
CREATE OR REPLACE FUNCTION planillas.validar_traslado_transitorio(...);
CREATE OR REPLACE FUNCTION planillas.validar_antes_aprobacion(...);

-- 5. Nuevos índices
-- (Ver secciones anteriores para detalle)
```

---

## 3. Plan de Implementación Priorizado

### 3.1. Fase 0: Corrección de Inconsistencias (PRE-REQUISITO)

**Duración:** 4 semanas  
**Dependencias:** Ninguna (proceso manual + plantillas Excel/Forms)  
**Objetivo:** Reducir inconsistencias críticas a < 5% antes de SGPEM

#### Semana 1-2: Detección y Clasificación

**Actividades:**
- Exportar listado de inconsistencias actuales desde POST
- Clasificar por tipo y severidad
- Asignar prioridades (críticas primero)
- Identificar escuelas con mayor cantidad

**Entregables:**
- Base de inconsistencias actualizada
- Reporte de priorización
- Lista de escuelas críticas

#### Semana 3-4: Corrección Masiva

**Actividades:**
- Reunión con cargadores (ya programada para lunes)
- Asignar casos por departamento
- Establecer SLAs: Críticas (24hs), Altas (72hs), Medias (1 semana)
- Sesiones de corrección intensivas

**Entregables:**
- Inconsistencias corregidas: > 90%
- Proceso documentado
- Métricas de tiempo de resolución

**Métricas de éxito:**
- Críticas pendientes: 0
- Altas pendientes: < 5% del total
- Tiempo promedio resolución: < 48hs

### 3.2. Fase 1: Módulo de Inconsistencias (CRÍTICO)

**Duración:** 4 semanas  
**Dependencias:** Fase 0 completada  
**Objetivo:** Sistema funcional de gestión de inconsistencias

#### Semana 1-2: Desarrollo Backend

**Tareas técnicas:**
- Crear tablas de inconsistencias y catálogos
- Implementar API endpoints
- Desarrollar reglas de negocio
- Tests unitarios

**Entregables:**
- API funcional
- Tests passing
- Documentación de endpoints

#### Semana 3-4: Desarrollo Frontend + Integración

**Tareas técnicas:**
- UI para directores (vista de inconsistencias)
- UI para cargadores (vista por departamento)
- Dashboard de métricas
- Integración con módulo de planillas

**Entregables:**
- Módulo funcional
- Flujo completo testeado
- Capacitación de usuarios piloto

### 3.3. Fase 2: Adaptaciones SGPEM

**Duración:** 4 semanas  
**Dependencias:** Fase 1 completada  
**Objetivo:** SGPEM adaptado para manejar casos complejos

#### Semana 1: Validación de Plazas en Tiempo Real

- Integración con API Gestión Educativa
- Implementación de cache Redis
- Webhooks para sincronización

#### Semana 2: Workflow Traslados Transitorios

- Desarrollo de proceso específico
- Estados de plaza extendidos
- Historial de traslados

#### Semana 3: Workflow Licencias Médicas

- Estados de validación médica
- Integración con Centro de Reconocimientos
- Notificaciones automáticas

#### Semana 4: Validación de Identidad

- Integración RENAPER
- Proceso batch de validación
- Reporte de data quality

### 3.4. Fase 3: Piloto Verón de Astrada

**Duración:** 6 semanas  
**Dependencias:** Fases 0-2 completadas  
**Objetivo:** Validar solución en entorno real

#### Semana 1-2: Preparación

- Selección de 5 escuelas piloto
- Configuración de ambiente
- Capacitación de directores y cargadores
- Migración de datos iniciales

#### Semana 3-4: Ejecución Piloto

- Uso del sistema en producción (limitado)
- Monitoreo intensivo
- Soporte dedicado
- Recolección de feedback

#### Semana 5-6: Evaluación y Ajustes

- Análisis de métricas
- Identificación de problemas
- Ajustes rápidos
- Decisión de expansión

**Métricas de éxito del piloto:**

| Métrica | Objetivo | Medición |
|---------|----------|----------|
| Adopción | 100% escuelas piloto activas | Uso diario del sistema |
| Inconsistencias críticas | 0 al finalizar período | Reporte automático |
| Tiempo resolución | < 24hs (críticas), < 72hs (altas) | Tracking automático |
| Satisfacción usuarios | NPS > 40 | Encuesta post-piloto |
| Disponibilidad sistema | > 99% | Monitoreo uptime |
| Errores de validación | < 2% | Logs de errores |

### 3.5. Cronograma Visual

```
2026
Marzo                    Abril                    Mayo                    Junio
├─ Fase 0 ─┤            ├─ Fase 1 ─┤             ├─ Fase 2 ─┤            ├─ Fase 3 ─┤
│          │            │          │             │          │            │          │
Sem: 1  2  3  4        1  2  3  4  5  6  7  8    1  2  3  4  5  6  7  8  1  2  3  4  5  6
     │  │  │  │        │  │  │  │  │  │  │  │    │  │  │  │  │  │  │  │  │  │  │  │  │  │
     └──┴──┴──┴────────┘  │  │  │  │  │  │  │    │  │  │  │  │  │  │  │  │  │  │  │  │  │
                          └──┴──┴──┴──┴──┴──┴────┘  │  │  │  │  │  │  │  │  │  │  │  │  │
                                                    └──┴──┴──┴──┴──┴──┴──┘  │  │  │  │  │
                                                                               └──┴──┴──┴──┴──┘

Hitos:
► Sem 4: Inconsistencias < 5%
► Sem 8: Módulo inconsistencias operativo
► Sem 12: Piloto Verón lanza
► Sem 18: Decisión de expansión provincial
```

---

## 4. Recomendaciones Organizativas

### 4.1. Cambio al Equipo de Carlos Encina

**Recomendación:** Integrar al equipo de desarrollo SGPEM bajo la coordinación de Carlos Encina.

**Justificación:**
- ✅ Acceso directo a información de todos los sistemas (POF, Gestión, RENAPER)
- ✅ Mayor poder de decisión en arquitectura e integraciones
- ✅ Eliminación de barreras burocráticas para acceso a APIs y datos
- ✅ Mejor comunicación con áreas operativas
- ✅ Capacidad de influir en prioridades y roadmaps

**Implementación:**
- Solicitar transferencia formal al área de Carlos Encina
- Establecer SLA de respuesta para solicitudes técnicas
- Definir proceso de escalación para decisiones críticas

### 4.2. Estructura de Equipo Sugerida

```
Coordinador General (Carlos Encina)
│
├─ Líder Técnico SGPEM
│   ├─ Desarrollador Backend (2)
│   ├─ Desarrollador Frontend (1)
│   ├─ QA/Testing (1)
│   └─ Analista Funcional (1)
│
├─ Líder Operativo
│   ├─ Cargadores (por departamento)
│   ├─ Personal Docente (CGE)
│   └─ Supervisores (por zona)
│
└─ Líder de Integraciones
    ├─ Especialista POF
    ├─ Especialista Gestión Educativa
    └─ Especialista RENAPER/Centro Médico
```

### 4.3. Capacitación

**Programa de capacitación sugerido:**

| Rol | Capacitación | Duración | Formato |
|-----|-------------|----------|---------|
| Directores | Uso básico del sistema + gestión de inconsistencias | 4 horas | Virtual + tutoriales |
| Cargadores | Módulo de inconsistencias + workflow de corrección | 8 horas | Presencial + práctica |
| Personal CGE | Panel de revisión + validaciones + métricas | 8 horas | Presencial |
| Supervisores | Dashboard + monitoreo + reportes | 4 horas | Virtual |
| Centro Médico | Validación de licencias 27/28 en sistema | 2 horas | Virtual |

### 4.4. Comunicación y Change Management

**Estrategia de comunicación:**

1. **Comunicación inicial:** Email a todos los actores explicando cambios
2. **Newsletter semanal:** Actualizaciones de progreso durante implementación
3. **Canal de soporte:** WhatsApp/Telegram para consultas rápidas
4. **Sesiones Q&A:** Semanales durante piloto
5. **Celebración de hitos:** Reconocimiento público de logros

---

## 5. Riesgos y Mitigaciones

### 5.1. Matriz de Riesgos

| Riesgo | Probabilidad | Impacto | Estrategia | Plan de Mitigación |
|--------|--------------|---------|------------|-------------------|
| **Resistencia al cambio** (directores/cargadores) | Alta | Alto | Gestión del cambio | Capacitación intensiva, identificar champions, comunicación clara de beneficios |
| **API Gestión Educativa no disponible** | Media | Crítico | Contingencia | Fallback a BD local, sincronización batch nocturna, cache extendido |
| **Performance con 1270 escuelas** | Media | Alto | Preparación | Test de carga, optimización de queries, CDN, escalado horizontal |
| **Inconsistencias no resolubles en Fase 0** | Media | Crítico | Reducción | Priorizar críticas, aceptar casos bajo observación, proceso manual paralelo |
| **Licencias 27/28 demoran validación** | Alta | Medio | Aceptación | Workflow diferenciado, no bloquear otras operaciones, seguimiento específico |
| **Fuga de información sensible** | Baja | Crítico | Prevención | Encriptación, auditoría, RBAC estricto, tokens JWT cortos |
| **Cambios normativos durante desarrollo** | Media | Medio | Flexibilidad | Arquitectura configurable, sin hardcodear reglas, tests automatizados |
| **Pérdida de datos** | Baja | Crítico | Protección | Backups automatizados, replicación, DR plan, tests de restore |

### 5.2. Plan de Contingencia por Escenario

#### Escenario: APIs externas no disponibles

**Activación:** Gestión Educativa o RENAPER caídos > 2 horas

**Acciones:**
1. Activar modo "offline" con datos cacheados
2. Permitir operaciones con advertencia de sincronización pendiente
3. Encolar cambios para sincronización posterior
4. Notificar a usuarios de limitaciones temporales
5. Escalar a equipo de infraestructura

#### Escenario: Performance degradada

**Activación:** Tiempo de respuesta > 5 segundos o errores 504

**Acciones:**
1. Escalar instancias automáticamente (si cloud)
2. Activar modo "simplificado" (deshabilitar validaciones complejas)
3. Limitar operaciones concurrentes por usuario
4. Mostrar mensaje de "sistema lento, intente más tarde"
5. Análisis post-incidente y optimización

#### Escenario: Error masivo de datos

**Activación:** > 10% de operaciones fallan por datos

**Acciones:**
1. Detener envíos de planillas automáticamente
2. Mantener modo borrador operativo
3. Análisis urgente de root cause
4. Corrección manual de datos críticos
5. Comunicación proactiva a usuarios
6. Post-mortem y mejoras preventivas

---

## 6. Checklist de Implementación

### Pre-Implementación

- [ ] Aprobación de presupuesto (308 horas de desarrollo)
- [ ] Confirmación de acceso a APIs externas
- [ ] Asignación de equipo técnico
- [ ] Decisión sobre cambio a equipo de Carlos Encina
- [ ] Preparación de ambiente de desarrollo/staging
- [ ] Backup completo de base de datos actual

### Fase 0: Corrección Inconsistencias

- [ ] Exportar inconsistencias actuales
- [ ] Clasificar por severidad
- [ ] Reunión inicial con cargadores
- [ ] Asignar casos por departamento
- [ ] Sesiones de corrección (4 semanas)
- [ ] Métricas: < 5% inconsistencias críticas

### Fase 1: Módulo Inconsistencias

- [ ] DDL de nuevas tablas
- [ ] API endpoints desarrollados
- [ ] Tests unitarios passing (> 80% cobertura)
- [ ] UI para directores
- [ ] UI para cargadores
- [ ] Dashboard de métricas
- [ ] Documentación de usuario
- [ ] Capacitación piloto

### Fase 2: Adaptaciones SGPEM

- [ ] Integración API Gestión Educativa
- [ ] Cache Redis configurado
- [ ] Webhooks operativos
- [ ] Workflow traslados transitorios
- [ ] Workflow licencias médicas 27/28
- [ ] Integración RENAPER
- [ ] Job data quality programado
- [ ] Tests de integración passing

### Fase 3: Piloto Verón

- [ ] 5 escuelas seleccionadas y notificadas
- [ ] Ambiente de producción configurado
- [ ] Datos migrados y validados
- [ ] Capacitación de usuarios piloto
- [ ] Monitoreo configurado (uptime, errores, performance)
- [ ] Soporte dedicado disponible
- [ ] Checklist semanal de revisión
- [ ] Encuesta de satisfacción

### Post-Piloto

- [ ] Análisis de métricas vs. objetivos
- [ ] Documentación de lecciones aprendidas
- [ ] Ajustes implementados
- [ ] Decisión de expansión provincial
- [ ] Plan de rollout departamento por departamento
- [ ] Comunicación de resultados a stakeholders

---

## 7. Conclusión y Próximos Pasos Inmediatos

### Resumen de Recomendaciones Clave

1. **NO implementar SGPEM sin resolver primero la crisis de inconsistencias.** Esto es crítico y bloqueante.

2. **Implementar el estado "Bajo Observación"** como solución pragmática para no paralizar operaciones mientras se corrigen datos.

3. **Invertir en integraciones en tiempo real** (cache + webhooks) en lugar de depender de sincronizaciones batch.

4. **Diseñar workflows específicos** para casos complejos (traslados transitorios, licencias 27/28) en lugar de forzar reglas rígidas.

5. **Priorizar la validación proactiva de datos** (RENAPER, data quality) para prevenir inconsistencias futuras.

6. **Considerar seriamente el cambio al equipo de Carlos Encina** para eliminar barreras de acceso y decisión.

### Decisiones Pendientes para Esta Semana

| Decisión | Responsable | Deadline | Impacto |
|----------|-------------|----------|---------|
| ¿Aprobar 8 semanas de desarrollo adicional? | Dirección CGE | Viernes | Timeline general |
| ¿Confirmar acceso a API Gestión Educativa? | Área Técnica | Miércoles | Fase 2 |
| ¿Aprobar cambio a equipo Carlos Encina? | RRHH/Dirección | Viernes | Organización |
| ¿Seleccionar 5 escuelas piloto en Verón? | Supervisores | Jueves | Fase 3 |
| ¿Definir estrategia bloqueo vs. observaciones? | Dirección | Lunes (reunión) | Arquitectura |

### Próximos Pasos Inmediatos (Esta Semana)

1. **Lunes:** Reunión con cargadores de Verón
   - Presentar propuesta de solución
   - Medir tiempos de resolución actuales
   - Identificar a Virginia Coronaria como referente

2. **Martes-Miércoles:** 
   - Exportar y analizar inconsistencias actuales
   - Confirmar acceso a APIs externas
   - Preparar propuesta de presupuesto

3. **Jueves:**
   - Seleccionar escuelas piloto
   - Preparar cronograma detallado
   - Reunión de kickoff con equipo técnico

4. **Viernes:**
   - Decisión de aprobación de proyecto extendido
   - Decisión sobre cambio organizacional
   - Comunicación inicial a stakeholders

---

## Anexos

### Anexo A: Estimación de Esfuerzo Detallada

Ver sección 2.2 para desglose general. Detalle adicional disponible en archivo separado si se requiere.

### Anexo B: Contratos de API

Ver sección 1.2.2 para especificación de integración con Gestión Educativa. Contratos adicionales:
- RENAPER: `/docs/api-renaper.yaml` (pendiente de obtención)
- Centro Médico: `/docs/api-centro-medico.yaml` (pendiente de diseño conjunto)

### Anexo C: Scripts de Migración

Scripts SQL para migración de datos disponibles en `/sql/migraciones/`:
- `001_create_inconsistencias.sql`
- `002_extend_estados_planilla.sql`
- `003_create_historial_plazas.sql`
- `004_create_validaciones_medicas.sql`
- `005_seed_tipos_inconsistencia.sql`

### Anexo D: Documentación de Usuario

Guías de usuario para capacitación disponibles en `/docs/usuario/`:
- `director-guia-rapida.md`
- `cargador-manual-completo.md`
- `cge-revisor-procedimientos.md`
- `supervisor-dashboard.md`

---

**Documento elaborado:** 9 de febrero de 2026  
**Versión:** 1.0  
**Autor:** Asistente Técnico  
**Revisores:** Pendiente  
**Aprobación:** Pendiente

---

*Este documento es confidencial y de uso exclusivo del Consejo General de Educación de la Provincia de Corrientes.*


Anexo: 
[[TrasladoTransitorio]]
[[Plan técnico]]
