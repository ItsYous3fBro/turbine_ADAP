# Diagrama Físico Entidad-Relación - Gonzalo (v2)

## Enfoque: Crecimiento Operativo y Seguridad
La segunda versión evoluciona el esquema inicial para adaptarlo a un entorno de trabajo más real. Se introduce la necesidad de gestionar múltiples contactos por cliente, asegurar el acceso de los usuarios y guardar físicamente los documentos generados.

## Cambios y Mejoras respecto a la v1

### 1. Nuevas Tablas Incorporadas
* **`sesion`:** Para gestionar los accesos de los usuarios mediante tokens, permitiendo cerrar sesiones remotamente o aplicar caducidad.
* **`contacto`:** Separado del cliente principal, permitiendo que una misma empresa tenga varias personas de contacto (con distintos emails o teléfonos).
* **`documento_factura`:** Una tabla dedicada exclusivamente a guardar dónde está alojado el PDF de la factura (rutas en el servidor y en la nube) y su enlace para compartir.

### 2. Nuevos Tipos de Datos (Físico)
* **Precisión Temporal (`TIMESTAMP`):** A diferencia del `DATE` de la v1, ahora necesitamos guardar la hora, minuto y segundo exactos en los que caduca una sesión de usuario por motivos de seguridad.
* **Cadenas Largas (`VARCHAR(255)`):** Implementadas para almacenar tokens de sesión seguros, rutas completas de archivos y huellas criptográficas de los PDFs, que requieren mayor longitud que un texto normal.