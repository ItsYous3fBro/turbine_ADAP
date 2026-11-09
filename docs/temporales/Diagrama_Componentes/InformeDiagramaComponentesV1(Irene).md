# Informe: Diagrama de Componentes

**Proyecto:** Gestor de facturas Turbine
**Grupo:** Ramón Llull · 3º B

---

## 1. Visión general y estructura modular

El diseño estructural del *Gestor de facturas Turbine* se ha planteado como una **arquitectura por capas basada en componentes**. Para conseguir una alta cohesión y un bajo acoplamiento, el sistema se ha dividido en seis paquetes:

1. **Capa de Acceso:** puertas de entrada al sistema (*Interfaz Web* y *API*).
2. **Gestión de Negocio:** núcleo de la lógica de dominio (*Autenticación Usuarios y Permisos*, *Gestión de Clientes*, *Facturación* y *Cobros*).
3. **Servicios:** módulos auxiliares de propósito específico (*Firma Digital* y *Notificaciones*).
4. **Infraestructura:** persistencia, almacenamiento físico y disparador temporal (*Base de Datos*, *Almacenamiento docs* y *Temporizador*).
5. **Sistemas Externos:** adaptadores para la integración con terceros (*Proveedor Identidad*, *Pasarela de Pago* y *Proveedor Correo*).
6. **Observabilidad y Trazabilidad:** registro de la actividad del sistema (*Auditoría*).

**Alcance del diagrama.** El diagrama representa los componentes principales a un nivel abstracto. Las funcionalidades de aprobaciones (FR16), incidencias (FR22), informes y dashboard (FR24, FR25) y configuración (FR27) no se muestran como componentes independientes.

---

## 2. Decisiones arquitectónicas sobre relaciones y componentes

### 2.1. Delegación de la Capa de Acceso

La *Interfaz Web* y la *API* no contienen lógica de negocio. Su única responsabilidad es canalizar las peticiones consumiendo las interfaces que ofrece el paquete de Gestión de Negocio: *IAuth*, *IClientes*, *ICobros* e *IFacturacion*.

La *API* se conecta además a *Autenticación Usuarios y Permisos* mediante *IAuth*, porque toda llamada de un sistema externo debe validar sus credenciales y su rol técnico (FR2, FR28).

### 2.2. La auditoría como aspecto transversal

El componente *Auditoría* se ha aislado en su propio paquete de Observabilidad. La trazabilidad es un requisito transversal: toda acción económica, de clientes o de permisos queda registrada y el registro es inalterable (FR26, NFR8).

En lugar de acoplar cada módulo a *Auditoría*, se ha establecido una conexión a nivel de paquete: Gestión de Negocio consume *ILog*. Se entiende que todos los componentes del paquete registran sus acciones a través de esta interfaz, y *Auditoría* actúa como un observador externo independiente de la lógica de dominio.

### 2.3. Conexión a la base de datos a nivel de paquete

Con el mismo criterio que en la auditoría, la persistencia de los datos estructurados se resuelve conectando todo el paquete de Gestión de Negocio a *IDatabase*. Se entiende que todos los componentes del paquete usan esta interfaz. Esto reduce el número de conexiones del diagrama y mejora su legibilidad sin perder rigor.

Los componentes que están fuera del paquete y también guardan datos propios, *Notificaciones* (estados de leído y atendido, FR21) y *Auditoría* (registro de auditoría, FR26), se conectan individualmente a *Base de Datos* mediante *IDatabase*.

### 2.4. *Firma Digital* como servicio sin estado

*Firma Digital* se ha diseñado como un servicio sin estado propio. *Facturación* le entrega el documento, *Firma Digital* aplica la firma con el certificado electrónico de Turbine (FR17) y lo devuelve mediante *IFirma*.

*Firma Digital* no accede a la infraestructura. Se respeta así el principio de responsabilidad única: el componente solo firma, y *Facturación* es la única responsable de guardar el PDF definitivo en *Almacenamiento docs* mediante *IDocs*, que ofrece el almacenamiento redundante exigido por FR17.

### 2.5. Flujo de *Notificaciones*

*Notificaciones* es el único punto de salida de los avisos del sistema. Los módulos de negocio (*Gestión de Clientes*, *Facturación* y *Cobros*) le solicitan avisos mediante *IAlerta*, y *Notificaciones* los dirige a dos destinos:

- **Avisos internos a los empleados**, con su estado de leído o atendido y su prioridad (FR21). Cada empleado recibe solo los de sus clientes.
- **Correos a los clientes** (envío de facturas, FR18, y recordatorios de cobro, FR19), que se derivan a *Proveedor Correo* mediante *IEnvío*.

Para ello, *Notificaciones* extrae las plantillas de mensajes de *Base de Datos* (FR27). Así, el negocio queda aislado de la lógica concreta del envío de correos.

### 2.6. El *Temporizador* como disparador cronológico

El *Temporizador* reside en la infraestructura, pero sus dependencias apuntan hacia el negocio (*IFacturacion* e *ICobros*). Es el disparador que inicia los procesos programados por fecha:

- La **facturación recurrente** del día 1 de cada mes (FR13), mediante *IFacturacion*.
- La **revisión diaria de recordatorios y vencimientos** (FR19), mediante *ICobros*.

### 2.7. Fronteras del sistema (adaptadores externos)

Para interactuar con terceros, el sistema delega en el paquete de Sistemas Externos. Los componentes internos consumen sus interfaces sin conocer la implementación real:

- *Autenticación Usuarios y Permisos* consume *IIdentidad* de *Proveedor Identidad*, para el acceso con cuenta Microsoft corporativa (FR1).
- *Cobros* consume *IGestionPagos* de *Pasarela de Pago*, que representa a Stripe (FR20).
- *Notificaciones* consume *IEnvío* de *Proveedor Correo* (FR18).

Esto cumple FR28: las integraciones quedan aisladas tras interfaces, de modo que un fallo de un proveedor no altera ni corrompe una factura, y se pueden cambiar de proveedor en el futuro.

Los movimientos de Revolut o de transferencias no se integran con un adaptador: se importan como archivo y los gestiona *Cobros* (FR20).
