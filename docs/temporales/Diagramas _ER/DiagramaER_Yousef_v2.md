# Diagrama físico Entidad-Relación · v2

**Proyecto:** Gestor de facturas Turbine · **Grupo:** Ramón Llull · 3.º B
**Entrega 2:** Diagrama físico Entidad-Relación (versión temporal 2)
**Base:** v1 + Diagrama de clases final (fusión) + Casos de uso v7
**Archivos:** `DiagramaER_Yousef_v2.puml` · `DiagramaER_Yousef_v2.png`

## 1. Qué cambia respecto a la v1

Todas las clases del diagrama de clases tienen ya su tabla: pasa de 16 a **37 tablas**, repartidas en **6 paquetes**, los mismos que el diagrama de clases. Además, las restricciones del diseño físico se muestran en el propio diagrama, en una sección «restricciones» dentro de cada tabla.

| Lo que pide la guía | Dónde está en la v2 |
|---|---|
| Entidades o tablas | 37 tablas |
| Atributos | Todas las columnas, con su tipo PostgreSQL |
| Claves primarias | `id` en cada tabla; `factura_rectificativa.factura_id` es clave primaria y foránea a la vez |
| Claves foráneas | Marcadas con `<<FK>>` |
| Relaciones y cardinalidades | 48 relaciones, con la cardinalidad en los dos extremos |
| Otros elementos del diseño físico | NOT NULL (`*`), únicos simples y compuestos, CHECK, índices (IDX) e índices únicos parciales (`WHERE`) |

### 1.1 Tablas nuevas

| Paquete | Tabla | Clase | Claves foráneas |
|---|---|---|---|
| Usuarios y acceso | sesion | Sesion | usuario_id |
| | token_temporal | TokenTemporal, Invitacion y EnlaceRecuperacion | usuario_id |
| | credencial_api | CredencialAPI | — |
| | notificacion | Notificacion | usuario_id |
| | vista_guardada | VistaGuardada | usuario_id |
| Clientes y suscripciones | excepcion_comercial | ExcepcionComercial | suscripcion_id |
| | propuesta_cambio_nivel | PropuestaCambioNivel | suscripcion_id · nivel_propuesto_id → nivel_tarifa |
| | solicitud_baja | SolicitudBaja | cliente_id · revisada_por_id → usuario · credencial_api_id |
| | importacion | Importacion | usuario_id |
| Facturación y aprobaciones | documento_factura | DocumentoFactura | factura_id (única) |
| Envíos y cobros | plantilla_mensaje | PlantillaMensaje | — |
| | pausa_recordatorios | PausaRecordatorios | factura_id · incidencia_id |
| Seguimiento y control | comentario | Comentario | incidencia_id · autor_id → usuario |
| | adjunto | Adjunto | incidencia_id |
| | registro_auditoria | RegistroAuditoria | usuario_id (admite nulos: lo puede hacer un proceso) |
| | ejecucion_proceso | EjecucionProceso | — |
| | copia_seguridad | CopiaSeguridad | — |
| Configuración e informes | configuracion | Configuracion | — |
| | objetivo_financiero | ObjetivoFinanciero | definido_por_id → usuario |
| | exportacion | Exportacion | usuario_id · programacion_id |
| | programacion_informe | ProgramacionInforme | usuario_id |

### 1.2 Columnas nuevas en tablas de la v1

| Tabla | Columna | Motivo |
|---|---|---|
| factura | `ejecucion_proceso_id` | Saber qué ejecución generó cada factura recurrente (CU-16, CU-41) |
| envio | `plantilla_id` | Cada envío usa la plantilla vigente (CU-20, CU-27) |
| solicitud_aprobacion | `excepcion_comercial_id`, `propuesta_cambio_nivel_id` | Completa la bandeja única de aprobación (decisión 3.1) |

## 2. Restricciones nuevas

Las restricciones de la v1 se mantienen y ahora también aparecen en el diagrama.

| Tabla | Restricción | Regla de la que sale |
|---|---|---|
| token_temporal | `token_hash` único · `tipo IN ('INVITACION','RECUPERACION')` | CU-02, CU-03, NFR4 |
| sesion | Índice parcial `(usuario_id) WHERE activa` | CU-01, CU-05 (invalidar las sesiones de un usuario) |
| vista_guardada | `UNIQUE (usuario_id, nombre)` · una sola vista predeterminada por usuario | CU-34 |
| solicitud_baja | Si `origen = 'API'`, tiene que tener credencial · una sola solicitud pendiente por cliente | CU-14 (3a: no duplicar la tarea abierta) |
| excepcion_comercial | `vigente_hasta >= vigente_desde` | CU-11 |
| solicitud_aprobacion | `num_nonnulls(factura_id, excepcion_comercial_id, propuesta_cambio_nivel_id) = 1` | CU-18 |
| documento_factura | `factura_id` único: como máximo un PDF por factura | FR17 |
| plantilla_mensaje | Una sola plantilla activa por tipo e idioma | CU-42 |
| pausa_recordatorios | Una sola pausa activa por factura | CU-33 |
| adjunto | `tamano > 0` | CU-32 |
| registro_auditoria | Solo admite inserciones: sin UPDATE ni DELETE (permisos de la base de datos) | R3, CU-41 |
| configuracion | `vigente_desde` único · plazos, minutos y días mayores que 0 | CU-42 |
| factura | Índices `(cliente_id)` y `(estado, fecha_vencimiento)` | CU-25, CU-27 (buscar las facturas vencidas cada día) |
| cliente | Índice `(nombre_comercial)` | CU-34 |

## 3. Decisiones de diseño

1. **Bandeja de aprobación con arco exclusivo.** `solicitud_aprobacion` tiene tres claves foráneas que admiten nulos (factura, excepción y propuesta) y un CHECK que obliga a rellenar exactamente una. Así cada referencia tiene clave foránea real. Una referencia genérica (tipo + id) no la tendría.
2. **Herencia de TokenTemporal en tabla única con discriminador `tipo`.** Invitacion y EnlaceRecuperacion no añaden columnas, así que no hace falta una tabla por subclase. Es la estrategia contraria a la de FacturaRectificativa (tabla por subclase, porque sí añade columnas), y así el modelo muestra las dos.
3. **La interfaz Aprobable no genera tabla.** Su papel lo hace el arco de la decisión 1.
4. **Composición 1 — 0..1 (Factura ◆ DocumentoFactura) como clave foránea única.** Una clave foránea normal permitiría varios PDF por factura.
5. **JSONB para filtros y valores de auditoría.** `vista_guardada.filtros`, `exportacion.filtros` y los valores anterior y nuevo de `registro_auditoria` tienen una estructura variable que no se consulta columna a columna.
6. **Configuración versionada.** Cada cambio inserta una fila nueva con su `vigente_desde`; la configuración vigente es la de fecha más reciente. Se conserva el histórico, igual que en el diagrama de clases.
7. **Las tablas sin relaciones son intencionadas.** `credencial_api` solo se relaciona con las solicitudes de baja. `copia_seguridad` y `configuracion` son registros de sistema que no pertenecen a ningún usuario.

## 4. Hoja de ruta

| Versión | Qué se añade |
|---|---|
| **v3** | Revisión cruzada tablas ↔ clases ↔ casos de uso (mismos nombres, tipos y cardinalidades), diccionario de datos columna a columna, valores de cada CHECK de enumerado y política de borrado (`ON DELETE`) de cada clave foránea. |
| **Final** | Un diagrama por paquete, más una vista general solo con nombres de tablas y relaciones (como el diagrama de clases), y el anexo completo. Opcional: script SQL `CREATE TABLE` para comprobar que el modelo es válido. |


