# Diagrama Entidad-Relación · v3

**Proyecto:** Gestor de facturas Turbine · **Grupo:** Ramón Llull · 3.º B
**Entrega 2:** Diagrama físico Entidad-Relación (versión temporal 3)
**Base:** v2 + Diagrama de clases final (fusión) + Casos de uso v7
**Archivos:**

| Diagrama | Archivos | Para qué sirve |
|---|---|---|
| Físico, notación pata de gallo | `DiagramaER_Yousef_v3.puml` · `.png` | El que pide la guía: tablas, columnas con su tipo, claves, restricciones e índices |
| Conceptual, notación Chen | `DiagramaER_Chen_Yousef_v3.dot` · `.png` | Las mismas entidades y relaciones, sin detalles de implementación |

## 1. Qué cambia respecto a la v2

1. **Nueva versión en notación Chen** con las 38 entidades y las 46 relaciones del modelo. Se ha generado con Graphviz, porque PlantUML coloca los diagramas Chen grandes en una sola fila muy larga.
2. **Política de borrado en el diagrama físico.** Las claves foráneas de las composiciones y de la herencia llevan `CASCADE`; el resto, `RESTRICT` (apartado 3).
3. **Valores de los CHECK de enumerados** (apartado 4) y **trazabilidad tablas ↔ clases ↔ casos de uso** (apartado 5).

El modelo de datos no cambia: siguen siendo 37 tablas y 48 relaciones.

## 2. Notación Chen

### 2.1 Símbolos

| Símbolo | Significado | Ejemplo |
|---|---|---|
| Rectángulo | Entidad | `factura` |
| Rectángulo doble | Entidad débil: no existe sin su dueña (composición del diagrama de clases) | `linea_factura`, `contacto`, `sesion` |
| Rombo | Relación | `factura_a` (cliente 1 — N factura) |
| Rombo doble | Relación identificativa de una entidad débil | `contiene` (factura — linea_factura) |
| Óvalo con texto subrayado | Atributo clave (parcial en las entidades débiles) | `id` |
| Óvalo doble | Atributo multivaluado | `configuracion.monedas_permitidas` |
| Óvalo discontinuo | Atributo derivado | `factura.total` (subtotal + impuestos) |
| Óvalo unido a un rombo | Atributo de la relación | `fecha_inicio` y `fecha_fin` de `delega` |
| Línea doble / simple | Participación total (obligatoria) / parcial | Toda factura pertenece a un cliente (doble); no toda factura tiene suscripción (simple) |
| 1, N, M | Cardinalidad | `delega`: usuario M — N cliente |
| Círculo «d» | Especialización disjunta (ISA). Con línea doble es total. | token_temporal → invitacion, enlace_recuperacion (total) · factura → factura_rectificativa (parcial) |

En el diagrama Chen solo se muestran las claves, los atributos de las relaciones y los atributos especiales (multivaluados y derivados). Con todos los atributos tendría unos 300 óvalos y sería ilegible. El resto de atributos está en el diagrama físico.

### 2.2 Del modelo Chen al modelo físico

| En Chen | En el diagrama físico | Ejemplo |
|---|---|---|
| Entidad | Tabla | `cliente` |
| Relación 1:N | Clave foránea en el lado N; NOT NULL si la participación es total | `factura.cliente_id NOT NULL` |
| Relación 1:1 | Clave foránea única | `documento_factura.factura_id` (UK) |
| Relación M:N | Tabla intermedia con las dos claves foráneas y los atributos de la relación | `delega` → tabla `delegacion` |
| Entidad débil | Clave foránea NOT NULL hacia la dueña con `ON DELETE CASCADE` | `linea_factura.factura_id` |
| ISA total y disjunta, sin atributos propios | Una sola tabla con una columna discriminadora | `token_temporal.tipo` |
| ISA parcial con atributos propios | Una tabla por subclase, cuya clave primaria es foránea a la del padre | `factura_rectificativa.factura_id` |
| Atributo multivaluado | Lo correcto es una tabla aparte. Hoy está guardado como lista en una columna (ver el apartado 6) | `monedas_permitidas` |
| Atributo derivado | Se guarda calculado y se protege con un CHECK | `CHECK total = subtotal + impuestos` |

## 3. Política de borrado

Nada se borra físicamente: clientes, facturas y usuarios se dan de baja cambiando su estado, para conservar el histórico (FR6, NFR6). Por eso todas las claves foráneas son `ON DELETE RESTRICT`, salvo las de las composiciones y la herencia, que son `CASCADE` porque la hija no tiene sentido sin su dueña.

| Clave foránea con CASCADE | Composición o herencia del diagrama de clases |
|---|---|
| sesion.usuario_id · token_temporal.usuario_id · vista_guardada.usuario_id | Usuario ◆ Sesion, TokenTemporal y VistaGuardada |
| contacto.cliente_id | Cliente ◆ Contacto |
| linea_factura.factura_id · documento_factura.factura_id · pausa_recordatorios.factura_id | Factura ◆ LineaFactura, DocumentoFactura y PausaRecordatorios |
| comentario.incidencia_id · adjunto.incidencia_id | Incidencia ◆ Comentario y Adjunto |
| factura_rectificativa.factura_id | FacturaRectificativa → Factura (herencia) |

## 4. Valores de los CHECK de enumerados

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
| incidencia.prioridad | BAJA, MEDIA, ALTA |
| incidencia.estado | ABIERTA, CERRADA |

## 5. Trazabilidad tablas ↔ clases ↔ casos de uso

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

Las 37 tablas salen de una clase y se usan en al menos un caso de uso. La interfaz Aprobable no genera tabla: su papel lo cumple el arco exclusivo de `solicitud_aprobacion`.

