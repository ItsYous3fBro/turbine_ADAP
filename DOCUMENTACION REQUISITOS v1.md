# DOCUMENTACION DE REQUISITOS FUNCIONALES Y NO FUNCIONALES

## Grupo: Ramón Llull

Trabajo TurbineH

3º B


## Requisitos funcionales

## Definición:

Un requisito funcional son el conjunto de características, acciones y reglas del negocio que el sistema debe de hacer. Son visibles e interactivas para los usuarios.

## Lista de requisitos:

- RF-01. Autentificación obligatoria mediante correo empresarial @turbieh o a través de cuenta de Microsoft, el cliente solicito que la aplicación solo pudiera ser utilizada por empleados afiliados a su empresa, además de la compatibilidad con Microsoft para acelerar procesos al trabajar con empresas o particulares.

- RF-02. División en 3 roles según la cantidad de permisos que tenga el usuario, para controlar que acciones puedan hacer cada usuario y así no comprometer la seguridad y datos de la empresa, además de tener un rol especial para los clientes.

- RF-03. Generar, enviar y cobrar facturas, acción principal para la cual la app es diseñada. Las facturas se enviarán mediante correo electrónico.

- RF-04. Generación de las facturas a el día uno de cada mes, para tener un periodo constante de envío de facturas que facilite la gestión de varios clientes a la vez. Si una empresa contrata el servicio en mitad de un mes, se cobrará inmediatamente una parte proporcional a los días restantes del mes.

- RF-05. Firma de las facturas por certificado digital, obligatoriedad legal, seguridad técnica y eficiencia.

- RF-06. Gestión del almacenamiento de las facturas mediante guardado local y en la nube, es recomendable tener dos copias de tus archivos por si alguna de las dos fallara.

- RF-07. Recordatorio de impago en los días 3 y 5 del mes, en caso de impago enviar mensajes con cierta periodicidad, el cliente tiene 5 días para pagar desde que se le remite la factura, para reducir casos de impago el sistema se encarga de enviar avisos.


- RF-08. El sistema debe generar desgloses semanales, mensuales, trimestrales y anuales, para tener una estadística de ingresos y datos de cada cliente y así estudiarlos.

- RF-09. Del cliente se debe guardar: datos personales del representante, nombre de la compañía, NIF y datos fiscales. Necesario para saber con quién estamos haciendo negocio (mandar las facturas, comunicación entre empresas…).

- RF-10. Un cliente no puede darse de baja con facturas pendientes de pago, algo obvio para evitar impagos.

- RF-11. Hay diferentes “niveles de suscripción”, el precio no es estático y depende de la facturación de la empresa. Regla impuesta por el cliente, ya que, su empresa tiene esas tarifas.

- RF-12. El sistema debe permitir la modificación de datos de las facturas tienen 3 estados (emitido, pagado, impagado), y así tener un registro de en qué estado se encuentra cada factura.

- RF-13. El sistema debe conectarse con Stripe para procesar los cobros y actualizar automáticamente el estado de la factura a "Pagada" tras recibir la confirmación de la transacción. Es la plataforma de pago en la nube elegida por el cliente.


## Requisito no funcional

## Definición:

Un requisito no funcional es una condición o estándar de calidad que define cómo debe comportarse un sistema y qué tan bien debe realizar sus tareas.

## Lista de requisitos:

- RNF-01. Rendimiento, seguro y de fácil uso, algo esencial para cualquier programa.

- RNF-02. Diseño acorde a los patrones y estética de la empresa, para tener una misma identidad en todos los programas de la empresa.

- RNF-03. Portabilidad a móviles, Tablet, y ordenadores, la app debe de poder ser usada en cualquier medio para que efectuar pagos y cobros sea más cómodo, y no sea necesario un ordenador.

- RNF-04. La base de datos debe garantizar transacciones ACID, para evitar inconsistencias contables o dobles cobros durante el procesamiento concurrente de eventos de pago.
