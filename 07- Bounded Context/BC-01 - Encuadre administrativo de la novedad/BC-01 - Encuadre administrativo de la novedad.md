---
proyecto: Sistematizacion de Novedades Laborales
tipo: Bounded context
estado: borrador
date: 2026-04-06
tags:
  - sgpem
  - bounded-context
  - encuadre
  - novedades-laborales
---

> [!abstract] Propósito
> Este bounded context existe para recibir una novedad, darle un encuadre administrativo inicial y derivarla al contexto funcional correcto. Su valor no esta en resolver toda la logica de negocio del caso, sino en evitar que el sistema mezcle recepcion, clasificacion y tratamiento profundo en un mismo paso. Tambien define la autoridad de encuadre, los estados de entrada y las condiciones minimas para admitir, observar, rechazar o derivar un caso.

> [!tip]
> Para el nivel tactico y los casos de negocio de este contexto, ver `[[Catalogo de decisiones y eventos - BC-01 - Encuadre administrativo de la novedad]]` y `[[Escenarios BDD - BC-01 - Encuadre administrativo de la novedad]]`.

## 1. Por qué es un bounded context separado

Muchas confusiones del dominio nacen antes del tratamiento especifico de la novedad. El problema inicial no siempre es `como se resuelve`, sino `que es`, `de donde vino`, `a quien afecta`, `a que familia pertenece` y `quien tiene autoridad para encuadrarlo`.

Separar este contexto permite que el resto del sistema reciba casos ya encuadrados con un lenguaje común.

## 2. Que incluye

- Recepción de novedades desde origen local, central o externo,
- Identificación inicial del tipo de novedad,
- Registro del origen del caso,
- Asociación mínima con agente y referencias del caso,
- Clasificación por familia funcional,
- Derivación al **bounded context** correspondiente,
- Estado inicial de `pendiente` u `observado` cuando el caso no puede derivarse todavía.

## 3. Qué no incluye

- La resolución profunda de licencias, reintegros o inasistencias,
- La lógica de cobertura transitoria,
- La lógica de movilidad funcional,
- La decisión de egreso definitivo,
- La consolidación y cierre del periodo,
- La administración estructural de POF.

## 4. Subpuntos iniciales

### 4.1 Orígenes de la novedad

| Origen                        | Peso de confianza inicial  | Uso esperado en BC-01                                                                                      |
| ----------------------------- | -------------------------- | ---------------------------------------------------------------------------------------------------------- |
| Establecimiento               | Variable segun familia     | Origen frecuente para interrupciones y casos locales; puede requerir validacion humana antes de derivar    |
| ==Nivel intermedio==          | ==Variable segun familia== | ==Puede aportar contexto o validacion adicional, pero no tiene una regla unica de confianza==              |
| Direccion de nivel            | Alta                       | Puede habilitar preclasificacion o derivacion automatica en familias estructuradas si no hay contradiccion |
| **Sistema** central           | Variable según familia     | Puede recibir, observar o redirigir casos, pero no reemplaza la autoridad funcional final de cada familia  |
| Integración externa           | Alta                       | Puede habilitar preclasificacion o derivación automática si el evento es confiable, completo y consistente |
| Conciliacion o regularizacion | Variable                   | Reingresa casos ya tratados o inconsistentes con trazabilidad previa                                       |

### 4.2 Clasificación inicial

| Familia de la novedad       | Puede preclasificarse automaticamente | Revision humana obligatoria | Ultima palabra de encuadre | Observaciones                                                                                                                                         |
| --------------------------- | ------------------------------------- | --------------------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| Interrupciones temporales   | Depende subtipo                       | Depende subtipo             | Depende subtipo            | Ver submatriz interna de interrupciones temporales                                                                                                    |
| Cobertura transitoria       | Si                                    | No por defecto              | **Sistema** central        | Requiere pregunta dominante clara sobre reemplazo y se efectiviza por toma de posesión, a su vez, puede venir de la designación como estado validado. |
| Movilidad funcional         | Si                                    | No por defecto              | Direccion de nivel         | Aplica especialmente a casos con acto y trazabilidad de origen/destino                                                                                |
| Egresos definitivos         | Si                                    | No por defecto              | Direccion de nivel         | Requiere fuerte trazabilidad de causal y fecha                                                                                                        |
| Ocupacion estable de cargos | Si                                    | No por defecto              | Direccion de nivel         | Requiere lectura clara de ocupacion estable, cargo y acto suficiente                                                                                  |

#### 4.2.1 Submatriz interna de interrupciones temporales

| Subtipo                          | Puede preclasificarse o derivarse automaticamente   | Revision humana obligatoria | Ultima palabra operativa | Soporte distintivo                             |
| -------------------------------- | --------------------------------------------------- | --------------------------- | ------------------------ | ---------------------------------------------- |
| Licencias medicas                | Si, con origen confiable y evidencia consistente    | No por defecto              | Integracion externa      | Certificado medico o aprobacion/evento externo |
| Licencias extraordinarias        | Si, cuando el acto y el subtipo son claros          | No por defecto              | **Sistema** central      | Acto administrativo                            |
| Licencias para personal suplente | No por defecto                                      | Si segun duda o ambiguedad  | Direccion de nivel       | Acto administrativo o constancia suficiente    |
| Inasistencias y paro             | Si, con registro local completo y sin contradiccion | No por defecto              | Establecimiento          | Constancia local                               |
| Reintegros                       | Si, si existe antecedente trazable                  | No por defecto              | **Sistema** central      | Referencia a antecedente y fecha de retorno    |

### 4.3 Datos mínimos iniciales

| Campo                                           | Universal | Efecto si falta |
| ----------------------------------------------- | --------- | --------------- |
| Origen                                          | Si        | Pendiente       |
| Actor que informa                               | Si        | Pendiente       |
| Agente o referencia equivalente                 | Si        | Rechazo         |
| Establecimiento o unidad de referencia          | Si        | Pendiente       |
| Fecha basica del hecho o vigencia               | Si        | Pendiente       |
| Motivo o descripcion inicial                    | Si        | Pendiente       |
| Cargo, plaza o referencia funcional equivalente | Si        | Rechazo         |
### 4.4 Evidencia mínima de admisión

| Tipo de evidencia | Sirve para admitir en BC-01 | Observaciones |
| ----------------- | --------------------------- | ------------- |
| Acto administrativo | Si | Habilita lectura fuerte cuando el caso depende de respaldo formal |
| Evento externo confiable | Si | Puede habilitar preclasificacion o derivacion automatica segun familia |
| Solo relato sin respaldo | No para clasificar o derivar en forma definitiva | Si el origen es confiable, los datos minimos estan completos y no hay contradiccion fuerte, el caso puede quedar `pendiente`; con faltante critico o contradiccion fuerte debe rechazarse |

Si falta `acto administrativo` o `evento externo`, pero el caso tiene origen confiable y datos mínimos completos, el caso puede ingresar como `pendiente`.

### 4.5 Estados de encuadre

| Estado | Significado | Puede derivarse |
| ------ | ----------- | --------------- |
| Recibida | El caso ingreso al sistema con identificacion basica | No |
| Preclasificada | El sistema o el operador ya detecto una familia tentativa | No |
| Pendiente | Faltan datos o evidencia simples de completitud | No |
| Observada | Hay dudas de encuadre, consistencia o contradiccion relevante | No |
| Clasificada | La familia y el destino funcional quedaron confirmados | Si |
| Derivada | El caso ya fue enviado al bounded context funcional correspondiente | No |
| Rechazada | El caso no puede admitirse en BC-01 por una causal suficiente | No |

### 4.6 Regla base de automatización

La automatización no se rige por una regla única, sino por una **matriz por familia**.

| Familia                     | Regla base de automatizacion                                                                                                 |
| --------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| Interrupciones temporales   | Depende subtipo; ver submatriz interna de interrupciones temporales                                                          |
| Cobertura transitoria       | Puede derivarse automaticamente si la familia es inferible, los datos minimos estan completos y no hay contradiccion         |
| Movilidad funcional         | Puede derivarse automaticamente si existe origen confiable, datos minimos completos y soporte suficiente del movimiento      |
| Egresos definitivos         | Puede derivarse automaticamente si existe origen confiable, datos minimos completos y no hay contradiccion de causal o fecha |
| Ocupacion estable de cargos | Puede derivarse automaticamente si existe origen confiable, lectura clara de ocupacion estable y datos minimos completos     |

En general, la derivación automática se apoya en una combinación de estas condiciones, según familia:

- origen confiable,
- familia inferible por pregunta dominante,
- campos mínimos completos,
- evidencia valida,
- ausencia de contradicciones.
## 5. Conceptos principales

| Concepto | Significado en BC-01 |
| -------- | -------------------- |
| Caso recibido | Novedad que ingresa al dominio con identificacion administrativa basica |
| Origen de la novedad | Fuente que dispara el ingreso y condiciona el peso de confianza inicial |
| Familia funcional | Recorte principal al que debe derivarse el caso |
| Clasificacion inicial | Resultado del encuadre temprano segun pregunta dominante |
| Pendiente | Estado por faltante simple de completitud o evidencia |
| Observacion temprana | Estado por inconsistencia, duda de encuadre o contradiccion |
| Derivacion | Envio del caso al bounded context funcional correcto |
| Rechazo | No admision del caso por causal suficiente |

## 6. Eventos principales

- novedad recibida,
- novedad pre clasificada,
- novedad pendiente de completitud,
- novedad observada por inconsistencia o duda de encuadre,
- novedad clasificada,
- novedad derivada a contexto funcional,
- novedad rechazada por imposibilidad de encuadre.

## 7. Decisiones principales

- a qué familia pertenece el caso,
- si hay información suficiente para continuar,
- si el caso debe quedar pendiente por faltante simple,
- si el caso requiere observación previa,
- a que bounded context debe derivarse,
- si el origen del caso condiciona su tratamiento posterior,
- si la derivación puede ser automatica o exige revision humana,
- si la causal de rechazo es terminal o admite nuevo ingreso según evidencia posterior.

## 8. Reglas centrales de delimitacion

1. Toda novedad debe tener un encuadre inicial antes de ser tratada a fondo.
2. Ningún caso debe entrar directamente a un flujo especifico sin familia o tipo reconocible.
3. Este contexto clasifica y deriva; no resuelve el negocio especifico de cada familia.
4. Si faltan datos mínimos o evidencia simple, el caso no desaparece: queda `pendiente`.
5. Si existe inconsistencia, contradicción o duda de encuadre, el caso queda `observado`.
6. Las reglas de familia deben ser canónicamente definidas aquí, aunque su negocio profundo viva en otros contextos.
7. El encuadre se decide principalmente por la `pregunta dominante del caso`, no solo por el rotulo informado.
8. No se admite un caso basado solo en relato sin respaldo mínimo suficiente.
9. La autoridad de encuadre es mixta por familia; BC-01 no impone una autoridad unica para todo el dominio.
10. El rechazo es terminal, como minimo, para casos `fuera de dominio`, `sin agente`, `sin cargo/plaza` o con `contradiccion fuerte`.

## 9. Relación con otros bounded contexts

- deriva a `[[BC-02 - Interrupciones temporales]]` cuando el efecto principal es transitorio,
- deriva a `[[BC-03 - Cobertura transitoria]]` cuando el caso nace como cobertura,
- deriva a `[[BC-04 - Movilidad funcional]]` cuando el caso es un movimiento funcional,
- deriva a `[[BC-05 - Egresos definitivos]]` cuando el efecto es de cierre definitivo,
- deriva a `[[BC-06 - Ocupacion estable de cargos]]` cuando el caso consiste en ocupar establemente un cargo,
- consume entradas de `[[BC-07 - Integraciones y conciliacion]]`, especialmente cuando el origen confiable es externo,
- entrega casos clasificados a `[[BC-08 - Consolidacion y cierre]]` solo de modo indirecto, a través de los contextos funcionales.

## 10. Puntos de interfaz relevantes

- orígenes con mayor peso de confianza inicial, especialmente `Integracion externa` y `Direccion de nivel`,
- toma de posesión como requisito de efectivizacion en coberturas, traslados y ocupaciones estables según la familia,
- actos administrativos que llegan desde fuera del sistema,
- evidencias mínimas que no siempre están completas al inicio,
- distinción entre `pendiente`, `observado` y `rechazado`,
- matriz por familia para automatización y derivación.

## 11. Preguntas abiertas para investigación

- que condiciones exactas deben disparar automatización segura por subtipo dentro de `Interrupciones temporales`,
- que combinaciones exactas de condiciones habilitan derivación automática por familia,
- cuando corresponde reabrir un rechazo y cuando debe exigirse un nuevo ingreso vinculado,
- que campos adicionales deben ser obligatorios según familia aunque no sean universales.
