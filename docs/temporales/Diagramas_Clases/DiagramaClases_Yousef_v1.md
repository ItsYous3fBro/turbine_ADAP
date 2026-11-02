# Diagrama de clases · v1

**Proyecto:** Gestor de facturas Turbine · **Grupo:** Ramón Llull · 3.º B
**Entrega 2:** Diagrama UML de clases (v1)
**Archivos:** `DiagramaClases_Yousef_v1.puml` · `DiagramaClases_Yousef_v1.png`

## 1. Alcance de la v1

La v1 recoge el **modelo de dominio**: las clases que representan los datos y las reglas del negocio, organizadas en tres paquetes. Las clases técnicas (controladores, servicios, repositorios) quedan para versiones posteriores.

En total hay 16 clases, 1 interfaz y 9 enumerados.

Contiene todos los elementos que pide la guía:

| Elemento | Dónde aparece |
|---|---|
| Clases, atributos y operaciones | Las 16 clases del dominio |
| Asociaciones con multiplicidad | Por ejemplo, Usuario 1 — 0..* Cliente (responsable) |
| Generalización | FacturaRectificativa → Factura |
| Realización de interfaz | Factura y ExcepcionComercial implementan Aprobable |
| Composición | Cliente ◆ Contacto, Factura ◆ LineaFactura |
| Agregación | Envio ◇ Contacto (destinatarios) |

## 2. Clases y casos de uso de los que salen

| Paquete | Clase | Qué representa | Casos de uso |
|---|---|---|---|
| Usuarios y control | Usuario | Empleado con cuenta y rol | CU-01 a CU-06 |
| | Notificacion | Aviso del trabajo diario (regla R2) | CU-31 |
| | RegistroAuditoria | Quién cambió qué y cuándo (regla R3) | CU-41 |
| Clientes y suscripciones | Cliente | Empresa cliente con sus datos fiscales | CU-07, CU-08, CU-13, CU-14 |
| | Contacto | Persona de contacto con su función | CU-07, CU-20 |
| | Suscripcion | Relación comercial recurrente | CU-09, CU-10, CU-16, CU-36 |
| | NivelTarifa | Tramo de tarifa según la facturación anual | CU-09, CU-12, CU-42 |
| | ExcepcionComercial | Descuento o ajuste pendiente de aprobación | CU-11 |
| Facturación, cobros y seguimiento | Factura | Factura puntual o recurrente con su estado | CU-15 a CU-17, CU-25 |
| | FacturaRectificativa | Corrige o anula una factura emitida | CU-22 |
| | LineaFactura | Concepto, cantidad, precio, descuento e impuesto | CU-15 |
| | Concepto | Catálogo de conceptos facturables | CU-15, CU-42 |
| | Aprobable (interfaz) | Cualquier operación que puede retenerse | CU-18 |
| | SolicitudAprobacion | Retención y resolución con el principio de los cuatro ojos | CU-18, CU-19 |
| | Envio | Envío de factura o recordatorio y su resultado | CU-20, CU-21, CU-26, CU-27 |
| | Cobro | Pago recibido, vinculado o pendiente de revisión | CU-28 a CU-30 |
| | Incidencia | Problema de facturación; puede pausar los recordatorios | CU-32, CU-33 |

## 3. Decisiones de diseño

1. **El rol es un atributo (enumerado `Rol`), no una jerarquía de subclases de Usuario.** El rol de una persona puede cambiar en tiempo de ejecución (CU-05), y un objeto no puede cambiar de clase. La herencia entre roles del diagrama de casos de uso se resuelve con permisos.
2. **Los estados son enumerados.** Así se reflejan exactamente los estados de la regla R4 y los de clientes, suscripciones y cuentas, sin inventar clases.
3. **FacturaRectificativa hereda de Factura y además la referencia.** Es una factura con número propio que se emite igual (CU-17), y por eso hereda. La asociación «rectifica» la vincula con la original.
4. **La interfaz Aprobable permite una sola SolicitudAprobacion.** Facturas y excepciones comerciales comparten la misma bandeja de aprobación sin repetir la clase. Esto refleja la relación `<<extend>>`/`<<include>>` con CU-18.
5. **Las composiciones marcan la dependencia de vida.** Las líneas no existen sin su factura, ni los contactos sin su cliente. Envio solo agrega contactos porque estos existen por sí mismos.
6. **Los importes son `Decimal`, nunca coma flotante (NFR9).** Las fechas con hora usan `DateTime`, guardado en UTC (NFR10).
7. **Las notificaciones y la auditoría son clases, no casos de uso.** Es coherente con las reglas R2 y R3 del documento de casos de uso.
