@startuml
skinparam componentStyle uml2
skinparam nodesep 80
skinparam ranksep 110
skinparam padding 15

package "Capa de acceso" {
  [Interfaz web] as web
  [API] as api
}

package "Gestión de negocio" as pGestion {
  [Autenticación y permisos] as auth
  [Clientes] as cli
  [Facturación] as fact
  [Cobros] as cob
}

package "Servicios" {
  [Firma digital] as firma
  [Notificaciones] as notif
}

package "Infraestructura" {
  [Base de datos] as db
  [Temporizador] as temp
  [Almacenamiento de documentos] as docs
}

package "Sistemas externos" {
  [Proveedor de identidad] as ident
  [Pasarela de pago] as pago
  [Proveedor de correo] as correo
}

package "Observabilidad" {
  [Auditoría] as aud
}

' Acceso -> negocio
web -(0- auth : IAuth
api -(0- auth : IAuth
web -(0- cli : IClientes
api -(0- cli : IClientes
web -(0- fact : IFacturacion
web -(0- cob : ICobros

' Negocio -> negocio
cli -(0- fact : IFacturacion
cob -(0- fact : IFacturacion

' Negocio -> servicios
fact -(0- firma : IFirma
cli -(0- notif : IAlerta
fact -(0- notif : IAlerta
cob -(0- notif : IAlerta

' Temporizador -> negocio
temp -(0- fact : IFacturacion
temp -(0- cob : ICobros

' Infraestructura y trazabilidad
fact -(0- docs : IDocs
pGestion -(0- db : IDatabase
notif -(0- db : IDatabase
aud -(0- db : IDatabase
pGestion -(0- aud : ILog

' Externos
auth -(0- ident : IIdentidad
cob -(0- pago : IGestionPagos
notif -(0- correo : IEnvio

@enduml