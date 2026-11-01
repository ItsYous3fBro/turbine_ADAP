**Requisitos funcionales y No funcionales básicos (primera versión)**

**Funcionales**
-Autenticacion para iniciar sesion: Se pedia un correo que sea un correo propio (@turbineh) para poder acceder al sistema. 
-Niveles de permisos: El sistema debe tener al menos dos niveles de permisos que le permitan tener diferentes accesos y acciones (Admin y usuario normal). 
-Gestionar clientes: El sistema debe poder gestionar los datos de los clientes y asignarle un plan segun su facturacion y dar de baja al cliente si no tiene facturas pagadas. 
-Cobros y recordatorios: El sistema debe tener un sistema de recordatorios y alertas de cobro para asegurar los pagos. Tratar el envio de facturas por email/whatsapp, se debe reclamar los impagos automáticamente. 
-Facturas: Solo se pueden modificar en el mismo trimestre en el que se emitieron.
-Pago: Stripe como pasarela de pago integrada.
-Recuento de movimientos y datos: El sistema ha de tener un panel de control que sea administrado por el administrador donde se vean como van las finanzas de la empresa por semanas meses o el tiempo que sea sin tener que ir de factura en factura, ver un resumen general de los movimientos.
-Recordatorios necesarios: Envío automático de recordatorios los días 3 y 5.
-Firma digital: Requisito legal obligatorio de firmar las facturas con certificado digital.
-Presencia de una API en el sistema: Puede servir de conexion entre otros programas para que se conecten al sistema y sea más cómodo el tratar los movimientos/acciones que se haga en el sistema. Por ejemplo que el Brain pregunte a la aplicacion de facturacion si un cliente ha pagado por ejemplo.

**No Funcionales**
-Rendimiento y Usabilidad: Aplicacion web disponible para generar facturas rápidamente y con el menor número de clics (sea lo más cómodo para usar posible).
-Diseño: La aplicación ha de tener una interfaz clara y acorde a la comodidad que se pide. 
-Seguridad en acciones y datos: Básicamente necesitamos que los datos de los clientes esten bien protegidos a la hora de hacer las facturas etc, cifrar los datos de alguna manera. Y por otro lado transacciones ACID en la base de datos nos permite asegurar que guardamos los datos correctamente y si por ejemplo se paga y falla al hacer la facturacion o algo se deshace el primer paso asegurandonos de que al cliente no se le cobre el dinero y el sistema se quede pillado por asi decirlo.
-En todos los dispositivos: La aplicación ha de ser funcional en móviles, tablets, etc.
