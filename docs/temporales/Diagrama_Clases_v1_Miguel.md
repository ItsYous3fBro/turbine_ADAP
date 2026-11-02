# Diagrama UML de clases — Primera versión Miguel

```plantuml
@startuml
title Gestor de facturas Turbine - Diagrama de clases Miguel v1

top to bottom direction
skinparam backgroundColor #FFFFFF
skinparam shadowing false
skinparam linetype ortho
skinparam classAttributeIconSize 0
skinparam nodesep 45
skinparam ranksep 55
skinparam class {
  BackgroundColor #F3F7FC
  BorderColor #365575
  ArrowColor #526579
  FontColor #1F2937
}
hide empty methods

class Usuario {
  - id: Integer
  - nombre: String
  - email: String
  - rol: String
  - activo: Boolean
}

class Cliente {
  - id: Integer
  - razonSocial: String
  - identificadorFiscal: String
  - domicilioFiscal: String
  - pais: String
  - moneda: String
  - estado: String
}

class Contacto {
  - id: Integer
  - nombre: String
  - email: String
  - telefono: String
  - tipo: String
  - activo: Boolean
}

class Suscripcion {
  - id: Integer
  - fechaInicio: Date
  - fechaFin: Date [0..1]
  - estado: String
  + pausar(): void
  + reactivar(): void
}

class Tarifa {
  - id: Integer
  - nombreNivel: String
  - importeMensual: Decimal
  - moneda: String
}

class Factura {
  - id: Integer
  - numero: String [0..1]
  - fechaEmision: Date [0..1]
  - fechaVencimiento: Date [0..1]
  - estado: String
  - moneda: String
  - total: Decimal
  + calcularTotal(): Decimal
}

class LineaFactura {
  - concepto: String
  - cantidad: Decimal
  - precioUnitario: Decimal
  - porcentajeDescuento: Decimal
  - porcentajeImpuesto: Decimal
  + calcularSubtotal(): Decimal
}

class Cobro {
  - id: Integer
  - fecha: DateTime
  - importe: Decimal
  - moneda: String
  - origen: String
  - referencia: String
  - estado: String
}

Usuario "1" -- "0..*" Cliente : responsable de
Cliente "1" -- "0..*" Contacto : tiene
Cliente "1" -- "0..*" Suscripcion : contrata
Tarifa "1" -- "0..*" Suscripcion : se aplica a
Cliente "1" -- "0..*" Factura : recibe
Suscripcion "0..1" -- "0..*" Factura : origina
Factura "1" *-- "0..*" LineaFactura : contiene
Factura "0..1" -- "0..*" Cobro : se vincula con

@enduml
```

## Decisiones principales

- Se utiliza una única clase `Usuario` con un atributo `rol` para distinguir entre usuario estándar, administrador y superadministrador.
- `Cliente` representa a la empresa y `Contacto` a sus personas de contacto. Cada cliente tiene un responsable.
- `Suscripcion` y `Tarifa` se separan para que varias suscripciones puedan utilizar una misma tarifa. 
- Una misma clase `Factura` representa las facturas puntuales y recurrentes. Su relación con `Suscripcion` es opcional, y el número y las fechas pueden estar vacíos mientras sea un borrador.
- Las líneas pertenecen exclusivamente a una factura, por eso se utiliza composición. Se permiten borradores sin líneas, pero la emisión deberá exigir al menos una. Cada línea conserva el precio, descuento e impuesto aplicados.
- `Cobro` se separa de `Factura` para representar pagos pendientes de conciliación. Como simplificación inicial, cada cobro puede vincularse como máximo con una factura.
- Los importes utilizan `Decimal` para evitar errores de precisión. Los roles y estados se representan como `String`; sus valores se concretarán en una versión posterior.