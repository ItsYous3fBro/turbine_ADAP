## Requisitos funcionales

## Gestión de facturas

RF-01 La factura debe poder modificarse (solo en el mismo trimestre en el que se emitió). RF-02 Las facturas tienen 3 estados: emitido, pagado e impagado. RF-03 El sistema genera y envía facturas y facturas recurrentes. RF-04 Las facturas se generan el día 1 de cada mes (recurrentes por lo general). RF-05 Si una empresa contrata el servicio en mitad de un mes, se le cobrará inmediatamente

una parte proporcional a los días restantes del mes.

RF-06 Firma de facturas con certificado digital. RF-07 Se envían las facturas a través de email. RF-08 Las facturas deben almacenarse de manera local, se enviarán al cliente y se guardarán

en la nube.

## Pagos y cobros

RF-09 La pasarela de pago será Stripe (probablemente). RF-10 Debe emitir recordatorios los días 3 y 5 en caso de que no se haya pagado todavía

(puesto que se debe pagar en 5 días).

RF-11 Caso de impago: enviar mensajes con periodicidad.

## Clientes

RF-12 Se deben guardar los siguientes datos del cliente: persona, compañía (nombre, NIF,

todos datos fiscales).

RF-13 Un cliente no puede darse de baja con facturas pendientes de pago.

## Niveles de suscripción

RF-14 Hay diferentes “niveles de suscripción”, el precio no es estático y depende de la

facturación de la empresa.

RF-15 (Opcional) Como se revisa “el nivel de suscripción” anualmente, se puede enviar una

notificación a los administradores cuando una empresa sobrepase la facturación máxima de su nivel.

## Usuarios, roles y acceso

RF-16 Login con nombre y contraseña. RF-17 Probablemente también con Microsoft. RF-18 Se debe estar logueado para poder usar la aplicación. RF-19 Un usuario con el rol de superadministrador tiene control total sobre el sistema (tal vez

pueda editar los permisos de los administradores como en Discord).

RF-20 El administrador tendrá control parcial sobre el sistema (no está muy claro todavía). RF-21 Los usuarios solo son lectores, no tienen control sobre el sistema.


## Dashboard y métricas

RF-22 El sistema proporciona a los administradores un dashboard con diferentes métricas.

RF-23 El sistema debe ofrecer a los administradores datos semanales, mensuales y

trimestrales.

## Requisitos no funcionales

## Usabilidad

RNF-01 Simplicidad absoluta de uso (probablemente bajo aprendizaje).

RNF-02 Prioridad: que hagan falta pocos clics.

## Interfaz y diseño

RNF-03 Estética igual a la de la web.

RNF-04 La interfaz debe estar correctamente organizada por categorías, por ejemplo las

facturas deben estar organizadas por sus 3 estados (emitida, pagada, impagada).

## Compatibilidad

RNF-05 Estará disponible para móviles, tablets y ordenadores.
