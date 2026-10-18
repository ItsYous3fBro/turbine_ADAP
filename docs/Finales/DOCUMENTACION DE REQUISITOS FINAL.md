<<<<<<< HEAD
# **Documento de Requisitos**

**Grupo:** Ramón Llull

**Curso:** 3º B

## **1. Descripción General y contextualización**

TurbineH es una startup de origen española cuyo producto principal es **Brain**, una inteligencia artificial destinada a ayudar en la organización de empresas. En este proyecto se solicita el desarrollo de una aplicación independiente (no integrada en Brain, aunque preparada para integrarse en el futuro mediante API) para la gestión integral de facturas y clientes, así como la generación de métricas analíticas sobre los ingresos del negocio.

Roles del sistema:

* **Usuario estándar .** Empleado que gestiona los clientes que tiene asignados: facturas, cobros e incidencias.

* **Administrador.** Aprueba excepciones y facturas, asigna clientes, gestiona usuarios, concilia pagos y consulta el dashboard.

* **Superadministrador.** Configura el sistema (tarifas, series, plazos, recordatorios), supervisa permisos y accede a la auditoría completa.


## **2. Requisitos de Negocio**

*Definición:* Son las metas y objetivos de alto nivel que explican las razones estratégicas para realizar el proyecto.

* **BR1.** Automatizar la facturación y el cobro: automatizar la generación, el envío y el cobro de facturas recurrentes y puntuales, reduciendo el trabajo administrativo manual.

* **BR2.** Reducir los impagos mediante recordatorios y seguimiento automáticos.

* **BR3.** Control legal y económico: garantizar el control y la trazabilidad de facturas y decisiones económicas (tarifas, excepciones, cambios); conservar históricos según la ley, facilitar el trabajo con la gestoría y no alterar nunca facturas históricas.

* **BR4.** Operativa rápida: reducir el tiempo y el número de clics en las operaciones frecuentes.

* **BR5.** Gestión de clientes y suscripciones: facilitar la gestión de la información de los clientes y de sus suscripciones.

* **BR6.** Visibilidad financiera: dar a los administradores visibilidad de la situación financiera mediante métricas y paneles de control.
* **BR7.** Escalabilidad: crecer en número de clientes (miles), facturas, usuarios (decenas o cientos), países y divisas sin reconstruir la arquitectura ni el modelo de datos.

* **BR8.** Acceso desde cualquier lugar y dispositivo (escritorio, tablet y móvil).

* **BR9.** Independencia respecto a Brain: la aplicación es un producto independiente, abierto a integrarse en el futuro mediante API.

## **3. Requisitos de Usuarios**

*Definición:* Necesidades y servicios que el sistema debe ofrecer a los usuarios en función de sus roles y perfiles de permisos.

### **Rol: Usuario Estándar**


* **UR1.** El usuario podrá consultar y modificar la información de los clientes y contactos que tiene asignados, darlos de alta y gestionar sus suscripciones (crearlas, modificarlas y pausarlas); la cancelación de una suscripción y los cambios de tarifa requerirán aprobación de un administrador. FR relacionados: FR5, FR6, FR7, FR9, FR10, FR16.

* **UR2.** El usuario podrá crear facturas puntuales o recurrentes y previsualizarlas antes de su emisión oficial. FR relacionados: FR12, FR13.

* **UR3.** El usuario podrá seguir el estado de cobro de sus clientes (vencimientos, impagos, pagos recibidos) y descargar los documentos. FR relacionados: FR14, FR17, FR20.

* **UR4.** El usuario podrá proponer excepciones comerciales (descuentos, ajustes de tarifa) para un cliente. FR relacionados: FR10, FR16.

* **UR5.** El usuario podrá abrir incidencias de facturación y realizar comentarios sobre las mismas, así como marcar incidencias sobre facturas. FR relacionados: FR22.

* **UR6.** El usuario podrá pausar los recordatorios de una factura específica indicando el motivo justificado y la fecha de revisión. FR relacionados: FR19, FR22.

* **UR7.** El usuario recibirá notificaciones y tareas únicamente de los clientes que tiene a su cargo. FR relacionados: FR21.

* **UR8.** El usuario podrá buscar con rapidez clientes y facturas mediante búsquedas avanzadas y guardar filtros frecuentes. FR relacionados: FR23.

### **Rol: Administrador**

* **UR9.** El administrador podrá aprobar o rechazar excepciones, descuentos, cambios de tarifa, importes sensibles y facturas pendientes de validación. FR relacionados: FR10, FR16.

* **UR10.** El administrador podrá asignar o desasignar clientes a los usuarios y gestionar delegaciones temporales. FR relacionados: FR4.
* **UR11.** El administrador podrá crear, invitar y desactivar usuarios del sistema. FR relacionados: FR3.

* **UR12.** El administrador podrá realizar la conciliación manual de pagos dudosos o no automatizados. FR relacionados: FR20.

* **UR13.** El administrador podrá consultar el *dashboard* financiero, generar informes y exportar datos detallados. FR relacionados: FR24, FR25.

* **UR14.** El administrador podrá rectificar facturas y cerrar periodos de facturación. FR relacionados: FR15.

### **Rol: Súper Administrador**

* **UR15.** El súper administrador podrá configurar el sistema: tarifas por nivel, series de numeración, impuestos, plazos, cadencia de los recordatorios, plantillas y parámetros generales. FR relacionados: FR10, FR14, FR19, FR27.

* **UR16.** El súper administrador podrá gestionar y supervisar los permisos asignados a los administradores y gestionar usuarios. FR relacionados: FR2, FR3.

* **UR17.** El súper administrador podrá consultar el registro de auditoría completo de todas las acciones del sistema (sin poder modificarlo) y la salud técnica del sistema. FR relacionados: FR26, FR27.

### **Rol: Sistema Externo**

* **UR18.** Un sistema externo podrá consultar por API si un cliente tiene deuda pendiente y notificar una solicitud de baja. FR relacionados: FR11, FR28.

## **4. Requisitos Funcionales**

*Definición:* Funcionalidades concretas, características y reglas de negocio interactivas que el sistema debe proporcionar, indicando sus dependencias técnicas y funcionales.

### **A. Acceso, usuarios y permisos**

* **FR1.** Autenticación y sesiones. Acceso restringido a empleados autorizados: solo con correo del dominio corporativo de la empresa o con cuenta de Microsoft corporativa. Con correo corporativo se usa contraseña y no existe autorregistro (las cuentas las crea un administrador). Recuperación de contraseña mediante enlace caducable, expiración de sesión por inactividad, protección frente a intentos fallidos (bloqueo temporal) e invalidación de las sesiones activas al desactivar un usuario. Criterios de aceptación: un correo de dominio no autorizado es rechazado; tras 5 intentos de login fallidos la cuenta queda bloqueada temporalmente; una sesión inactiva 30 minutos se cierra; las sesiones de un usuario desactivado dejan de ser válidas en menos de 1 minuto. Dependencias: ninguna.

* **FR2.** Control de acceso basado en roles (RBAC). Modelo de autorización único y centralizado que restringe acciones y protege los datos. Tres roles de gestión (Usuario estándar, Administrador, Superadministrador) y un rol técnico para sistemas externos (API). Cada recurso y acción se autoriza en un único punto, tanto en interfaz como en API. Un usuario estándar solo accede a los clientes asignados a él; un administrador accede a todos; el superadministrador además a configuración y auditoría. Criterios de aceptación: matriz de pruebas rol × recurso sin accesos indebidos (incluidos intentos por URL directa). Dependencias: FR1.

* **FR3.** Gestión de usuarios. Ciclo de vida de las cuentas: alta, invitación por correo, asignación de rol y desactivación (no borrado físico, para conservar la auditoría). El superadministrador supervisa los permisos de los administradores. Criterios de aceptación: un usuario invitado puede activar su cuenta; un usuario desactivado no puede iniciar sesión y conserva su historial. Dependencias: FR1, FR2.

* **FR4.** Asignación de clientes y delegaciones. Cada cliente tiene un responsable interno (usuario estándar). El administrador asigna y desasigna clientes y puede delegarlos temporalmente (con fecha de inicio y fin); el nuevo responsable hereda las tareas pendientes del cliente y, al terminar la delegación, el cliente vuelve al responsable original. Criterios de aceptación: tras una delegación, las notificaciones y tareas del cliente llegan al delegado y dejan de llegar al original; todo queda auditado. Dependencias: FR2, FR5.

### **B. Clientes y contactos**

* **FR5.** Registro de clientes y datos fiscales. Almacenamiento estructurado de la información de cada empresa cliente: nombre comercial, razón social, identificador fiscal (sin asumir formato español), domicilio, país, moneda, representante legal y datos fiscales. Borrado lógico y reactivación de fichas (nunca se elimina físicamente un cliente con histórico). Un cliente puede tener moneda y país distintos de España. Criterios de aceptación: se puede registrar un cliente de cualquier país con un identificador fiscal no español sin modificar código. Dependencias: FR2.

* **FR6.** Contactos múltiples e historial de destinatarios. Cada empresa puede tener varias personas de contacto (comercial, administración, impagos; extensible), activas o inactivas. Se registra el histórico de destinatarios de cada factura enviada, aunque el contacto cambie o se anonimice después. Criterios de aceptación: dada una factura enviada, se puede consultar a quién se envió y cuándo, aunque el contacto ya esté inactivo. Dependencias: FR5.

* **FR7.** Detección de clientes duplicados. Si se intenta registrar una empresa con un identificador fiscal ya existente, el sistema emite una alerta e impide el duplicado, ofreciendo abrir o reactivar la ficha existente. Criterios de aceptación: alta con NIF duplicado genera alerta; en importación CSV el duplicado aparece en el informe de errores. Dependencias: FR5.

* **FR8.** Importación y exportación de datos de clientes (CSV). Importación CSV con previsualización, mapeo de columnas, informe de errores y resumen de resultados. Importación del histórico de facturas marcadas como «migradas» (no generan cobros ni recordatorios). Exportación CSV de clientes en codificación UTF-8. Criterios de aceptación: un CSV con filas erróneas importa las válidas y devuelve un informe con las fallidas y su motivo. Dependencias: FR5, FR7.

### **C. Suscripciones y tarifas**

* **FR9.** Suscripciones y ciclo de vida comercial del cliente. Ficha comercial (no es una factura) que establece la relación con el cliente. Soporta fechas de inicio futuras y pausas con motivo y fecha de reactivación, y conserva el historial de cambios de la suscripción. Las suscripciones las gestiona el responsable del cliente; cancelarlas o cambiar su tarifa requiere aprobación de un administrador. Criterios de aceptación: una suscripción con inicio futuro no genera facturas hasta su fecha; una pausada no genera facturas recurrentes. Dependencias: FR5, FR10, FR13.

* **FR10.** Tarifas por nivel, descuentos y excepciones. Gestión dinámica de niveles de suscripción con tarifas variables según el volumen de facturación anual del cliente. La tabla de tarifas por rango es configuración (la gestiona el superadministrador) y no altera facturas históricas. Revisión anual: el sistema propone el rango correspondiente y un administrador lo aprueba (aprobación humana). Los descuentos y excepciones comerciales los propone un usuario estándar y los aprueba un administrador. Criterios de aceptación: cambiar una tarifa afecta solo a facturas futuras; un descuento sin aprobar no se aplica. Dependencias: FR2, FR9, FR16.

* **FR11.** Baja de clientes. Gestión controlada de la baja: se restringe la baja de clientes con facturas pendientes de pago. Si el cliente solicita la baja, se detienen las nuevas mensualidades pero se mantiene el recobro de la deuda previa. Una baja solicitada por un sistema externo (API) crea una tarea de revisión y nunca se ejecuta automáticamente. El cliente puede reactivarse (recuperar su ficha) siempre que la legalidad permita conservar sus datos. Criterios de aceptación: intentar dar de baja a un cliente con deuda es bloqueado con mensaje claro; una solicitud API genera tarea y no borra nada. Dependencias: FR5, FR14, FR28.

### **D. Facturación**

* **FR12.** Facturas puntuales y catálogo de conceptos. Creación manual de facturas con varias líneas (concepto, cantidad, precio, descuento e impuesto configurable). Vista previa obligatoria antes de la emisión, bloqueo con mensajes claros si faltan datos fiscales del cliente y aviso configurable si el importe se sale del patrón habitual del cliente. Catálogo de conceptos frecuentes: modificar un concepto no modifica facturas ya emitidas. Criterios de aceptación: no se puede emitir una factura sin datos fiscales completos; los totales de la vista previa coinciden con el PDF final. Dependencias: FR5, FR14, FR16.

* **FR13.** Facturación recurrente automática. Emisión mensual automática a partir de las suscripciones activas, el día 1 de cada mes. En altas a mitad de periodo se calcula la parte proporcional (prorrateo) del primer mes, visible antes de confirmar el alta, y se factura y cobra de forma inmediata; excepción: sin prorrateo para suscripciones con inicio futuro (empiezan en su fecha). No se emiten facturas durante las pausas y al llegar la fecha de reactivación acordada se avisa al responsable. Se registra cada ejecución del proceso y se recuperan las ejecuciones perdidas sin duplicar facturas. Las facturas recurrentes estándar no pasan por aprobación, para mantener la automatización. Criterios de aceptación: tras una caída durante la generación, al reanudar no hay facturas duplicadas ni faltantes; el prorrateo es correcto en fin de mes y años bisiestos. Dependencias: FR9, FR14.

* **FR14.** Numeración, emisión y estados de la factura. Ciclo de vida integral (generación, envío y cobro). Series de numeración configurables por el superadministrador; el número único se asigna de forma segura (sin huecos ni duplicados) al emitir. Estados: borrador, pendiente de aprobación, aprobada, emitida, enviada, vencida, pagada, anulada, rectificada. «Pagada» solo se alcanza tras un cobro validado, y solo pueden borrarse los borradores. Criterios de aceptación: dos emisiones simultáneas nunca obtienen el mismo número; cada cambio de estado queda registrado con usuario y fecha. Dependencias: FR2, FR20.

* **FR15.** Inmutabilidad y rectificación. Una factura emitida no se puede editar ni borrar; cualquier corrección requiere una factura rectificativa con numeración única vinculada a la original. La rectificación exige permiso y, según el importe, aprobación de un administrador. El administrador puede además cerrar periodos de facturación. Criterios de aceptación: cualquier intento de modificar una factura emitida (UI o API) es rechazado y registrado. Dependencias: FR14, FR16.

* **FR16.** Flujo de aprobaciones. Validación previa de operaciones económicas sensibles: quedan en estado «Pendiente de aprobación», en una bandeja de validación, los grandes descuentos, importes atípicos, modificaciones manuales de importes o vencimientos, facturas puntuales que lo requieran y rectificaciones. Requieren la aprobación de un administrador distinto al creador (o del superadministrador). El administrador ve el motivo de retención y puede aprobar, rechazar o reasignar la validación a otro administrador; cada decisión deja registro de auditoría. Criterios de aceptación: una factura retenida no puede emitirse hasta ser aprobada; el creador no puede aprobar su propia factura. Dependencias: FR2, FR14.

* **FR17.** Documento PDF, firma digital y almacenamiento. PDF generado a partir de los datos con la identidad corporativa y conservado exactamente como se emitió. Firma digital obligatoria mediante certificado electrónico (cumplimiento legal, seguridad técnica y eficiencia). Almacenamiento redundante en soporte local y en la nube (copias frente a fallos). El acceso a documentos exige sesión iniciada o enlace temporal. Criterios de aceptación: toda factura emitida tiene un PDF firmado inalterable, disponible en ambos almacenamientos. Dependencias: FR14.

* **FR18.** Envío de facturas. Entrega al cliente tras la emisión: envío automático por correo electrónico a los contactos adecuados, con WhatsApp simulado como canal complementario. Se registran destinatarios, intentos, resultado y reintentos sin duplicar la factura. Si el correo rebota, no se marca como notificada, se avisa al responsable y se permite corregir el contacto y reenviar. Criterios de aceptación: un rebote genera notificación al responsable y la factura queda como «no notificada»; reenviar no crea una factura nueva. Dependencias: FR6, FR14, FR17.

### **E. Cobros**

* **FR19.** Recordatorios y recobro. Reducción de impagos mediante seguimiento automático: tras la emisión, recordatorio hacia el día 3 y otro al vencimiento (plazo por defecto: 5 días); después, flujo de impago cada 2–3 días hasta el pago o la intervención manual. Fechas, cadencia y plazos configurables por el superadministrador. Pausa de recordatorios por disputa (por factura) con motivo justificado y fecha de revisión. Al llegar la fecha de revisión, los recordatorios se reanudan automáticamente y se avisa al responsable del cliente. El responsable o un administrador pueden levantar la pausa antes de esa fecha. Criterios de aceptación: una factura pausada no genera recordatorios hasta su fecha de revisión; los cambios de estado quedan auditados. Dependencias: FR14, FR18, FR22.

* **FR20.** Cobros y conciliación. Vinculación de pagos con facturas: integración con Stripe (simulada en el proyecto, extensible a otras fuentes), de forma que al confirmarse el pago la factura pasa a «Pagada»; importación de movimientos de Revolut o transferencias. Idempotencia: un mismo aviso de pago recibido dos veces no duplica el cobro. Las discrepancias (importe distinto, referencia dudosa) pasan a una cola de revisión manual donde el administrador resuelve la diferencia o revierte conciliaciones previas, con registro de auditoría. Criterios de aceptación: un evento de pago duplicado se ignora; un cobro con importe distinto va a la cola de revisión. Dependencias: FR14, FR28.

### **F. Seguimiento y trabajo diario**

* **FR21.** Notificaciones internas y trabajo diario. Avisos sobre la cartera de clientes (pagos, impagos, rebotes, disputas, aprobaciones, vencimientos); cada usuario recibe solo las de sus clientes. Estados de notificación (leído / atendido) y prioridad. Pantalla de trabajo diario con las tareas y cobros pendientes. El superadministrador configura qué notificaciones llegan por qué canal (correo, interno…). Criterios de aceptación: un impago de un cliente solo notifica a su responsable; las tareas se agrupan por prioridad. Dependencias: FR4, FR19, FR20.

* **FR22.** Incidencias, comentarios y adjuntos. Gestión de problemas internos de facturación: una incidencia tiene responsable, prioridad y la opción de pausar los recordatorios de impago. Comentarios y adjuntos (limitados en tamaño y formato). Notas privadas exclusivas de administradores (visibles para un usuario si un administrador lo autoriza). Al cerrar una incidencia que pausaba los recordatorios, estos se reanudan automáticamente. Criterios de aceptación: un adjunto fuera de formato o tamaño es rechazado; una nota privada no es visible para usuarios estándar. Dependencias: FR2, FR5.

* **FR23.** Búsqueda avanzada, filtros y acciones masivas. Buscador global por NIF, nombre, número de factura o correo, con filtros combinables guardables como vistas predeterminadas. Acciones masivas seguras: confirmación previa y recuento exacto de elementos procesados y fallidos. Los resultados respetan los permisos del usuario. Criterios de aceptación: una acción masiva con fallos parciales informa exactamente de cuántos elementos se procesaron y cuáles fallaron. Dependencias: FR2.

### **G. Información y control**

* **FR24.** Dashboard y métricas financieras. Panel interactivo para administradores con previsión de ingresos, cobrado, vencido y mayores deudores. Desgloses de ingresos semanales, mensuales, trimestrales y anuales para el estudio analítico de clientes. Navegación hasta el máximo nivel de detalle (de agregado a factura). Objetivos mensuales o anuales; si cambian a medio plazo se deja constancia del vigente en cada periodo. Criterios de aceptación: los totales del dashboard coinciden con base de datos y PDFs. Dependencias: FR14, FR20.

* **FR25.** Informes, exportaciones y paquete para la gestoría. Exportación de informes en CSV y PDF que respetan los permisos de quien los solicita; se registra fecha/hora, filtros y usuario de cada exportación. Envíos programados de informes y paquete periódico ZIP de documentación para la gestoría (que no necesita usuario inicialmente). Las exportaciones se retienen un tiempo configurable y pueden regenerarse si datos y permisos lo permiten. Criterios de aceptación: dos usuarios con distintos permisos obtienen informes distintos con los mismos filtros; las exportaciones antiguas caducan solas. Dependencias: FR2, FR14, FR20.

* **FR26.** Auditoría e historial. Registro inalterable de la actividad: se registra quién, qué, cuándo (y valor anterior y nuevo) para toda acción relevante, en especial las económicas y de permisos. Panel de auditoría reservado al superadministrador (consulta, nunca edición). A nivel operativo, cada cliente tiene una línea de tiempo legible de su historial comercial. Criterios de aceptación: ningún rol puede modificar ni borrar registros de auditoría. Dependencias: FR2.

* **FR27.** Configuración, plantillas y salud del sistema. Parámetros globales del negocio aislados del uso diario: panel de configuración de impuestos, series de facturación, plazos, recordatorios, monedas y tarifas. Editor de plantillas de mensajes multidioma (inicialmente español e inglés), con variables dinámicas y vista previa. Pantalla de salud técnica: correos atascados, estado de backups y errores del servidor. Criterios de aceptación: cambiar un parámetro de configuración queda auditado y no altera datos históricos. Dependencias: FR2.

### **H. Integración**

* **FR28.** API e integraciones. API segura para otros sistemas: un sistema externo puede consultar si un cliente tiene deuda pendiente y notificar una solicitud de baja (esta crea una tarea, no borra el cliente). Las integraciones (Stripe, correo, Revolut) quedan aisladas tras interfaces: si falla un envío o un proveedor, la factura no se borra ni se corrompe. Criterios de aceptación: una llamada API sin credenciales válidas es rechazada; un fallo del proveedor de correo no altera la factura. Dependencias: FR2, FR11, FR14, FR20.


## **5. Requisitos No Funcionales**

*Definición:* Restricciones y atributos de calidad del sistema, estructurados rigurosamente con métricas de aceptación y métodos de verificación.

* **NFR1. Usabilidad y eficiencia de interacción**
* *Descripción:* Aplicación intuitiva, de curva de aprendizaje casi nula, con pocos clics en las operaciones frecuentes.
* *Métrica / Criterio de aceptación:* Un usuario que conoce el proceso administrativo crea un cliente estándar y deja preparada su suscripción en menos de 5 minutos con ayuda mínima; las funciones más comunes requieren como máximo 7 clics y 3 pantallas.
* *Método de verificación:* Pruebas de usabilidad cronometradas con al menos 5 personas y recuento de clics.
* *Dependencias:* FR5, FR12, FR23.


* **NFR2. Diseño e identidad corporativa**
* *Descripción:* La interfaz visual debe ajustarse estrictamente a los patrones de diseño y a la imagen de la empresa.
* *Métrica / Criterio de aceptación:* El 100% de las vistas principales usa los colores, tipografía y logo de la guía de estilo corporativa.
* *Método de verificación:* Revisión visual comparativa y auditoría de componentes frontend.


* **NFR3. Diseño responsive, accesibilidad y portabilidad**
* *Descripción:* Experiencia de uso fluida y adaptable en dispositivos de escritorio, tablets y móviles; las tareas urgentes deben poder realizarse desde el móvil.
* *Métrica / Criterio de aceptación:* Cumplimiento de las pautas WCAG 2.1 nivel AA y ausencia de desbordamientos y de barras de desplazamiento horizontal en un rango de resolución de 360px a 1920px.
* *Método de verificación:* Pruebas manuales multiplataforma (Chrome DevTools) y análisis automático con Lighthouse o Axe Core.


* **NFR4. Seguridad de documentos, credenciales y control de acceso**
* *Descripción:* Protección estricta de accesos, cifrado de comunicaciones y ocultación de secretos.
* *Métrica / Criterio de aceptación:* Contraseñas hasheadas de forma segura; datos fiscales y documentos cifrados; tráfico 100% HTTPS; enlaces temporales con caducidad máxima de 15 minutos; bloqueo del 100% de los accesos sin permiso (URL o API) mediante autorización centralizada; ausencia de secretos en el repositorio.
* *Método de verificación:* Pruebas de endpoints sin sesión, análisis de tráfico con Wireshark, escaneo con gitleaks, inspección de código y matriz de pruebas rol × recurso.
* *Dependencias:* FR1, FR2, FR17, FR25, FR28.


* **NFR5. Gestión de sesiones**
* *Descripción:* Control riguroso del ciclo de vida de la sesión de usuario.
* *Métrica / Criterio de aceptación:* Caducidad por inactividad a los 30 minutos, invalidación de sesiones de usuarios desactivados en menos de 1 minuto y bloqueo temporal tras 5 fallos de login.
* *Método de verificación:* Pruebas de integración de autenticación.
* *Dependencias:* FR1.


* **NFR6. Protección de datos y conservación legal**
* *Descripción:* Minimización y conservación de datos personales y contables conforme a la ley, sin invalidar facturas históricas.
* *Métrica / Criterio de aceptación:* Los datos de un contacto pueden anonimizarse sin romper las facturas antiguas donde aparece; las descargas temporales caducan y desaparecen solas.
* *Método de verificación:* Pruebas con datos ficticios: anonimizar o borrar contactos y comprobar que sus facturas siguen intactas, legales y accesibles.
* *Dependencias:* FR5, FR6, FR25, FR26.


* **NFR7. Fiabilidad transaccional ACID e idempotencia**
* *Descripción:* La base de datos debe garantizar transacciones ACID para evitar inconsistencias contables, pérdida de datos, duplicidades o dobles cobros durante eventos concurrentes.
* *Métrica / Criterio de aceptación:* Cero facturas o cobros duplicados ante dobles clics masivos o avisos de pago repetidos; cero anomalías de integridad o discrepancias contables en pruebas de estrés transaccional simultáneo.
* *Método de verificación:* Pruebas unitarias de concurrencia e integración sobre los flujos de pago; reenvío intencionado de cobros y clics simultáneos en un entorno de pruebas.
* *Dependencias:* FR13, FR14, FR18, FR20.


* **NFR8. Trazabilidad e inmutabilidad**
* *Descripción:* Todos los cambios económicos, transaccionales o de permisos deben ser completamente rastreables e inalterables en el pasado.
* *Métrica / Criterio de aceptación:* 100% de las acciones críticas registradas en los logs de auditoría sin posibilidad de modificación posterior por parte de los usuarios; una factura emitida jamás se altera.
* *Método de verificación:* Auditoría de registros del sistema, intentos de modificar o borrar históricos con distintos roles y análisis de restricciones de modificación en tablas históricas.
* *Dependencias:* FR14, FR15, FR20, FR26.


* **NFR9. Integridad matemática y financiera**
* *Descripción:* Operaciones financieras exactas y coherentes, libres de pérdida de datos o errores de redondeo (prohibido usar tipos `float`; usar tipos decimales o enteros en céntimos).
* *Métrica / Criterio de aceptación:* Cero errores de céntimos o desajustes entre PDF, base de datos y dashboard sobre un conjunto de al menos 1.000 facturas de prueba, incluyendo prorrateos en fin de mes y años bisiestos.
* *Método de verificación:* Pruebas unitarias automatizadas de prorrateo, impuestos y totales, y validación cruzada (conciliación automática PDF–BD–dashboard).
* *Dependencias:* FR12, FR13, FR24.


* **NFR10. Fechas y zonas horarias**
* *Descripción:* Almacenamiento consistente de marcas de tiempo y fechas legales basadas en horario de negocio.
* *Métrica / Criterio de aceptación:* Todos los instantes se almacenan en UTC y las fechas legales de factura usan la fecha de negocio.
* *Método de verificación:* Pruebas unitarias y de integración simulando eventos cercanos a la medianoche.
* *Dependencias:* FR13, FR14.


* **NFR11. Copias de seguridad y recuperación**
* *Descripción:* Políticas robustas de respaldo y recuperación ante fallos catastróficos.
* *Métrica / Criterio de aceptación:* Copia diaria automática, pérdida máxima de datos de 24 horas, restauración completa en menos de 4 horas y cero duplicidades tras una caída durante procesos masivos (por ejemplo, la generación recurrente).
* *Método de verificación:* Simulacro de restauración documentado y prueba de fallo por interrupción de procesos.
* *Dependencias:* FR13, FR17, FR27.


* **NFR12. Rendimiento en consultas y operaciones**
* *Descripción:* Agilidad en consultas frecuentes y procesamiento asíncrono para tareas pesadas.
* *Métrica / Criterio de aceptación:* El 95% de las peticiones de consulta y búsqueda responden en menos de 500 ms bajo una carga de 100 usuarios concurrentes; las búsquedas y fichas de cliente se abren en menos de 1 segundo incluso en el peor caso; las tareas pesadas se ejecutan en segundo plano con indicador de progreso visible.
* *Método de verificación:* Pruebas de carga con Apache JMeter o k6 y pruebas de operaciones asíncronas.
* *Dependencias:* FR5, FR12, FR23, FR24.


* **NFR13. Escalabilidad**
* *Descripción:* Capacidad de crecimiento sin degradación del rendimiento ni rediseño del modelo.
* *Métrica / Criterio de aceptación:* Tiempos de respuesta estables con 10.000 clientes y 500.000 facturas de prueba; paginación y filtrado en servidor (máximo 100 filas por petición, ninguna pantalla carga el histórico completo); la generación mensual procesa 3.000 clientes en un tiempo acotado y documentado.
* *Método de verificación:* Pruebas de carga y de volumen con datos sintéticos e inspección de consultas.
* *Dependencias:* FR13, FR23.

=======
# **Documento de Requisitos**

**Grupo:** Ramón Llull

**Curso:** 3º B

## **1. Descripción General y contextualización**

TurbineH es una startup de origen española cuyo producto principal es **Brain**, una inteligencia artificial destinada a ayudar en la organización de empresas. En este proyecto se solicita el desarrollo de una aplicación independiente (no integrada en Brain, aunque preparada para integrarse en el futuro mediante API) para la gestión integral de facturas y clientes, así como la generación de métricas analíticas sobre los ingresos del negocio.

Roles del sistema:

* **Usuario estándar .** Empleado que gestiona los clientes que tiene asignados: facturas, cobros e incidencias.

* **Administrador.** Aprueba excepciones y facturas, asigna clientes, gestiona usuarios, concilia pagos y consulta el dashboard.

* **Superadministrador.** Configura el sistema (tarifas, series, plazos, recordatorios), supervisa permisos y accede a la auditoría completa.


## **2. Requisitos de Negocio**

*Definición:* Son las metas y objetivos de alto nivel que explican las razones estratégicas para realizar el proyecto.

* **BR1.** Automatizar la facturación y el cobro: automatizar la generación, el envío y el cobro de facturas recurrentes y puntuales, reduciendo el trabajo administrativo manual.

* **BR2.** Reducir los impagos mediante recordatorios y seguimiento automáticos.

* **BR3.** Control legal y económico: garantizar el control y la trazabilidad de facturas y decisiones económicas (tarifas, excepciones, cambios); conservar históricos según la ley, facilitar el trabajo con la gestoría y no alterar nunca facturas históricas.

* **BR4.** Operativa rápida: reducir el tiempo y el número de clics en las operaciones frecuentes.

* **BR5.** Gestión de clientes y suscripciones: facilitar la gestión de la información de los clientes y de sus suscripciones.

* **BR6.** Visibilidad financiera: dar a los administradores visibilidad de la situación financiera mediante métricas y paneles de control.
* **BR7.** Escalabilidad: crecer en número de clientes (miles), facturas, usuarios (decenas o cientos), países y divisas sin reconstruir la arquitectura ni el modelo de datos.

* **BR8.** Acceso desde cualquier lugar y dispositivo (escritorio, tablet y móvil).

* **BR9.** Independencia respecto a Brain: la aplicación es un producto independiente, abierto a integrarse en el futuro mediante API.

## **3. Requisitos de Usuarios**

*Definición:* Necesidades y servicios que el sistema debe ofrecer a los usuarios en función de sus roles y perfiles de permisos.

### **Rol: Usuario Estándar**


* **UR1.** El usuario podrá consultar y modificar la información de los clientes y contactos que tiene asignados, darlos de alta y gestionar sus suscripciones (crearlas, modificarlas y pausarlas); la cancelación de una suscripción y los cambios de tarifa requerirán aprobación de un administrador. FR relacionados: FR5, FR6, FR7, FR9, FR10, FR16.

* **UR2.** El usuario podrá crear facturas puntuales o recurrentes y previsualizarlas antes de su emisión oficial. FR relacionados: FR12, FR13.

* **UR3.** El usuario podrá seguir el estado de cobro de sus clientes (vencimientos, impagos, pagos recibidos) y descargar los documentos. FR relacionados: FR14, FR17, FR20.

* **UR4.** El usuario podrá proponer excepciones comerciales (descuentos, ajustes de tarifa) para un cliente. FR relacionados: FR10, FR16.

* **UR5.** El usuario podrá abrir incidencias de facturación y realizar comentarios sobre las mismas, así como marcar incidencias sobre facturas. FR relacionados: FR22.

* **UR6.** El usuario podrá pausar los recordatorios de una factura específica indicando el motivo justificado y la fecha de revisión. FR relacionados: FR19, FR22.

* **UR7.** El usuario recibirá notificaciones y tareas únicamente de los clientes que tiene a su cargo. FR relacionados: FR21.

* **UR8.** El usuario podrá buscar con rapidez clientes y facturas mediante búsquedas avanzadas y guardar filtros frecuentes. FR relacionados: FR23.

### **Rol: Administrador**

* **UR9.** El administrador podrá aprobar o rechazar excepciones, descuentos, cambios de tarifa, importes sensibles y facturas pendientes de validación. FR relacionados: FR10, FR16.

* **UR10.** El administrador podrá asignar o desasignar clientes a los usuarios y gestionar delegaciones temporales. FR relacionados: FR4.
* **UR11.** El administrador podrá crear, invitar y desactivar usuarios del sistema. FR relacionados: FR3.

* **UR12.** El administrador podrá realizar la conciliación manual de pagos dudosos o no automatizados. FR relacionados: FR20.

* **UR13.** El administrador podrá consultar el *dashboard* financiero, generar informes y exportar datos detallados. FR relacionados: FR24, FR25.

* **UR14.** El administrador podrá rectificar facturas y cerrar periodos de facturación. FR relacionados: FR15.

### **Rol: Súper Administrador**

* **UR15.** El súper administrador podrá configurar el sistema: tarifas por nivel, series de numeración, impuestos, plazos, cadencia de los recordatorios, plantillas y parámetros generales. FR relacionados: FR10, FR14, FR19, FR27.

* **UR16.** El súper administrador podrá gestionar y supervisar los permisos asignados a los administradores y gestionar usuarios. FR relacionados: FR2, FR3.

* **UR17.** El súper administrador podrá consultar el registro de auditoría completo de todas las acciones del sistema (sin poder modificarlo) y la salud técnica del sistema. FR relacionados: FR26, FR27.

### **Rol: Sistema Externo**

* **UR18.** Un sistema externo podrá consultar por API si un cliente tiene deuda pendiente y notificar una solicitud de baja. FR relacionados: FR11, FR28.

## **4. Requisitos Funcionales**

*Definición:* Funcionalidades concretas, características y reglas de negocio interactivas que el sistema debe proporcionar, indicando sus dependencias técnicas y funcionales.

### **A. Acceso, usuarios y permisos**

* **FR1.** Autenticación y sesiones. Acceso restringido a empleados autorizados: solo con correo del dominio corporativo de la empresa o con cuenta de Microsoft corporativa. Con correo corporativo se usa contraseña y no existe autorregistro (las cuentas las crea un administrador). Recuperación de contraseña mediante enlace caducable, expiración de sesión por inactividad, protección frente a intentos fallidos (bloqueo temporal) e invalidación de las sesiones activas al desactivar un usuario. Criterios de aceptación: un correo de dominio no autorizado es rechazado; tras 5 intentos de login fallidos la cuenta queda bloqueada temporalmente; una sesión inactiva 30 minutos se cierra; las sesiones de un usuario desactivado dejan de ser válidas en menos de 1 minuto. Dependencias: ninguna.

* **FR2.** Control de acceso basado en roles (RBAC). Modelo de autorización único y centralizado que restringe acciones y protege los datos. Tres roles de gestión (Usuario estándar, Administrador, Superadministrador) y un rol técnico para sistemas externos (API). Cada recurso y acción se autoriza en un único punto, tanto en interfaz como en API. Un usuario estándar solo accede a los clientes asignados a él; un administrador accede a todos; el superadministrador además a configuración y auditoría. Criterios de aceptación: matriz de pruebas rol × recurso sin accesos indebidos (incluidos intentos por URL directa). Dependencias: FR1.

* **FR3.** Gestión de usuarios. Ciclo de vida de las cuentas: alta, invitación por correo, asignación de rol y desactivación (no borrado físico, para conservar la auditoría). El superadministrador supervisa los permisos de los administradores. Criterios de aceptación: un usuario invitado puede activar su cuenta; un usuario desactivado no puede iniciar sesión y conserva su historial. Dependencias: FR1, FR2.

* **FR4.** Asignación de clientes y delegaciones. Cada cliente tiene un responsable interno (usuario estándar). El administrador asigna y desasigna clientes y puede delegarlos temporalmente (con fecha de inicio y fin); el nuevo responsable hereda las tareas pendientes del cliente y, al terminar la delegación, el cliente vuelve al responsable original. Criterios de aceptación: tras una delegación, las notificaciones y tareas del cliente llegan al delegado y dejan de llegar al original; todo queda auditado. Dependencias: FR2, FR5.

### **B. Clientes y contactos**

* **FR5.** Registro de clientes y datos fiscales. Almacenamiento estructurado de la información de cada empresa cliente: nombre comercial, razón social, identificador fiscal (sin asumir formato español), domicilio, país, moneda, representante legal y datos fiscales. Borrado lógico y reactivación de fichas (nunca se elimina físicamente un cliente con histórico). Un cliente puede tener moneda y país distintos de España. Criterios de aceptación: se puede registrar un cliente de cualquier país con un identificador fiscal no español sin modificar código. Dependencias: FR2.

* **FR6.** Contactos múltiples e historial de destinatarios. Cada empresa puede tener varias personas de contacto (comercial, administración, impagos; extensible), activas o inactivas. Se registra el histórico de destinatarios de cada factura enviada, aunque el contacto cambie o se anonimice después. Criterios de aceptación: dada una factura enviada, se puede consultar a quién se envió y cuándo, aunque el contacto ya esté inactivo. Dependencias: FR5.

* **FR7.** Detección de clientes duplicados. Si se intenta registrar una empresa con un identificador fiscal ya existente, el sistema emite una alerta e impide el duplicado, ofreciendo abrir o reactivar la ficha existente. Criterios de aceptación: alta con NIF duplicado genera alerta; en importación CSV el duplicado aparece en el informe de errores. Dependencias: FR5.

* **FR8.** Importación y exportación de datos de clientes (CSV). Importación CSV con previsualización, mapeo de columnas, informe de errores y resumen de resultados. Importación del histórico de facturas marcadas como «migradas» (no generan cobros ni recordatorios). Exportación CSV de clientes en codificación UTF-8. Criterios de aceptación: un CSV con filas erróneas importa las válidas y devuelve un informe con las fallidas y su motivo. Dependencias: FR5, FR7.

### **C. Suscripciones y tarifas**

* **FR9.** Suscripciones y ciclo de vida comercial del cliente. Ficha comercial (no es una factura) que establece la relación con el cliente. Soporta fechas de inicio futuras y pausas con motivo y fecha de reactivación, y conserva el historial de cambios de la suscripción. Las suscripciones las gestiona el responsable del cliente; cancelarlas o cambiar su tarifa requiere aprobación de un administrador. Criterios de aceptación: una suscripción con inicio futuro no genera facturas hasta su fecha; una pausada no genera facturas recurrentes. Dependencias: FR5, FR10, FR13.

* **FR10.** Tarifas por nivel, descuentos y excepciones. Gestión dinámica de niveles de suscripción con tarifas variables según el volumen de facturación anual del cliente. La tabla de tarifas por rango es configuración (la gestiona el superadministrador) y no altera facturas históricas. Revisión anual: el sistema propone el rango correspondiente y un administrador lo aprueba (aprobación humana). Los descuentos y excepciones comerciales los propone un usuario estándar y los aprueba un administrador. Criterios de aceptación: cambiar una tarifa afecta solo a facturas futuras; un descuento sin aprobar no se aplica. Dependencias: FR2, FR9, FR16.

* **FR11.** Baja de clientes. Gestión controlada de la baja: se restringe la baja de clientes con facturas pendientes de pago. Si el cliente solicita la baja, se detienen las nuevas mensualidades pero se mantiene el recobro de la deuda previa. Una baja solicitada por un sistema externo (API) crea una tarea de revisión y nunca se ejecuta automáticamente. El cliente puede reactivarse (recuperar su ficha) siempre que la legalidad permita conservar sus datos. Criterios de aceptación: intentar dar de baja a un cliente con deuda es bloqueado con mensaje claro; una solicitud API genera tarea y no borra nada. Dependencias: FR5, FR14, FR28.

### **D. Facturación**

* **FR12.** Facturas puntuales y catálogo de conceptos. Creación manual de facturas con varias líneas (concepto, cantidad, precio, descuento e impuesto configurable). Vista previa obligatoria antes de la emisión, bloqueo con mensajes claros si faltan datos fiscales del cliente y aviso configurable si el importe se sale del patrón habitual del cliente. Catálogo de conceptos frecuentes: modificar un concepto no modifica facturas ya emitidas. Criterios de aceptación: no se puede emitir una factura sin datos fiscales completos; los totales de la vista previa coinciden con el PDF final. Dependencias: FR5, FR14, FR16.

* **FR13.** Facturación recurrente automática. Emisión mensual automática a partir de las suscripciones activas, el día 1 de cada mes. En altas a mitad de periodo se calcula la parte proporcional (prorrateo) del primer mes, visible antes de confirmar el alta, y se factura y cobra de forma inmediata; excepción: sin prorrateo para suscripciones con inicio futuro (empiezan en su fecha). No se emiten facturas durante las pausas y al llegar la fecha de reactivación acordada se avisa al responsable. Se registra cada ejecución del proceso y se recuperan las ejecuciones perdidas sin duplicar facturas. Las facturas recurrentes estándar no pasan por aprobación, para mantener la automatización. Criterios de aceptación: tras una caída durante la generación, al reanudar no hay facturas duplicadas ni faltantes; el prorrateo es correcto en fin de mes y años bisiestos. Dependencias: FR9, FR14.

* **FR14.** Numeración, emisión y estados de la factura. Ciclo de vida integral (generación, envío y cobro). Series de numeración configurables por el superadministrador; el número único se asigna de forma segura (sin huecos ni duplicados) al emitir. Estados: borrador, pendiente de aprobación, aprobada, emitida, enviada, vencida, pagada, anulada, rectificada. «Pagada» solo se alcanza tras un cobro validado, y solo pueden borrarse los borradores. Criterios de aceptación: dos emisiones simultáneas nunca obtienen el mismo número; cada cambio de estado queda registrado con usuario y fecha. Dependencias: FR2, FR20.

* **FR15.** Inmutabilidad y rectificación. Una factura emitida no se puede editar ni borrar; cualquier corrección requiere una factura rectificativa con numeración única vinculada a la original. La rectificación exige permiso y, según el importe, aprobación de un administrador. El administrador puede además cerrar periodos de facturación. Criterios de aceptación: cualquier intento de modificar una factura emitida (UI o API) es rechazado y registrado. Dependencias: FR14, FR16.

* **FR16.** Flujo de aprobaciones. Validación previa de operaciones económicas sensibles: quedan en estado «Pendiente de aprobación», en una bandeja de validación, los grandes descuentos, importes atípicos, modificaciones manuales de importes o vencimientos, facturas puntuales que lo requieran y rectificaciones. Requieren la aprobación de un administrador distinto al creador (o del superadministrador). El administrador ve el motivo de retención y puede aprobar, rechazar o reasignar la validación a otro administrador; cada decisión deja registro de auditoría. Criterios de aceptación: una factura retenida no puede emitirse hasta ser aprobada; el creador no puede aprobar su propia factura. Dependencias: FR2, FR14.

* **FR17.** Documento PDF, firma digital y almacenamiento. PDF generado a partir de los datos con la identidad corporativa y conservado exactamente como se emitió. Firma digital obligatoria mediante certificado electrónico (cumplimiento legal, seguridad técnica y eficiencia). Almacenamiento redundante en soporte local y en la nube (copias frente a fallos). El acceso a documentos exige sesión iniciada o enlace temporal. Criterios de aceptación: toda factura emitida tiene un PDF firmado inalterable, disponible en ambos almacenamientos. Dependencias: FR14.

* **FR18.** Envío de facturas. Entrega al cliente tras la emisión: envío automático por correo electrónico a los contactos adecuados, con WhatsApp simulado como canal complementario. Se registran destinatarios, intentos, resultado y reintentos sin duplicar la factura. Si el correo rebota, no se marca como notificada, se avisa al responsable y se permite corregir el contacto y reenviar. Criterios de aceptación: un rebote genera notificación al responsable y la factura queda como «no notificada»; reenviar no crea una factura nueva. Dependencias: FR6, FR14, FR17.

### **E. Cobros**

* **FR19.** Recordatorios y recobro. Reducción de impagos mediante seguimiento automático: tras la emisión, recordatorio hacia el día 3 y otro al vencimiento (plazo por defecto: 5 días); después, flujo de impago cada 2–3 días hasta el pago o la intervención manual. Fechas, cadencia y plazos configurables por el superadministrador. Pausa de recordatorios por disputa (por factura) con motivo justificado y fecha de revisión. Al llegar la fecha de revisión, los recordatorios se reanudan automáticamente y se avisa al responsable del cliente. El responsable o un administrador pueden levantar la pausa antes de esa fecha. Criterios de aceptación: una factura pausada no genera recordatorios hasta su fecha de revisión; los cambios de estado quedan auditados. Dependencias: FR14, FR18, FR22.

* **FR20.** Cobros y conciliación. Vinculación de pagos con facturas: integración con Stripe (simulada en el proyecto, extensible a otras fuentes), de forma que al confirmarse el pago la factura pasa a «Pagada»; importación de movimientos de Revolut o transferencias. Idempotencia: un mismo aviso de pago recibido dos veces no duplica el cobro. Las discrepancias (importe distinto, referencia dudosa) pasan a una cola de revisión manual donde el administrador resuelve la diferencia o revierte conciliaciones previas, con registro de auditoría. Criterios de aceptación: un evento de pago duplicado se ignora; un cobro con importe distinto va a la cola de revisión. Dependencias: FR14, FR28.

### **F. Seguimiento y trabajo diario**

* **FR21.** Notificaciones internas y trabajo diario. Avisos sobre la cartera de clientes (pagos, impagos, rebotes, disputas, aprobaciones, vencimientos); cada usuario recibe solo las de sus clientes. Estados de notificación (leído / atendido) y prioridad. Pantalla de trabajo diario con las tareas y cobros pendientes. El superadministrador configura qué notificaciones llegan por qué canal (correo, interno…). Criterios de aceptación: un impago de un cliente solo notifica a su responsable; las tareas se agrupan por prioridad. Dependencias: FR4, FR19, FR20.

* **FR22.** Incidencias, comentarios y adjuntos. Gestión de problemas internos de facturación: una incidencia tiene responsable, prioridad y la opción de pausar los recordatorios de impago. Comentarios y adjuntos (limitados en tamaño y formato). Notas privadas exclusivas de administradores (visibles para un usuario si un administrador lo autoriza). Al cerrar una incidencia que pausaba los recordatorios, estos se reanudan automáticamente. Criterios de aceptación: un adjunto fuera de formato o tamaño es rechazado; una nota privada no es visible para usuarios estándar. Dependencias: FR2, FR5.

* **FR23.** Búsqueda avanzada, filtros y acciones masivas. Buscador global por NIF, nombre, número de factura o correo, con filtros combinables guardables como vistas predeterminadas. Acciones masivas seguras: confirmación previa y recuento exacto de elementos procesados y fallidos. Los resultados respetan los permisos del usuario. Criterios de aceptación: una acción masiva con fallos parciales informa exactamente de cuántos elementos se procesaron y cuáles fallaron. Dependencias: FR2.

### **G. Información y control**

* **FR24.** Dashboard y métricas financieras. Panel interactivo para administradores con previsión de ingresos, cobrado, vencido y mayores deudores. Desgloses de ingresos semanales, mensuales, trimestrales y anuales para el estudio analítico de clientes. Navegación hasta el máximo nivel de detalle (de agregado a factura). Objetivos mensuales o anuales; si cambian a medio plazo se deja constancia del vigente en cada periodo. Criterios de aceptación: los totales del dashboard coinciden con base de datos y PDFs. Dependencias: FR14, FR20.

* **FR25.** Informes, exportaciones y paquete para la gestoría. Exportación de informes en CSV y PDF que respetan los permisos de quien los solicita; se registra fecha/hora, filtros y usuario de cada exportación. Envíos programados de informes y paquete periódico ZIP de documentación para la gestoría (que no necesita usuario inicialmente). Las exportaciones se retienen un tiempo configurable y pueden regenerarse si datos y permisos lo permiten. Criterios de aceptación: dos usuarios con distintos permisos obtienen informes distintos con los mismos filtros; las exportaciones antiguas caducan solas. Dependencias: FR2, FR14, FR20.

* **FR26.** Auditoría e historial. Registro inalterable de la actividad: se registra quién, qué, cuándo (y valor anterior y nuevo) para toda acción relevante, en especial las económicas y de permisos. Panel de auditoría reservado al superadministrador (consulta, nunca edición). A nivel operativo, cada cliente tiene una línea de tiempo legible de su historial comercial. Criterios de aceptación: ningún rol puede modificar ni borrar registros de auditoría. Dependencias: FR2.

* **FR27.** Configuración, plantillas y salud del sistema. Parámetros globales del negocio aislados del uso diario: panel de configuración de impuestos, series de facturación, plazos, recordatorios, monedas y tarifas. Editor de plantillas de mensajes multidioma (inicialmente español e inglés), con variables dinámicas y vista previa. Pantalla de salud técnica: correos atascados, estado de backups y errores del servidor. Criterios de aceptación: cambiar un parámetro de configuración queda auditado y no altera datos históricos. Dependencias: FR2.

### **H. Integración**

* **FR28.** API e integraciones. API segura para otros sistemas: un sistema externo puede consultar si un cliente tiene deuda pendiente y notificar una solicitud de baja (esta crea una tarea, no borra el cliente). Las integraciones (Stripe, correo, Revolut) quedan aisladas tras interfaces: si falla un envío o un proveedor, la factura no se borra ni se corrompe. Criterios de aceptación: una llamada API sin credenciales válidas es rechazada; un fallo del proveedor de correo no altera la factura. Dependencias: FR2, FR11, FR14, FR20.


## **5. Requisitos No Funcionales**

*Definición:* Restricciones y atributos de calidad del sistema, estructurados rigurosamente con métricas de aceptación y métodos de verificación.

* **NFR1. Usabilidad y eficiencia de interacción**
* *Descripción:* Aplicación intuitiva, de curva de aprendizaje casi nula, con pocos clics en las operaciones frecuentes.
* *Métrica / Criterio de aceptación:* Un usuario que conoce el proceso administrativo crea un cliente estándar y deja preparada su suscripción en menos de 5 minutos con ayuda mínima; las funciones más comunes requieren como máximo 7 clics y 3 pantallas.
* *Método de verificación:* Pruebas de usabilidad cronometradas con al menos 5 personas y recuento de clics.
* *Dependencias:* FR5, FR12, FR23.


* **NFR2. Diseño e identidad corporativa**
* *Descripción:* La interfaz visual debe ajustarse estrictamente a los patrones de diseño y a la imagen de la empresa.
* *Métrica / Criterio de aceptación:* El 100% de las vistas principales usa los colores, tipografía y logo de la guía de estilo corporativa.
* *Método de verificación:* Revisión visual comparativa y auditoría de componentes frontend.


* **NFR3. Diseño responsive, accesibilidad y portabilidad**
* *Descripción:* Experiencia de uso fluida y adaptable en dispositivos de escritorio, tablets y móviles; las tareas urgentes deben poder realizarse desde el móvil.
* *Métrica / Criterio de aceptación:* Cumplimiento de las pautas WCAG 2.1 nivel AA y ausencia de desbordamientos y de barras de desplazamiento horizontal en un rango de resolución de 360px a 1920px.
* *Método de verificación:* Pruebas manuales multiplataforma (Chrome DevTools) y análisis automático con Lighthouse o Axe Core.


* **NFR4. Seguridad de documentos, credenciales y control de acceso**
* *Descripción:* Protección estricta de accesos, cifrado de comunicaciones y ocultación de secretos.
* *Métrica / Criterio de aceptación:* Contraseñas hasheadas de forma segura; datos fiscales y documentos cifrados; tráfico 100% HTTPS; enlaces temporales con caducidad máxima de 15 minutos; bloqueo del 100% de los accesos sin permiso (URL o API) mediante autorización centralizada; ausencia de secretos en el repositorio.
* *Método de verificación:* Pruebas de endpoints sin sesión, análisis de tráfico con Wireshark, escaneo con gitleaks, inspección de código y matriz de pruebas rol × recurso.
* *Dependencias:* FR1, FR2, FR17, FR25, FR28.


* **NFR5. Gestión de sesiones**
* *Descripción:* Control riguroso del ciclo de vida de la sesión de usuario.
* *Métrica / Criterio de aceptación:* Caducidad por inactividad a los 30 minutos, invalidación de sesiones de usuarios desactivados en menos de 1 minuto y bloqueo temporal tras 5 fallos de login.
* *Método de verificación:* Pruebas de integración de autenticación.
* *Dependencias:* FR1.


* **NFR6. Protección de datos y conservación legal**
* *Descripción:* Minimización y conservación de datos personales y contables conforme a la ley, sin invalidar facturas históricas.
* *Métrica / Criterio de aceptación:* Los datos de un contacto pueden anonimizarse sin romper las facturas antiguas donde aparece; las descargas temporales caducan y desaparecen solas.
* *Método de verificación:* Pruebas con datos ficticios: anonimizar o borrar contactos y comprobar que sus facturas siguen intactas, legales y accesibles.
* *Dependencias:* FR5, FR6, FR25, FR26.


* **NFR7. Fiabilidad transaccional ACID e idempotencia**
* *Descripción:* La base de datos debe garantizar transacciones ACID para evitar inconsistencias contables, pérdida de datos, duplicidades o dobles cobros durante eventos concurrentes.
* *Métrica / Criterio de aceptación:* Cero facturas o cobros duplicados ante dobles clics masivos o avisos de pago repetidos; cero anomalías de integridad o discrepancias contables en pruebas de estrés transaccional simultáneo.
* *Método de verificación:* Pruebas unitarias de concurrencia e integración sobre los flujos de pago; reenvío intencionado de cobros y clics simultáneos en un entorno de pruebas.
* *Dependencias:* FR13, FR14, FR18, FR20.


* **NFR8. Trazabilidad e inmutabilidad**
* *Descripción:* Todos los cambios económicos, transaccionales o de permisos deben ser completamente rastreables e inalterables en el pasado.
* *Métrica / Criterio de aceptación:* 100% de las acciones críticas registradas en los logs de auditoría sin posibilidad de modificación posterior por parte de los usuarios; una factura emitida jamás se altera.
* *Método de verificación:* Auditoría de registros del sistema, intentos de modificar o borrar históricos con distintos roles y análisis de restricciones de modificación en tablas históricas.
* *Dependencias:* FR14, FR15, FR20, FR26.


* **NFR9. Integridad matemática y financiera**
* *Descripción:* Operaciones financieras exactas y coherentes, libres de pérdida de datos o errores de redondeo (prohibido usar tipos `float`; usar tipos decimales o enteros en céntimos).
* *Métrica / Criterio de aceptación:* Cero errores de céntimos o desajustes entre PDF, base de datos y dashboard sobre un conjunto de al menos 1.000 facturas de prueba, incluyendo prorrateos en fin de mes y años bisiestos.
* *Método de verificación:* Pruebas unitarias automatizadas de prorrateo, impuestos y totales, y validación cruzada (conciliación automática PDF–BD–dashboard).
* *Dependencias:* FR12, FR13, FR24.


* **NFR10. Fechas y zonas horarias**
* *Descripción:* Almacenamiento consistente de marcas de tiempo y fechas legales basadas en horario de negocio.
* *Métrica / Criterio de aceptación:* Todos los instantes se almacenan en UTC y las fechas legales de factura usan la fecha de negocio.
* *Método de verificación:* Pruebas unitarias y de integración simulando eventos cercanos a la medianoche.
* *Dependencias:* FR13, FR14.


* **NFR11. Copias de seguridad y recuperación**
* *Descripción:* Políticas robustas de respaldo y recuperación ante fallos catastróficos.
* *Métrica / Criterio de aceptación:* Copia diaria automática, pérdida máxima de datos de 24 horas, restauración completa en menos de 4 horas y cero duplicidades tras una caída durante procesos masivos (por ejemplo, la generación recurrente).
* *Método de verificación:* Simulacro de restauración documentado y prueba de fallo por interrupción de procesos.
* *Dependencias:* FR13, FR17, FR27.


* **NFR12. Rendimiento en consultas y operaciones**
* *Descripción:* Agilidad en consultas frecuentes y procesamiento asíncrono para tareas pesadas.
* *Métrica / Criterio de aceptación:* El 95% de las peticiones de consulta y búsqueda responden en menos de 500 ms bajo una carga de 100 usuarios concurrentes; las búsquedas y fichas de cliente se abren en menos de 1 segundo incluso en el peor caso; las tareas pesadas se ejecutan en segundo plano con indicador de progreso visible.
* *Método de verificación:* Pruebas de carga con Apache JMeter o k6 y pruebas de operaciones asíncronas.
* *Dependencias:* FR5, FR12, FR23, FR24.


* **NFR13. Escalabilidad**
* *Descripción:* Capacidad de crecimiento sin degradación del rendimiento ni rediseño del modelo.
* *Métrica / Criterio de aceptación:* Tiempos de respuesta estables con 10.000 clientes y 500.000 facturas de prueba; paginación y filtrado en servidor (máximo 100 filas por petición, ninguna pantalla carga el histórico completo); la generación mensual procesa 3.000 clientes en un tiempo acotado y documentado.
* *Método de verificación:* Pruebas de carga y de volumen con datos sintéticos e inspección de consultas.
* *Dependencias:* FR13, FR23.

>>>>>>> 78ffabd4e119fa6b9f69a57e46d838166d6d5c05
