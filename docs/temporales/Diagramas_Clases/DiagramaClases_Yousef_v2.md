# Diagrama de clases · v2

**Proyecto:** Gestor de facturas Turbine · **Grupo:** Ramón Llull · 3.º B
**Entrega 2:** Diagrama UML de clases (versión temporal 2)
**Base:** v1 + Casos de uso v7 (CU-01 a CU-42) + Documento de requisitos v3
**Archivos:** `DiagramaClases_Yousef_v2.puml` · `DiagramaClases_Yousef_v2.png`

## 1. Qué cambia respecto a la v1

La v2 completa el modelo de dominio: todos los casos de uso tienen ya las clases que necesitan. Pasa de 16 a **24 clases**, con **1 interfaz** y de 9 a **11 enumerados**, repartidos en **4 paquetes**.

### 1.1 Clases nuevas

| Clase | Paquete | Qué representa | Casos de uso |
|---|---|---|---|
| Invitacion | Usuarios y control | Enlace de alta con caducidad | CU-03, CU-04 |
| Delegacion | Clientes y suscripciones | Cesión temporal de un cliente a otro empleado | CU-06, CU-36 |
| PeriodoFacturacion | Facturación, cobros y seguimiento | Periodo contable que se puede cerrar | CU-17, CU-24 |
| Comentario | Facturación, cobros y seguimiento | Comentario, adjunto o nota privada de una incidencia | CU-32 |
| ParametroConfiguracion | Configuración y análisis (nuevo) | Tarifas, plazos, umbrales, series… vigentes desde una fecha | CU-42 |
| PlantillaMensaje | Configuración y análisis | Texto de los mensajes por tipo e idioma | CU-20, CU-27, CU-42 |
| ObjetivoFinanciero | Configuración y análisis | Objetivo de ingresos vigente desde una fecha | CU-37, CU-38 |
| Exportacion | Configuración y análisis | Informe generado o programado, con caducidad | CU-39, CU-40 |

### 1.2 Atributos y enumerados nuevos

| Clase | Cambio | Motivo |
|---|---|---|
| Factura | + `tipo : TipoFactura` (PUNTUAL, RECURRENTE, PRORRATEO) | Distingue las facturas de CU-15, CU-16 y CU-10, que se emiten con el mismo CU-17 |
| Factura | + `migrada`, `incobrable` (Boolean) + `marcarVencida()` | Son marcas, no estados (regla R4): una factura vencida puede ser además incobrable |
| Suscripcion | + `fechaFin` | Permite guardar el histórico (ver la decisión 2.1) |
| Cobro | + `estado : EstadoCobro` (VINCULADO, EN_REVISION, NO_IDENTIFICADO) | Refleja la cola de revisión manual (CU-29, CU-30) |
| Envio | + `destinatarios : String` | Ver la decisión 2.2 |

### 1.3 Relaciones nuevas o revisadas

| Relación | Tipo | Motivo |
|---|---|---|
| Cliente 1 — 0..* Suscripcion | Asociación (antes 0..1) | Decisión 2.1 |
| PeriodoFacturacion 1 ◇ 0..* Factura | Agregación (nueva) | Cada factura pertenece a un periodo, pero no desaparece al cerrarlo |
| Incidencia 1 ◆ 0..* Comentario | Composición (nueva) | Los comentarios no existen sin su incidencia |
| Delegacion → Cliente / Usuario | Asociaciones (nuevas) | Quién lleva qué cliente y entre qué fechas |
| Envio → PlantillaMensaje (0..1) | Asociación (nueva) | Cada envío usa la plantilla vigente |
| Envio ◇ Contacto | **Eliminada** | Sustituida por el atributo `destinatarios` (decisión 2.2) |

## 2. Decisiones tomadas sobre las dudas de la v1

1. **Suscripciones por cliente: 0..* (histórico).** CU-09 exige que la suscripción quede registrada «con su historial de cambios», y una baja seguida de una reactivación crea una suscripción nueva. **Regla:** como máximo una suscripción no cancelada por cliente; se comprobará en el código y en el diagrama E-R.
2. **Destinatarios de los envíos: texto guardado en el envío.** FR6 obliga a conservar el historial de destinatarios, y NFR6 permite anonimizar contactos. Si el envío apuntase al contacto, al anonimizarlo se perdería a quién se envió. Por eso se guarda una copia en el momento del envío.
3. **Un cobro paga una sola factura (Cobro 0..* — 0..1 Factura).** Ningún caso de uso habla de un pago que cubra varias facturas, y el pago parcial (CU-29, 7a) ya está contemplado. Si el cliente lo pidiera, se añadiría una clase intermedia «Imputacion».

## 3. Elementos que pide la guía

| Elemento | Ejemplos en la v2 |
|---|---|
| Clases, atributos y operaciones | 24 clases; las de negocio tienen operaciones (`emitir()`, `cerrar()`, `vincular()`…) |
| Asociaciones y multiplicidades | Todas las relaciones tienen multiplicidad en los dos extremos |
| Generalización | FacturaRectificativa → Factura |
| Interfaz y realización | Factura y ExcepcionComercial implementan Aprobable |
| Composición | Cliente ◆ Contacto · Factura ◆ LineaFactura · Incidencia ◆ Comentario |
| Agregación | PeriodoFacturacion ◇ Factura |

## 4. Hoja de ruta

| Versión | Qué se añade |
|---|---|
| **v3** | Revisión cruzada con los 42 casos de uso: cada paso del sistema tiene su método. Diccionario de clases y trazabilidad casos de uso ↔ clases (H-010). Nombres alineados con el diagrama E-R (la regla de una sola suscripción no cancelada, los destinatarios como texto). |
| **Final** | Un diagrama por paquete, como los 6 de casos de uso, más una vista general con las clases sin atributos. Tipos y visibilidad revisados. Fusión con el PO. Opcional: capa de servicios, que enlaza con los diagramas de secuencia y de arquitectura. |

## 5. Para revisar en equipo

- **ParametroConfiguracion** guarda la configuración como pares clave–valor, que es lo más simple. La alternativa es una clase por tipo (TablaTarifas, SerieNumeracion…), más precisa pero más grande. Decidid antes de hacer el diagrama E-R.
- **Prioridad y estado de Incidencia** siguen siendo `String`. En la v3 pueden pasar a enumerados si el equipo lo prefiere.
