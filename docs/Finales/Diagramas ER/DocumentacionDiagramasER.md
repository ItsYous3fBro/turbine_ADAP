# Diagrama físico Entidad-Relación · Documentación

**Proyecto:** Gestor de facturas Turbine · **Grupo:** Ramón Llull · 3.º B
**Entrega 2:** Diagrama físico Entidad-Relación
**Base:** Diagrama de clases final y Casos de uso
**Notación:** pata de gallo · **SGBD de referencia:** PostgreSQL (decisión D-01)

## 1. Cómo se presenta el modelo

La base de datos tiene un único **modelo físico** de **38 tablas y 62 relaciones**. Igual que el diagrama de clases, se presenta en **varios diagramas**, cada uno una vista del mismo modelo.

| Diagrama | Archivo | Qué muestra |
|---|---|---|
| 0 · Vista general | `DiagramaER_0_VistaGeneral` | Las 38 tablas, solo con su nombre, agrupadas en las 4 vistas, con todas las relaciones y sus cardinalidades. Es el mapa de la base de datos. |
| 1 · Usuarios, acceso, control e informes | `DiagramaER_1_UsuariosControl` | 13 tablas: usuarios, sesiones, tokens, credenciales API, notificaciones, vistas guardadas, auditoría, procesos, copias de seguridad, configuración, objetivos e informes. |
| 2 · Clientes y suscripciones | `DiagramaER_2_Clientes` | 9 tablas: cliente, contactos, delegaciones, niveles de tarifa, suscripciones, excepciones comerciales, propuestas de cambio de nivel, solicitudes de baja e importaciones. |
| 3 · Facturación y aprobaciones | `DiagramaER_3_Facturacion` | 8 tablas: series, periodos, factura, rectificativa, líneas, conceptos, documento PDF y solicitudes de aprobación. |
| 4 · Envíos, cobros e incidencias | `DiagramaER_4_EnviosCobrosIncidencias` | 8 tablas: envíos, plantillas, cobros, conciliaciones, pausas de recordatorios, incidencias, comentarios y adjuntos. |
| Anexo · Modelo completo | `DiagramaER_Anexo_Completo` | Todo en un solo diagrama |

Cada diagrama está disponible como imagen (carpeta *Diagramas ER PNG*) y como código PlantUML (carpeta *Diagramas ER PUML*).

**Reglas de las vistas**

1. Cada tabla se define con todas sus columnas en **un solo** diagrama de detalle.
2. Si una tabla de otra vista participa en una relación, aparece en **gris y solo con su nombre**.
3. Las relaciones entre vistas se dibujan igual en las dos, con la misma cardinalidad.
4. Las vistas siguen los paquetes del diagrama de clases. Las vistas 1 y 4 juntan paquetes pequeños para no tener diagramas de dos o tres tablas.

### 1.1 Leyenda de los diagramas

| Símbolo | Significado |
|---|---|
| `*` | Columna NOT NULL |
| `<<PK>>` · `<<FK>>` · `<<UK>>` | Clave primaria · clave foránea · valor único |
| `\|\|` · `\|o` · `\|{` · `o{` | Exactamente uno · cero o uno · uno o más · cero o más |
| `.. restricciones ..` | Sección con los CHECK, únicos compuestos e índices (IDX) de cada tabla |
| `CASCADE` junto a una FK | Borrado en cascada; el resto de claves foráneas son `ON DELETE RESTRICT` |
| Tabla en gris | Tabla definida en otro diagrama (solo nombre) |

Convenciones de tipos: claves `BIGINT` autonuméricas, importes `NUMERIC(12,2)`, fechas con hora `TIMESTAMPTZ` (UTC), enumerados como `VARCHAR` + `CHECK`.

## 2. Elementos que pide la guía

| Elemento (guía de la Entrega 2) | Dónde verlo |
|---|---|
| Entidades o tablas | 38 tablas, en los diagramas 1 a 4 |
| Atributos | Todas las columnas, con su tipo PostgreSQL (`NUMERIC(12,2)`, `DATE`, `TIMESTAMPTZ`, `VARCHAR(n)`, `JSONB`…) |
| Claves primarias | `<<PK>>`: `id` en cada tabla; `factura_rectificativa.factura_id` es clave primaria y foránea a la vez (herencia) |
| Claves foráneas | `<<FK>>`, con `CASCADE` en las composiciones (apartado 5) |
| Relaciones | 62, con su nombre |
| Cardinalidades | Pata de gallo en los dos extremos |
| Otros elementos del diseño físico | NOT NULL (`*`), únicos simples (`<<UK>>`) y compuestos, CHECK, índices (IDX), índices únicos parciales (`WHERE`), disparador (TRIGGER), tablas intermedias N:M (`delegacion` y `conciliacion`) y política de borrado |
| Coherencia con el resto del diseño | Mismos nombres que el diagrama de clases y trazabilidad tablas ↔ clases ↔ casos de uso (apartado 8) |

## 3. Descripción de las vistas

### 3.1 Vista 1 · Usuarios, acceso, control e informes (13 tablas)

Agrupa los paquetes *Usuarios y acceso*, *Seguimiento y control* y *Configuración e informes*. `usuario` es la tabla más conectada del modelo: casi todas las acciones registran quién las hizo.

| Tabla | Qué guarda | Claves y restricciones destacadas |
|---|---|---|
| `usuario` | Cuentas de acceso: nombre, correo, contraseña (hash) o cuenta Microsoft, rol, estado, intentos fallidos y bloqueo | `email` y `cuenta_microsoft` únicos · `CHECK intentos_fallidos >= 0` |
| `sesion` | Sesiones abiertas, con su última actividad y caducidad | `usuario_id` CASCADE · `IDX (usuario_id) WHERE activa` |
| `token_temporal` | Tokens de invitación y de recuperación de contraseña | `token_hash` único · `CHECK tipo IN ('INVITACION','RECUPERACION')` |
| `credencial_api` | Sistemas externos autorizados a usar la API (clave en hash) | `sistema` único |
| `notificacion` | Avisos para un usuario, con el cliente, factura, incidencia o solicitud de baja a la que se refieren | `CHECK num_nonnulls(cliente_id, factura_id, incidencia_id, solicitud_baja_id) <= 1` · `IDX (usuario_id, atendida)` |
| `vista_guardada` | Filtros guardados por cada usuario (JSONB) | `UNIQUE (usuario_id, nombre)` · `UNIQUE (usuario_id) WHERE predeterminada` |
| `registro_auditoria` | Histórico de acciones, con el valor anterior y el nuevo (JSONB) | Solo INSERT, sin UPDATE ni DELETE · `IDX (entidad, entidad_id, fecha)` |
| `ejecucion_proceso` | Ejecuciones de los procesos automáticos: inicio, fin, si fue completa y resultado | — |
| `copia_seguridad` | Copias de seguridad: fecha, estado, ubicación y tamaño | — |
| `configuracion` | Parámetros del sistema: plazo de pago, cadencia de recordatorios, umbrales, minutos de bloqueo, retención de exportaciones, impuestos permitidos y correo de la gestoría | `vigente_desde` único · `CHECK` de plazos, minutos y días `> 0` |
| `objetivo_financiero` | Objetivos financieros con importe, moneda y fecha de inicio | `definido_por_id` → `usuario` |
| `exportacion` | Archivos de informes generados, con filtros, formato y caducidad | `programacion_id` opcional |
| `programacion_informe` | Envíos periódicos de informes: periodicidad, destinatarios y próxima ejecución | — |

### 3.2 Vista 2 · Clientes y suscripciones (9 tablas)

| Tabla | Qué guarda | Claves y restricciones destacadas |
|---|---|---|
| `cliente` | Ficha del cliente: nombre comercial, razón social, identificador fiscal, domicilio, país, moneda, representante legal, facturación anual, alta y estado | `identificador_fiscal` único · `responsable_id` → `usuario` · `IDX (nombre_comercial)` |
| `contacto` | Personas de contacto: correo, teléfono, función, idioma y si está activo | `cliente_id` CASCADE |
| `delegacion` | Tabla intermedia usuario ↔ cliente: un usuario trabaja temporalmente con los clientes de otro | `CHECK fecha_fin >= fecha_inicio` · `UNIQUE (cliente_id) WHERE estado = 'VIGENTE'` |
| `nivel_tarifa` | Tramos de tarifa: facturación mínima y máxima, cuota mensual y vigencia | `CHECK facturacion_maxima > facturacion_minima` · `CHECK cuota_mensual >= 0` |
| `suscripcion` | Suscripción de un cliente a un nivel, con fechas, estado y datos de pausa | `UNIQUE (cliente_id) WHERE estado <> 'CANCELADA'` |
| `excepcion_comercial` | Ajustes comerciales de una suscripción: tipo, valor, vigencia y motivo | `CHECK vigente_hasta >= vigente_desde` |
| `propuesta_cambio_nivel` | Propuestas de pasar una suscripción a otro nivel según la facturación anual | `nivel_propuesto_id` → `nivel_tarifa` |
| `solicitud_baja` | Solicitudes de baja de un cliente, hechas por un empleado o por la API | `CHECK origen <> 'API' OR credencial_api_id IS NOT NULL` · `UNIQUE (cliente_id) WHERE estado = 'PENDIENTE'` |
| `importacion` | Importaciones de datos: filas válidas, filas erróneas e informe de errores | `usuario_id` → `usuario` |

### 3.3 Vista 3 · Facturación y aprobaciones (8 tablas)

| Tabla | Qué guarda | Claves y restricciones destacadas |
|---|---|---|
| `serie_facturacion` | Series de numeración: código, formato y último número usado | `codigo` único · `CHECK ultimo_numero >= 0` |
| `periodo_facturacion` | Periodos de facturación y su estado (ABIERTO, INCOMPLETO, CERRADO) | `fecha_inicio` único · `CHECK (estado = 'CERRADO') = (cerrado_en IS NOT NULL)` |
| `factura` | Factura: cliente, suscripción, serie, periodo, número, fechas, tipo, estado, moneda, importes, marcas de migrada e incobrable y datos fiscales emitidos (JSONB) | `UNIQUE (serie_id, numero)` · `UNIQUE (suscripcion_id, periodo_id) WHERE tipo = 'RECURRENTE'` · `CHECK total = subtotal + impuestos` · `IDX (estado, fecha_vencimiento)` |
| `factura_rectificativa` | Datos propios de las rectificativas: factura original, motivo y si anula totalmente | `factura_id` PK y FK (CASCADE) · `CHECK factura_id <> factura_original_id` |
| `linea_factura` | Líneas de una factura: descripción, cantidad, precio, descuento e impuesto | `factura_id` CASCADE · `CHECK cantidad > 0` · descuento e impuesto entre 0 y 100 |
| `concepto` | Catálogo de conceptos con precio base e impuesto | `nombre` único |
| `documento_factura` | PDF de la factura: ruta local y en la nube, huella y si está firmado | `factura_id` único y CASCADE |
| `solicitud_aprobacion` | Bandeja de aprobaciones de facturas, excepciones comerciales y propuestas de cambio de nivel | `CHECK num_nonnulls(factura_id, excepcion_comercial_id, propuesta_cambio_nivel_id) = 1` · `CHECK resuelta_por_id <> creada_por_id` |

La regla de la tabla `factura` según su estado: salvo en BORRADOR, el número, la serie, el periodo, la fecha de emisión y los datos fiscales emitidos son obligatorios. Además, una factura solo puede ser incobrable si está VENCIDA.

### 3.4 Vista 4 · Envíos, cobros e incidencias (8 tablas)

| Tabla | Qué guarda | Claves y restricciones destacadas |
|---|---|---|
| `envio` | Envíos de facturas, recordatorios e informes: canal, destinatarios, contenido enviado, identificador del proveedor, reintentos y resultado | `CHECK tipo = 'INFORME' OR factura_id IS NOT NULL` · `CHECK reintentos >= 0` |
| `plantilla_mensaje` | Plantillas de mensaje por tipo e idioma: asunto y cuerpo | `UNIQUE (tipo, idioma) WHERE activa` |
| `cobro` | Cobros recibidos: importe, moneda, fecha, origen, referencia y estado | `UNIQUE (origen, referencia)` · `CHECK importe > 0` |
| `conciliacion` | Tabla intermedia cobro ↔ factura: importe aplicado de cada cobro a cada factura, con validación y reversión | `CHECK importe_aplicado > 0` · una reversión exige fecha y usuario · disparador de importes |
| `pausa_recordatorios` | Pausas de los recordatorios de una factura, con motivo, revisión y reanudación | `factura_id` CASCADE · `UNIQUE (factura_id) WHERE activa` · `CHECK activa = (fecha_reanudacion IS NULL)` |
| `incidencia` | Incidencias de un cliente, con factura afectada, responsable, prioridad, estado y fechas | `CHECK fecha_cierre >= fecha_apertura` |
| `comentario` | Comentarios de una incidencia, públicos o privados | `incidencia_id` CASCADE · `CHECK privado OR autorizado_id IS NULL` |
| `adjunto` | Archivos adjuntos de una incidencia: nombre, formato, tamaño y ruta | `incidencia_id` CASCADE · `CHECK tamano > 0` |

La conciliación permite que un cobro se reparta entre varias facturas y que una factura se pague con varios cobros. El disparador comprueba que la suma de los importes aplicados vigentes no supere ni el importe del cobro ni el total de la factura, y la factura pasa a PAGADA cuando la suma iguala su total. Al revertir una conciliación se conserva la fila con estado REVERTIDA.

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

## 7. Reglas de negocio que impone la base de datos

| Regla | Restricción |
|---|---|
| Un cliente no se repite | `cliente.identificador_fiscal` único |
| El número de factura no se repite dentro de una serie | `UNIQUE (serie_id, numero)` |
| Una suscripción no se factura dos veces en el mismo periodo | `UNIQUE (suscripcion_id, periodo_id) WHERE tipo = 'RECURRENTE'` |
| Una factura emitida tiene todos sus datos | `CHECK estado = 'BORRADOR' OR (numero, serie_id, periodo_id, fecha_emision y datos_fiscales_emitidos NOT NULL)` |
| El total cuadra | `CHECK total = subtotal + impuestos` |
| Solo una factura vencida puede ser incobrable | `CHECK NOT incobrable OR estado = 'VENCIDA'` |
| Un cliente solo tiene una suscripción no cancelada | `UNIQUE (cliente_id) WHERE estado <> 'CANCELADA'` |
| Una aprobación no la resuelve quien la creó (principio de los cuatro ojos) | `CHECK resuelta_por_id <> creada_por_id` |
| Una solicitud de aprobación se refiere a una sola cosa | `CHECK num_nonnulls(factura_id, excepcion_comercial_id, propuesta_cambio_nivel_id) = 1` |
| Un cobro no se registra dos veces | `UNIQUE (origen, referencia)` |
| Los cobros aplicados no superan el cobro ni la factura | Disparador en `conciliacion` |
| Una factura tiene como mucho una pausa de recordatorios activa | `UNIQUE (factura_id) WHERE activa` |
| Un cliente solo tiene una solicitud de baja pendiente | `UNIQUE (cliente_id) WHERE estado = 'PENDIENTE'` |
| Un cliente solo tiene una delegación vigente | `UNIQUE (cliente_id) WHERE estado = 'VIGENTE'` |
| Una baja por API identifica a la credencial | `CHECK origen <> 'API' OR credencial_api_id IS NOT NULL` |
| La auditoría no se modifica | Solo INSERT en `registro_auditoria` |

## 8. Trazabilidad tablas ↔ clases ↔ casos de uso

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

## 9. Decisiones de diseño

1. **Claves primarias artificiales (`id BIGINT`).** El número de factura o el identificador fiscal pueden faltar (borradores, clientes incompletos), así que no sirven como clave primaria. Se declaran como únicos.
2. **Bandeja de aprobación con arco exclusivo.** `solicitud_aprobacion` tiene tres claves foráneas que admiten nulos y un CHECK que obliga a rellenar exactamente una. Así cada referencia tiene clave foránea real; una referencia genérica (tipo + id) no la tendría.
3. **Dos estrategias de herencia.** Tabla por subclase para FacturaRectificativa, porque añade columnas, y tabla única para TokenTemporal, porque sus subclases no añaden nada.
4. **Cobros repartidos con conciliación.** Un cobro puede pagar varias facturas y una factura puede pagarse con varios cobros, por eso `cobro` no apunta a `factura` y la relación se resuelve en la tabla intermedia `conciliacion`, que además conserva el histórico de reversiones.
5. **Envíos y recordatorios en una sola tabla `envio`,** distinguidos por `tipo` (FACTURA, RECORDATORIO, INFORME).
6. **Datos fiscales fijos en la factura emitida.** `datos_fiscales_emitidos` (JSONB) es obligatorio al emitir, así la factura conserva los datos aunque cambie el cliente.
7. **Las reglas de negocio pasan a restricciones de la base de datos** (apartado 7).
8. **Índices para las consultas frecuentes:** las facturas vencidas que recorre cada día el proceso de recordatorios (`factura (estado, fecha_vencimiento)`), la búsqueda de clientes por nombre comercial, la bandeja de notificaciones y el historial de un registro (`registro_auditoria (entidad, entidad_id, fecha)`).
9. **JSONB** para filtros y valores de auditoría, que tienen una estructura variable y no se consultan columna a columna.
10. **Destinatarios y contenido como texto en `envio`,** junto con `contacto_id` opcional, para conservar el historial aunque se anonimice un contacto (FR6, NFR6).
11. **Configuración versionada:** cada cambio inserta una fila con su `vigente_desde`, y la vigente es la más reciente.
