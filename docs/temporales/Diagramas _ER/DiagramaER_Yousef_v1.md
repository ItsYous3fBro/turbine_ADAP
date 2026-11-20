# Diagrama Entidad-Relación · v1

**Proyecto:** Gestor de facturas Turbine · **Grupo:** Ramón Llull · 3.º B
**Entrega 2:** Diagrama Entidad-Relación (versión temporal 1)
**Base:** Diagrama de clases final (fusión) y Casos de uso v7
**Archivos:** `DiagramaER_Yousef_v1.puml` · `DiagramaER_Yousef_v1.png`

## 1. Alcance de la v1

La v1 recoge el **núcleo del modelo de datos**: las 16 tablas que soportan el flujo principal (cliente → suscripción → factura → envío → cobro), agrupadas en 4 paquetes. Se usa la notación **pata de gallo**, con los atributos, sus tipos de dato, las claves y las cardinalidades.

| Elemento | Dónde aparece |
|---|---|
| Atributos con tipo de dato | Todas las tablas (`NUMERIC(12,2)`, `DATE`, `TIMESTAMPTZ`, `VARCHAR(n)`…) |
| Claves primarias | `id` en todas las tablas; en `factura_rectificativa`, `factura_id` es clave primaria y foránea a la vez |
| Claves foráneas | Por ejemplo, `factura.cliente_id` → `cliente.id` |
| Cardinalidades | En los dos extremos de cada relación (‖, ∣o, ∣{, o{) |
| Tabla intermedia N:M | `delegacion`, entre `usuario` y `cliente` |
| Herencia | `factura_rectificativa` → `factura` (una tabla por subclase) |
| Restricciones | NOT NULL, UNIQUE y CHECK (apartado 4) |

## 2. Cómo se pasa del diagrama de clases a tablas

| Elemento UML | En el modelo de datos | Ejemplo |
|---|---|---|
| Clase | Tabla en minúsculas y con guiones bajos | `LineaFactura` → `linea_factura` |
| Atributo | Columna; NOT NULL si es obligatorio | `Factura.total` → `total NUMERIC(12,2) NOT NULL` |
| Asociación 1 — 0..* | Clave foránea en el lado «muchos» | `Cliente 1 — 0..* Factura` → `factura.cliente_id` |
| Multiplicidad 0..1 | Clave foránea que admite nulos | `Factura 0..1 — 0..* Cobro` → `cobro.factura_id` admite nulos |
| Asociación N:M | Tabla intermedia con dos claves foráneas | `usuario` ↔ `cliente` → `delegacion` |
| Composición (◆) | Clave foránea NOT NULL con `ON DELETE CASCADE` | `linea_factura.factura_id` |
| Agregación (◇) | Clave foránea que admite nulos, sin borrado en cascada | `factura.periodo_id` |
| Generalización | Tabla por subclase: la clave primaria de la hija es foránea a la del padre | `factura_rectificativa.factura_id` |
| Enumerado | `VARCHAR` con `CHECK` de los valores permitidos | `estado IN ('BORRADOR', …, 'ANULADA')` |
| Interfaz (Aprobable) | No genera tabla | Ver la decisión 5.4 |
| `Decimal` / `DateTime` | `NUMERIC(12,2)` / `TIMESTAMPTZ`, guardado en UTC | NFR9 y NFR10 |

## 3. Tablas y clases de las que salen

| Paquete | Tabla | Clase | Claves foráneas |
|---|---|---|---|
| Usuarios y clientes | usuario | Usuario | — |
| | cliente | Cliente | responsable_id → usuario |
| | contacto | Contacto | cliente_id → cliente |
| | delegacion | Delegacion | usuario_id → usuario · cliente_id → cliente |
| Suscripciones | nivel_tarifa | NivelTarifa | — |
| | suscripcion | Suscripcion | cliente_id → cliente · nivel_tarifa_id → nivel_tarifa |
| Facturación | serie_facturacion | SerieFacturacion | — |
| | periodo_facturacion | PeriodoFacturacion | — |
| | factura | Factura | cliente_id, suscripcion_id, serie_id, periodo_id |
| | factura_rectificativa | FacturaRectificativa | factura_id → factura (herencia) · factura_original_id → factura |
| | linea_factura | LineaFactura | factura_id → factura · concepto_id → concepto |
| | concepto | Concepto | — |
| | solicitud_aprobacion | SolicitudAprobacion | factura_id → factura · creada_por_id y resuelta_por_id → usuario |
| Envíos, cobros e incidencias | envio | Envio | factura_id → factura |
| | cobro | Cobro | factura_id → factura |
| | incidencia | Incidencia | cliente_id → cliente · factura_id → factura · responsable_id → usuario |

## 4. Restricciones

| Tabla | Restricción | Regla de la que sale |
|---|---|---|
| usuario | `email` y `cuenta_microsoft` únicos · `intentos_fallidos >= 0` | CU-01 |
| cliente | `identificador_fiscal` único | CU-07 |
| delegacion | `fecha_fin >= fecha_inicio` | CU-06 |
| nivel_tarifa | `facturacion_maxima > facturacion_minima` · `cuota_mensual >= 0` | FR10, CU-42 |
| suscripcion | Como máximo una suscripción no cancelada por cliente: índice único parcial `(cliente_id) WHERE estado <> 'CANCELADA'` | CU-09 |
| serie_facturacion | `codigo` único · `ultimo_numero >= 0` | FR14 |
| factura | `UNIQUE (serie_id, numero)`: numeración sin repetidos dentro de la serie | FR14 |
| factura | `estado = 'BORRADOR'` o (`numero`, `serie_id`, `periodo_id` y `fecha_emision` no nulos): un borrador todavía no tiene número ni periodo | CU-15, CU-17 |
| factura | `total = subtotal + impuestos` · `fecha_vencimiento >= fecha_emision` | CU-15, CU-17 |
| factura | `NOT incobrable OR estado = 'VENCIDA'` | R4, CU-23 |
| factura_rectificativa | `factura_id <> factura_original_id` | CU-22 |
| linea_factura | `cantidad > 0` · `descuento` e `impuesto` entre 0 y 100 | CU-15 |
| solicitud_aprobacion | `resuelta_por_id <> creada_por_id` (principio de los cuatro ojos) | CU-19 |
| envio | `tipo = 'INFORME'` o `factura_id` no nulo · `reintentos >= 0` | CU-20, CU-40 |
| cobro | `UNIQUE (origen, referencia)`: un mismo pago no se registra dos veces | CU-28 |
| cobro | `importe > 0` · `estado <> 'VINCULADO'` o `factura_id` no nulo | CU-29, CU-30 |
| Todas | Enumerados con `CHECK (… IN (…))` y los valores del diagrama de clases | Diagrama 4 de clases |

## 5. Decisiones de diseño

1. **Tabla por subclase para FacturaRectificativa.** La rectificativa es una factura con su propio número (comparte las columnas de `factura`) y añade el motivo y la referencia a la original. Una tabla única con muchas columnas nulas o una tabla independiente que repitiese las columnas serían peores.
2. **Claves primarias artificiales (`id BIGINT`).** El número de factura o el identificador fiscal pueden faltar (borradores, clientes incompletos), así que no sirven como clave primaria. Se declaran como únicos.
3. **El responsable es una clave foránea en `cliente`, y la delegación una tabla aparte.** Un cliente tiene un solo responsable (1:N). En cambio, un usuario puede llevar temporalmente clientes de otros, y un cliente puede delegarse varias veces a lo largo del tiempo, por lo que la relación es N:M con fechas.
4. **La interfaz Aprobable no genera tabla.** En la v1, `solicitud_aprobacion` solo apunta a `factura`. En la v2 se añaden `excepcion_comercial_id` y `propuesta_cambio_nivel_id`, que admiten nulos, con un CHECK que obliga a que haya exactamente una de las tres. Se descarta una referencia genérica (tipo + id) porque no admite clave foránea.
5. **Nada se borra físicamente.** Clientes y facturas se dan de baja o se anulan cambiando su estado, porque hay que conservar el histórico (FR6, NFR6). Los `ON DELETE CASCADE` de las composiciones solo documentan la dependencia de vida.
6. **Destinatarios y contenido como texto en `envio`.** Así se conserva el historial aunque un contacto se anonimice (FR6, NFR6). Es la misma decisión que en el diagrama de clases.

## 6. Hoja de ruta

| Versión | Qué se añade |
|---|---|
| **v2** | Las 21 tablas restantes: sesion, token_temporal (tabla única con discriminador), credencial_api, solicitud_baja, importacion, excepcion_comercial, propuesta_cambio_nivel, documento_factura, pausa_recordatorios, comentario, adjunto, notificacion, vista_guardada, registro_auditoria, ejecucion_proceso, copia_seguridad, configuracion, plantilla_mensaje, objetivo_financiero, exportacion y programacion_informe. Arco exclusivo de `solicitud_aprobacion`. |
| **v3** | Revisión cruzada tablas ↔ clases ↔ casos de uso, diccionario de datos completo (columna a columna) e índices para las búsquedas de CU-34. |
| **Final** | Vistas por paquete, como el diagrama de clases, y script SQL (`CREATE TABLE`) opcional para comprobar que el modelo es válido. |

## 7. Puntos que debe decidir el equipo

- **Notación.** La v1 usa pata de gallo (modelo lógico, con tablas y tipos). Si el profesorado pide la notación de Chen (entidades, rombos y atributos en óvalos), se puede añadir un diagrama conceptual aparte.
- **Herencia de TokenTemporal (v2).** La propuesta es una tabla única con discriminador `tipo` (INVITACION, RECUPERACION), porque las dos subclases no añaden columnas. Es la estrategia contraria a la de las facturas, y así el modelo muestra las dos.
