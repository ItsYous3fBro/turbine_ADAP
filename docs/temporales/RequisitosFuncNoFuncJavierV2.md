**Requisitos funcionales y No funcionales V2 (ampliación)**

**Requisitos Funcionales:**

- RF1.Gestion del cliente: Se debe controlar los estados del cliente si esta activo, pusado, baja solicitada, inactivo, bloqueado. Al pausar una suscripción, pedir el motivo y la fecha de reactivación.

-RF2.Detectar clientes duplicados: El sistema debe avisar si se intenta registrar una empresa con un identificador fiscal que ya existe.

-RF3.Ficha Comercial: Debe permitir asociar múltiples contactos a una misma empresa comercial, administración, impagos y guardar el historial de a qué destinatario se envió cada factura.

-RF4.Gestor de aprobaciones: Cualquier modificación en el importe, hacer descuentos, cambio de vencimiento o factura puntual debe quedar en estado *Pendiente de aprobación* hasta que la valide un perfil con rol de **Administrador** o **SuperAdmin**.

-RF5.Borradores en las facturas: Se podrán guardar facturas en estado **Borrador** antes de mandarlas. De cara a errores de entrega o imprevistos con el cliente, se podrá abrir una *Incidencia* que pare el flujo automático de cobros y recordatorios.

-RF6.Rectificar facturas: Las facturas emitidas llevarán una numeración única. Una factura emitida no se podrá editar ni borrar, si luego se necesita corregir la factura deberá realizarse mediante una **factura rectificativa**.

-RF7.Conciliar pagos: Aparte de integrar Stripe, el sistema deberá permitir registrar e importar operaciones bancarias procedentes de distintas fuentes, como Revolut o transferencias bancarias. Cuando el importe recibido no coincida exactamente con el de la factura, la operación quedará pendiente de conciliación manual.

-RF8.Importar y exportar datos: El sistema deberá permitir importar y exportar datos de clientes a través de archivos CSV, además de crear periódicamente un archivo comprimido ZIP con la documentación necesaria para la gestoría. Esto ayudara a que no haya errores a la hora de tratar los datos en las facturas etc.


**Requisitos No Funcionales:**

-RNF1.Registro de auditoría: La aplicación debe guardar un historial de cualquier acción importante que se haga (como crear un cliente, aprobar un descuento, cambiar fechas de pago o tocar la configuración). Este historial es intocable para evitar lios.

-RNF2.Modo de pruebas y previsualización: El sistema debe incluir un entorno de test que permita ver cómo quedan los correos y los recordatorios antes de mandarlos a clientes reales.

-RNF3.Control de fallos: Los procesos automáticos como el crear las facturas el día 1 deben ser capaces de reanudarse si el sistema se cae o ocurre algun problema, asi nos aseguramos de que al reiniciar continúen por donde se quedaron sin duplicar facturas ni cobros.

-RNF4.Gestión de sesiones: El sistema cerrará la sesión del usuario automáticamente si pasa mucho tiempo inactivo. Si se deshabilita la cuenta de un empleado, el sistema tiene que destruir sus sesiones y tokens para impedir que siga dentro de la app.