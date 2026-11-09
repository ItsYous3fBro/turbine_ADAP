# Documentación del diagrama de componentes

**Proyecto:** Gestor de facturas Turbine
**Grupo:** Ramón Llull · 3º B

---

## 1. Objetivo

Este documento describe la arquitectura del *Gestor de facturas Turbine* mediante un diagrama de componentes UML. Explica qué componentes tiene el sistema, qué interfaces ofrece y requiere cada uno, y por qué se han tomado las decisiones de diseño principales.

## 2. Visión general

La arquitectura es **por capas basada en componentes**, con el objetivo de conseguir **alta cohesión** (cada componente hace una sola cosa) y **bajo acoplamiento** (los componentes se comunican solo a través de interfaces).

El sistema se divide en seis paquetes:

| Paquete | Responsabilidad |
|---|---|
| Capa de acceso | Puertas de entrada al sistema (web y API). No contiene lógica de negocio. |
| Gestión de negocio | Lógica de dominio: autenticación, clientes, facturación y cobros. |
| Servicios | Módulos auxiliares: firma digital y notificaciones. |
| Infraestructura | Persistencia, almacenamiento de documentos y disparador temporal. |
| Sistemas externos | Adaptadores hacia terceros: identidad, pago y correo. |
| Observabilidad | Registro de la actividad del sistema (auditoría). |

## 3. Notación utilizada

El diagrama usa la notación de **interfaz ofrecida y requerida** de UML:

```
A -(0- B : IX
```

- **B ofrece** la interfaz `IX` (el círculo).
- **A requiere** la interfaz `IX` (el semicírculo).
- En la práctica: A depende de B, pero a través de un contrato (`IX`) y no de B directamente.

Una misma interfaz puede ser requerida por varios componentes. Por ejemplo, `IFacturacion` la consumen cuatro componentes distintos.

## 4. Componentes

### 4.1. Capa de acceso

| Componente | Descripción | Interfaces que requiere |
|---|---|---|
| Interfaz web | Aplicación que usan los empleados. Solo canaliza peticiones. | `IAuth`, `IClientes`, `IFacturacion`, `ICobros` |
| API | Entrada para sistemas externos. Valida credenciales y rol técnico. | `IAuth`, `IClientes` |

### 4.2. Gestión de negocio

| Componente | Descripción | Ofrece | Requiere |
|---|---|---|---|
| Autenticación y permisos | Gestiona el acceso, los roles y los permisos. | `IAuth` | `IIdentidad` |
| Clientes | Gestiona el registro y los datos fiscales de los clientes. | `IClientes` | `IFacturacion`, `IAlerta` |
| Facturación | Emite facturas puntuales y recurrentes, y las deja firmadas y guardadas. | `IFacturacion` | `IFirma`, `IAlerta`, `IDocs` |
| Cobros | Seguimiento de pagos, recordatorios y vencimientos. | `ICobros` | `IFacturacion`, `IAlerta`, `IGestionPagos` |

### 4.3. Servicios

| Componente | Descripción | Ofrece | Requiere |
|---|---|---|---|
| Firma digital | Aplica la firma con el certificado electrónico de Turbine. No tiene estado propio. | `IFirma` | — |
| Notificaciones | Único punto de salida de avisos: internos para empleados y correos para clientes. | `IAlerta` | `IEnvio`, `IDatabase` |

### 4.4. Infraestructura

| Componente | Descripción | Ofrece | Requiere |
|---|---|---|---|
| Base de datos | Persistencia de los datos estructurados. | `IDatabase` | — |
| Almacenamiento de documentos | Guarda los PDF definitivos con almacenamiento redundante. | `IDocs` | — |
| Temporizador | Dispara los procesos programados por fecha. | — | `IFacturacion`, `ICobros` |

### 4.5. Sistemas externos

| Componente | Descripción | Ofrece |
|---|---|---|
| Proveedor de identidad | Acceso con cuenta Microsoft corporativa. | `IIdentidad` |
| Pasarela de pago | Cobro con tarjeta mediante Stripe. | `IGestionPagos` |
| Proveedor de correo | Envío de correos a clientes. | `IEnvio` |

### 4.6. Observabilidad

| Componente | Descripción | Ofrece | Requiere |
|---|---|---|---|
| Auditoría | Registro inalterable de las acciones económicas, de clientes y de permisos. | `ILog` | `IDatabase` |

## 5. Catálogo de interfaces

| Interfaz | Ofrecida por | Requerida por | Propósito |
|---|---|---|---|
| `IAuth` | Autenticación y permisos | Interfaz web, API | Validar credenciales, roles y permisos |
| `IClientes` | Clientes | Interfaz web, API | Gestionar clientes |
| `IFacturacion` | Facturación | Interfaz web, Clientes, Cobros, Temporizador | Emitir y consultar facturas |
| `ICobros` | Cobros | Interfaz web, Temporizador | Gestionar y revisar cobros |
| `IFirma` | Firma digital | Facturación | Firmar un documento |
| `IAlerta` | Notificaciones | Clientes, Facturación, Cobros | Solicitar un aviso |
| `IDocs` | Almacenamiento de documentos | Facturación | Guardar el PDF definitivo |
| `IDatabase` | Base de datos | Gestión de negocio (paquete), Notificaciones, Auditoría | Persistir datos |
| `ILog` | Auditoría | Gestión de negocio (paquete) | Registrar acciones |
| `IIdentidad` | Proveedor de identidad | Autenticación y permisos | Iniciar sesión con Microsoft |
| `IGestionPagos` | Pasarela de pago | Cobros | Gestionar pagos con tarjeta |
| `IEnvio` | Proveedor de correo | Notificaciones | Enviar correos |

## 6. Decisiones de diseño

### 6.1. La capa de acceso no contiene lógica de negocio

La interfaz web y la API solo canalizan peticiones y consumen las interfaces del negocio. La API se conecta solo a `IAuth` e `IClientes`, porque toda llamada de un sistema externo debe validar sus credenciales y su rol técnico.

### 6.2. Conexión a nivel de paquete para base de datos y auditoría

Todo el paquete de Gestión de negocio se conecta a `IDatabase` e `ILog`. Se entiende que todos sus componentes usan esas interfaces. Esto reduce el número de líneas del diagrama y mejora la legibilidad sin perder rigor.

Los componentes que están fuera del paquete y también guardan datos (Notificaciones y Auditoría) se conectan individualmente a la base de datos.

### 6.3. La auditoría es un aspecto transversal

La trazabilidad es un requisito transversal: toda acción económica, de clientes o de permisos queda registrada y el registro no se puede alterar. Por eso Auditoría tiene su propio paquete y actúa como observador independiente de la lógica de dominio.

### 6.4. Firma digital como servicio sin estado

Facturación entrega el documento a Firma digital, que lo firma y lo devuelve. Firma digital **no accede a la infraestructura**: solo firma. Facturación es la única responsable de guardar el PDF definitivo mediante `IDocs`. Así se respeta el principio de responsabilidad única.

### 6.5. Notificaciones como único punto de salida de avisos

Clientes, Facturación y Cobros solicitan avisos mediante `IAlerta`. Notificaciones los dirige a dos destinos:

- **Avisos internos** a los empleados, con estado de leído o atendido y prioridad. Cada empleado recibe solo los de sus clientes.
- **Correos a los clientes** (envío de facturas y recordatorios de cobro), que se derivan al proveedor de correo mediante `IEnvio`.

El negocio queda aislado de la lógica concreta del envío de correos.

### 6.6. El temporizador apunta hacia el negocio

Aunque reside en Infraestructura, sus dependencias van hacia Facturación y Cobros. Es quien inicia los procesos programados:

- **Facturación recurrente** el día 1 de cada mes (mediante `IFacturacion`).
- **Revisión diaria** de recordatorios y vencimientos (mediante `ICobros`).

### 6.7. Sistemas externos tras interfaces

Los componentes internos consumen interfaces de los adaptadores externos sin conocer la implementación real. Si un proveedor falla o se cambia en el futuro, solo se ve afectado un componente, y un fallo externo no corrompe una factura.

Los movimientos bancarios por transferencia no usan un adaptador: se importan como archivo y los gestiona Cobros.

### 6.8. Dependencias entre módulos de negocio

La única dependencia directa entre módulos de negocio es hacia Facturación: Clientes y Cobros le piden generar o consultar facturas mediante `IFacturacion`. El resto de la comunicación entre módulos pasa por servicios (Notificaciones) o infraestructura.

## 7. Alcance y limitaciones

El diagrama representa los componentes principales a un nivel abstracto. Las siguientes funcionalidades **no se muestran como componentes independientes**:

- Aprobaciones
- Incidencias
- Informes y dashboard
- Configuración
