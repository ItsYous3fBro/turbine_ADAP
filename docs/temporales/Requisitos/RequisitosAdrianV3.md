# **Documento de requisitos: sistema de facturación**

## **Requisitos de negocio**

* **BR1:** Automatizar la generación, el envío y el cobro de las facturas recurrentes y puntuales.  
* **BR2:** Reducir los impagos mediante recordatorios y seguimiento automáticos.  
* **BR3:** Garantizar el control y la trazabilidad de las facturas y de las decisiones económicas (tarifas, excepciones, cambios).  
* **BR4:** Reducir el tiempo y el número de clics necesarios para las operaciones frecuentes.  
* **BR5:** Facilitar la gestión de la información de los clientes y sus suscripciones.  
* **BR6:** Dar a los administradores visibilidad de la situación financiera mediante métricas.  
* **BR7:** Permitir que el sistema crezca en clientes, usuarios, países y divisas sin rehacerlo.

  ## **Requisitos de usuario**

  ### **Usuario**

* **UR1:** El usuario podrá consultar y modificar la información de los clientes que tiene asignados.  
* **UR2:** El usuario podrá crear facturas puntuales y previsualizarlas antes de emitirlas.  
* **UR3:** El usuario podrá seguir el estado de cobro de sus clientes (vencimientos, impagos y pagos recibidos).  
* **UR4:** El usuario podrá proponer excepciones comerciales para un cliente.  
* **UR5:** El usuario podrá abrir incidencias de facturación y comentarlas.  
* **UR6:** El usuario podrá pausar los recordatorios de una factura indicando el motivo.  
* **UR7:** El usuario recibirá notificaciones sobre sus clientes y verá lo que tiene pendiente.  
* **UR8:** El usuario podrá buscar clientes y facturas.

  ### **Administrador**

* **UR9:** El administrador podrá aprobar las excepciones y las facturas pendientes de aprobación.  
* **UR10:** El administrador podrá asignar y desasignar clientes a los usuarios y delegarlos temporalmente.  
* **UR11:** El administrador podrá crear, invitar y desactivar usuarios.  
* **UR12:** El administrador podrá conciliar manualmente los pagos dudosos.  
* **UR13:** El administrador podrá consultar el dashboard y exportar informes.

  ### **Superadministrador**

* **UR14:** El superadministrador podrá configurar las tarifas por nivel, las series de numeración y los plazos y la cadencia de recordatorios.  
* **UR15:** El superadministrador podrá gestionar los permisos de los administradores.  
* **UR16:** El superadministrador podrá consultar la auditoría de acciones.

  ### **Sistema externo**

* **UR17:** Un sistema externo podrá consultar si un cliente tiene deuda pendiente y notificar una solicitud de baja.

  ## **Requisitos funcionales**

  ### **Gestión de facturas**

* **FR1:** La factura se puede modificar antes de emitirla. Después, cualquier cambio relevante debe ser trazable y, si legalmente no procede modificarla, se hace con una factura rectificativa o el procedimiento equivalente.  
* **FR2:** Las facturas tienen varios estados: borrador, aprobada/lista para emitir, emitida, enviada, vencida, pagada, parcialmente conciliada, anulada (cuando legalmente proceda) y rectificada (nombres provisionales).  
* **FR3:** El sistema genera y envía facturas puntuales y recurrentes.  
* **FR4:** Las facturas recurrentes se generan el día 1 de cada mes.  
* **FR5:** Si una empresa contrata a mitad de mes, se le cobra enseguida la parte proporcional de los días que quedan.  
* **FR6:** El cálculo de la parte proporcional es editable y se ve antes de confirmar el alta.  
* **FR7:** No se asumen meses de 30 días. Se documenta la regla elegida, incluido el caso de alta el último día del mes.  
* **FR8:** Una factura puede tener varias líneas (concepto, cantidad, precio, descuento, impuesto). Existe un catálogo ligero de conceptos, y si un concepto cambia, las facturas antiguas no.  
* **FR9:** Antes de emitir hay vista previa. Si falta un dato fiscal imprescindible se bloquea la emisión y se dice qué falta.  
* **FR10:** Los cambios manuales de importe, los descuentos extraordinarios y las facturas de importe muy alto quedan pendientes de aprobación antes de enviarse. El sistema explica por qué.  
* **FR11:** Nadie puede alterar una factura en silencio. Todo cambio deja rastro.  
* **FR12:** Las facturas tienen numeración ordenada.  
* **FR13:** Las series de numeración las configura el superadministrador. El número es único dentro de la serie aunque dos personas emitan a la vez, y los borradores no consumen número.  
* **FR14:** Las rectificativas parten de la factura original, quedan vinculadas a ella y se conserva el PDF original emitido (no se regenera con plantillas nuevas).  
* **FR15:** Las facturas se firman con certificado digital.  
* **FR16:** Las facturas se envían por email.  
* **FR17:** Las facturas se guardan en local, se envían al cliente y también se guardan en la nube.  
* **FR18:** Cada envío queda registrado (canal, destinatario, fecha, resultado), distinguiendo el original de los reintentos. Reintentar no duplica la factura. Si el correo rebota, no se marca como notificada y se avisa al responsable.

  ### **Pagos y cobros**

* **FR19:** La pasarela de pago será Stripe (probablemente).  
* **FR20:** Stripe se simula con eventos de pago. Si llega dos veces el mismo evento, el cobro no se contabiliza dos veces.  
* **FR21:** La conciliación asocia cada movimiento a una factura y admite otras fuentes además de Stripe. Si hay dudas, falta referencia o el importe no coincide, queda para revisión manual. Toda conciliación se puede revertir dejando rastro.  
* **FR22:** Se envían recordatorios los días 3 y 5 si la factura sigue sin pagar (plazo de 5 días).  
* **FR23:** Los plazos y la cadencia de recordatorios son configurables. Si el cliente tiene un plazo pactado, ese manda. Se pueden pausar los recordatorios de una factura (disputa) con motivo y fecha de revisión.  
* **FR24:** Si hay impago, se envían mensajes periódicamente.

  ### **Clientes**

* **FR25:** Se guardan los datos de la empresa y de las personas (nombre, NIF y datos fiscales).  
* **FR26:** Un cliente es una empresa con varias personas físicas. Al enviar una factura se registran todas las personas implicadas.  
* **FR27:** Hay una vista para ver de un vistazo todos los datos del cliente y su suscripción. No se llama "contrato".  
* **FR28:** El cliente tiene estados (pendiente de activación, activo, pausado, baja solicitada, inactivo) y cada cambio tiene consecuencias explícitas.  
* **FR29:** Al crear un cliente se avisa de posibles duplicados por NIF. Los clientes no se borran, se desactivan (borrado lógico), y nunca se fusionan automáticamente.  
* **FR30:** Un cliente puede darse de baja aunque tenga facturas pendientes. Se dejan de generar mensualidades desde la fecha de baja, pero el recobro de lo anterior sigue vivo. Hay que poder consultar si hay deuda.  
* **FR31:** Se pueden importar clientes desde CSV, con mapeo de columnas, previsualización, aviso de duplicados y resumen de creados, rechazados y errores.  
* **FR32:** La suscripción se puede pausar con fecha de inicio y fin. Al llegar el fin se avisa al responsable.

  ### **Suscripciones y tarifas**

* **FR33:** Hay distintos niveles de suscripción y el precio depende de la facturación de la empresa.  
* **FR34:** Las tarifas por nivel no están en el código. El superadministrador las modifica fácilmente, sin tocar facturas históricas.  
* **FR35:** (Opcional) Como el nivel se revisa cada año, se avisa a los administradores cuando una empresa supera la facturación máxima de su nivel.  
* **FR36:** Un cambio de tarifa lo aprueba un humano y se registra quién.  
* **FR37:** Se contemplan excepciones, como una oferta para un cliente concreto.  
* **FR38:** Un usuario puede proponer una excepción, pero la aprueba un administrador.  
* **FR39:** Es excepción cualquier cosa extraordinaria (precios anormales, ofertas, descuentos…), incluso al darse de alta.

  ### **Usuarios, roles y acceso**

* **FR40:** Se entra con usuario y contraseña.  
* **FR41:** Probablemente también con cuenta de Microsoft.  
* **FR42:** Hay que estar logueado para usar la aplicación.  
* **FR43:** No hay autorregistro. El superadministrador o un administrador con permiso crea o invita usuarios, con email del dominio corporativo. Al desactivar a alguien se conserva el rastro de lo que hizo.  
* **FR44:** El superadministrador tiene control total (quizá pueda editar permisos de los administradores).  
* **FR45:** El administrador tiene control parcial (límites por definir).  
* **FR46:** El usuario operativo solo trabaja sobre los clientes que tiene asignados. Puede ver y modificar su información operativa, pero no cambia tarifas ni configuración global.  
* **FR47:** Un administrador asigna y desasigna clientes a usuarios y puede delegarlos temporalmente (vacaciones). El nuevo responsable hereda las tareas pendientes.

  ### **Seguimiento y control**

* **FR48:** Se pueden abrir incidencias de facturación (cliente y factura opcional) con responsable, estado, prioridad, comentarios internos y resolución. Una incidencia puede pausar recordatorios.  
* **FR49:** Hay notificaciones internas (pago recibido, factura vencida, rebote, aprobación…) con prioridad y estado leída/resuelta, y una pantalla de trabajo diario con lo pendiente.  
* **FR50:** Los administradores tienen un dashboard con distintas métricas.  
* **FR51:** Los administradores ven datos semanales, mensuales y trimestrales.  
* **FR52:** Hay informes filtrables (periodo, cliente, responsable, estado, tipo…) exportables a CSV y PDF respetando los permisos del usuario. Las cifras del dashboard se pueden rastrear hasta las facturas que las suman.  
* **FR53:** Hay búsqueda global por nombre, razón social, NIF, contacto o número de factura.

  ### **Sistema e integración**

* **FR54:** Debe poder usarse como API (consultar deuda, clientes y facturas, recibir solicitudes de baja) sin saltarse las reglas de la interfaz.  
* **FR55:** Los procesos automáticos (generación mensual, recordatorios, conciliación) se pueden reintentar sin duplicar nada. Dejan registro de ejecución y un fallo en un cliente no detiene al resto.  
* **FR56:** Hay auditoría de las acciones de negocio (quién creó, cambió, aprobó, emitió o reasignó), no editable por usuarios normales.

  ## **Requisitos no funcionales**

  ### **Usabilidad**

* **NFR1:** Curva de aprendizaje.  
  * **Descripción:** Muy simple de usar, con curva de aprendizaje casi nula.  
  * **Métrica / Criterio de aceptación:** Un usuario que conoce el proceso administrativo pero no la aplicación crea un cliente estándar y deja preparada su suscripción en menos de 5 minutos, con ayuda mínima.   
  * **Método de verificación:** Prueba de usabilidad cronometrada.  
* **NFR2:** Pocos clics.  
  * **Descripción:** Pocos clics y pocas ventanas diferentes.  
  * **Métrica / Criterio de aceptación:** Las funciones más usadas o más comunes requieren como máximo 7 clics y 3 pantallas.  
  * **Método de verificación:** Recuento de clics  durante las pruebas de usabilidad.  
* **NFR3:** Coherencia visual.  
  * **Descripción:** La estética es igual a la de la web.  
  * **Métrica / Criterio de aceptación:** El 100 % de las vistas principales usa los colores, la tipografía y el logo definidos en la guía de estilo de la web.  
  * **Método de verificación:** Revisión visual comparativa.  
* **NFR4:** Organización por categorías.  
  * **Descripción:** La interfaz se organiza por categorías (por ejemplo, facturas agrupadas por estado).  
  * **Métrica / Criterio de aceptación:** Las facturas de cualquier estado son accesibles en 3 clics como máximo desde la pantalla principal.  
  * **Método de verificación:** Prueba manual de navegación y pruebas de usabilidad.  
* **NFR5:** Usabilidad responsive.  
  * **Descripción:** Disponible en móviles, tabletas y ordenadores.  
  * **Métrica / Criterio de aceptación:** Debe verse correctamente en cualquier dispositivo movil.  
  * **Método de verificación:** Pruebas manuales con alguna aplicación. (Probablemente Chrome Dev Tools)  
* **NFR6:** Accesibilidad.  
  * **Descripción:** Accesible: etiquetas y contraste correctos, manejable con teclado y con zoom, controles sin depender de pasar el ratón y errores claros ("falta el NIF", no "error").  
  * **Métrica / Criterio de aceptación:** Todos los mensajes de error muestran texto comprensible, sin códigos técnicos.  
  * **Método de verificación:** Análisis y pruebas manuales, tambien con teclado y zoom al 200 %.

    ### **Datos**

* **NFR7:** Modelo de datos abierto.  
  * **Descripción:** Modelo de datos abierto: documentos fiscales de cualquier país (no solo NIF) y cualquier divisa (no solo euro).  
  * **Métrica / Criterio de aceptación:** Se pueden crear clientes de cualquier país con identificadores fiscales distintos y enviar facturas correctamente sin modificar el código (con configuración por parte del usuario).  
  * **Método de verificación:** Pruebas de integración con casos de varios países y divisas y revisión del modelo de datos.  
* **NFR8:** Precisión numérica.  
  * **Descripción:** Los importes usan precisión decimal (sin float), con regla de redondeo documentada. El PDF, la base de datos y el dashboard cuadran siempre.  
  * **Métrica / Criterio de aceptación:** Diferencia de 0 céntimos entre PDF, base de datos y dashboard sobre un conjunto de al menos 1.000 facturas de prueba. No se usan tipos float en importes.  
  * **Método de verificación:** Pruebas unitarias con casos de redondeo, comparación automática de totales e inspección de código.  
* **NFR9:** Fechas y zonas horarias.  
  * **Descripción:** Los instantes se guardan de forma consistente y las fechas legales de factura usan la fecha de negocio, no la hora del servidor.  
  * **Métrica / Criterio de aceptación:** Todos los instantes se almacenan en UTC.   
  * **Método de verificación:** Pruebas unitarias y de integración con eventos simulados cerca de la medianoche.

    ### **Seguridad**

* **NFR10:** Seguridad de documentos y credenciales.  
  * **Descripción:** Los documentos no tienen URLs públicas adivinables (los enlaces temporales caducan). Las contraseñas no se guardan en texto plano, las comunicaciones van cifradas y los secretos no están en el repositorio.  
  * **Métrica / Criterio de aceptación:** El 100 % de las descargas exige autenticación y permiso. Los enlaces temporales caducan en 15 minutos como máximo. Las contraseñas se almacenan con hash seguro. Todo el tráfico usa HTTPS. No hay secretos en el repositorio.  
  * **Método de verificación:** Pruebas de endpoints sin sesión y con usuario sin permiso, análisis de tráfico con Wireshark, escaneo de secretos con gitleaks o similares, e inspección de código.  
* **NFR11:** Gestión de sesiones.  
  * **Descripción:** Las sesiones caducan por inactividad, se invalidan al desactivar al usuario y hay protección básica contra intentos de login fallidos.  
  * **Métrica / Criterio de aceptación:** La sesión caduca tras 30 minutos de inactividad. Las sesiones de un usuario desactivado se invalidan en menos de 1 minuto. Tras 5 intentos fallidos consecutivos se aplica un bloqueo temporal.  
  * **Método de verificación:** Pruebas de integración de autenticación.

    ### **Rendimiento y fiabilidad**

* **NFR12:** Rendimiento en consultas.  
  * **Descripción:** Las búsquedas y aperturas habituales son casi inmediatas, y las operaciones pesadas (informes grandes, PDFs masivos) van en segundo plano con estado visible.  
  * **Métrica / Criterio de aceptación:**Las búsquedas y aperturas de cliente o factura responde en menos de 1000 ms con 100 usuarios concurrentes. Las operaciones pesadas muestran estado (pendiente, procesando, completado con errores) y no bloquean la interfaz.  
  * **Método de verificación:** Pruebas de carga con Apache JMeter o similares  y prueba manual de operaciones en segundo plano.  
* **NFR13:** Escalabilidad.  
  * **Descripción:** Escala a miles de clientes, cientos de miles de facturas y cientos de usuarios sin rehacer el modelo. Las tablas se paginan en servidor.  
  * **Métrica / Criterio de aceptación:** Con 10.000 clientes y 500.000 facturas de prueba se mantiene el criterio de NFR12. Ninguna petición devuelve más de 100 filas.  
  * **Método de verificación:** Pruebas de carga con datos sintéticos e inspección de las consultas y de la paginación.  
* **NFR14:** Copias de seguridad y recuperación.  
  * **Descripción:** Copias de seguridad automáticas con política definida y restauración probada. Tras una caída no se pierde ni se duplica ninguna emisión.  
  * **Métrica / Criterio de aceptación:** Copia automática diaria de datos y documentos, con una pérdida de datos máxima de 24 horas y una restauración completa en menos de 4 horas. Tras una caída simulada durante la generación mensual no hay facturas duplicadas ni perdidas.  
  * **Método de verificación:** Ejecución documentada del procedimiento de restauración en un entorno de pruebas y prueba de fallo (interrumpir el proceso mensual y relanzarlo).

    ### **Mantenibilidad**

* **NFR15:** Mantenibilidad y testabilidad.  
  * **Descripción:** Monolito modular, con cada regla en un único sitio y la configuración fuera del código. Las reglas importantes (prorrateo, impuestos, permisos, conciliación) llevan pruebas automáticas.  
  * **Métrica / Criterio de aceptación:** Alta cobertura de pruebas en los módulos de reglas de negocio. Cambiar un parámetro de configuración (por ejemplo, la cadencia de recordatorios) se hace en un único lugar.  
  * **Método de verificación:** Informe de cobertura de la herramienta de pruebas, revisión de código y análisis estático.  
* **NFR16:** Integraciones encapsuladas.  
  * **Descripción:** Las integraciones (Stripe, correo, WhatsApp) están encapsuladas, de modo que cambiar un proveedor no obliga a tocar el modelo de factura.  
  * **Métrica / Criterio de aceptación:** Sustituir un proveedor de pagos simulado por otro requiere implementar solo la interfaz de integración, sin modificar ninguna entidad del modelo de factura.  
  * **Método de verificación:** Prueba añadiendo un segundo proveedor simulado y revisión de dependencias entre módulos.  
    

