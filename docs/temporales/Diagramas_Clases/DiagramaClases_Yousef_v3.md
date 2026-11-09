# Diagrama de clases · v3

**Proyecto:** Gestor de facturas Turbine · **Grupo:** Ramón Llull · 3.º B
**Entrega 2:** Diagrama UML de clases (versión temporal 3)
**Base:** v2 + revisión paso a paso de los 42 casos de uso (v7)
**Archivos:** `DiagramaClases_Yousef_v3.puml` · `DiagramaClases_Yousef_v3.png`

## 1. Qué se ha hecho en la v3

La v3 es la **revisión cruzada**. Se ha recorrido cada paso que hace el sistema en los 42 casos de uso y se ha comprobado que existe la clase, el atributo o el método que lo hace posible. Todo lo que faltaba se ha añadido.

Resultado: **31 clases** (una de ellas abstracta), **1 interfaz** y **13 enumerados**, en **6 paquetes**. La tabla del apartado 4 muestra qué clases usa cada caso de uso, y es la trazabilidad casos de uso ↔ clases que pide H-010.

### 1.1 Huecos encontrados y cómo se han cubierto

| Caso de uso | Qué faltaba | Cambio en la v3 |
|---|---|---|
| CU-01 | Bloqueo temporal tras 5 fallos; acceso con Microsoft | Usuario + `bloqueadoHasta`, `cuentaMicrosoft`, `registrarIntentoFallido()` |
| CU-02, CU-03 | Enlace de recuperación (como la invitación, de un solo uso y con caducidad) | Nueva clase abstracta **TokenTemporal**, con dos hijas: **Invitacion** y **EnlaceRecuperacion** (nueva generalización) |
| CU-04, CU-05 | Invalidar las sesiones al desactivar una cuenta o cambiar su rol | Usuario + `invalidarSesiones()` |
| CU-07 | Domicilio y representante legal, comprobar los datos fiscales | Cliente + `domicilio`, `representanteLegal`, `datosFiscalesCompletos()` |
| CU-08 | Resultado de la importación y su informe de errores | Nueva clase **Importacion** |
| CU-09, CU-12 | Facturación anual para proponer el nivel de tarifa | Cliente + `facturacionAnual`; NivelTarifa + `incluye()` |
| CU-12, CU-18 | Las propuestas de cambio de nivel también se aprueban | Nueva clase **PropuestaCambioNivel**, que implementa Aprobable |
| CU-13, CU-14 | Autenticar al sistema externo por API | Nueva clase **CredencialAPI** |
| CU-16 | Cuota con la tarifa vigente y las excepciones aprobadas | Suscripcion + `calcularCuotaMensual()` |
| CU-12, CU-16, CU-36, CU-41 | Registrar cada ejecución de un proceso automático y si quedó incompleta | Nueva clase **EjecucionProceso** |
| CU-15 | Aviso de importe atípico | Factura + `esImporteAtipico()` |
| CU-17, CU-24 | Saber si una fecha cae en un periodo abierto | PeriodoFacturacion + `admiteFecha()` |
| CU-17, CU-25 | PDF firmado y enlace temporal de 15 minutos | Factura + `documentoPdf`, `generarEnlaceTemporal()` |
| CU-19 | Reasignar una aprobación a otro administrador | SolicitudAprobacion + `reasignar()` |
| CU-20, CU-26 | Reintentos e identificador del proveedor de correo para localizar el rebote | Envio + `reintentos`, `idProveedor`, `registrarRebote()` |
| CU-27 | Saber si hoy toca recordatorio | Factura + `correspondeRecordatorio()`, `marcarVencida()` |
| CU-33 | La pausa es de la factura, no de la incidencia | Atributos de pausa trasladados de Incidencia a **Factura** + `pausarRecordatorios()` |
| CU-23 | Declarar una factura incobrable | Factura + `declararIncobrable()` |
| CU-29, CU-30 | Cobro no identificado | Cobro + `marcarNoIdentificado()` |
| CU-32 | Prioridad y estado eran texto libre | Nuevos enumerados **PrioridadIncidencia** y **EstadoIncidencia** |
| CU-34 | Guardar búsquedas como vista predeterminada | Nueva clase **VistaGuardada** |
| CU-39, CU-40 | Destinatarios de los informes programados | Exportacion + `destinatarios`; TipoEnvio + INFORME |

### 1.2 Otros ajustes

- **SolicitudAprobacion → Aprobable** pasa de 0..1 a 0..*: una factura rechazada puede volver a retenerse más adelante.
- **El paquete «Facturación, cobros y seguimiento» se divide en tres:** Facturación y aprobaciones, Envíos y cobros, y Seguimiento y control. Así queda listo para la versión final, que tendrá un diagrama por paquete.

## 2. Elementos que pide la guía

| Elemento | Ejemplos en la v3 |
|---|---|
| Clases, atributos y operaciones | 31 clases; cada paso del sistema de los casos de uso tiene su método |
| Asociaciones y multiplicidades | En los dos extremos de todas las relaciones |
| Generalización | FacturaRectificativa → Factura · TokenTemporal → Invitacion, EnlaceRecuperacion |
| Clase abstracta e interfaz | TokenTemporal (abstracta) · Aprobable, implementada por Factura, ExcepcionComercial y PropuestaCambioNivel |
| Composición | Cliente ◆ Contacto · Factura ◆ LineaFactura · Incidencia ◆ Comentario · Usuario ◆ TokenTemporal · Usuario ◆ VistaGuardada |
| Agregación | PeriodoFacturacion ◇ Factura |

## 3. Diccionario de clases

| Clase | Qué representa |
|---|---|
| Usuario | Empleado con cuenta, rol (estándar, administrador o superadministrador) y estado |
| TokenTemporal *(abstracta)* | Código de un solo uso con caducidad |
| Invitacion | Token para activar una cuenta nueva |
| EnlaceRecuperacion | Token para restablecer la contraseña (15 min) |
| CredencialAPI | Credencial de un sistema externo autorizado (rol técnico de FR2) |
| Cliente | Empresa cliente con sus datos fiscales, facturación anual y estado |
| Contacto | Persona de contacto del cliente con su función |
| Delegacion | Cesión temporal de un cliente a otro empleado |
| Suscripcion | Relación comercial recurrente; un cliente guarda su histórico |
| NivelTarifa | Tramo de tarifa por facturación anual y su cuota mensual |
| ExcepcionComercial | Descuento o ajuste sujeto a aprobación |
| PropuestaCambioNivel | Cambio de tramo propuesto en la revisión anual, sujeto a aprobación |
| Importacion | Carga masiva desde CSV y su informe de errores |
| Aprobable *(interfaz)* | Contrato de toda operación que puede retenerse para aprobación |
| Factura | Factura puntual, recurrente o de prorrateo, con su estado y sus marcas |
| FacturaRectificativa | Factura que corrige o anula otra ya emitida |
| LineaFactura | Concepto, cantidad, precio, descuento e impuesto |
| Concepto | Elemento del catálogo de conceptos facturables |
| PeriodoFacturacion | Periodo contable; una vez cerrado no admite emisiones |
| SolicitudAprobacion | Retención de una operación, con quién la crea y quién la resuelve |
| Envio | Envío de una factura, un recordatorio o un informe, y su resultado |
| Cobro | Pago recibido por pasarela, transferencia o Revolut |
| Notificacion | Aviso o tarea del trabajo diario de un empleado |
| Incidencia | Problema de facturación de un cliente, con prioridad y estado |
| Comentario | Comentario, adjunto o nota privada de una incidencia |
| VistaGuardada | Combinación de filtros de búsqueda guardada por un empleado |
| RegistroAuditoria | Quién cambió qué, cuándo, y el valor anterior y nuevo |
| EjecucionProceso | Ejecución de un proceso automático y si quedó completa |
| ParametroConfiguracion | Parámetro de negocio (clave–valor) vigente desde una fecha |
| PlantillaMensaje | Texto de un mensaje por tipo e idioma |
| ObjetivoFinanciero | Objetivo de ingresos vigente desde una fecha |
| Exportacion | Informe generado o programado, con su caducidad |

## 4. Trazabilidad casos de uso ↔ clases

| Caso de uso | Clases principales |
|---|---|
| CU-01 Iniciar sesión | Usuario |
| CU-02 Recuperar contraseña | Usuario, EnlaceRecuperacion |
| CU-03 Activar cuenta | Usuario, Invitacion |
| CU-04 Gestionar usuarios estándar | Usuario, Invitacion |
| CU-05 Gestionar roles de administración | Usuario |
| CU-06 Asignar y delegar clientes | Cliente, Usuario, Delegacion |
| CU-07 Gestionar cliente | Cliente, Contacto, Suscripcion, RegistroAuditoria |
| CU-08 Importar datos desde CSV | Importacion, Cliente, Factura |
| CU-09 Gestionar suscripción | Suscripcion, NivelTarifa, Cliente |
| CU-10 Facturar prorrateo | Suscripcion, Factura, Cobro |
| CU-11 Proponer excepción comercial | ExcepcionComercial, SolicitudAprobacion |
| CU-12 Proponer revisión anual de tarifa | PropuestaCambioNivel, NivelTarifa, SolicitudAprobacion, EjecucionProceso |
| CU-13 Consultar deuda de cliente | CredencialAPI, Cliente, Factura |
| CU-14 Solicitar baja por API | CredencialAPI, Cliente, Notificacion |
| CU-15 Crear factura puntual | Factura, LineaFactura, Concepto |
| CU-16 Generar facturas recurrentes | Suscripcion, Factura, EjecucionProceso |
| CU-17 Emitir factura | Factura, PeriodoFacturacion |
| CU-18 Retener para aprobación | SolicitudAprobacion, Aprobable, Notificacion |
| CU-19 Resolver aprobación pendiente | SolicitudAprobacion, Aprobable |
| CU-20 Enviar factura | Envio, Contacto, PlantillaMensaje |
| CU-21 Reenviar factura | Envio, Contacto |
| CU-22 Rectificar o anular factura | FacturaRectificativa, Factura |
| CU-23 Declarar factura incobrable | Factura |
| CU-24 Cerrar periodo de facturación | PeriodoFacturacion |
| CU-25 Consultar facturas y estado de cobro | Factura, Envio, Cobro |
| CU-26 Registrar rebote de envío | Envio, Factura, Notificacion |
| CU-27 Enviar recordatorios de cobro | Factura, Envio, PlantillaMensaje |
| CU-28 Registrar cobro de la pasarela | Cobro |
| CU-29 Conciliar cobros | Cobro, Factura |
| CU-30 Vincular cobro con factura | Cobro, Factura, Notificacion |
| CU-31 Gestionar trabajo diario | Notificacion |
| CU-32 Gestionar incidencia | Incidencia, Comentario |
| CU-33 Pausar recordatorios | Factura, Incidencia |
| CU-34 Buscar clientes y facturas | VistaGuardada, Cliente, Factura |
| CU-35 Ejecutar acción masiva | Factura, Cliente |
| CU-36 Aplicar vencimientos programados | Suscripcion, Delegacion, Factura, Exportacion, TokenTemporal, EjecucionProceso |
| CU-37 Consultar dashboard financiero | Factura, Cobro, ObjetivoFinanciero |
| CU-38 Definir objetivos financieros | ObjetivoFinanciero |
| CU-39 Exportar informes y datos | Exportacion |
| CU-40 Enviar informes programados | Exportacion, Envio |
| CU-41 Supervisar el sistema | RegistroAuditoria, EjecucionProceso, Envio |
| CU-42 Configurar el sistema | ParametroConfiguracion, PlantillaMensaje, NivelTarifa, Concepto |

Los 42 casos de uso tienen al menos una clase, y todas las clases se usan en al menos un caso de uso.

## 5. Para la versión final

1. **Un diagrama por paquete** (6), como los diagramas de casos de uso, más una vista general con las clases sin atributos.
2. **Coherencia con el diagrama Entidad-Relación:** mismos nombres de tablas y columnas. Además:
   - La regla «como máximo una suscripción no cancelada por cliente» debe aparecer como restricción en el E-R.
   - Los enumerados pasan a columnas con valores permitidos (CHECK).
3. **Decisión pendiente del equipo:** ParametroConfiguracion se mantiene como clave–valor. Si se cambia a una clase por tipo, hay que hacerlo antes del E-R.
4. **Opcional:** añadir la capa de servicios (por ejemplo, ServicioFacturacion, que coordina emitir, enviar y retener). Enlaza con los diagramas de secuencia y de arquitectura de la Entrega 2.
5. **Fusión con el PO** y revisión final de tipos y visibilidad.
