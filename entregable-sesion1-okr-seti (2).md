# Entregable Sesión 1 — OKR SETI

## 1. Análisis del Problema

### 1.1 Situación actual

SETI es una organización de aproximadamente 1.000 colaboradores que busca modernizar su ejecución estratégica mediante la metodología OKR (Objectives & Key Results). Actualmente, la gestión de OKR se realiza en archivos estáticos (Excel) que los colaboradores no consultan con regularidad.

### 1.2 Problemas identificados

**Excel no escala.** La herramienta actual no soporta la complejidad de una organización de este tamaño. Los archivos estáticos generan versiones desactualizadas, duplicidad de información y baja adopción por parte de los equipos.

**Desconexión operativa (silos).** Áreas críticas como Ingeniería y Comercial operan de forma aislada, sin coordinación visible hacia metas comunes. No existe un mecanismo que fuerce o facilite la alineación transversal entre líneas de negocio.

**Falta de visibilidad ejecutiva.** La gerencia no cuenta con un panel que permita identificar rápidamente (en segundos) si la empresa va por buen camino o si hay desviaciones estratégicas. La información se obtiene de forma manual y tardía.

**Reactividad en lugar de proactividad.** Sin alertas automáticas ni semáforos, los incumplimientos se detectan cuando ya es demasiado tarde para corregir el rumbo. No hay mecanismo de alerta temprana.

### 1.3 Problema central

El desafío de SETI **no es la definición de la estrategia, sino su seguimiento y escalabilidad**. La estrategia existe, pero no hay una herramienta que permita operacionalizarla, medirla y hacerla visible en tiempo real para toda la organización.

---

## 2. Entendimiento del Dominio

### 2.1 ¿Qué son los OKR?

OKR (Objectives and Key Results) es un marco de gestión que conecta la estrategia con la ejecución:

- **Objetivos (O):** Metas aspiracionales y cualitativas que definen el rumbo. Funcionan como la "Estrella del Norte". No son métricas, son declaraciones de intención estratégica vinculadas a un periodo cuatrimestral.
- **Resultados Clave (KR):** Métricas cuantificables que validan si se está alcanzando el objetivo. Cada KR tiene un peso o ponderación específica (por ejemplo, un KR puede representar el 60% del éxito de un objetivo).

### 2.2 Reglas semánticas

**Para redactar Objetivos:**
- Deben ser cualitativos y motivadores.
- Responden a *qué se quiere lograr* de forma global.
- Están vinculados a periodos cuatrimestrales (Q).

**Para redactar Resultados Clave:**
- Deben ser medibles numéricamente (cuantificación obligatoria).
- Cada KR tiene un peso asignado que refleja su impacto relativo.
- Deben permitir adjuntar evidencias y comentarios de progreso, no solo un porcentaje.

### 2.3 Estructura organizacional y despliegue

El despliegue sigue una jerarquía de "padres e hijos" en tres niveles:

| Nivel | Responsable | Función |
|---|---|---|
| Estratégico | Gerencia General | Define la planeación anual y el rumbo global |
| Táctico | Gerencias de Línea de Negocio | Crean objetivos derivados de la estrategia general |
| Operativo | Líderes de Área / Departamentos | Redactan OKR que se alinean a los objetivos de su gerencia |

La alineación no es estrictamente vertical: áreas de soporte (como el CoE) pueden vincular sus KR a objetivos de diferentes líneas de negocio (alineación transversal).

### 2.4 Gestión y seguimiento

- **Ciclos:** Planeación anual ejecutada en 3 cuatrimestres (Q1, Q2, Q3).
- **Seguimiento mensual obligatorio:** Se reporta la "temperatura" de los KR cada mes.
- **Bitácora de avance (check-ins):** Registro de porcentaje + comentarios + evidencias.
- **Semáforo automático:** Alerta en rojo si el avance de un KR es inferior al 40% respecto al tiempo transcurrido.
- **Ajustes mid-cycle:** Permitidos si cambian las condiciones, pero con log de auditoría (quién, qué, cuándo, por qué).

### 2.5 Roles y permisos

| Rol | Permiso |
|---|---|
| Owner del OKR + jefe directo | Editar progreso |
| Gerencia General | Habilitar y configurar la planeación anual |
| Cualquier colaborador | Visualizar todos los OKR (transparencia total) |
| Sistema | Bloquear datos al cierre de cuatrimestre (solo consulta histórica) |

### 2.6 Principios del modelo SETI

- **Transparencia total** (modelo Google): todos ven los OKR de todos.
- **Agilidad:** los OKR se pueden ajustar, no están grabados en piedra.
- **Trazabilidad:** cualquier cambio queda en un log de auditoría.
- **Proactividad:** semáforos automáticos para detectar desviaciones a tiempo.

---

## 3. Estructura de Solución y Arquitectura Conceptual

### 3.1 Enfoque de solución

Se requiere una **aplicación web** que reemplace los archivos estáticos actuales y permita la gestión centralizada, en tiempo real y colaborativa de los OKR en SETI. La solución debe cubrir:

- Creación y edición de OKR con jerarquía padre-hijo.
- Alineación transversal entre áreas.
- Seguimiento mensual con check-ins (porcentaje, comentarios, evidencias).
- Cálculo automático de progreso basado en ponderación de KR.
- Dashboard ejecutivo con visibilidad inmediata del estado estratégico.
- Sistema de semáforos automáticos.
- Log de auditoría para cambios mid-cycle.
- Gestión de roles y permisos (owner, jefe directo, gerencia, visualización general).
- Bloqueo de datos al cierre de cuatrimestre.

### 3.2 Arquitectura conceptual (alto nivel)

```
┌─────────────────────────────────────────────────┐
│                   Frontend                       │
│         (SPA - Dashboard + Gestión OKR)          │
├─────────────────────────────────────────────────┤
│                   API REST                       │
│              (Backend / Servicios)                │
├──────────┬──────────┬───────────┬───────────────┤
│  Módulo  │  Módulo  │  Módulo   │    Módulo     │
│   OKR    │ Seguim.  │ Reportes  │   Auditoría   │
├──────────┴──────────┴───────────┴───────────────┤
│              Base de Datos                        │
│     (OKR, usuarios, check-ins, audit log)        │
├─────────────────────────────────────────────────┤
│         Autenticación / Autorización             │
│      (Roles: owner, jefe, gerencia, viewer)      │
└─────────────────────────────────────────────────┘
```

### 3.3 Módulos principales

| Módulo | Responsabilidad |
|---|---|
| **OKR** | CRUD de objetivos y KR, jerarquía padre-hijo, alineación transversal, ponderación |
| **Seguimiento** | Check-ins mensuales, registro de evidencias, cálculo automático de progreso, semáforos |
| **Reportes / Dashboard** | Vista ejecutiva, progreso por área/gerencia, filtros por periodo, exportación |
| **Auditoría** | Log de cambios, historial de modificaciones mid-cycle, bloqueo por cierre de Q |
| **Usuarios y Roles** | Gestión de permisos, estructura organizacional, asignación de owners |

### 3.4 Stack tecnológico (nivel conceptual)

| Capa | Tecnología | Justificación |
|---|---|---|
| **Frontend** | Angular | SPA con soporte robusto para dashboards complejos, navegación jerárquica y componentes reutilizables. Buen manejo de formularios reactivos para los check-ins y edición de OKR. |
| **Backend** | Java + Spring Boot | API REST con lógica de negocio para cálculos de ponderación, semáforos automáticos, permisos por rol y auditoría. Spring Security para el manejo de autenticación/autorización. |
| **Base de datos** | PostgreSQL | Relacional, adecuada para la estructura jerárquica de OKR (padre-hijo), relaciones entre entidades claras y soporte para consultas complejas en reportes. |
| **Autenticación** | Spring Security + SSO/LDAP | Integración con el directorio corporativo existente para autenticación centralizada. |
| **Auditoría** | Hibernate Envers o tabla de auditoría propia | Registro automático de cambios en OKR (quién, qué, cuándo, por qué) para trazabilidad mid-cycle. |

> **Nota:** La definición detallada de versiones, infraestructura de despliegue y ambientes queda para la fase de diseño técnico.
