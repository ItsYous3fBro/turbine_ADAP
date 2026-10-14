# Requisitos del sistema de facturación

## Requisitos funcionales

### Gestión de facturas
- **RF-01:** La factura se puede modificar antes de emitirla. Después, cualquier cambio relevante debe ser trazable y, si legalmente no procede modificarla, se hace con una factura rectificativa o el procedimiento equivalente.
- **RF-02:** Las facturas tienen varios estados: borrador, aprobada/lista para emitir, emitida, enviada, vencida, pagada, parcialmente conciliada, anulada (cuando legalmente proceda) y rectificada (nombres provisionales).
- **RF-03:** El sistema genera y envía facturas puntuales y recurrentes.
- **RF-04:** Las facturas recurrentes se generan el día 1 de cada mes.
- **RF-05:** Si una empresa contrata a mitad de mes, se le cobra enseguida la parte proporcional de los días que quedan.
- **RF-06:** El cálculo de la parte proporcional es editable y se ve antes de confirmar el alta.
- **RF-07:** No se asumen meses de 30 días. Se documenta la regla elegida, incluido el caso de alta el último día del mes.
- **RF-08:** Una factura puede tener varias líneas (concepto, cantidad, precio, descuento, impuesto). Existe un catálogo ligero de conceptos, y si un concepto cambia, las facturas antiguas no.
- **RF-09:** Antes de emitir hay vista previa. Si falta un dato fiscal imprescindible se bloquea la emisión y se dice qué falta.
- **RF-10:** Los cambios manuales de importe, los descuentos extraordinarios y las facturas de importe muy alto quedan pendientes de aprobación antes de enviarse. El sistema explica por qué.
- **RF-11:** Nadie puede alterar una factura en silencio. Todo cambio deja rastro.
- **RF-12:** Las facturas tienen numeración ordenada.
- **RF-13:** Las series de numeración las configura el superadministrador. El número es único dentro de la serie aunque dos personas emitan a la vez, y los borradores no consumen número.
- **RF-14:** Las rectificativas parten de la factura original, quedan vinculadas a ella y se conserva el PDF original emitido (no se regenera con plantillas nuevas).
- **RF-15:** Las facturas se firman con certificado digital.
- **RF-16:** Las facturas se envían por email.
- **RF-17:** Las facturas se guardan en local, se envían al cliente y también se guardan en la nube.
- **RF-18:** Cada envío queda registrado (canal, destinatario, fecha, resultado), distinguiendo el original de los reintentos. Reintentar no duplica la factura. Si el correo rebota, no se marca como notificada y se avisa al responsable.

### Pagos y cobros
- **RF-19:** La pasarela de pago será Stripe (probablemente).
- **RF-20:** Stripe se simula con eventos de pago. Si llega dos veces el mismo evento, el cobro no se contabiliza dos veces.
- **RF-21:** La conciliación asocia cada movimiento a una factura y admite otras fuentes además de Stripe. Si hay dudas, falta referencia o el importe no coincide, queda para revisión manual. Toda conciliación se puede revertir dejando rastro.
- **RF-22:** Se envían recordatorios los días 3 y 5 si la factura sigue sin pagar (plazo de 5 días).
- **RF-23:** Los plazos y la cadencia de recordatorios son configurables. Si el cliente tiene un plazo pactado, ese manda. Se pueden pausar los recordatorios de una factura (disputa) con motivo y fecha de revisión.
- **RF-24:** Si hay impago, se envían mensajes periódicamente.

### Clientes
- **RF-25:** Se guardan los datos de la empresa y de las personas (nombre, NIF y datos fiscales).
- **RF-26:** Un cliente es una empresa con varias personas físicas. Al enviar una factura se registran todas las personas implicadas.
- **RF-27:** Hay una vista para ver de un vistazo todos los datos del cliente y su suscripción. No se llama "contrato".
- **RF-28:** El cliente tiene estados (pendiente de activación, activo, pausado, baja solicitada, inactivo) y cada cambio tiene consecuencias explícitas.
- **RF-29:** Al crear un cliente se avisa de posibles duplicados por NIF. Los clientes no se borran, se desactivan (borrado lógico), y nunca se fusionan automáticamente.
- **RF-30:** Un cliente puede darse de baja aunque tenga facturas pendientes. Se dejan de generar mensualidades desde la fecha de baja, pero el recobro de lo anterior sigue vivo. Hay que poder consultar si hay deuda.
- **RF-31:** Se pueden importar clientes desde CSV, con mapeo de columnas, previsualización, aviso de duplicados y resumen de creados, rechazados y errores.
- **RF-32:** La suscripción se puede pausar con fecha de inicio y fin. Al llegar el fin se avisa al responsable.

### Suscripciones y tarifas
- **RF-33:** Hay distintos niveles de suscripción y el precio depende de la facturación de la empresa.
- **RF-34:** Las tarifas por nivel no están en el código. El superadministrador las modifica fácilmente, sin tocar facturas históricas.
- **RF-35:** (Opcional) Como el nivel se revisa cada año, se avisa a los administradores cuando una empresa supera la facturación máxima de su nivel.
- **RF-36:** Un cambio de tarifa lo aprueba un humano y se registra quién.
- **RF-37:** Se contemplan excepciones, como una oferta para un cliente concreto.
- **RF-38:** Un usuario puede proponer una excepción, pero la aprueba un administrador.
- **RF-39:** Es excepción cualquier cosa extraordinaria (precios anormales, ofertas, descuentos…), incluso al darse de alta.

### Usuarios, roles y acceso
- **RF-40:** Se entra con usuario y contraseña.
- **RF-41:** Probablemente también con cuenta de Microsoft.
- **RF-42:** Hay que estar logueado para usar la aplicación.
- **RF-43:** No hay autorregistro. El superadministrador o un administrador con permiso crea o invita usuarios, con email del dominio corporativo. Al desactivar a alguien se conserva el rastro de lo que hizo.
- **RF-44:** El superadministrador tiene control total (quizá pueda editar permisos de los administradores).
- **RF-45:** El administrador tiene control parcial (límites por definir).
- **RF-46:** El usuario operativo solo trabaja sobre los clientes que tiene asignados. Puede ver y modificar su información operativa, pero no cambia tarifas ni configuración global.
- **RF-47:** Un administrador asigna y desasigna clientes a usuarios y puede delegarlos temporalmente (vacaciones). El nuevo responsable hereda las tareas pendientes.

### Seguimiento y control
- **RF-48:** Se pueden abrir incidencias de facturación (cliente y factura opcional) con responsable, estado, prioridad, comentarios internos y resolución. Una incidencia puede pausar recordatorios.
- **RF-49:** Hay notificaciones internas (pago recibido, factura vencida, rebote, aprobación…) con prioridad y estado leída/resuelta, y una pantalla de trabajo diario con lo pendiente.
- **RF-50:** Los administradores tienen un dashboard con distintas métricas.
- **RF-51:** Los administradores ven datos semanales, mensuales y trimestrales.
- **RF-52:** Hay informes filtrables (periodo, cliente, responsable, estado, tipo…) exportables a CSV y PDF respetando los permisos del usuario. Las cifras del dashboard se pueden rastrear hasta las facturas que las suman.
- **RF-53:** Hay búsqueda global por nombre, razón social, NIF, contacto o número de factura.

### Sistema e integración
- **RF-54:** Debe poder usarse como API (consultar deuda, clientes y facturas, recibir solicitudes de baja) sin saltarse las reglas de la interfaz.
- **RF-55:** Los procesos automáticos (generación mensual, recordatorios, conciliación) se pueden reintentar sin duplicar nada. Dejan registro de ejecución y un fallo en un cliente no detiene al resto.
- **RF-56:** Hay auditoría de las acciones de negocio (quién creó, cambió, aprobó, emitió o reasignó), no editable por usuarios normales.

---

## Requisitos no funcionales

### Usabilidad
- **RNF-01:** Muy simple de usar, con curva de aprendizaje casi nula.
- **RNF-02:** Pocos clics y pocas ventanas diferentes.
- **RNF-03:** La estética es igual a la de la web.
- **RNF-04:** La interfaz se organiza por categorías (por ejemplo, facturas agrupadas por estado).
- **RNF-05:** Disponible en móviles, tabletas y ordenadores.
- **RNF-06:** Accesible: etiquetas y contraste correctos, manejable con teclado y con zoom, controles sin depender de pasar el ratón y errores claros ("falta el NIF", no "error 422").

### Datos
- **RNF-07:** Modelo de datos abierto: documentos fiscales de cualquier país (no solo NIF) y cualquier divisa (no solo euro).
- **RNF-08:** Los importes usan precisión decimal (sin `float`), con regla de redondeo documentada. El PDF, la base de datos y el dashboard cuadran siempre.
- **RNF-09:** Los instantes se guardan de forma consistente y las fechas legales de factura usan la fecha de negocio, no la hora del servidor.

### Seguridad
- **RNF-10:** Los documentos no tienen URLs públicas adivinables (los enlaces temporales caducan). Las contraseñas no se guardan en texto plano, las comunicaciones van cifradas y los secretos no están en el repositorio.
- **RNF-11:** Las sesiones caducan por inactividad, se invalidan al desactivar al usuario y hay protección básica contra intentos de login fallidos.

### Rendimiento y fiabilidad
- **RNF-12:** Las búsquedas y aperturas habituales son casi inmediatas, y las operaciones pesadas (informes grandes, PDFs masivos) van en segundo plano con estado visible.
- **RNF-13:** Escala a miles de clientes, cientos de miles de facturas y cientos de usuarios sin rehacer el modelo. Las tablas se paginan en servidor.
- **RNF-14:** Copias de seguridad automáticas con política definida y restauración probada. Tras una caída no se pierde ni se duplica ninguna emisión.

### Mantenibilidad
- **RNF-15:** Monolito modular, con cada regla en un único sitio y la configuración fuera del código. Las reglas importantes (prorrateo, impuestos, permisos, conciliación) llevan pruebas automáticas.
- **RNF-16:** Las integraciones (Stripe, correo, WhatsApp) están encapsuladas, de modo que cambiar un proveedor no obliga a tocar el modelo de factura.
