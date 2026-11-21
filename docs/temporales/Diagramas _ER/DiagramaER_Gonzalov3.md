# Diagrama Físico Entidad-Relación - Gonzalo (v3)

## Enfoque: Versión Definitiva (Fusión)
Esta es la versión final del sistema, compuesta por un total de 13 tablas. Refleja fielmente las necesidades descubiertas en la fusión del diagrama de clases, aportando herramientas para la resolución de problemas, el seguimiento del trabajo y la observabilidad (auditoría) de todo el sistema.

## Cambios y Mejoras respecto a la v2

### 1. Nuevas Tablas Incorporadas (Seguimiento y Control)
* **`incidencia`:** Permite registrar cualquier problema asociado a una factura (errores de cobro, quejas del cliente).
* **`pausa_recordatorios`:** Una tabla puente vital que detiene las automatizaciones de cobro cuando una factura está en estado de revisión por una incidencia.
* **`registro_auditoria`:** Un log inmutable que guarda quién hizo qué, cuándo y qué datos modificó, esencial para el cumplimiento normativo.

### 2. Nuevos Tipos de Datos (Físico)
* **Textos Ilimitados (`TEXT`):** Se aplica en la descripción de las incidencias y en los valores de auditoría. Permite a los usuarios o al sistema escribir registros tan largos como necesiten sin que la base de datos corte la información.
* **Lógica Binaria (`BOOLEAN`):** Se introducen campos de "Verdadero/Falso" directamente en la tabla `factura` (como `migrada` o `incobrable`). Esto permite cambiar drásticamente el comportamiento de una factura en el sistema sin necesidad de crear tablas adicionales complejas.

## Visión Global del Sistema Final
Con esta estructura, la base de datos es capaz de soportar todo el ciclo de vida comercial: desde que el usuario se loguea de forma segura, hasta que se cobra la factura o se gestiona un impago, dejando rastro auditable de cada paso.