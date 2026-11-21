# Diagrama Físico Entidad-Relación - Gonzalo (v1)

## Enfoque: Producto Mínimo Viable (MVP)
Esta primera versión establece la estructura de base de datos estrictamente necesaria para que el sistema funcione en su "camino feliz". El objetivo es poder registrar clientes, asignarles una suscripción y emitirles facturas de forma consistente.

## Tablas Principales
* **Gestión de Acceso:** `usuario`
* **Negocio Core:** `cliente`, `nivel_tarifa`, `suscripcion`
* **Facturación básica:** `factura`, `linea_factura`, `cobro`

## Decisiones de Diseño Físico (Base)
Al ser la versión inicial, se establecen los tipos de datos estándar que definirán el rendimiento de la base de datos:
* **Claves e Identificadores (`INT`):** Se utilizan números enteros para relacionar las tablas de forma rápida y ocupar poco espacio en disco.
* **Precisión Financiera (`DECIMAL(12,2)`):** Todo lo relacionado con dinero (cuotas, totales, precios unitarios) usa este formato para garantizar exactitud al céntimo y evitar errores de redondeo.
* **Textos Acotados (`VARCHAR`):** Se limita el espacio de textos como el NIF (50 caracteres) o la razón social (200 caracteres) para optimizar la memoria.
* **Fechas Legales (`DATE`):** Para la emisión de facturas o fechas de cobro, solo nos interesa el día en el calendario, no la hora exacta.