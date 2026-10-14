# Documento de requisitos

## 1\. Requisitos de negocio

* **BR1.** Automatización de la facturación y seguimiento de cobros para conseguir reducir el trabajo administrativo manual.
* **BR2.** Conseguir que las operaciones sean muy rápidas (pocos clics).
* **BR3.** Ofrecer una aplicación capaz de ser usada en cualquier lugar y dispositivo.
* **BR4.** Escalar la capacidad administrativa para ser capaz de gestionar a miles de clientes y facturas junto a decenas o cientos de usuarios sin tener que rehacer todo el modelo.
* **BR5.** Mantener la aplicación como un producto totalmente independiente del núcleo de la empresa conocido como Brain, aunque esto está abierto a integrarse en un futuro mediante una API.
* **BR7.** Conservar un control sobre los temas legales y económicos. Esto se aplica a conservar históricos según la ley, facilitar trabajo con gestoría o no alterar facturas históricas.

## 2\. Requisitos de usuario

Aquí distinguiremos entre varios usuarios:

### Usuario operativo / lector / empleado

* **UR1.** Debe poder dar de alta a clientes y contactos que tenga asignados, y crear sus facturas puntuales o recurrentes al igual que tener la capacidad de la consulta del estado de los cobros, descarga de documentos y marca de incidencias.
* **UR2.** Debe recibir notificaciones y tareas de únicamente los clientes que tiene a su cargo.
* **UR3.** Debe tener la posibilidad de buscar con rapidez a sus clientes o facturas y podrá guardar filtros frecuentes.

### Administrador

* **UR4.** Debe tener la posibilidad de asignar, designar y delegar temporalmente clientes a usuarios operativos.
* **UR5.** Podrá aprobar o rechazar descuentos, cambios de tarifa e importes sensibles.
* **UR6.** Tendrá acceso al dashboard financiero y podrá generar informes y exportar datos de este.
* **UR7.** Podrá rectificar facturas y cerrar periodos de facturación.

### Superadministrador

* **UR8.** Debe poder configurar el sistema (plazos, recordatorios, impuestos, tarifas anuales, parámetros del sistema, plantillas, etc.) y gestionar usuarios.
* **UR9.** Podrá consultar la salud del sistema y la auditoría completa (no podrá reescribirla) teniendo acceso más profundos que el administrador.

## 3\. Requisitos funcionales

Vamos a separarlos un poco por categorías:

### Acceso y usuarios

* **FR1. Autenticación y sesiones.** El acceso será solamente permitido con un email del dominio corporativo (@turbine) y contraseña, no habrá autorregistro y se permitirá la recuperación de contraseña con enlace caducable, la expiración de sesión por inactividad, la protección ante intentos fallidos e invalidación de sesiones al desactivar un usuario.
* **FR2. Usuarios, roles y permisos.** Mediante un modelo coherente de autorización único se dará el alta, la invitación y la desactivación de usuarios con los tres roles mencionados previamente con los permisos que conllevan.

  * Dependencias: FR1.
* **FR3. Asignación de clientes y delegaciones.** Cada cliente tiene un responsable interno/usuario operativo asignado por el administrador (también puede delegar clientes temporalmente y el nuevo responsable hereda las tareas pendientes del cliente).

  * Dependencias: FR2, FR4.

### Clientes y suscripciones

* **FR4. Clientes, contactos e importación.** Registro de empresas con datos comerciales y fiscales (nombre comercial, razón social, identificador fiscal sin asumir formato español, domicilio, país, moneda), una o varias personas de contacto (activas o inactivas, se mantendrá un histórico de a quién se envió cada factura), detección de duplicados por identificador fiscal, borrado lógico y reactivación de fichas. Se dará una importación CSV con previsualización, mapeo de columnas, informe de errores y resumen de resultados; importación de histórico de facturas como "migradas".

  * Dependencias: FR2.
* **FR5. Suscripción, tarifas y ciclo de vida del cliente.** Se debe llevar un ficha comercial (no factura) estableciendo la relación comercial. Existirá una tabla de tarifas por rango de facturación anual guardada como configuración, sin alterar facturas históricas. Anualmente se revisará esta tarifa con propuesta de rango y aprobación humana, principalmente del administrador, habrán descuentos y excepciones con aprobación del administrador. Para las suscripciones se tendrán en cuenta las fechas de inicios futuras y, pausas con motivos y fechas de reactivación. Cuando un cliente solicite su baja, se detendrán nuevas mensualidades pero se mantendrá el recobro de la deuda previa y podrá recuperar su ficha antes de un duplicado siempre y cuando la legalidad permita mantener la información.

  * Dependencias: FR4, FR9, FR11.

### Facturación

* **FR6. Facturas puntuales.** Se podrán realizar facturas con varias líneas con datos cómo el concepto, cantidad, precio, descuento e impuesto configurable que contarán con una vista previa antes de ser emitidas, bloqueos con mensajes claros si faltan datos fiscales y algún tipo de aviso configurable si el importe se sale del patrón del cliente, también se llevarán conceptos frecuentes en un catálogo (la modificación de un concepto no modifica el histórico).

  * Dependencias: FR4, FR8.
* **FR7. Facturación recurrente automática.** Se generará de forma mensual el día 1 a partir de la suscripción, con prorrateo del primer mes (visible antes de confirmar el alta), excepciones explícitas (sin prorrateo para facturas con inicio futuro), no se emitirán facturas durante pausas (aunque se mandará un aviso cuando llegue la fecha acordada previamente de la pausa y se mantendrá el histórico) y se llevará un registro de cada ejecución con recuperación de ejecuciones perdidas sin duplicar.

  * Dependencias: FR5, FR8.
* **FR8. Numeración, emisión y ciclo de vida de la factura.** Tendremos series configurables por el superadministrador con número único asignado de forma segura al emitir; estados analizados a partir del ciclo real (borrador, pendiente de aprobación, emitida, enviada, vencida, pagada, anulada, rectificada…) con "pagada" solo tras cobro validado. La factura emitida no será editable (cambios mediante rectificativa vinculada, con permiso y aprobación según importe), sólo se permitirá el borrado de borradores bajo las restricciones propuestas.

  * Dependencias: FR2, FR9.
* **FR9. Aprobaciones.** El sistema debe retener temporalmente cualquier factura con condiciones inusuales (grandes descuentos, importes atípicos o modificaciones manuales) en una bandeja de validación. Para ser emitidas, requerirán la aprobación de un usuario administrador distinto al que las creó. Las facturas recurrentes estándar se emitirán automáticamente sin pasar por este proceso para no perder la automatización. Además, el sistema debe permitir a los administradores visualizar el motivo de la retención, aprobar, rechazar o reasignar la validación a otro administrador dejando un registro de auditoría de cada decisión.

  * Dependencias: FR2, FR18.
* **FR10. Documento PDF y envío.** Un PDF generado a partir de los datos con la identidad corporativa y conservado tal como se emitió se enviará automaticamente por email (WhatsApp simulado como canal complementario) registrando destinatarios, intento, resultado y reintentos sin duplicar la factura, si el correo rebota, no se marca como notificada, se avisa al responsable y se permite corregir el contacto y reenviar.

  * Dependencias: FR4, FR8, FR18.

### Cobros

* **FR11. Recordatorios y recobro.** Tras la emisión de una factura se generará recordatorio hacia el día 3 y al vencimiento (por defecto 5 días), después se producirá un flujo de impago cada 2-3 días hasta el pago o la intervención manual. Estos tiempos serán configurables y se dará la pausa de recordatorios por disputa con motivo y fecha de revisión, cancelación o "incobrable" solo con acción administrativa controlada.

  * Dependencias: FR8, FR10, FR14.
* **FR12. Cobros y conciliación.** El sistema procesará eventos de pago de pasarelas externas (simulando Stripe, pero extensible a otras fuentes) para vincularlos automáticamente con las facturas correspondientes. En caso de discrepancias (importes distintos o referencias dudosas), el cobro pasará a una cola de revisión manual donde los administradores podrán resolver la diferencia o revertir conciliaciones previas, registrándose cada acción en el historial de auditoría.

  * Dependencias: FR8, FR17.

### Seguimiento y trabajo diario

* **FR13. Notificaciones internas y trabajo diario.** El sistema notificará a los responsables sobre eventos clave de su cartera de clientes (pagos, impagos, rebotes, disputas o aprobaciones) mediante canales configurables y estados de prioridad (leído/atendido), centralizando todas estas alertas en una pantalla de trabajo diario que mostrará visualmente las tareas y cobros pendientes de resolver. El superadministrador debe poder elegir qué notificaciones le llegan a qué canal (correo, interno, etc.)

  * Dependencias: FR3, FR11, FR12.
* **FR14. Incidencias, comentarios y adjuntos.** El sistema permitirá registrar problemas internos asociados a la facturación (asignando un responsable, prioridad y la opción de pausar temporalmente los recordatorios de impago). Admitirá subir documentos adjuntos limitados en peso/formato y añadir comentarios, incluyendo notas privadas exclusivas para los administradores (visibles para usuario si alguien lo requiere).

  * Dependencias: FR2, FR4.
* **FR15. Búsqueda, filtros y acciones masivas.** El sistema contará con un buscador global (por NIF, nombre, número de factura o email) y filtros combinables que podrán guardarse como vistas predeterminadas. Permitirá ejecutar acciones masivas de forma segura: exigirá confirmación previa y mostrará un recuento exacto de los elementos procesados con éxito y los que fallaron.

  * Dependencias: FR2.

### Información y control

* **FR16. Dashboard, informes y exportaciones.** Los administradores tendrán un dashboard interactivo con métricas clave (previsiones de ingresos, cobrado, vencido, mayores deudores) navegables hasta el máximo nivel de detalle e información cómo objetivos mensuales o anuales (si se cambia a medio plazo hay que dejar constancia del vigente). El sistema permitirá exportar informes (CSV/PDF) que respeten estrictamente los permisos del usuario que los pide (hay que registrar fecha/hora, filtros usados y usuario que lo generó), además de poder programar envíos automáticos y generar un paquete periódico empaquetado para la gestoría (esta no necesita tener usuario inicialmente). Las exportaciones se retendrán por un tiempo configurable y podrán ser regeneradas si los datos y permisos lo permiten.

  * Dependencias: FR2, FR8, FR12.
* **FR17. Auditoría e historial.** El sistema registrará de forma inalterable absolutamente todo lo que ocurra (quién tocó qué dato y cuándo) en un panel reservado para el superadministrador. A nivel operativo, cada cliente tendrá una línea de tiempo visual y fácil de leer con su historial comercial.

  * Dependencias: FR2.
* **FR18. Configuración, plantillas y salud del sistema.** Los parámetros fijos del negocio (impuestos, series de facturación, plazos, monedas) se configurarán en un panel aislado del uso diario. El sistema incluirá un editor multidioma (inicialmente español e inglés) para las plantillas de mensajes (con variables dinámicas y vista previa) y una pantalla de "salud técnica" para revisar correos atascados, estado de los backups o errores del servidor.

  * Dependencias: FR2.

### Integración

* **FR19. API e integraciones.** El sistema expondrá una API segura para que otros softwares consulten el estado de los clientes o envíen solicitudes controladas (por ejemplo, si una app externa pide dar de baja a un cliente, el sistema creará una tarea para revisarlo, en lugar de borrarlo automáticamente). Las conexiones con herramientas como Stripe o el email estarán aisladas para que, si falla un envío, la factura no se borre ni se corrompa.

  * Dependencias: FR5, FR8, FR12.
* **FR20. Presupuestos (ampliación opcional).** El sistema permitirá emitir presupuestos comerciales y registrar su estado (aceptado/rechazado). Si un cliente lo acepta, el presupuesto podrá transformarse automáticamente en una factura real para no tener que teclear todo de nuevo, manteniendo ambos documentos vinculados en el historial.

  * Dependencias: FR6.

## 4\. Requisitos no funcionales

* **NFR1. Seguridad integral (cifrado y control de acceso).** Protección de información sensible y autorización estricta para garantizar que cada usuario acceda solo a lo que le corresponde.

  * Dependencias: FR1, FR10, FR2, FR3, FR16, FR19.
  * Métrica / Criterio de aceptación: Se cifrarán las contraseñas, datos fiscales y documentos. Tráfico 100% HTTPS. El sistema bloqueará el 100% de los intentos de acceso sin permiso (URL o API) usando un modelo de autorización centralizado. El código fuente no expondrá claves y el acceso a documentos exigirá sesión iniciada o enlace temporal.
  * Método de verificación: Inspección del código, uso de herramientas de ciberseguridad para buscar brechas y matriz de pruebas automáticas/manuales cruzando todos los roles con todos los recursos (incluyendo intentos de copia directa de URLs).
* **NFR2. Rendimiento y eficiencia de uso.** Las pantallas y búsquedas habituales responden de forma casi inmediata; lo pesado va en segundo plano. Operativa rápida con pocos clics, sin necesidad de aprendizaje previo.

  * Dependencias: FR15, FR16, FR4, FR6.
  * Métrica / Criterio de aceptación: El sistema debe ser rápido y fluido, garantizando que las búsquedas y las fichas de clientes se abran en menos de 1 segundo (incluso en el peor de los casos). Para evitar que la pantalla se quede "congelada", las operaciones pesadas trabajarán por detrás, mostrando únicamente un indicador visual de progreso al usuario.
  * Método de verificación: Se someterá al sistema a pruebas de carga o "estrés" utilizando programas especiales que inyectarán datos inventados masivos, para comprobar que el sistema aguanta el tirón sin volverse lento. Pruebas de usabilidad con al menos 5 personas, midiendo tiempos y clics.
* **NFR3. Escalabilidad.** Crecer en clientes, facturas y usuarios sin rehacer el modelo.

  * Dependencias: FR15, FR7.
  * Métrica / Criterio de aceptación: Paginación y filtrado en servidor (ninguna pantalla carga el histórico completo); la generación mensual procesa 3.000 clientes en un tiempo acotado y documentado.
  * Método de verificación: Pruebas de volumen con datos sintéticos y revisión de las consultas.
* **NFR4. Diseño responsive, accesibilidad e identidad corporativa.** Interfaz utilizable en móvil, tablet y escritorio, accesible y fiel a la imagen de Turbine.

  * Dependencias: FR13, FR16.
  * Métrica / Criterio de aceptación: La web se adaptará perfectamente a móviles, tablets y monitores sin barras de desplazamiento horizontales. Desde el móvil se podrán hacer tareas urgentes, respetando siempre los colores y logos de Turbine.
  * Método de verificación: Simular distintas pantallas con Chrome DevTools y usar herramientas automáticas (como Lighthouse) para comprobar que el diseño encaja y es accesible.
* **NFR5. Fiabilidad e integridad transaccional.** Transacciones seguras sin pérdida de datos, duplicidades ni errores matemáticos. Importes exactos y fechas coherentes.

  * Dependencias: FR7, FR8, FR10, FR12, FR6, FR16.
  * Métrica / Criterio de aceptación: Transacciones ACID reales. Cero facturas o cobros duplicados aunque los usuarios hagan dobles clics masivos o la pasarela envíe el mismo aviso de pago dos veces. Importes cuadrados al céntimo (sin errores de redondeo float) y totales que coincidan exactamente en PDF, base de datos y dashboard.
  * Método de verificación: Enviar cobros repetidos aposta y simular clics simultáneos masivos en un entorno de pruebas para confirmar que el sistema bloquea los duplicados. Además, pruebas unitarias de prorrateo, impuestos y totales (incluyendo fin de mes y años bisiestos) y conciliación automática PDF–BD–dashboard.
* **NFR6. Disponibilidad y recuperación.** Las caídas breves no pierden emisiones y los datos son recuperables.

  * Dependencias: FR7, FR18.
  * Métrica / Criterio de aceptación: Copias de seguridad diarias. Si el servidor se cae, el sistema debe recuperarse en menos de 4 horas sin perder datos ni duplicar las facturas que se estaban generando durante el apagón.
  * Método de verificación: Simulacro de restauración documentado y prueba de caída durante la generación recurrente.
* **NFR7. Trazabilidad e inmutabilidad.** Todo cambio económico o de permisos es rastreable y el pasado no se puede reescribir.

  * Dependencias: FR8, FR12, FR17.
  * Métrica / Criterio de aceptación: Todo queda grabado en el registro de auditoría (quién, qué y cuándo) y es imborrable. Una factura ya emitida es sagrada y jamás se puede alterar.
  * Método de verificación: Intentar modificar o borrar registros e históricos con diferentes tipos de usuario para comprobar que el sistema bloquea cualquier manipulación.
* **NFR8. Protección de datos y conservación.** Minimización y conservación de datos personales y contables según la ley, sin invalidar facturas históricas.

  * Dependencias: FR4, FR16, FR17.
  * Métrica / Criterio de aceptación: Los datos privados de un contacto se pueden ocultar (anonimizar) sin que esto rompa las facturas antiguas donde aparecía. Las descargas de datos temporales caducan y desaparecen solas.
  * Método de verificación: Hacer pruebas con datos inventados, borrar contactos y confirmar que sus facturas históricas siguen intactas, legales y accesibles.
* **NFR9. Mantenibilidad, compatibilidad y despliegue.** Código testeado, infraestructura reproducible y soporte multinavegador e internacional.

  * Dependencias: FR10, FR16, FR18, FR19.
  * Métrica / Criterio de aceptación: Al menos el 80% de la lógica de negocio y económica tendrá pruebas automáticas. Existirán manuales claros para instalar el sistema desde cero. Los archivos exportados (CSV) se codificarán en UTF-8 (sin caracteres rotos). Las plantillas admitirán multidioma (es/en) y funcionarán en cualquier navegador moderno.
  * Método de verificación: Revisión de cobertura de código, prueba de despliegue siguiendo únicamente el manual de instalación, y apertura en distintos navegadores exportando textos con caracteres especiales.

