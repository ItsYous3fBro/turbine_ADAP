\###NOTAS para turbine H dueños de  BrainOS

Obj: Hacer un sistema de facturación interna que emita, gestione y realice un seguimiento de facturas y  pagos. Sistema para facturar a mis clientes (yo registró al cliente/base de datos de clientes).

La factura:

* Pocos clics.
* La factura se debe poder modificar (solo dentro de un mismo trimestre)
* Se paga en 5 días (recordatorio día 3 y 5)
* Flujo de impagos tras día 5: mensaje con periodicidad (no has pagado…)
* Investigar *verifactus* (sist. legis. facturas) → no tiene por qué satisfacerlo (pregunta Juanchi)
* Firmar facturas con certificado digital
* Siempre que un cliente se dé de baja hay que gestionar su facturación (no puedes darte de baja hasta que saldes todas tus facturas impagadas)
* Enviar facturas por mensajes a través de email
* Que el sistema refleje a nivel notable cómo va la facturación con los clientes, en una especie de dashboard con metricas disponible solo para los administradores(informes anuales).
* Solo se dedica a envío de facturas y seguimiento del pago, la pasarela de pago sera stripe. Qué hacemos cuando se ha recibido el pago →. lista de clientes, facturación y seguimiento de cobros.
* Debe de funcionar en Móviles, tablets, portátiles
* 3 roles: 1 SuperAdministrador que lo pueda ver, controlar y configurar todo, administradores que pueden hacer cosas parecidas al administrador y configurar parte sistema, resto seran usuarios,  lectores que solo puede ver lo que se le ha asignado
* Login con nombre y contraseña de usuarios→ TurbineH → Iniciar sesion de Microsoft
* Hace falta API/ documentación —> SI hace falta
* Datos semanales, mensuales y trimestrales (solo visible para administradores)
* Datos cliente: persona, compañía (nombre, NIF, todos datos fiscales)
* Anualmente se revisa el nivel de fact de la compañía y se comprueba si cambia o no.
* El sistema que genera las facturas distingue entre facturas y facturas recurrentes (se envian las 2)
* Habilitar uso de firma/certificado digital
* Tipos de planes mensuales (a final de año se mira si cambiar o no de plan ):
* Cada usuario (trabajador) se encarga de factura exclusivamente a X empresa
* Precio de factura depende de la facturacion anual del cliente (ver imagen)
* Factura 3 estados: Emitido(pendiente de cobro), pagado, e impagado -> Recurrencia
* Estetica web igual a la suya(turbineH)
* Facturacion el dia 1, si se contrata antes, una factura puntual de la diferencia de mes restante.
* Dividir las categorias para que sean visibles, los 3 estados de factura, poder separar para visualizar las facturas de cada tipo.
* Base de datos para guardar clientes y facturas.
* Facturas se guardan de manera local, se envian al cliente y se guardan en la nube
* Importacion y encriptacion¿?
* HAcer login antes de usar la app
* SOLO CORREO CORPORATIVO

\###PREGUNTAS

* Enviar facturas al gestor¿?
* Proveedor de la suite empresarial(correo)¿?
* 

\###NOTAS

* Solución lo más eficiente posible, preferible ingeniería tradicional a IA.
* Mantener información cliente máximo posible.

