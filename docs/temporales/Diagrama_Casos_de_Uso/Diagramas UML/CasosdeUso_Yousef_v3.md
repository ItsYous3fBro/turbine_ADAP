# Casos de uso · versión temporal v3

**Proyecto:** Gestor de facturas Turbine
**Autor:** Yousef · **Fecha:** 15/10/2026 · **Cambios v3:** diagrama dividido en tres por áreas
**Base:** `RequisitosV2Irene.md` 

> Versión temporal para fusionar con las del resto del subgrupo de casos de uso. .

---

## 1. Actores

| Actor | Tipo | Descripción | Requisitos |
|---|---|---|---|
| **Lector** (usuario operativo) | Principal | Empleado que trabaja solo con los clientes que tiene asignados | UR1–UR3 |
| **Administrador** | Principal | Hereda todo lo del Lector sobre todos los clientes; además asigna, aprueba, rectifica y ve el dashboard | UR4–UR7 |
| **Superadministrador** | Principal | Hereda todo lo del Administrador; además configura el sistema, gestiona usuarios y ve la auditoría | UR8–UR9 |
| **Temporizador** | Secundario | Representa las tareas automáticas que se ejecutan por fecha (día 1, recordatorios) | FR7, FR11 |
| **Pasarela de pago** | Secundario | Stripe simulado; informa de los cobros | FR12 |
| **Sistema externo / agente IA** | Secundario | Otro software que consulta o pide acciones por API | FR19, BR5 |
| **Cliente** | Secundario | Empresa que recibe facturas y recordatorios; no entra en la aplicación | FR10, FR11 |

La relación entre los tres roles es una **generalización**: Superadministrador → Administrador → Lector.
---

## 2. Diagramas

Para que se lean y expliquen con facilidad, el sistema se divide en **tres diagramas**, uno por área. En cada diagrama solo aparecen los actores que intervienen en él.

### 2.1 Diagrama 1 · Acceso, usuarios y clientes

Quién entra en el sistema, quién puede hacer qué y cómo se gestionan los clientes y sus suscripciones. Incluye la API, porque los sistemas externos consultan y piden acciones sobre clientes.

Imagen: `CasosdeUso_Yousef_v3_D1_AccesoClientes.png`


### 2.2 Diagrama 2 · Facturación

El núcleo del sistema. Todas las formas de generar una factura (puntual, recurrente o rectificativa) **incluyen** «Emitir y enviar factura», y la retención para aprobación la **extiende** solo cuando la factura es inusual.

Imagen: `CasosdeUso_Yousef_v3_D2_Facturacion.png` 


### 2.3 Diagrama 3 · Cobros, seguimiento y control

Lo que pasa después de emitir: recordatorios y cobros (que siempre **notifican** al responsable), el trabajo diario del Lector y las herramientas de control del Administrador y el Superadministrador.

Imagen: `CasosdeUso_Yousef_v3_D3_CobrosControl.png` 

**Relaciones principales**

- **`<<include>>` (siempre ocurre):**
  - Crear factura puntual (CU-09), emitir recurrentes (CU-13) y rectificar (CU-14) **incluyen** Emitir y enviar factura (CU-10). Así la numeración, el PDF y el envío se describen una sola vez.
  - Registrar un cobro (CU-17) y el flujo de impago (CU-16) **incluyen** Notificar al responsable (CU-18).
- **`<<extend>>` (solo bajo una condición):**
  - Retener para aprobación (CU-11) **extiende** la emisión, solo si la factura tiene condiciones inusuales (FR9).
  - Recuperar contraseña (CU-02) extiende el inicio de sesión.
  - Exportar informes (CU-24) extiende el dashboard.

---

## 3. Lista de casos de uso

| ID | Caso de uso | Actor principal | Requisitos que cubre |
|---|---|---|---|
| CU-01 | Iniciar sesión | Lector (todos) | FR1 · NFR1 |
| CU-02 | Recuperar contraseña | Lector (todos) | FR1 |
| CU-03 | Gestionar usuarios y roles | Superadministrador | FR2 · UR8 · NFR1 |
| CU-04 | Asignar y delegar clientes | Administrador | FR3 · UR4 |
| CU-05 | Gestionar clientes y contactos | Lector | FR4 · FR17 (línea de tiempo) · UR1 · NFR8 |
| CU-06 | Importar clientes desde CSV | Administrador | FR4 · NFR9 |
| CU-07 | Gestionar suscripción | Lector | FR5 · UR1 |
| CU-08 | Revisar tramo de tarifa anual | Administrador | FR5 · UR5 |
| CU-09 | Crear factura puntual | Lector | FR6 · FR8 · UR1 · BR2 |
| CU-10 | Emitir y enviar factura | (incluido) | FR8 · FR10 · NFR5 · NFR7 |
| CU-11 | Retener factura para aprobación | (extensión) | FR9 |
| CU-12 | Aprobar o rechazar factura retenida | Administrador | FR9 · UR5 · FR17 |
| CU-13 | Emitir facturas recurrentes | Temporizador | FR7 · BR1 · NFR3 · NFR5 · NFR6 |
| CU-14 | Rectificar o anular factura | Administrador | FR8 · UR7 · BR7 · NFR7 |
| CU-15 | Consultar facturas y descargar PDF | Lector | FR10 · FR15 · UR1 |
| CU-16 | Enviar recordatorios y flujo de impago | Temporizador | FR11 · BR1 |
| CU-17 | Registrar cobro | Pasarela de pago | FR12 · NFR5 |
| CU-18 | Notificar al responsable | (incluido) | FR13 · UR2 |
| CU-19 | Resolver conciliación manual | Administrador | FR12 · FR17 |
| CU-20 | Gestionar incidencias y disputas | Lector | FR14 · FR11 (pausa) · UR1 |
| CU-21 | Consultar trabajo diario | Lector | FR13 · UR2 |
| CU-22 | Buscar y ejecutar acciones masivas | Lector | FR15 · UR3 · NFR2 |
| CU-23 | Consultar dashboard | Administrador | FR16 · UR6 |
| CU-24 | Exportar informes | (extensión) | FR16 · UR6 · NFR8 · NFR9 |
| CU-25 | Consultar auditoría | Superadministrador | FR17 · UR9 · NFR7 |
| CU-26 | Configurar sistema y plantillas | Superadministrador | FR18 · FR13 (canales) · UR8 |
| CU-27 | Consultar salud del sistema | Superadministrador | FR18 · UR9 · NFR6 |
| CU-28 | Consultar estado de cliente (API) | Sistema externo | FR19 · BR5 |
| CU-29 | Solicitar baja externa (API) | Sistema externo | FR19 · FR5 |
| CU-30 | Gestionar presupuestos *(opcional)* | Lector | FR20 |



## 5. Matriz de trazabilidad (requisito → casos de uso)

| Requisito | Casos de uso |
|---|---|
| FR1 Autenticación y sesiones | CU-01, CU-02 |
| FR2 Usuarios, roles y permisos | CU-03 |
| FR3 Asignación y delegaciones | CU-04 |
| FR4 Clientes, contactos e importación | CU-05, CU-06 |
| FR5 Suscripción, tarifas y ciclo de vida | CU-07, CU-08, CU-29 |
| FR6 Facturas puntuales | CU-09 |
| FR7 Facturación recurrente | CU-13 |
| FR8 Numeración y ciclo de vida de la factura | CU-09, CU-10, CU-14 |
| FR9 Aprobaciones | CU-11, CU-12 |
| FR10 PDF y envío | CU-10, CU-15 |
| FR11 Recordatorios y recobro | CU-16, CU-20 |
| FR12 Cobros y conciliación | CU-17, CU-19 |
| FR13 Notificaciones y trabajo diario | CU-18, CU-21, CU-26 |
| FR14 Incidencias, comentarios y adjuntos | CU-20 |
| FR15 Búsqueda, filtros y acciones masivas | CU-15, CU-22 |
| FR16 Dashboard, informes y exportaciones | CU-23, CU-24 |
| FR17 Auditoría e historial | CU-05, CU-12, CU-19, CU-25 |
| FR18 Configuración, plantillas y salud | CU-26, CU-27 |
| FR19 API e integraciones | CU-28, CU-29 |
| FR20 Presupuestos (opcional) | CU-30 |

Los 20 requisitos funcionales quedan cubiertos por al menos un caso de uso. Los **no funcionales** no generan casos de uso propios (no son acciones de un actor), sino que condicionan cómo se realizan; por eso aparecen como requisitos asociados en la tabla del apartado 3.

---

## 6. Notas para la fusión con el subgrupo


- **Edición de facturas:** el cliente pidió poder modificarlas dentro del trimestre; aquí se sigue el criterio de FR8 (rectificativa). Hay que decidirlo con el subgrupo de requisitos y con Nestor.
- **Pendiente para v3:** especificación completa (precondiciones, postcondiciones, flujos alternativos numerados) de los casos principales: CU-09, CU-13, CU-16 y CU-17.
