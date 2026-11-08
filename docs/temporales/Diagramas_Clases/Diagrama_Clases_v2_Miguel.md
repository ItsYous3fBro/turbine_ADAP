# Diagrama de clases — Turbine

## Versión 2 Miguel

La idea es que el diagrama represente el **modelo del sistema**, no cada caso de uso como una clase independiente.

## Clases principales añadidas o completadas

- **Usuario:** representa a los empleados internos. El rol distingue Empleado, Administrador y Superadministrador.
- **Cliente:** almacena los datos principales de cada empresa cliente.
- **Contacto:** representa a las personas de contacto asociadas a un cliente.
- **Suscripcion:** representa la facturación mensual de un cliente.
- **Tarifa:** determina la cuota mensual según el rango de facturación.
- **Factura:** representa tanto facturas puntuales como las generadas desde una suscripción.
- **LineaFactura:** contiene los conceptos que forman una factura.
- **Cobro:** registra el pago comprobado mediante la fuente externa.
- **Notificacion:** representa el envío de facturas y recordatorios.
- **Configuracion:** recoge parámetros generales como el plazo de pago y la frecuencia de recordatorios.
- **RegistroAuditoria:** conserva las operaciones importantes realizadas por los usuarios.

## Relaciones importantes

- Un **Usuario** puede ser responsable de varios **Clientes**, mientras que cada cliente tiene un responsable principal.
- Un **Cliente** tiene uno o varios **Contactos**.
- Un cliente puede tener una **Suscripción** o funcionar únicamente mediante facturas puntuales.
- Una **Tarifa** puede utilizarse en muchas suscripciones.
- Un cliente puede tener muchas **Facturas**.
- Una suscripción puede generar muchas facturas mensuales.
- Una factura contiene una o varias **Líneas de factura**.
- Una factura puede tener un **Cobro** asociado.
- Una factura y un cliente pueden tener múltiples **Notificaciones**.
- Las acciones importantes realizadas por los usuarios quedan reflejadas en **RegistroAuditoria**.

---

# PlantUML completo

```plantuml
@startuml

left to right direction

title Turbine - Diagrama de clases (Versión casi final)

skinparam classAttributeIconSize 0
skinparam shadowing false
skinparam linetype ortho
hide empty members

package "Usuarios y clientes" {

class Usuario {
  - id: UUID
  - nombre: String
  - email: String
  - contrasena: String
  - rol: RolUsuario
  - activo: Boolean
  + iniciarSesion()
  + activar()
  + desactivar()
}

enum RolUsuario {
  EMPLEADO
  ADMINISTRADOR
  SUPERADMINISTRADOR
}

class Cliente {
  - id: UUID
  - razonSocial: String
  - nif: String
  - direccionFiscal: String
  - ubicacion: String
  - sector: String
  - fechaAlta: Date
  - estado: String
  + actualizarDatos()
  + solicitarBaja()
  + calcularDeuda(): Decimal
}

class Contacto {
  - id: UUID
  - nombre: String
  - email: String
  - telefono: String
  - activo: Boolean
  + actualizarDatos()
}

}

package "Suscripción y facturación" {

class Suscripcion {
  - id: UUID
  - fechaInicio: Date
  - cuotaMensual: Decimal
  - activa: Boolean
  + activar()
  + cancelar()
  + generarFacturaMensual()
}

class Tarifa {
  - id: UUID
  - facturacionMinima: Decimal
  - facturacionMaxima: Decimal
  - precioMensual: Decimal
  + calcularPrecio(): Decimal
}

class Factura {
  - id: UUID
  - numero: String
  - fechaEmision: Date
  - fechaVencimiento: Date
  - tipo: String
  - estado: EstadoFactura
  - total: Decimal
  - pdf: String
  + calcularTotal(): Decimal
  + generarPDF()
  + enviar()
  + marcarPagada()
}

enum EstadoFactura {
  PENDIENTE
  VENCIDA
  PAGADA
  ANULADA
}

class LineaFactura {
  - descripcion: String
  - cantidad: Integer
  - precioUnitario: Decimal
  - iva: Decimal
  + calcularSubtotal(): Decimal
}

}

package "Cobros y seguimiento" {

class Cobro {
  - id: UUID
  - fecha: DateTime
  - importe: Decimal
  - referenciaExterna: String
  - estado: String
  + verificar()
  + registrar()
}

class Notificacion {
  - id: UUID
  - tipo: String
  - canal: String
  - fechaEnvio: DateTime
  - estado: String
  + enviar()
}

}

package "Administración" {

class Configuracion {
  - plazoPagoDias: Integer
  - diasRecordatorio: String
  - frecuenciaImpago: Integer
  + modificar()
}

class RegistroAuditoria {
  - id: UUID
  - fecha: DateTime
  - accion: String
  - entidad: String
  - detalle: String
}

}


Usuario "1" -- "0..*" Cliente : responsable
Cliente "1" *-- "1..*" Contacto

Cliente "1" -- "0..1" Suscripcion
Tarifa "1" -- "0..*" Suscripcion

Cliente "1" -- "0..*" Factura
Suscripcion "0..1" -- "0..*" Factura : genera

Factura "1" *-- "1..*" LineaFactura
Factura "1" -- "0..1" Cobro

Factura "1" -- "0..*" Notificacion
Cliente "1" -- "0..*" Notificacion

Usuario "1" -- "0..*" RegistroAuditoria : realiza

Configuracion "1" -- "0..*" Tarifa : define

@enduml

```
