# **Diagramas de Secuencia · Gestor de facturas TurbineH**

**Grupo:** Ramón Llull · **Curso:** 3º B

## **1. Resumen**

Este documento resume los diagramas de secuencia de 12 casos de uso del gestor de facturas y clientes de TurbineH: qué es cada caso, a qué bloque pertenece, qué necesita y qué deja hecho, y qué muestra su diagrama.

| Caso de uso | Bloque | Actor principal | Nivel |
|---|---|---|---|
| CU-01 Iniciar sesión | D1 · Acceso, usuarios y permisos | Usuario estándar (Microsoft, secundario) | Componentes y clases |
| CU-07 Gestionar cliente | D2 · Clientes y suscripciones | Usuario estándar | Componentes y clases |
| CU-15 Crear factura puntual | D3 · Facturación y aprobaciones | Usuario estándar | Componentes y clases |
| CU-16 Generar facturas recurrentes | D3 · Facturación y aprobaciones | Temporizador | Componentes y clases |
| CU-17 Emitir factura | D3 · Facturación y aprobaciones | Servicio de Facturación (interno) | Sistema |
| CU-20 Enviar factura | D3 · Facturación y aprobaciones | Servicio de Emisión (interno) | Sistema |
| CU-22 Rectificar o anular factura | D3 · Facturación y aprobaciones | Administrador | Componentes y clases |
| CU-27 Enviar recordatorios de cobro | D4 · Cobros | Temporizador | Sistema |
| CU-32 Gestionar incidencia | D5 · Seguimiento y trabajo diario | Usuario estándar | Sistema |
| CU-34 Buscar clientes y facturas | D5 · Seguimiento y trabajo diario | Usuario estándar | Sistema |
| CU-37 Consultar dashboard financiero | D6 · Información y configuración | Administrador | Sistema |
| CU-39 Exportar informes y datos | D6 · Información y configuración | Usuario estándar | Componentes y clases |

## **2. Criterios comunes**

* **Dos niveles de detalle.** *Componentes y clases:* participantes del diagrama de componentes (con interfaces `[IAuth]`, `[IFacturacion]`, `[IDatabase]`, `[ILog]`...) y objetos del diagrama de clases con sus métodos. *Sistema:* una sola caja, el Gestor de facturas (Turbine), con los actores y la Base de datos.
* **Numeración** según los pasos del caso de uso; los flujos alternativos con su código (1a, 4a, 7b...) en bloques `alt`, `opt` o `loop`.
* **`«include»` y `«extend»`** como fragmentos `ref`, llamadas entre participantes o bloques `opt`.
* **Auditoría:** las acciones importantes quedan registradas, también las fallidas.
* **Sin comprobación de permisos dibujada,** salvo en CU-37, donde decide el camino del diagrama.
* **Servicio de Facturación, Servicio de Emisión y Módulo de Envío** son partes internas del componente Facturación y aprobaciones, usadas en CU-17 y CU-20 para no partir de otros casos de uso.

---

## **3. D1 · Acceso, usuarios y permisos**

### **CU-01 · Iniciar sesión**
**Definición:** acceso al sistema con correo corporativo y contraseña, o con cuenta Microsoft.
**Precondición:** el usuario tiene una cuenta con dominio autorizado y no está bloqueada.
**Postcondición:** se crea una sesión que expira a los 30 minutos de inactividad; el intento queda registrado. Tras 5 fallos consecutivos la cuenta pasa a BLOQUEADA.
**Relaciones:** flujo alternativo 1a (acceso con Microsoft).
**Diagrama:** 1a recoge el acceso con contraseña (comprueba dominio, usuario, credenciales y estado, y crea la sesión); 1b recoge el acceso con Microsoft.
**Decisión clave:** los errores son genéricos para no revelar si falló el dominio o la contraseña.

## **4. D2 · Clientes y suscripciones**

### **CU-07 · Gestionar cliente**
**Definición:** alta, consulta y actualización de la ficha, reactivación y baja de un cliente.
**Precondición:** usuario autenticado. En el alta, el identificador fiscal no está duplicado.
**Postcondición:** cliente creado (ACTIVO o INCOMPLETO), actualizado, reactivado o dado de baja (borrado lógico, sin perder datos ni facturas). Todo queda en Auditoría.
**Relaciones:** consulta la deuda a Facturación en la baja.
**Diagrama:** cuatro diagramas: 7a alta, 7b consulta y actualización de la ficha, 7c reactivación y 7d baja.
**Decisión clave:** con deuda pendiente la baja queda como BAJA_PENDIENTE y se mantienen los recordatorios; la baja pide confirmación al empleado.

## **5. D3 · Facturación y aprobaciones**

Cadena de una factura: CU-15, CU-16 y CU-22 la crean y hacen `«include»` de CU-17; CU-18 (retener para aprobación) extiende a CU-15 y CU-22; CU-17 hace `«include»` de CU-20.

### **CU-15 · Crear factura puntual**
**Definición:** creación de una factura individual para un cliente, con líneas, totales y vencimiento.
**Precondición:** usuario autenticado y cliente existente.
**Postcondición:** factura emitida mediante CU-17, o guardada como borrador, o retenida pendiente de aprobación (CU-18).
**Relaciones:** `«include»` CU-17; extendido por CU-18.
**Diagrama:** 15a es el escenario principal (borrador, líneas, totales, vista previa, confirmación y emisión); 15b recoge retomar una factura aprobada, importe atípico, guardar como borrador, retención y fallo de CU-17.
**Decisión clave:** el borrador se guarda en cada cambio, para no perderlo si el proceso se interrumpe.

### **CU-16 · Generar facturas recurrentes**
**Definición:** proceso automático que factura las suscripciones activas de cada periodo.
**Precondición:** el Temporizador lanza la ejecución; hay suscripciones activas sin factura en el periodo.
**Postcondición:** una factura emitida por suscripción y ejecución registrada como completa o incompleta (si alguna falla).
**Relaciones:** `«include»` CU-17.
**Diagrama:** 16a registra el inicio, recorre las suscripciones, emite una factura por cada una y registra el fin; 16b recoge la recuperación diaria de un periodo incompleto y el fallo de una suscripción.
**Decisión clave:** si CU-17 falla en una suscripción, el borrador se borra y el proceso sigue con las demás.

### **CU-17 · Emitir factura**
**Definición:** convierte una factura en borrador o aprobada en una factura emitida con número correlativo.
**Precondición:** factura en estado *Borrador* o *Aprobada*.
**Postcondición:** factura *Emitida*, numerada, y envío lanzado. Si los datos no son válidos, no se numera ni se guarda.
**Relaciones:** incluido por CU-15, CU-16 y CU-22; `«include»` CU-20.
**Diagrama:** el Servicio de Facturación pide emitir; el sistema valida, asigna número, guarda como *Emitida* y llama al Módulo de Envío. Un `alt` recoge los datos inválidos.
**Decisión clave:** el número se asigna después de validar, para no consumir números de la serie con facturas rechazadas.

### **CU-20 · Enviar factura**
**Definición:** envío de la factura emitida por correo electrónico al cliente.
**Precondición:** factura *Emitida* (o reenvío con CU-21) y cliente con correo de contacto.
**Postcondición:** envío registrado como *Enviada*. Si hay rebote, se registra con CU-26 y queda *Envío fallido*.
**Relaciones:** incluido por CU-17 y CU-21; actores Proveedor de correo y Cliente.
**Diagrama:** genera el PDF, compone el mensaje, lo entrega al Proveedor de correo, que lo envía al Cliente, y registra el envío. Un `opt` final recoge el rebote.
**Decisión clave:** el PDF se genera dentro del envío, así los reenvíos llevan siempre la versión actual.

### **CU-22 · Rectificar o anular factura**
**Definición:** corrección o anulación de una factura ya emitida mediante una factura rectificativa.
**Precondición:** usuario Administrador y factura emitida (enviada, vencida, pagada...). Un borrador o una factura pendiente de aprobación no se pueden rectificar.
**Postcondición:** rectificativa emitida mediante CU-17 y original pasada a RECTIFICADA o ANULADA (nunca se edita).
**Relaciones:** `«include»` CU-17; extendido por CU-18.
**Diagrama:** 22a es el escenario principal (comprobar la factura, crear la rectificativa, correcciones, vista previa con la diferencia y emisión); 22b recoge anular, retomar una rectificativa aprobada, retención y factura no emitida.
**Decisión clave:** rectificar y anular usan el mismo mensaje, con un parámetro que indica si la anulación es total.

## **6. D4 · Cobros**

### **CU-27 · Enviar recordatorios de cobro**
**Definición:** envío automático y periódico de recordatorios a clientes con facturas pendientes o vencidas.
**Precondición:** el Temporizador lanza la ejecución; hay facturas con cobro pendiente que cumplen la regla de recordatorio y no están pausadas (CU-33).
**Postcondición:** un recordatorio enviado por factura y cada uno registrado.
**Relaciones:** actores Proveedor de correo y Cliente.
**Diagrama:** el sistema busca las facturas pendientes y, en un `loop`, compone y envía un recordatorio por cada una vía Proveedor de correo, que lo entrega al Cliente.
**Decisión clave:** el registro se hace dentro de cada vuelta del `loop`, así un fallo no pierde los recordatorios ya enviados.

## **7. D5 · Seguimiento y trabajo diario**

### **CU-32 · Gestionar incidencia**
**Definición:** consulta, registro y actualización de incidencias asociadas a facturas.
**Precondición:** usuario autenticado con rol estándar o superior.
**Postcondición:** incidencia registrada (ABIERTA) o actualizada y anotada en el historial. Si afecta al cobro, los recordatorios de la factura quedan pausados.
**Relaciones:** extendido por CU-33 (pausar recordatorios).
**Diagrama:** consulta del listado y dos alternativas, registrar una incidencia nueva (valida datos y factura) o actualizar una existente (valida el cambio de estado). Un `opt` invoca CU-33 si afecta al cobro.
**Decisión clave:** la pausa de recordatorios es una extensión, no parte del flujo, porque solo se ejecuta en algunos casos.

### **CU-34 · Buscar clientes y facturas**
**Definición:** búsqueda unificada de clientes y facturas por texto y filtros.
**Precondición:** usuario autenticado con rol estándar o superior.
**Postcondición:** listado de resultados; si se lanzó una acción masiva, queda aplicada sobre la selección, con resumen por elemento.
**Relaciones:** extendido por CU-35 (acción masiva) si se seleccionan varios resultados.
**Diagrama:** valida criterios, consulta clientes y facturas, combina los resultados y los devuelve. Tras la selección, un `opt` ejecuta CU-35 si hay varios resultados.
**Decisión clave:** los criterios se validan antes de consultar la base de datos.

## **8. D6 · Información y configuración**

### **CU-37 · Consultar dashboard financiero**
**Definición:** consulta de indicadores del periodo (facturado, cobrado, pendiente, vencido) y cumplimiento de objetivos.
**Precondición:** usuario autenticado como Administrador o Superadministrador.
**Postcondición:** dashboard mostrado; no se modifica ningún dato. Un usuario estándar recibe acceso denegado.
**Relaciones:** usa los objetivos de CU-38.
**Diagrama:** verifica el rol; si está autorizado, obtiene el resumen y los objetivos, calcula los indicadores y devuelve el dashboard (con `opt` para cambiar de periodo); si no, responde acceso denegado.
**Decisión clave:** sin datos los indicadores salen a cero, y sin objetivos se muestran los datos sin comparativa.

### **CU-39 · Exportar informes y datos**
**Definición:** exportación de informes en CSV (UTF-8) o PDF, con opción de programar su envío.
**Precondición:** usuario autenticado.
**Postcondición:** archivo generado y guardado como exportación, limitado a los permisos del usuario; o programación guardada. Sin datos, no se genera archivo.
**Relaciones:** la programación la ejecuta CU-40.
**Diagrama:** 39a es el escenario principal (informe, filtros, formato, generación y descarga opcional); 39b recoge programar el envío y regenerar una exportación caducada.
**Decisión clave:** los datos se consultan con los permisos del usuario, así dos usuarios con los mismos filtros pueden obtener informes distintos.
