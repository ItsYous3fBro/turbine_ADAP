@startuml

left to right direction

title Sistema de facturación y seguimiento de cobros - Turbine

actor "Empleado" as Empleado
actor "Administrador" as Admin
actor "Sistema de pagos" as Pagos
actor "Servicio de notificaciones" as Notificaciones

Admin -|> Empleado


usecase "Iniciar sesión" as Login

usecase "Gestionar clientes" as Clientes

usecase "Gestionar facturas" as Facturas
usecase "Consultar facturas" as ConsultarFacturas
usecase "Enviar factura" as EnviarFactura

usecase "Comprobar pago" as ComprobarPago
usecase "Enviar recordatorio\nde pago" as Recordatorio

usecase "Gestionar usuarios" as Usuarios
usecase "Consultar dashboard" as Dashboard


Empleado --> Login
Empleado --> Clientes
Empleado --> Facturas
Empleado --> ConsultarFacturas
Empleado --> ComprobarPago

Admin --> Usuarios
Admin --> Dashboard

Facturas ..> EnviarFactura : <<include>>

Pagos --> ComprobarPago

Notificaciones --> EnviarFactura
Notificaciones --> Recordatorio

@enduml