# Diagrama físico Entidad-Relación · Versión final (fusión)

**Proyecto:** Gestor de facturas Turbine · **Grupo:** Ramón Llull · 3.º B
**Entrega 2:** Diagrama físico Entidad-Relación
**Base:** Diagrama de clases final (fusión) y Casos de uso v7. Evolución: v1 (núcleo, 16 tablas), v2 (modelo completo, 37 tablas, con restricciones físicas), v3 (política de borrado, valores de los CHECK y trazabilidad), versión final (presentación en vistas) y **fusión** con las mejores aportaciones de los compañeros.
**Notación:** pata de gallo · **SGBD de referencia:** PostgreSQL (decisión D-01)

## 1. Cómo se presenta el modelo

La base de datos tiene un único **modelo físico** (38 tablas y 62 relaciones). Igual que el diagrama de clases, se presenta en **varios diagramas**, cada uno una vista del mismo modelo.

| Diagrama | Archivo | Qué muestra |
|---|---|---|
| 0 · Vista general | `DiagramaER_0_VistaGeneral` | Las 38 tablas, solo con su nombre, agrupadas en las 4 vistas, con todas las relaciones y sus cardinalidades. Es el mapa de la base de datos. |
| 1 · Usuarios, acceso, control e informes | `DiagramaER_1_UsuariosControl` | 13 tablas: usuarios, sesiones, tokens, credenciales API, notificaciones, vistas guardadas, auditoría, procesos, copias de seguridad, configuración, objetivos e informes. |
| 2 · Clientes y suscripciones | `DiagramaER_2_Clientes` | 9 tablas: cliente, contactos, delegaciones, niveles de tarifa, suscripciones, excepciones, propuestas de cambio de nivel, solicitudes de baja e importaciones. |
| 3 · Facturación y aprobaciones | `DiagramaER_3_Facturacion` | 8 tablas: factura, rectificativa, líneas, conceptos, series, periodos, documento PDF y solicitudes de aprobación. |
| 4 · Envíos, cobros e incidencias | `DiagramaER_4_EnviosCobrosIncidencias` | 8 tablas: envíos, plantillas, cobros, conciliaciones, pausas de recordatorios, incidencias, comentarios y adjuntos. |
| Anexo · Modelo completo | `DiagramaER_Anexo_Completo` | Todo en un solo diagrama, por si el profesorado lo pide. |

**Reglas de las vistas**

1. Cada tabla se define con todas sus columnas en **un solo** diagrama de detalle.
2. Si una tabla de otra vista participa en una relación, aparece en **gris y solo con su nombre**.
3. Las relaciones entre vistas se dibujan igual en las dos, con la misma cardinalidad.
4. Las vistas siguen los paquetes del diagrama de clases. Las vistas 1 y 4 juntan paquetes pequeños para no tener diagramas de dos o tres tablas.

## 2. Qué aporta la fusión

Se parte de nuestro diagrama final y se incorporan las aportaciones del diagrama de Miguel que el equipo ha elegido.

### 2.1 Incorporado del diagrama de Miguel

| Aportación | Qué cambia en el modelo | Por qué | Casos de uso |
|---|---|---|---|
| **Conciliación de cobros** | Tabla nueva `conciliacion` (cobro_id, factura_id, importe_aplicado, estado, reversión). `cobro` deja de tener `factura_id`. | Un cobro puede repartirse entre varias facturas y una factura pagarse con varios cobros. Resuelve la observación del profesor sobre el pago parcial (CU-29 y CU-30). La reversión conserva el histórico. | CU-28, CU-29, CU-30 |
| **Contexto de las notificaciones** | `notificacion` gana cliente_id, factura_id, incidencia_id y solicitud_baja_id, que admiten nulos | Cada aviso indica a qué se refiere | CU-14, CU-31 |
| **Quién hizo cada cosa** | factura.creada_por_id · periodo_facturacion.cerrado_por_id y cerrado_en · delegacion.autorizada_por_id · solicitud_aprobacion.asignada_a_id y motivo_decision · pausa_recordatorios.creada_por_id | Trazabilidad de las acciones de cada usuario | CU-06, CU-15, CU-18, CU-19, CU-24, CU-33 |
| **Fechas de seguimiento** | incidencia.fecha_apertura y fecha_cierre · pausa_recordatorios.fecha_reanudacion · delegacion.estado, con una sola delegación vigente por cliente | Se puede medir cuánto dura cada incidencia o pausa | CU-06, CU-32, CU-33, CU-36 |
| **Datos de la factura emitida** | factura.datos_fiscales_emitidos (JSONB), obligatorio al emitir | Los datos fiscales quedan fijos aunque cambie el cliente | CU-17, CU-22 |
| **Contacto en el envío** | envio.contacto_id, que admite nulos, además del texto de destinatarios | Se sabe a qué contacto se envió y se conserva el texto si se anonimiza | CU-20, CU-21 |

### 2.2 Mantenido de nuestro diagrama (Miguel no lo tenía)

- **Modelo completo:** 38 tablas frente a 23. Miguel no tenía configuración, plantillas, sesiones, tokens, credenciales API, exportaciones, programaciones, procesos, copias de seguridad, conceptos, excepciones comerciales ni solicitudes de baja.
- **Diseño físico completo:** tipos con longitud, CHECK, índices, índices únicos parciales y política de borrado. En el diagrama de Miguel las reglas eran notas comentadas que no se ven.
- **Herencia** de FacturaRectificativa (tabla por subclase) y TokenTemporal (tabla única), frente a la referencia de factura a sí misma.
- **Arco exclusivo** en `solicitud_aprobacion` con su CHECK, que incluye las excepciones comerciales.
- **Envíos y recordatorios en una sola tabla `envio`**, distinguidos por `tipo`, en lugar de dos tablas.
- **Presentación en vistas** y trazabilidad tablas ↔ clases ↔ casos de uso.

### 2.3 Descartado del diagrama de Miguel

- **Tabla `Tarea`:** las tareas de revisión se modelan como notificaciones con contexto, sin añadir otra entidad.
- **`ActividadIncidencia` única** para comentarios, notas y adjuntos: se mantienen `comentario` y `adjunto` separados, con sus propias columnas y restricciones.
- **Referencia de factura a sí misma** para las rectificativas: se mantiene la herencia.
- **`id_cliente` en auditoría:** solo serviría para los cambios de clientes. Lo general es guardar el tipo de entidad y el id del registro afectado (`entidad` + `entidad_id`, apartado 2.4).

### 2.4 Correcciones tras la revisión

Tras la corrección del subagente se aplicaron estas mejoras, en los dos diagramas:

| Tabla | Cambio | Casos de uso |
|---|---|---|
| conciliacion | `revertida_por_id`. La reversión exige fecha y usuario. La regla de importes (Σ aplicado vigente ≤ importe del cobro y ≤ total de la factura) se declara como disparador, porque afecta a varias filas. | CU-29, CU-30 |
| factura | `UNIQUE (suscripcion_id, periodo_id) WHERE tipo = 'RECURRENTE'`: una suscripción no se factura dos veces en el mismo periodo | CU-16 |
| registro_auditoria | `entidad_id`, con el índice `(entidad, entidad_id, fecha)`: se puede reconstruir el historial de un registro concreto | CU-41 |
| pausa_recordatorios | `CHECK activa = (fecha_reanudacion IS NULL)` | CU-33 |
| notificacion | `CHECK num_nonnulls(...) <= 1`: cada notificación se refiere a una cosa como máximo | CU-31 |
| comentario | `autorizado_id`, con `CHECK privado OR autorizado_id IS NULL`: usuario autorizado a leer una nota privada | CU-32 |
| periodo_facturacion | `cerrado` (BOOLEAN) pasa a `estado` (ABIERTO, INCOMPLETO, CERRADO) | CU-16, CU-24 |

### 2.5 Coherencia con el diagrama de clases

Todos los cambios de la fusión están también en el diagrama de clases (versión fusión actualizada):

- clase nueva **Conciliacion**, que sustituye la asociación directa Factura — Cobro;
- asociaciones de **Notificacion** con Cliente, Factura, Incidencia y SolicitudBaja;
- los atributos y asociaciones nuevos («quién hizo cada cosa», fechas, datos fiscales emitidos, contacto del envío, autorizado del comentario, id de la entidad auditada);
- enumerados nuevos: EstadoConciliacion, EstadoDelegacion y EstadoPeriodo.

## 3. Elementos que pide la guía

| Elemento (guía de la Entrega 2) | Dónde verlo |
|---|---|
| Entidades o tablas | 38 tablas, en los diagramas 1 a 4 |
| Atributos | Todas las columnas, con su tipo PostgreSQL (`NUMERIC(12,2)`, `DATE`, `TIMESTAMPTZ`, `VARCHAR(n)`, `JSONB`…) |
| Claves primarias | `<<PK>>`: `id` en cada tabla; `factura_rectificativa.factura_id` es clave primaria y foránea a la vez (herencia) |
| Claves foráneas | `<<FK>>`, con `CASCADE` en las composiciones (apartado 5) |
| Relaciones | 62, con su nombre |
| Cardinalidades | Pata de gallo en los dos extremos (‖ uno · ∣o cero o uno · ∣{ uno o más · o{ cero o más) |
| Otros elementos del diseño físico | NOT NULL (`*`), únicos simples (`<<UK>>`) y compuestos, CHECK, índices (IDX), índices únicos parciales (`WHERE`), tablas intermedias N:M (`delegacion` y `conciliacion`) y política de borrado |
| Coherencia con el resto del diseño | Mismos nombres que el diagrama de clases y trazabilidad tablas ↔ clases ↔ casos de uso (apartado 7) |

## 4. Del diagrama de clases a tablas

| Elemento UML | En el modelo físico | Ejemplo |
|---|---|---|
| Clase | Tabla en minúsculas y con guiones bajos | `LineaFactura` → `linea_factura` |
| Asociación 1 — 0..* | Clave foránea en el lado «muchos»; NOT NULL si la multiplicidad es 1 | `factura.cliente_id NOT NULL` |
| Multiplicidad 0..1 | Clave foránea que admite nulos | `factura.suscripcion_id` |
| Composición 1 — 0..1 | Clave foránea única | `documento_factura.factura_id` |
| Asociación N:M | Tabla intermedia | `delegacion` (usuario ↔ cliente, con fechas) · `conciliacion` (cobro ↔ factura, con importe aplicado) |
| Composición (◆) | Clave foránea NOT NULL con `ON DELETE CASCADE` | `linea_factura.factura_id` |
| Agregación (◇) | Clave foránea que admite nulos, sin cascada | `factura.periodo_id` |
| Generalización con atributos propios | Tabla por subclase (su clave primaria es foránea a la del padre) | `factura_rectificativa` |
| Generalización sin atributos propios | Tabla única con columna discriminadora | `token_temporal.tipo` |
| Interfaz (Aprobable) | Sin tabla: arco exclusivo con un CHECK `num_nonnulls(...) = 1` | `solicitud_aprobacion` |
| Enumerado | `VARCHAR` + `CHECK` con los valores permitidos | apartado 6 |
| Multiplicidad 0..1 hacia varias clases | Una clave foránea opcional por cada una | `notificacion` → cliente, factura, incidencia, solicitud de baja |
| `Decimal` / `DateTime` | `NUMERIC(12,2)` / `TIMESTAMPTZ` en UTC | NFR9 y NFR10 |

## 5. Política de borrado

Nada se borra físicamente: clientes, facturas y usuarios se dan de baja cambiando su estado, para conservar el histórico (FR6, NFR6). Por eso todas las claves foráneas son `ON DELETE RESTRICT`, salvo las de las composiciones y la herencia, que son `CASCADE` porque la hija no tiene sentido sin su dueña.

| Clave foránea con CASCADE | Composición o herencia del diagrama de clases |
|---|---|
| sesion.usuario_id · token_temporal.usuario_id · vista_guardada.usuario_id | Usuario ◆ Sesion, TokenTemporal y VistaGuardada |
| contacto.cliente_id | Cliente ◆ Contacto |
| linea_factura.factura_id · documento_factura.factura_id · pausa_recordatorios.factura_id | Factura ◆ LineaFactura, DocumentoFactura y PausaRecordatorios |
| comentario.incidencia_id · adjunto.incidencia_id | Incidencia ◆ Comentario y Adjunto |
| factura_rectificativa.factura_id | FacturaRectificativa → Factura (herencia) |

## 6. Valores de los CHECK de enumerados

| Columna | Valores permitidos |
|---|---|
| usuario.rol | ESTANDAR, ADMINISTRADOR, SUPERADMINISTRADOR |
| usuario.estado | INVITADA, ACTIVA, BLOQUEADA, DESACTIVADA |
| token_temporal.tipo | INVITACION, RECUPERACION |
| cliente.estado | ACTIVO, INCOMPLETO, BAJA_PENDIENTE, DADO_DE_BAJA |
| contacto.funcion | COMERCIAL, ADMINISTRACION, IMPAGOS |
| suscripcion.estado | PROGRAMADA, ACTIVA, PAUSADA, CANCELADA |
| solicitud_baja.origen | EMPLEADO, API |
| solicitud_baja.estado · solicitud_aprobacion.estado | PENDIENTE, APROBADA, RECHAZADA |
| factura.tipo | PUNTUAL, RECURRENTE, PRORRATEO |
| factura.estado | BORRADOR, PENDIENTE_APROBACION, APROBADA, EMITIDA, ENVIADA, NO_NOTIFICADA, VENCIDA, PAGADA, RECTIFICADA, ANULADA |
| envio.tipo | FACTURA, RECORDATORIO, INFORME |
| cobro.origen | PASARELA, TRANSFERENCIA, REVOLUT |
| cobro.estado | VINCULADO, EN_REVISION, NO_IDENTIFICADO |
| conciliacion.estado | VIGENTE, REVERTIDA |
| periodo_facturacion.estado | ABIERTO, INCOMPLETO, CERRADO |
| delegacion.estado | VIGENTE, FINALIZADA, REVOCADA |
| incidencia.prioridad | BAJA, MEDIA, ALTA |
| incidencia.estado | ABIERTA, CERRADA |

## 7. Trazabilidad tablas ↔ clases ↔ casos de uso

| Tabla | Clase | Casos de uso |
|---|---|---|
| usuario | Usuario | CU-01, CU-02, CU-03, CU-04, CU-05, CU-06 |
| sesion | Sesion | CU-01, CU-04, CU-05 |
| token_temporal | TokenTemporal (Invitacion, EnlaceRecuperacion) | CU-02, CU-03, CU-04, CU-36 |
| credencial_api | CredencialAPI | CU-13, CU-14 |
| notificacion | Notificacion | CU-14, CU-18, CU-26, CU-30, CU-31 |
| vista_guardada | VistaGuardada | CU-34 |
| cliente | Cliente | CU-06, CU-07, CU-08, CU-09, CU-13, CU-14, CU-34, CU-35 |
| contacto | Contacto | CU-07, CU-20, CU-21 |
| delegacion | Delegacion | CU-06, CU-36 |
| nivel_tarifa | NivelTarifa | CU-09, CU-12, CU-42 |
| suscripcion | Suscripcion | CU-07, CU-09, CU-10, CU-16, CU-36 |
| excepcion_comercial | ExcepcionComercial | CU-11 |
| propuesta_cambio_nivel | PropuestaCambioNivel | CU-12 |
| solicitud_baja | SolicitudBaja | CU-07, CU-14 |
| importacion | Importacion | CU-08 |
| serie_facturacion | SerieFacturacion | CU-17, CU-42 |
| periodo_facturacion | PeriodoFacturacion | CU-17, CU-24 |
| factura | Factura | CU-08, CU-10, CU-13, CU-15, CU-16, CU-17, CU-22, CU-23, CU-25, CU-26, CU-27, CU-29, CU-30, CU-33, CU-34, CU-35, CU-37 |
| factura_rectificativa | FacturaRectificativa | CU-22 |
| linea_factura | LineaFactura | CU-15 |
| concepto | Concepto | CU-15, CU-42 |
| documento_factura | DocumentoFactura | CU-17, CU-25 |
| solicitud_aprobacion | SolicitudAprobacion | CU-11, CU-12, CU-18, CU-19 |
| envio | Envio | CU-20, CU-21, CU-25, CU-26, CU-27, CU-40, CU-41 |
| plantilla_mensaje | PlantillaMensaje | CU-20, CU-27, CU-42 |
| cobro | Cobro | CU-10, CU-25, CU-28, CU-29, CU-30, CU-37 |
| conciliacion | Conciliacion | CU-28, CU-29, CU-30 |
| pausa_recordatorios | PausaRecordatorios | CU-27, CU-33, CU-36 |
| incidencia | Incidencia | CU-32, CU-33 |
| comentario | Comentario | CU-32 |
| adjunto | Adjunto | CU-32 |
| registro_auditoria | RegistroAuditoria | CU-07, CU-41 |
| ejecucion_proceso | EjecucionProceso | CU-12, CU-16, CU-36, CU-41 |
| copia_seguridad | CopiaSeguridad | CU-41 |
| configuracion | Configuracion | CU-42 |
| objetivo_financiero | ObjetivoFinanciero | CU-37, CU-38 |
| exportacion | Exportacion | CU-36, CU-39, CU-40 |
| programacion_informe | ProgramacionInforme | CU-40 |

Las 38 tablas salen de una clase y se usan en al menos un caso de uso. La interfaz Aprobable no genera tabla: su papel lo cumple el arco exclusivo de `solicitud_aprobacion`.

## 8. Decisiones de diseño

1. **Claves primarias artificiales (`id BIGINT`).** El número de factura o el identificador fiscal pueden faltar (borradores, clientes incompletos), así que no sirven como clave primaria. Se declaran como únicos.
2. **Bandeja de aprobación con arco exclusivo.** `solicitud_aprobacion` tiene tres claves foráneas que admiten nulos y un CHECK que obliga a rellenar exactamente una. Así cada referencia tiene clave foránea real; una referencia genérica (tipo + id) no la tendría.
3. **Dos estrategias de herencia.** Tabla por subclase para FacturaRectificativa, porque añade columnas, y tabla única para TokenTemporal, porque sus subclases no añaden nada.
4. **Las reglas de negocio pasan a restricciones de la base de datos:** numeración única por serie, una sola suscripción no cancelada por cliente, principio de los cuatro ojos, idempotencia de los cobros (`UNIQUE (origen, referencia)`), una sola pausa activa por factura, una sola solicitud de baja pendiente por cliente, una sola delegación vigente por cliente y una sola factura recurrente por suscripción y periodo.
5. **Índices para las consultas frecuentes:** las facturas vencidas que recorre cada día el proceso de recordatorios (`factura (estado, fecha_vencimiento)`), la búsqueda de clientes (CU-34) y la bandeja de notificaciones.
6. **JSONB** para filtros y valores de auditoría, que tienen una estructura variable y no se consultan columna a columna.
7. **Destinatarios y contenido como texto en `envio`**, para conservar el historial aunque se anonimice un contacto (FR6, NFR6).
8. **Configuración versionada:** cada cambio inserta una fila con su `vigente_desde`, y la vigente es la más reciente.


