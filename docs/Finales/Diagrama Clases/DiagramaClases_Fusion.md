# Diagrama UML de clases · Versión final (fusión)

**Proyecto:** Gestor de facturas Turbine · **Grupo:** Ramón Llull · 3.º B
**Entrega 2:** Diagrama UML de clases
**Base:** Documento de requisitos v3 y Casos de uso v7 (CU-01 a CU-42). Evolución: v1 (modelo base), v2 (modelo completo), v3 (revisión cruzada con los casos de uso) versión final (presentación en vistas) y **fusión** con las mejores aportaciones del diagrama de Miguel.

## 1. Cómo se presenta el modelo

El sistema tiene un único **modelo de clases** (39 clases, 1 interfaz y 13 enumerados). Para que se lea bien, se presenta en **varios diagramas**, cada uno una vista del mismo modelo. Es la práctica habitual en UML y la misma decisión que tomamos con los casos de uso (6 diagramas en lugar de uno ilegible).

| Diagrama | Archivo | Qué muestra |
|---|---|---|
| 0 · Vista general | `DiagramaClases_0_VistaGeneral` | Las 39 clases y la interfaz, solo con el nombre, agrupadas en 6 paquetes con todas sus relaciones. Es el mapa del sistema. |
| 1 · Usuarios, acceso y control | `DiagramaClases_1_UsuariosControl` | Detalle de 16 clases: usuarios, sesiones, tokens, credenciales API, notificaciones, auditoría, procesos, copias de seguridad, configuración e informes. |
| 2 · Clientes y suscripciones | `DiagramaClases_2_Clientes` | Detalle de 9 clases: cliente, contactos, delegaciones, suscripción, tarifas, excepciones, propuestas de cambio de nivel, solicitudes de baja e importación. |
| 3 · Facturación y cobros | `DiagramaClases_3_Facturacion` | Detalle de 15 clases: factura, rectificativa, líneas, conceptos, series, documento PDF, periodos, aprobaciones, envíos, cobros, pausas de recordatorios, incidencias, comentarios y adjuntos. |
| 4 · Enumerados | `DiagramaClases_4_Enumerados` | Los 13 enumerados con sus valores permitidos. |
| Anexo · Modelo completo | `DiagramaClases_Anexo_Completo` | Todo en un solo diagrama, por si el profesorado lo pide. |

**Reglas de las vistas**

1. Cada clase se define con atributos y operaciones en **un solo** diagrama de detalle.
2. Si una clase de otro diagrama participa en una relación, aparece en **gris y solo con su nombre** (clase de referencia).
3. Todos los diagramas usan los mismos nombres, tipos y multiplicidades. Las relaciones entre vistas se repiten de forma idéntica en ambas.
4. Los atributos con valores fijos usan un enumerado como tipo (`estado : EstadoFactura`). Sus valores están en el diagrama 4.
5. Las reglas de negocio clave van como **notas** junto a su clase (inmutabilidad, cuatro ojos, idempotencia de cobros, caducidad de sesiones, baja con deuda…).

## 2. Qué aporta la fusión

Se parte de nuestro diagrama final y se incorporan las aportaciones del diagrama de Miguel que lo mejoran.

### 2.1 Incorporado del diagrama de Miguel

| Aportación | Por qué mejora el modelo | Casos de uso |
|---|---|---|
| **Sesion** (composición de Usuario) | Hace explícitas la caducidad a los 30 min y la invalidación de sesiones (NFR5); antes era solo un método | CU-01, CU-04, CU-05 |
| **SerieFacturacion** con `siguienteNumero()` | La numeración sin huecos (FR14) pasa a ser responsabilidad de una clase, no de un texto en Factura | CU-17, CU-42 |
| **DocumentoFactura** (composición de Factura) | PDF firmado con copia local y en la nube, huella y enlace temporal (FR17); sustituye al atributo `documentoPdf` | CU-17, CU-25 |
| **PausaRecordatorios** (composición de Factura) | Guarda el histórico de pausas y la incidencia que las motiva; sustituye a dos atributos de Factura | CU-27, CU-33, CU-36 |
| **SolicitudBaja** | Las bajas pedidas por un empleado o por API quedan registradas y revisables | CU-07, CU-14 |
| **Adjunto** (composición de Incidencia) | Formato y tamaño validables (FR22); antes era un texto en Comentario | CU-32 |
| **ProgramacionInforme**, separada de Exportacion | Distingue el informe generado de su programación periódica | CU-39, CU-40 |
| **CopiaSeguridad** | Hace visible el estado de las copias (NFR11) | CU-41 |
| **Configuracion** con atributos tipados | Resuelve la duda pendiente: sustituye a ParametroConfiguracion clave–valor. Se le añade `vigenteDesde` para no alterar el histórico | CU-42 |
| Atributos nuevos | Factura: `moneda`, `subtotal`, `impuestos`, `periodoServicio` · Cobro: `moneda`, `motivoDiscrepancia` · Envio: `contenidoEnviado`, `reintentar()` · Contacto: `idioma` · Cliente: `fechaAlta` · NivelTarifa: vigencia · tokens con `tokenHash` | Varios |
| Notas con reglas de negocio | Hacen el diagrama autoexplicativo | — |

### 2.2 Mantenido de nuestro diagrama (Miguel no lo tenía)

- **Interfaz Aprobable**: una única bandeja de aprobación para Factura, ExcepcionComercial y PropuestaCambioNivel. Miguel usaba asociaciones opcionales sueltas.
- **ExcepcionComercial y PropuestaCambioNivel** como clases, que son lo que se aprueba en CU-11 y CU-12.
- **Enumerados para todos los estados.** Miguel solo tenía EstadoFactura y RolUsuario; el resto eran `String`.
- **Clase abstracta TokenTemporal** con Invitacion y EnlaceRecuperacion. Miguel no modelaba la invitación (CU-03).
- **Agregación** PeriodoFacturacion ◇ Factura. Su diagrama no tenía ninguna agregación, y la guía la pide.
- **LineaFactura 1..*** (toda factura tiene al menos una línea), frente al 0..* de Miguel.
- **Presentación en vistas**, con diccionario y trazabilidad casos de uso ↔ clases.

### 2.3 Descartado del diagrama de Miguel

- **Asociación Suscripcion — Adjunto**: ningún caso de uso la pide.
- **Asociaciones de Configuracion con Tarifa, Serie, Plantilla, CopiaSeguridad y CredencialAPI**: la configuración parametriza esos objetos, pero no es su dueña.
- **ElementoTrabajo**: equivale a nuestra Notificacion (regla R2), que se mantiene.
- **Rol «titular» en Delegacion**: se deduce del responsable del cliente.

## 3. Elementos que pide la guía

| Elemento | Dónde verlo |
|---|---|
| Clases, atributos y operaciones | Diagramas 1, 2 y 3 |
| Asociaciones y multiplicidades | En todos los diagramas de detalle, en los dos extremos de cada relación |
| Generalización | FacturaRectificativa → Factura (diagrama 3) · TokenTemporal → Invitacion, EnlaceRecuperacion (diagrama 1) |
| Clase abstracta e interfaz | TokenTemporal (diagrama 1) · Aprobable, implementada por Factura, ExcepcionComercial y PropuestaCambioNivel (diagramas 2 y 3) |
| Composición | Cliente ◆ Contacto · Factura ◆ LineaFactura · Factura ◆ DocumentoFactura · Factura ◆ PausaRecordatorios · Incidencia ◆ Comentario · Incidencia ◆ Adjunto · Usuario ◆ TokenTemporal · Usuario ◆ Sesion · Usuario ◆ VistaGuardada |
| Agregación | PeriodoFacturacion ◇ Factura (diagrama 3) |
| Coherencia con la Entrega 1 | Trazabilidad casos de uso ↔ clases (apartado 7) |

## 4. Decisiones de diseño

1. **El rol es un atributo (`Rol`), no una jerarquía de subclases de Usuario.** El rol puede cambiar en tiempo de ejecución (CU-05) y un objeto no puede cambiar de clase.
2. **Los estados son enumerados**, para reflejar exactamente la regla R4 y los estados de clientes, suscripciones, cuentas, cobros e incidencias. *Migrada* e *incobrable* son marcas (Boolean) y no estados, porque se combinan con ellos.
3. **FacturaRectificativa hereda de Factura y además la referencia.** Se emite igual que cualquier factura (CU-17) y queda vinculada a la original.
4. **Una única bandeja de aprobación.** La interfaz Aprobable permite que facturas, excepciones comerciales y propuestas de cambio de nivel compartan SolicitudAprobacion (CU-18 y CU-19).
5. **TokenTemporal abstracta.** Invitación y enlace de recuperación comparten la misma regla: un solo uso y con caducidad.
6. **Las composiciones marcan dependencia de vida:** las líneas no existen sin su factura, ni los contactos sin su cliente, ni los comentarios sin su incidencia. El periodo de facturación **agrega** facturas, porque estas no desaparecen al cerrarlo.
7. **Histórico de suscripciones (Cliente 1 — 0..* Suscripcion)** con la regla de que como máximo una no está cancelada (CU-09).
8. **Los destinatarios se guardan como texto en Envio**, para conservar el historial (FR6) aunque se anonimice un contacto (NFR6).
9. **Un cobro paga como máximo una factura.** El pago parcial ya está contemplado (CU-29).
10. **Importes en `Decimal` y fechas con hora en `DateTime` UTC** (NFR9 y NFR10).
11. **Notificaciones, auditoría y ejecuciones de procesos son clases**, coherentes con las reglas R2 y R3 del documento de casos de uso.
12. **La configuración es una clase tipada y versionada** (`vigenteDesde`): cada cambio crea una configuración nueva y la anterior se conserva.
13. **Tokens y claves se guardan como hash** (`tokenHash`, `hashClave`, `hashContrasena`), nunca en claro (NFR4).

## 5. Cómo leer los diagramas

- **Vista general (0):** sirve para explicar la estructura en un minuto. Usuario está en el centro, porque casi todo tiene un responsable o un autor. Factura es el núcleo de la facturación. Cliente y Suscripcion son el origen de las facturas recurrentes.
- **Diagramas de detalle (1–3):** sirven para justificar atributos, métodos y multiplicidades.
- **Lectura de una relación:** se lee en la dirección de la flecha del nombre. Por ejemplo, «Usuario 1 — responsable de — 0..* Cliente» significa que cada cliente tiene un responsable y cada usuario puede llevar varios clientes.

## 6. Diccionario de clases

| Clase | Qué representa |
|---|---|
| Usuario | Empleado con cuenta, rol (estándar, administrador o superadministrador) y estado |
| TokenTemporal *(abstracta)* | Código de un solo uso con caducidad |
| Invitacion | Token para activar una cuenta nueva |
| EnlaceRecuperacion | Token para restablecer la contraseña (15 min) |
| Sesion | Sesión abierta de un usuario, con su caducidad por inactividad |
| CredencialAPI | Credencial de un sistema externo autorizado (rol técnico de FR2) |
| Cliente | Empresa cliente con sus datos fiscales, facturación anual y estado |
| Contacto | Persona de contacto del cliente con su función |
| Delegacion | Cesión temporal de un cliente a otro empleado |
| Suscripcion | Relación comercial recurrente; un cliente guarda su histórico |
| NivelTarifa | Tramo de tarifa por facturación anual y su cuota mensual |
| ExcepcionComercial | Descuento o ajuste sujeto a aprobación |
| PropuestaCambioNivel | Cambio de tramo propuesto en la revisión anual, sujeto a aprobación |
| SolicitudBaja | Petición de baja de un cliente, de un empleado o por API, pendiente de revisión |
| Importacion | Carga masiva desde CSV y su informe de errores |
| Aprobable *(interfaz)* | Contrato de toda operación que puede retenerse para aprobación |
| Factura | Factura puntual, recurrente o de prorrateo, con su estado y sus marcas |
| FacturaRectificativa | Factura que corrige o anula otra ya emitida |
| LineaFactura | Concepto, cantidad, precio, descuento e impuesto |
| Concepto | Elemento del catálogo de conceptos facturables |
| SerieFacturacion | Serie de numeración que asigna el siguiente número sin huecos |
| DocumentoFactura | PDF firmado de la factura, con copia local y en la nube |
| PeriodoFacturacion | Periodo contable; una vez cerrado no admite emisiones |
| SolicitudAprobacion | Retención de una operación, con quién la crea y quién la resuelve |
| Envio | Envío de una factura, un recordatorio o un informe, y su resultado |
| Cobro | Pago recibido por pasarela, transferencia o Revolut |
| PausaRecordatorios | Pausa de los recordatorios de una factura, con motivo y fecha de revisión |
| Notificacion | Aviso o tarea del trabajo diario de un empleado |
| Incidencia | Problema de facturación de un cliente, con prioridad y estado |
| Comentario | Comentario o nota privada de una incidencia |
| Adjunto | Archivo adjunto a una incidencia, con formato y tamaño validados |
| VistaGuardada | Combinación de filtros de búsqueda guardada por un empleado |
| RegistroAuditoria | Quién cambió qué, cuándo, y el valor anterior y nuevo |
| EjecucionProceso | Ejecución de un proceso automático y si quedó completa |
| CopiaSeguridad | Copia de seguridad y su estado de verificación |
| Configuracion | Parámetros de negocio tipados (plazos, umbrales, monedas…) vigentes desde una fecha |
| PlantillaMensaje | Texto de un mensaje por tipo e idioma |
| ObjetivoFinanciero | Objetivo de ingresos vigente desde una fecha |
| Exportacion | Informe o exportación generada, con su caducidad |
| ProgramacionInforme | Envío periódico de un informe a unos destinatarios |

## 7. Trazabilidad casos de uso ↔ clases

| Caso de uso | Clases principales |
|---|---|
| CU-01 Iniciar sesión | Usuario, Sesion |
| CU-02 Recuperar contraseña | Usuario, EnlaceRecuperacion |
| CU-03 Activar cuenta | Usuario, Invitacion |
| CU-04 Gestionar usuarios estándar | Usuario, Invitacion, Sesion |
| CU-05 Gestionar roles de administración | Usuario, Sesion |
| CU-06 Asignar y delegar clientes | Cliente, Usuario, Delegacion |
| CU-07 Gestionar cliente | Cliente, Contacto, Suscripcion, SolicitudBaja, RegistroAuditoria |
| CU-08 Importar datos desde CSV | Importacion, Cliente, Factura |
| CU-09 Gestionar suscripción | Suscripcion, NivelTarifa, Cliente |
| CU-10 Facturar prorrateo | Suscripcion, Factura, Cobro |
| CU-11 Proponer excepción comercial | ExcepcionComercial, SolicitudAprobacion |
| CU-12 Proponer revisión anual de tarifa | PropuestaCambioNivel, NivelTarifa, SolicitudAprobacion, EjecucionProceso |
| CU-13 Consultar deuda de cliente | CredencialAPI, Cliente, Factura |
| CU-14 Solicitar baja por API | CredencialAPI, SolicitudBaja, Cliente, Notificacion |
| CU-15 Crear factura puntual | Factura, LineaFactura, Concepto |
| CU-16 Generar facturas recurrentes | Suscripcion, Factura, EjecucionProceso |
| CU-17 Emitir factura | Factura, SerieFacturacion, DocumentoFactura, PeriodoFacturacion |
| CU-18 Retener para aprobación | SolicitudAprobacion, Aprobable, Notificacion |
| CU-19 Resolver aprobación pendiente | SolicitudAprobacion, Aprobable |
| CU-20 Enviar factura | Envio, Contacto, PlantillaMensaje |
| CU-21 Reenviar factura | Envio, Contacto |
| CU-22 Rectificar o anular factura | FacturaRectificativa, Factura |
| CU-23 Declarar factura incobrable | Factura |
| CU-24 Cerrar periodo de facturación | PeriodoFacturacion |
| CU-25 Consultar facturas y estado de cobro | Factura, DocumentoFactura, Envio, Cobro |
| CU-26 Registrar rebote de envío | Envio, Factura, Notificacion |
| CU-27 Enviar recordatorios de cobro | Factura, PausaRecordatorios, Envio, PlantillaMensaje |
| CU-28 Registrar cobro de la pasarela | Cobro |
| CU-29 Conciliar cobros | Cobro, Factura |
| CU-30 Vincular cobro con factura | Cobro, Factura, Notificacion |
| CU-31 Gestionar trabajo diario | Notificacion |
| CU-32 Gestionar incidencia | Incidencia, Comentario, Adjunto |
| CU-33 Pausar recordatorios | PausaRecordatorios, Factura, Incidencia |
| CU-34 Buscar clientes y facturas | VistaGuardada, Cliente, Factura |
| CU-35 Ejecutar acción masiva | Factura, Cliente |
| CU-36 Aplicar vencimientos programados | Suscripcion, Delegacion, PausaRecordatorios, Exportacion, TokenTemporal, EjecucionProceso |
| CU-37 Consultar dashboard financiero | Factura, Cobro, ObjetivoFinanciero |
| CU-38 Definir objetivos financieros | ObjetivoFinanciero |
| CU-39 Exportar informes y datos | Exportacion |
| CU-40 Enviar informes programados | ProgramacionInforme, Exportacion, Envio |
| CU-41 Supervisar el sistema | RegistroAuditoria, EjecucionProceso, CopiaSeguridad, Envio |
| CU-42 Configurar el sistema | Configuracion, SerieFacturacion, PlantillaMensaje, NivelTarifa, Concepto |

Los 42 casos de uso tienen al menos una clase, y todas las clases se usan en al menos un caso de uso.
