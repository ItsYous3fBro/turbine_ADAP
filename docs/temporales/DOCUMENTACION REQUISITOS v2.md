# DOCUMENTACION DE REQUISITOS

Grupo: Ramón Llull

3º B


## Definición del caso:

TurbineH es una startup de origen español. La cual tiene un producto que es Brain una

IA destinada a ayudar a la organización de empresas. En este caso nos piden una aplicación para gestionar las facturas y a los clientes,

además de generar métricas de los ingresos del negocio.

## Requisitos de negocio

## Definición:

Son las metas, objetivos y resultados de alto nivel que explican por qué se realiza un proyecto o una iniciativa de cambio en una organización

## Lista de requisitos:

- BR1. Garantizar que el software de facturación funcione de manera independiente de Brain.

- BR2. Automatización de la facturación y seguimiento de cobros para conseguir reducir el trabajo administrativo manual.

- BR3. Conservar un control sobre los temas legales y económicos. Esto se aplica a conservar históricos según la ley, facilitar trabajo con gestoría o no alterar facturas históricas.

- BR4. Ofrecer una aplicación capaz de ser usada en cualquier lugar y dispositivo.

- BR5. Dar a los administradores visibilidad de la situación financiera mediante métricas.

- BR6. Permitir que el sistema crezca en clientes, usuarios, países y divisas sin rehacerlo.


## Requisitos de usuario

## Definición:

Son las especificaciones del sistema, software, máquina o producto debe hacer para satisfacer las necesidades y expectativas de quien lo va a utilizar. En nuestro caso, hay tres tipos de usuario, administrador y el súper administrador, cada uno con más nivel de permisos que el

anterior.

## Lista de requisitos: Usuario

- UR1 El usuario podrá consultar y modificar la información de los clientes que tiene asignados.

- UR2 El usuario podrá crear facturas puntuales y pre-visualizarlas antes de emitirlas.

- UR5 El usuario podrá abrir incidencias de facturación y comentarlas.

- UR6 El usuario podrá pausar los recordatorios de una factura indicando el motivo.

- UR7 El usuario recibirá notificaciones sobre sus clientes y verá lo que tiene pendiente.

- UR8 El usuario podrá buscar clientes y facturas.

## Administrador

- UR9 El administrador podrá aprobar las excepciones y las facturas pendientes de aprobación.

- UR10 El administrador podrá asignar y desasignar clientes a los usuarios y delegarlos temporalmente.

- UR11 El administrador podrá crear, invitar y desactivar usuarios.

- UR12 El administrador podrá conciliar manualmente los pagos dudosos.

- UR13 El administrador podrá consultar el dashboard y exportar informes.


## Súper administrador

- UR14 El súper administrador podrá configurar las tarifas por nivel, las series de numeración y los plazos y la cadencia de recordatorios.

- UR15 El súper administrador podrá gestionar los permisos de los administradores.

- UR16 El súper administrador podrá consultar la auditoría de acciones.

## Requisitos funcionales

## Definición:

Un requisito funcional son el conjunto de características, acciones y reglas del negocio que el sistema debe de hacer. Son visibles e interactivas para los usuarios.

## Lista de requisitos:

- FR1. División en 3 roles según la cantidad de permisos que tenga el usuario, para controlar que acciones puedan hacer cada usuario y así no comprometer la seguridad y datos de la empresa, además de tener un rol especial para los clientes.

- FR2. Detectar clientes duplicados, el sistema debe avisar si se intenta registrar una empresa con un identificador fiscal que ya existe.

- FR3. Autentificación obligatoria mediante correo empresarial @turbieh o a través de cuenta de Microsoft, el cliente solicito que la aplicación solo pudiera ser utilizada por empleados afiliados a su empresa, además de la compatibilidad con Microsoft para acelerar procesos al trabajar con empresas o particulares.

- FR4. Generar, enviar y cobrar facturas, acción principal para la cual la app es diseñada. Las facturas se enviarán mediante correo electrónico.

- FR5. Generación de las facturas a el día uno de cada mes, para tener un periodo constante de envío de facturas que facilite la gestión de varios clientes a la vez. Si una empresa contrata el servicio en mitad de un mes, se


cobrará inmediatamente una parte proporcional a los días restantes del mes.

- FR6. Firma de las facturas por certificado digital, obligatoriedad legal, seguridad técnica y eficiencia.

- FR7. Gestión del almacenamiento de las facturas mediante guardado local y en la nube, es recomendable tener dos copias de tus archivos por si alguna de las dos fallara.

- FR8. Múltiples contactos a una misma empresa comercial, administración, impagos y guardar el historial de a qué destinatario se envió cada factura.

- FR9. Importar y exportar datos: El sistema deberá permitir importar y exportar datos de clientes a través de archivos CSV, además de crear periódicamente un archivo comprimido ZIP con la documentación necesaria para la gestoría. Esto ayudara a que no haya errores a la hora de tratar los datos en las facturas etc.

- FR10. Recordatorio de impago en los días 3 y 5 del mes, en caso de impago enviar mensajes con cierta periodicidad, el cliente tiene 5 días para pagar desde que se le remite la factura, para reducir casos de impago el sistema se encarga de enviar avisos.

- FR11. El sistema debe generar desgloses semanales, mensuales, trimestrales y anuales, para tener una estadística de ingresos y datos de cada cliente y así estudiarlos.

- FR12. Del cliente se debe guardar: datos personales del representante, nombre de la compañía, NIF y datos fiscales. Necesario para saber con quién estamos haciendo negocio (mandar las facturas, comunicación entre empresas…).

- FR13. Un cliente no puede darse de baja con facturas pendientes de pago, algo obvio para evitar impagos.

- FR14. Hay diferentes “niveles de suscripción”, el precio no es estático y depende de la facturación de la empresa. Regla impuesta por el cliente, ya que, su empresa tiene esas tarifas.


- FR15. El sistema debe permitir la modificación de datos de las facturas tienen 3 estados (emitido, pagado, impagado), y así tener un registro de en qué estado se encuentra cada factura.

- FR16. El sistema debe conectarse con Stripe para procesar los cobros y actualizar automáticamente el estado de la factura a "Pagada" tras recibir la confirmación de la transacción. El sistema deberá permitir registrar e importar operaciones bancarias procedentes de distintas fuentes, como Revolut o transferencias bancarias.

- FR17. Rectificar facturas: Las facturas emitidas llevarán una numeración única. Una factura emitida no se podrá editar ni borrar, si luego se necesita corregir la factura deberá realizarse mediante una factura rectificativa.

- FR18. Cualquier modificación en el importe, hacer descuentos, cambio de vencimiento o factura puntual debe quedar en estado Pendiente de aprobación hasta que la valide un perfil con rol de Administrador o SuperAdmin.

- FR19. Búsqueda, filtros y acciones masivas. El sistema contará con un buscador global (por NIF, nombre, número de factura o email) y filtros combinables que podrán guardarse como vistas predeterminadas.


## Requisito no funcional

## Definición:

Un requisito no funcional es una condición o estándar de calidad que define cómo debe comportarse un sistema y qué tan bien debe realizar sus tareas.

## Lista de requisitos:

- NFR1. Rendimiento, seguro y de fácil uso, algo esencial para cualquier programa.

- NFR2. Diseño acorde a los patrones y estética de la empresa, para tener una misma identidad en todos los programas de la empresa.

- NFR3. Portabilidad a móviles, Tablet, y ordenadores, la app debe de poder ser usada en cualquier medio para que efectuar pagos y cobros sea más cómodo, y no sea necesario un ordenador.

- NFR4. La base de datos debe garantizar transacciones ACID, para evitar inconsistencias contables o dobles cobros durante el procesamiento concurrente de eventos de pago.

- NFR5. Trazabilidad e inmutabilidad. Todo cambio económico o de permisos es rastreable y el pasado no se puede reescribir.

- NFR6. Fiabilidad e integridad transaccional. Transacciones seguras sin pérdida de datos, duplicidades ni errores matemáticos. Importes exactos y fechas coherentes.
