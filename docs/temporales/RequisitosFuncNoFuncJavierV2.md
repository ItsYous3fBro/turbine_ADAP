# Requisitos funcionales y No funcionales V2 (ampliación de BORRADOR)

---

## Requisitos de Negocio

* **BR-01.** Garantizar que el software de facturacion funcione de manera independiente en cuanto a la IA (Brain) que es el producto que vende Turbine.
* **BR-02.** Se debe poder gestionar miles de clientes de manera eficiente.
* **BR-03.** Todo el trabajo de cobros, facturas etc debe llevarlo a acabo el software de manera eficiente evitando trabajo de más a los trabajadores.
* **BR-04.** Cumplir con la normativa sobre privacidad y conservación de datos.
* **BR-05.** Tanto la direccion, admin y altos cargos en la empresa deben tener una visión ya sea en un panel o lo que sea, de como va el dinero de la empresa de forma clara de todos los clientes.

---

## Requisitos de usuario

* **UR-01.** El empleado podrá crear nuevas fichas de clientes, incluyendo sus datos comerciales, la información de la persona de contacto y la tarifa acordada.
* **UR-02.** El admin podrá importar grandes cantidades de datos de clientes mediante archivos CSV y revisar una vista previa de los posibles errores antes de guardar la información.
* **UR-03.** El admin podrá gestionar un proceso de aprobación para validar los cambios en los precios, los descuentos especiales y las facturas puntuales de importes elevados.
* **UR-04.** El usuario podrá conciliar los pagos recibidos a través de pasarelas de pago o cuentas bancarias. Cuando los importes no coincidan exactamente, se hace una peticion de revision.
* **UR-05.** El empleado podrá crear y gestionar incidencias internas relacionadas con la facturación, por si hay que suspender temporalmente los recordatorios de pago y resolver las peticiones de los clientes.
* **UR-06.** El empleado podrá exportar la información financiera en formatos CSV y PDF.

---

## Requisitos Funcionales

* **RF1. Gestion del cliente:** Se debe controlar los estados del cliente si esta activo, pusado, baja solicitada, inactivo, bloqueado. Al pausar una suscripción, pedir el motivo y la fecha de reactivación.
* **RF2. Detectar clientes duplicados:** El sistema debe avisar si se intenta registrar una empresa con un identificador fiscal que ya existe.
* **RF3. Ficha Comercial:** Debe permitir asociar múltiples contactos a una misma empresa comercial, administración, impagos y guardar el historial de a qué destinatario se envió cada factura.
* **RF4. Gestor de aprobaciones:** Cualquier modificación en el importe, hacer descuentos, cambio de vencimiento o factura puntual debe quedar en estado *Pendiente de aprobación* hasta que la valide un perfil con rol de **Administrador** o **SuperAdmin**.
* **RF5. Borradores en las facturas:** Se podrán guardar facturas en estado **Borrador** antes de mandarlas. De cara a errores de entrega o imprevistos con el cliente, se podrá abrir una *Incidencia* que pare el flujo automático de cobros y recordatorios.
* **RF6. Rectificar facturas:** Las facturas emitidas llevarán una numeración única. Una factura emitida no se podrá editar ni borrar, si luego se necesita corregir la factura deberá realizarse mediante una **factura rectificativa**.
* **RF7. Conciliar pagos:** Aparte de integrar Stripe, el sistema deberá permitir registrar e importar operaciones bancarias procedentes de distintas fuentes, como Revolut o transferencias bancarias. Cuando el importe recibido no coincida exactamente con el de la factura, la operación quedará pendiente de conciliación manual.
* **RF8. Importar y exportar datos:** El sistema deberá permitir importar y exportar datos de clientes a través de archivos CSV, además de crear periódicamente un archivo comprimido ZIP con la documentación necesaria para la gestoría. Esto ayudara a que no haya errores a la hora de tratar los datos en las facturas etc.

---

## Requisitos No Funcionales

* **RNF1. Registro de auditoría:** La aplicación debe guardar un historial de cualquier acción importante que se haga (como crear un cliente, aprobar un descuento, cambiar fechas de pago o tocar la configuración). Este historial es intocable para evitar lios.
* **RNF2. Modo de pruebas y previsualización:** El sistema debe incluir un entorno de test que permita ver cómo quedan los correos y los recordatorios antes de mandarlos a clientes reales.
* **RNF3. Control de fallos:** Los procesos automáticos como el crear las facturas el día 1 deben ser capaces de reanudarse si el sistema se cae o ocurre algun problema, asi nos aseguramos de que al reiniciar continúen por donde se quedaron sin duplicar facturas ni cobros.
* **RNF4. Gestión de sesiones:** El sistema cerrará la sesión del usuario automáticamente si pasa mucho tiempo inactivo. Si se deshabilita la cuenta de un empleado, el sistema tiene que destruir sus sesiones y tokens para impedir que siga dentro de la app.