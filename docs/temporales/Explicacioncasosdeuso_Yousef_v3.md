\---



\## 4. Descripción de cada caso de uso



Todos los casos de los actores humanos requieren haber iniciado sesión (CU-01). El Lector solo actúa sobre los clientes que tiene asignados.



\### Acceso y usuarios



\*\*CU-01 · Iniciar sesión\*\* — \*Lector (y por herencia, todos)\* — \*\*FR1, NFR1\*\*

El usuario introduce su email corporativo y su contraseña; el sistema comprueba el dominio de Turbine y las credenciales y abre la sesión.

\*Alternativos:\* dominio no corporativo o credenciales erróneas → error; demasiados intentos fallidos → bloqueo temporal; sesión inactiva → expira.



\*\*CU-02 · Recuperar contraseña\*\* — \*extiende CU-01\* — \*\*FR1\*\*

Si el usuario olvidó la contraseña, solicita un enlace de recuperación que caduca tras un tiempo y define una nueva.



\*\*CU-03 · Gestionar usuarios y roles\*\* — \*Superadministrador\* — \*\*FR2, UR8\*\*

Invita, da de alta, edita y desactiva usuarios, asignándoles uno de los tres roles. Al desactivar un usuario se invalidan sus sesiones abiertas (FR1).



\*\*CU-04 · Asignar y delegar clientes\*\* — \*Administrador\* — \*\*FR3, UR4\*\*

Asigna a cada cliente un responsable. Puede delegarlo temporalmente (p. ej., por vacaciones); el sustituto hereda las tareas pendientes de ese cliente.



\### Clientes y suscripciones



\*\*CU-05 · Gestionar clientes y contactos\*\* — \*Lector\* — \*\*FR4, FR17, UR1\*\*

Da de alta o edita una empresa con sus datos fiscales y una o varias personas de contacto. El sistema detecta duplicados por identificador fiscal. Las bajas son lógicas y la ficha se puede reactivar. Desde la ficha se ve la línea de tiempo del cliente (FR17).

\*Alternativo:\* identificador fiscal ya existente → el sistema avisa y ofrece reactivar la ficha.



\*\*CU-06 · Importar clientes desde CSV\*\* — \*Administrador\* — \*\*FR4\*\*

Sube un CSV, revisa la previsualización y el mapeo de columnas, y confirma. El sistema muestra un informe de errores y un resumen. También permite importar facturas históricas marcadas como «migradas».



\*\*CU-07 · Gestionar suscripción\*\* — \*Lector\* — \*\*FR5, UR1\*\*

Crea la relación comercial del cliente (tramo de tarifa, fecha de inicio, posibles fechas futuras), la pausa con motivo y fecha de reactivación, o tramita la baja.

\*Alternativo:\* baja con deuda pendiente → se detienen las mensualidades pero sigue el recobro (CU-16).



\*\*CU-08 · Revisar tramo de tarifa anual\*\* — \*Administrador\* — \*\*FR5, UR5\*\*

Una vez al año, el sistema propone el nuevo tramo de cada cliente según su facturación anual; el administrador lo aprueba o lo rechaza. También aprueba descuentos y excepciones. Las facturas ya emitidas no cambian.



\### Facturación



\*\*CU-09 · Crear factura puntual\*\* — \*Lector\* — \*\*FR6, FR8, UR1, BR2\*\*

Elige el cliente, añade líneas (concepto, cantidad, precio, descuento, impuesto) o conceptos del catálogo, revisa la vista previa y confirma. Se ejecuta \*\*CU-10\*\*.

\*Alternativos:\* faltan datos fiscales → bloqueo con mensaje claro; importe fuera del patrón del cliente → aviso (y posible \*\*CU-11\*\*).



\*\*CU-10 · Emitir y enviar factura\*\* — \*incluido por CU-09, CU-13 y CU-14\* — \*\*FR8, FR10, NFR5, NFR7\*\*

El sistema asigna un número único de la serie, genera el PDF con la identidad corporativa, lo guarda tal como se emitió y lo envía por email al cliente (WhatsApp simulado como complemento). Registra destinatarios e intentos de envío.

\*Alternativo:\* el correo rebota → la factura no se marca como notificada y se avisa al responsable (CU-18) para corregir el contacto y reenviar.



\*\*CU-11 · Retener factura para aprobación\*\* — \*extiende CU-10\* — \*\*FR9\*\*

Condición: descuento grande, importe atípico o modificación manual. La factura queda en la bandeja de validación en estado «pendiente de aprobación» y no se emite hasta que se resuelva \*\*CU-12\*\*. Las recurrentes estándar nunca pasan por aquí.



\*\*CU-12 · Aprobar o rechazar factura retenida\*\* — \*Administrador\* — \*\*FR9, UR5, FR17\*\*

Un administrador \*\*distinto del que creó la factura\*\* ve el motivo de la retención y la aprueba (se emite), la rechaza o reasigna la validación a otro administrador. Cada decisión queda en la auditoría.



\*\*CU-13 · Emitir facturas recurrentes\*\* — \*Temporizador\* — \*\*FR7, BR1, NFR3, NFR5, NFR6\*\*

El día 1 de cada mes, el sistema genera la factura de cada suscripción activa (con prorrata en el primer mes) y ejecuta \*\*CU-10\*\* para cada una. No emite durante las pausas y registra cada ejecución.

\*Alternativo:\* el sistema estuvo caído → al recuperarse procesa la ejecución perdida \*\*sin duplicar\*\* facturas.



\*\*CU-14 · Rectificar o anular factura\*\* — \*Administrador\* — \*\*FR8, UR7, BR7, NFR7\*\*

Una factura emitida nunca se edita: el administrador crea una \*\*rectificativa\*\* vinculada a la original (que se emite mediante \*\*CU-10\*\*) o la anula, según permisos e importe. Solo los borradores se pueden borrar.



\*\*CU-15 · Consultar facturas y descargar PDF\*\* — \*Lector\* — \*\*FR10, FR15, UR1\*\*

Consulta las facturas de sus clientes filtrando por estado (borrador, emitida, vencida, pagada…) y descarga el PDF exactamente como se envió.



\*\*CU-30 · Gestionar presupuestos\*\* \*(opcional)\* — \*Lector\* — \*\*FR20\*\*

Crea un presupuesto y registra si el cliente lo acepta o lo rechaza. Si lo acepta, lo convierte en factura (\*\*CU-09\*\*) manteniendo ambos documentos vinculados.



\### Cobros y seguimiento



\*\*CU-16 · Enviar recordatorios y flujo de impago\*\* — \*Temporizador\* — \*\*FR11, BR1\*\*

Tras emitir una factura, el sistema envía un recordatorio el día 3 y otro al vencimiento (día 5). Después, uno cada 2–3 días hasta el cobro o una intervención manual. Cada seguimiento de impago se notifica al responsable (\*\*CU-18\*\*).

\*Alternativo:\* factura en disputa (CU-20) → los recordatorios se pausan hasta la fecha de revisión.



\*\*CU-17 · Registrar cobro\*\* — \*Pasarela de pago\* — \*\*FR12, NFR5\*\*

La pasarela informa de un pago; el sistema lo vincula a su factura, la marca como pagada, detiene los recordatorios y ejecuta \*\*CU-18\*\*.

\*Alternativos:\* el mismo aviso llega dos veces → se registra una sola vez (idempotencia); importe o referencia que no cuadran → pasa a la cola de \*\*CU-19\*\*.



\*\*CU-18 · Notificar al responsable\*\* — \*incluido por CU-16 y CU-17\* — \*\*FR13, UR2\*\*

El sistema avisa al usuario asignado al cliente del evento (cobro, impago, rebote, disputa o aprobación) por los canales configurados y lo añade a su pantalla de trabajo diario.



\*\*CU-19 · Resolver conciliación manual\*\* — \*Administrador\* — \*\*FR12, FR17\*\*

Revisa los cobros que no se han podido vincular automáticamente, los asigna a la factura correcta o revierte una conciliación errónea. Cada acción queda en la auditoría.



\*\*CU-20 · Gestionar incidencias y disputas\*\* — \*Lector\* — \*\*FR14, FR11, UR1\*\*

Registra una incidencia sobre una factura o cliente (responsable, prioridad, comentarios, adjuntos) y, si hay una disputa, pausa los recordatorios con motivo y fecha de revisión. Los administradores pueden añadir notas privadas.



\*\*CU-21 · Consultar trabajo diario\*\* — \*Lector\* — \*\*FR13, UR2\*\*

Ve en una sola pantalla las tareas, cobros pendientes y avisos de su cartera de clientes, y los marca como leídos o atendidos.



\*\*CU-22 · Buscar y ejecutar acciones masivas\*\* — \*Lector\* — \*\*FR15, UR3, NFR2\*\*

Busca por NIF, nombre, número de factura o email, combina filtros y los guarda como vistas. Puede lanzar acciones masivas: el sistema pide confirmación y muestra cuántos elementos se procesaron con éxito y cuántos fallaron.



\### Información, control e integración



\*\*CU-23 · Consultar dashboard\*\* — \*Administrador\* — \*\*FR16, UR6\*\*

Ve las métricas (previsión de ingresos, cobrado, vencido, mayores deudores, objetivos) y navega hasta el detalle.



\*\*CU-24 · Exportar informes\*\* — \*extiende CU-23\* — \*\*FR16, UR6, NFR8, NFR9\*\*

Exporta a CSV o PDF respetando sus permisos; el sistema registra quién, cuándo y con qué filtros. Puede programar envíos automáticos y el paquete periódico para la gestoría. Las exportaciones caducan tras un tiempo configurable.



\*\*CU-25 · Consultar auditoría\*\* — \*Superadministrador\* — \*\*FR17, UR9, NFR7\*\*

Consulta el registro inalterable de todas las acciones (quién tocó qué y cuándo). Puede leerlo, pero nadie puede modificarlo.



\*\*CU-26 · Configurar sistema y plantillas\*\* — \*Superadministrador\* — \*\*FR18, FR13, UR8\*\*

Configura impuestos, series de facturación, plazos de pago y recordatorios, monedas, tarifas y qué notificaciones llegan a cada canal. Edita las plantillas de mensajes (español e inglés) con variables y vista previa.



\*\*CU-27 · Consultar salud del sistema\*\* — \*Superadministrador\* — \*\*FR18, UR9, NFR6\*\*

Revisa correos atascados, el estado de las copias de seguridad y los errores del servidor.



\*\*CU-28 · Consultar estado de cliente (API)\*\* — \*Sistema externo\* — \*\*FR19, BR5\*\*

Un sistema autorizado consulta por API el estado de un cliente (p. ej., si tiene facturas pendientes).



\*\*CU-29 · Solicitar baja externa (API)\*\* — \*Sistema externo\* — \*\*FR19, FR5\*\*

Un sistema externo pide la baja de un cliente. El sistema \*\*no la ejecuta directamente\*\*: crea una tarea para que un responsable la revise (y se tramite con CU-07).



\---

