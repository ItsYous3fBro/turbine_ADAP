# **Diagramas de Clases · Gestor de facturas TurbineH**

**Grupo:** Ramón Llull · **Curso:** 3º B

## **1. Resumen**

Este documento resume el diagrama UML de clases del gestor de facturas y clientes de TurbineH. Para cada clase recoge qué representa, sus atributos y operaciones principales, con qué se relaciona y en qué casos de uso interviene.

El modelo es único (**40 clases, 1 interfaz y 16 enumerados**), pero se presenta en varios diagramas para que se pueda leer:

| Diagrama | Paquetes | Clases | Qué muestra |
|---|---|---|---|
| 0 · Vista general | Todos | 40 + 1 interfaz | Todas las clases, solo con el nombre, y todas las relaciones. Es el mapa del sistema. |
| 1 · Usuarios, acceso y control | Usuarios y acceso · Seguimiento y control · Configuración y análisis | 16 | Usuarios, sesiones, tokens, credenciales API, notificaciones, auditoría, procesos, copias de seguridad, configuración e informes |
| 2 · Clientes y suscripciones | Clientes y suscripciones | 9 | Cliente, contactos, delegaciones, suscripciones, tarifas, excepciones, propuestas de cambio de nivel, bajas e importaciones |
| 3 · Facturación y cobros | Facturación y aprobaciones · Envíos y cobros · Seguimiento y control | 16 | Factura y todo lo que la rodea: líneas, series, PDF, periodos, aprobaciones, envíos, cobros, conciliaciones, pausas e incidencias |
| 4 · Enumerados | — | 16 enumerados | Valores permitidos de los atributos de estado y tipo |
| Anexo · Modelo completo | Todos | Todas | Todo en un solo diagrama, por si se necesita verlo entero |

## **2. Criterios comunes**

* **Un modelo, varias vistas.** Cada clase se dibuja con sus atributos y operaciones en **un solo** diagrama de detalle. Si aparece en otro, sale **en gris y solo con el nombre** (clase de referencia), con la misma relación y multiplicidad.
* **Estados como enumerados.** Los atributos con valores fijos tienen un enumerado como tipo (`estado : EstadoFactura`); sus valores están en el diagrama 4. *Migrada* e *incobrable* son marcas (Boolean) y no estados, porque se combinan con ellos.
* **Rol como atributo, no como herencia.** El rol de un usuario puede cambiar (CU-05) y un objeto no puede cambiar de clase.
* **Tipos:** importes en `Decimal` (nunca coma flotante, NFR9) y fechas con hora en `DateTime` UTC (NFR10). Contraseñas, claves y tokens se guardan como hash (NFR4).
* **Reglas de negocio como notas** junto a su clase: inmutabilidad, principio de los cuatro ojos, idempotencia de cobros, caducidad de sesiones, baja con deuda…
* **Composición (◆)** cuando la parte no existe sin el todo (líneas de una factura, contactos de un cliente); **agregación (◇)** cuando sobrevive (facturas de un periodo).
* **Nombres compartidos** con el diagrama Entidad-Relación: cada clase es una tabla y cada enumerado una columna con valores permitidos.

---

## **3. Diagrama 0 · Vista general**

**Definición:** mapa del sistema con las 40 clases y la interfaz agrupadas en 6 paquetes, sin atributos, operaciones ni multiplicidades.
**Lectura:** **Usuario** está en el centro, porque casi todo tiene un responsable, un autor o alguien que lo aprueba. **Factura** es el núcleo de la facturación. **Cliente** y **Suscripcion** son el origen de las facturas recurrentes.
**Uso:** explicar la estructura en un minuto; el detalle está en los diagramas 1, 2 y 3.

## **4. Diagrama 1 · Usuarios, acceso y control**

### **Usuario**
**Definición:** empleado con cuenta en el sistema, con un rol (estándar, administrador o superadministrador) y un estado.
**Atributos clave:** `email`, `hashContrasena`, `cuentaMicrosoft`, `rol : Rol`, `estado : EstadoCuenta`, `intentosFallidos`, `bloqueadoHasta`.
**Operaciones:** `autenticar()`, `registrarIntentoFallido()`, `cambiarRol()`, `activar()`, `desactivar()`, `invalidarSesiones()`.
**Relaciones:** compone sus sesiones, tokens y vistas guardadas; es responsable de clientes; crea, aprueba, valida o autoriza la mayoría de las operaciones.
**Casos de uso:** CU-01 a CU-06.
**Decisión clave:** tras 5 intentos fallidos la cuenta pasa a BLOQUEADA hasta `bloqueadoHasta`.

### **Sesion**
**Definición:** sesión abierta de un usuario.
**Atributos clave:** `creadaEn`, `ultimaActividad`, `expiraEn`, `activa`.
**Operaciones:** `renovar()`, `invalidar()`.
**Relaciones:** composición de Usuario (1 — 0..*).
**Casos de uso:** CU-01, CU-04, CU-05.
**Decisión clave:** caduca tras 30 minutos de inactividad y se invalida en menos de un minuto al desactivar la cuenta (NFR5).

### **TokenTemporal** *(abstracta)* · **Invitacion** · **EnlaceRecuperacion**
**Definición:** código de un solo uso con caducidad. Invitacion activa una cuenta nueva; EnlaceRecuperacion restablece la contraseña (15 minutos).
**Atributos clave:** `tokenHash`, `fechaCaducidad`, `usado`.
**Operaciones:** `estaVigente()`, `invalidar()`.
**Relaciones:** las dos subclases heredan de TokenTemporal; composición de Usuario.
**Casos de uso:** CU-02, CU-03, CU-04, CU-36.
**Decisión clave:** clase abstracta, porque las dos subclases comparten la misma regla y no añaden atributos.

### **CredencialAPI**
**Definición:** credencial de un sistema externo autorizado (el rol técnico de FR2).
**Atributos clave:** `sistema`, `hashClave`, `activa`, `ultimoUso`.
**Operaciones:** `autenticar()`, `revocar()`.
**Relaciones:** recibe solicitudes de baja (SolicitudBaja 0..* — 0..1).
**Casos de uso:** CU-13, CU-14.

### **Notificacion**
**Definición:** aviso o tarea del trabajo diario de un empleado.
**Atributos clave:** `tipo`, `mensaje`, `prioridad`, `fecha`, `leida`, `atendida`.
**Operaciones:** `marcarLeida()`, `marcarAtendida()`.
**Relaciones:** la recibe un Usuario; puede referirse a un Cliente, una Factura, una Incidencia o una SolicitudBaja (0..1 cada una).
**Casos de uso:** CU-14, CU-18, CU-26, CU-30, CU-31.
**Decisión clave:** cada notificación indica a qué se refiere, para que el empleado vaya directo al elemento afectado (CU-31).

### **VistaGuardada**
**Definición:** combinación de filtros de búsqueda guardada por un empleado.
**Atributos clave:** `nombre`, `filtros`, `predeterminada`.
**Relaciones:** composición de Usuario.
**Casos de uso:** CU-34.

### **RegistroAuditoria**
**Definición:** quién cambió qué, cuándo, y el valor anterior y el nuevo.
**Atributos clave:** `fecha`, `accion`, `entidad`, `entidadId`, `valorAnterior`, `valorNuevo`.
**Relaciones:** lo realiza un Usuario (0..1: también lo puede hacer un proceso automático).
**Casos de uso:** CU-07, CU-41 y toda acción importante (regla R3).
**Decisión clave:** registro inmutable: ningún rol puede editarlo ni borrarlo (FR26).

### **EjecucionProceso**
**Definición:** ejecución de un proceso automático y si quedó completa.
**Atributos clave:** `proceso`, `inicio`, `fin`, `completa`, `resultado`.
**Relaciones:** genera facturas (0..1 — 0..*).
**Casos de uso:** CU-12, CU-16, CU-36, CU-41.
**Decisión clave:** una ejecución incompleta se recupera al día siguiente sin duplicar facturas.

### **CopiaSeguridad**
**Definición:** copia de seguridad del sistema y su estado de verificación.
**Atributos clave:** `fecha`, `estado`, `ubicacion`, `tamano`.
**Operaciones:** `verificar()`.
**Casos de uso:** CU-41 (NFR11).

### **Configuracion**
**Definición:** parámetros de negocio del sistema, tipados y versionados.
**Atributos clave:** `vigenteDesde`, `plazoPagoDias`, `cadenciaRecordatorios`, `fechaRevisionAnual`, umbrales de descuento, importe atípico y rectificación, `minutosBloqueoAcceso`, `retencionExportacionesDias`, `impuestosPermitidos`, `correoGestoria`.
**Operaciones:** `validar()`.
**Casos de uso:** CU-42.
**Decisión clave:** cada cambio crea una configuración nueva y la anterior se conserva, para no alterar el histórico. Las monedas no se configuran aquí: las gestiona la pasarela de pago (Stripe).

### **PlantillaMensaje**
**Definición:** texto de un mensaje (factura, recordatorio, informe…) por tipo e idioma.
**Atributos clave:** `tipo`, `idioma`, `asunto`, `cuerpo`, `activa`.
**Operaciones:** `validarVariables()`, `renderizar()`.
**Relaciones:** la usan los envíos (Envio 0..* — 0..1).
**Casos de uso:** CU-20, CU-27, CU-42.

### **ObjetivoFinanciero**
**Definición:** objetivo de ingresos vigente desde una fecha.
**Atributos clave:** `tipo`, `importe`, `moneda`, `vigenteDesde`.
**Relaciones:** lo define un Usuario.
**Casos de uso:** CU-37, CU-38.

### **Exportacion** · **ProgramacionInforme**
**Definición:** Exportacion es un informe o una exportación generada, con su caducidad; ProgramacionInforme es el envío periódico de un informe a unos destinatarios.
**Atributos clave:** Exportacion: `tipoInforme`, `formato`, `filtros`, `fechaGeneracion`, `caducaEl`, `ruta` · ProgramacionInforme: `periodicidad`, `destinatarios`, `proximaEjecucion`, `activa`.
**Operaciones:** `generar()`, `descargar()` · `ejecutar()`, `desactivar()`.
**Relaciones:** las dos pertenecen a un Usuario; una programación genera exportaciones (0..1 — 0..*).
**Casos de uso:** CU-36, CU-39, CU-40.
**Decisión clave:** se separan el informe generado y su programación, porque una programación produce un informe nuevo en cada ejecución.

## **5. Diagrama 2 · Clientes y suscripciones**

### **Cliente**
**Definición:** empresa cliente de Turbine, con sus datos fiscales, su facturación anual y su estado.
**Atributos clave:** `nombreComercial`, `razonSocial`, `identificadorFiscal`, `pais`, `moneda`, `facturacionAnual`, `fechaAlta`, `estado : EstadoCliente`.
**Operaciones:** `datosFiscalesCompletos()`, `calcularDeuda()`, `asignarResponsable()`, `darDeBaja()`, `reactivar()`.
**Relaciones:** tiene un Usuario responsable; compone sus contactos (1..*); tiene suscripciones y recibe facturas.
**Casos de uso:** CU-06, CU-07, CU-08, CU-13, CU-14, CU-34.
**Decisión clave:** la baja es lógica: con deuda pendiente el cliente queda en BAJA_PENDIENTE y no se pierden sus datos ni sus facturas (FR11, regla R6).

### **Contacto**
**Definición:** persona de contacto del cliente con su función (comercial, administración o impagos).
**Atributos clave:** `nombre`, `email`, `telefono`, `funcion : FuncionContacto`, `idioma`, `activo`.
**Operaciones:** `desactivar()`, `anonimizar()`.
**Relaciones:** composición de Cliente; los envíos pueden dirigirse a un contacto.
**Casos de uso:** CU-07, CU-20, CU-21.

### **Delegacion**
**Definición:** cesión temporal de un cliente a otro empleado.
**Atributos clave:** `fechaInicio`, `fechaFin`, `estado : EstadoDelegacion`.
**Operaciones:** `estaVigente()`, `revocar()`.
**Relaciones:** sobre un Cliente; con un Usuario delegado y otro que la autoriza.
**Casos de uso:** CU-06, CU-36.
**Decisión clave:** como máximo una delegación vigente por cliente; al vencer, el cliente vuelve a su responsable.

### **Suscripcion**
**Definición:** relación comercial recurrente de un cliente con un nivel de tarifa.
**Atributos clave:** `fechaInicio`, `fechaFin`, `estado : EstadoSuscripcion`, `motivoPausa`, `fechaReactivacion`.
**Operaciones:** `calcularCuotaMensual()`, `calcularProrrateo()`, `activar()`, `pausar()`, `reactivar()`, `cancelar()`.
**Relaciones:** de un Cliente (que guarda su histórico, 1 — 0..*); aplica un NivelTarifa; genera facturas.
**Casos de uso:** CU-09, CU-10, CU-16, CU-36.
**Decisión clave:** como máximo una suscripción no cancelada por cliente.

### **NivelTarifa**
**Definición:** tramo de tarifa según la facturación anual del cliente, con su cuota mensual.
**Atributos clave:** `nombre`, `facturacionMinima`, `facturacionMaxima`, `cuotaMensual`, vigencia.
**Operaciones:** `incluye(facturacion)`.
**Casos de uso:** CU-09, CU-12, CU-42.

### **ExcepcionComercial** · **PropuestaCambioNivel**
**Definición:** ExcepcionComercial es un descuento o ajuste sobre una suscripción; PropuestaCambioNivel es el cambio de tramo propuesto en la revisión anual.
**Relaciones:** las dos pertenecen a una Suscripcion e implementan **Aprobable**; la propuesta apunta al NivelTarifa propuesto.
**Casos de uso:** CU-11 y CU-12, retenidas para aprobación con CU-18 y CU-19.
**Decisión clave:** son clases propias porque son lo que se aprueba; así comparten la bandeja de aprobación con las facturas.

### **SolicitudBaja**
**Definición:** petición de baja de un cliente, hecha por un empleado o por API, pendiente de revisión.
**Atributos clave:** `origen`, `fechaSolicitud`, `motivo`, `estado`.
**Operaciones:** `aprobar()`, `descartar()`.
**Relaciones:** sobre un Cliente; revisada por un Usuario; recibida de una CredencialAPI si llega por API.
**Casos de uso:** CU-07, CU-14.
**Decisión clave:** si el cliente tiene deuda, la baja queda pendiente; una solicitud repetida no crea otra tarea.

### **Importacion**
**Definición:** carga masiva de datos desde CSV, con su informe de errores.
**Atributos clave:** `tipo`, `fecha`, `filasValidas`, `filasErroneas`, `informeErrores`.
**Relaciones:** la realiza un Usuario.
**Casos de uso:** CU-08.

## **6. Diagrama 3 · Facturación y cobros**

Cadena de una factura: se crea (CU-15, CU-16, CU-22), se puede retener para aprobación (CU-18, CU-19), se emite con número de su serie y su PDF (CU-17), se envía (CU-20), se recuerda (CU-27) y se cobra mediante conciliaciones (CU-28 a CU-30).

### **Aprobable** *(interfaz)*
**Definición:** contrato de toda operación que puede retenerse para aprobación.
**Operaciones:** `requiereAprobacion()`, `aplicarAprobacion()`, `rechazar()`.
**Relaciones:** la implementan Factura, ExcepcionComercial y PropuestaCambioNivel.
**Casos de uso:** CU-18, CU-19.
**Decisión clave:** una sola bandeja de aprobación para las tres operaciones, sin repetir la lógica.

### **Factura**
**Definición:** factura puntual, recurrente o de prorrateo, con su estado y sus marcas.
**Atributos clave:** `numero`, `fechaEmision`, `fechaVencimiento`, `tipo : TipoFactura`, `estado : EstadoFactura`, `subtotal`, `impuestos`, `total`, `migrada`, `incobrable`, `datosFiscalesEmitidos`.
**Operaciones:** `calcularTotales()`, `esImporteAtipico()`, `emitir()`, `marcarEnviada()`, `marcarVencida()`, `registrarPago()`, `importePendiente()`, `pausarRecordatorios()`, `declararIncobrable()`.
**Relaciones:** la recibe un Cliente; la puede generar una Suscripcion o una EjecucionProceso; la crea un Usuario; compone sus líneas (1..*), su PDF y sus pausas; pertenece a un periodo (agregación) y a una serie; se envía y se salda con conciliaciones.
**Casos de uso:** CU-15 a CU-17, CU-22 a CU-27, CU-34, CU-37.
**Decisión clave:** una factura emitida no se modifica ni se borra: se corrige con una FacturaRectificativa (FR15). Al emitir se congelan sus datos fiscales, y hay como máximo una recurrente por suscripción y periodo.

### **FacturaRectificativa**
**Definición:** factura que corrige o anula otra ya emitida.
**Atributos clave:** `motivo`, `anulaTotalmente`.
**Relaciones:** hereda de Factura y además la referencia («rectifica», 0..* — 1).
**Casos de uso:** CU-22.
**Decisión clave:** hereda porque se emite igual que cualquier factura (CU-17), con su propio número.

### **LineaFactura** · **Concepto**
**Definición:** LineaFactura es una línea con descripción, cantidad, precio, descuento e impuesto; Concepto es un elemento del catálogo de conceptos facturables.
**Operaciones:** `calcularImporte()`.
**Relaciones:** composición de Factura (1 — 1..*); una línea puede usar un Concepto (0..1).
**Casos de uso:** CU-15, CU-42.

### **SerieFacturacion**
**Definición:** serie de numeración de las facturas.
**Atributos clave:** `codigo`, `formato`, `ultimoNumero`, `activa`.
**Operaciones:** `siguienteNumero()`.
**Casos de uso:** CU-17, CU-42.
**Decisión clave:** la serie asigna el número **después** de validar la factura, sin huecos (FR14).

### **DocumentoFactura**
**Definición:** PDF firmado de la factura.
**Atributos clave:** `rutaLocal`, `rutaNube`, `huella`, `firmado`, `creadoEn`.
**Operaciones:** `descargar()`, `generarEnlaceTemporal()`.
**Relaciones:** composición de Factura (1 — 0..1).
**Casos de uso:** CU-17, CU-20, CU-25.
**Decisión clave:** inalterable y guardado en local y en la nube (FR17).

### **PeriodoFacturacion**
**Definición:** periodo contable que agrupa facturas.
**Atributos clave:** `fechaInicio`, `fechaFin`, `estado : EstadoPeriodo`, `cerradoEn`.
**Operaciones:** `admiteFecha()`, `marcarIncompleto()`, `cerrar()`.
**Relaciones:** agrega facturas (las facturas no desaparecen al cerrarlo); lo cierra un Usuario.
**Casos de uso:** CU-16, CU-17, CU-24.
**Decisión clave:** un periodo cerrado no admite emisiones con fecha dentro de él; si la generación recurrente falla, queda INCOMPLETO.

### **SolicitudAprobacion**
**Definición:** retención de una operación Aprobable hasta que un administrador la resuelve.
**Atributos clave:** `motivo`, `estado : EstadoAprobacion`, `fechaSolicitud`, `fechaResolucion`, `motivoDecision`.
**Operaciones:** `aprobar()`, `rechazar()`, `reasignar()`.
**Relaciones:** retiene un Aprobable; creada por un Usuario, asignada a otro y resuelta por otro.
**Casos de uso:** CU-18, CU-19.
**Decisión clave:** quien crea la operación nunca puede aprobarla (principio de los cuatro ojos, FR16).

### **Envio**
**Definición:** envío de una factura, un recordatorio o un informe, y su resultado.
**Atributos clave:** `fecha`, `tipo : TipoEnvio`, `canal`, `destinatarios`, `contenidoEnviado`, `idProveedor`, `reintentos`, `resultado`.
**Operaciones:** `registrarRebote()`, `reintentar()`.
**Relaciones:** de una Factura; usa una PlantillaMensaje; dirigido a un Contacto (0..1).
**Casos de uso:** CU-20, CU-21, CU-26, CU-27, CU-40.
**Decisión clave:** guarda una copia de los destinatarios y del contenido, que se conserva aunque el contacto se anonimice (FR6, NFR6).

### **Cobro**
**Definición:** pago recibido por pasarela, transferencia o Revolut.
**Atributos clave:** `importe`, `moneda`, `fecha`, `origen : OrigenCobro`, `referencia`, `estado : EstadoCobro`, `motivoDiscrepancia`.
**Operaciones:** `aplicar(factura, importe)`, `importeSinAplicar()`, `marcarNoIdentificado()`.
**Relaciones:** se aplica a facturas mediante conciliaciones (1 — 0..*).
**Casos de uso:** CU-10, CU-28 a CU-30, CU-37.
**Decisión clave:** la referencia externa es única: un aviso repetido de la pasarela no crea otro cobro (NFR7).

### **Conciliacion**
**Definición:** aplicación de una parte o de todo un cobro a una factura.
**Atributos clave:** `importeAplicado`, `fecha`, `estado : EstadoConciliacion`, `fechaReversion`, `motivoReversion`.
**Operaciones:** `estaVigente()`, `revertir()`.
**Relaciones:** une un Cobro con una Factura; validada y, en su caso, revertida por un Usuario.
**Casos de uso:** CU-28, CU-29, CU-30.
**Decisión clave:** resuelve el pago parcial: un cobro puede repartirse entre varias facturas y una factura pagarse con varios cobros. Lo aplicado no supera el importe del cobro ni el pendiente de la factura, la factura pasa a PAGADA cuando lo aplicado iguala su total, y una conciliación revertida se conserva en el histórico.

### **PausaRecordatorios**
**Definición:** pausa de los recordatorios de una factura.
**Atributos clave:** `motivo`, `inicio`, `fechaRevision`, `fechaReanudacion`, `activa`.
**Operaciones:** `reanudar()`.
**Relaciones:** composición de Factura; motivada por una Incidencia (0..1); creada por un Usuario.
**Casos de uso:** CU-27, CU-33, CU-36.
**Decisión clave:** es una clase y no un atributo de la factura, para guardar el histórico de pausas y su motivo.

### **Incidencia** · **Comentario** · **Adjunto**
**Definición:** Incidencia es un problema de facturación de un cliente; Comentario es un comentario o una nota privada; Adjunto es un archivo de la incidencia.
**Atributos clave:** Incidencia: `descripcion`, `prioridad`, `estado`, `fechaApertura`, `fechaCierre` · Comentario: `texto`, `fecha`, `privado` · Adjunto: `nombre`, `formato`, `tamano`, `ruta`.
**Operaciones:** `anadirComentario()`, `reasignar()`, `cerrar()` · `autorizar()` · `validar()`.
**Relaciones:** la incidencia es sobre un Cliente, puede afectar a una Factura y tiene un Usuario responsable; compone sus comentarios y adjuntos; un comentario tiene autor y, si es privado, puede tener un usuario autorizado a leerlo.
**Casos de uso:** CU-32, CU-33.
**Decisión clave:** comentarios y adjuntos son clases separadas porque tienen atributos y validaciones distintos (formato y tamaño del adjunto, FR22).

## **7. Diagrama 4 · Enumerados**

| Enumerado | Valores |
|---|---|
| Rol | ESTANDAR, ADMINISTRADOR, SUPERADMINISTRADOR |
| EstadoCuenta | INVITADA, ACTIVA, BLOQUEADA, DESACTIVADA |
| EstadoCliente | ACTIVO, INCOMPLETO, BAJA_PENDIENTE, DADO_DE_BAJA |
| FuncionContacto | COMERCIAL, ADMINISTRACION, IMPAGOS |
| EstadoDelegacion | VIGENTE, FINALIZADA, REVOCADA |
| EstadoSuscripcion | PROGRAMADA, ACTIVA, PAUSADA, CANCELADA |
| TipoFactura | PUNTUAL, RECURRENTE, PRORRATEO |
| EstadoFactura | BORRADOR, PENDIENTE_APROBACION, APROBADA, EMITIDA, ENVIADA, NO_NOTIFICADA, VENCIDA, PAGADA, RECTIFICADA, ANULADA |
| EstadoPeriodo | ABIERTO, INCOMPLETO, CERRADO |
| EstadoAprobacion | PENDIENTE, APROBADA, RECHAZADA |
| TipoEnvio | FACTURA, RECORDATORIO, INFORME |
| OrigenCobro | PASARELA, TRANSFERENCIA, REVOLUT |
| EstadoCobro | VINCULADO, EN_REVISION, NO_IDENTIFICADO |
| EstadoConciliacion | VIGENTE, REVERTIDA |
| PrioridadIncidencia | BAJA, MEDIA, ALTA |
| EstadoIncidencia | ABIERTA, CERRADA |

## **8. Relación con los diagramas de secuencia**

Los objetos que aparecen en los diagramas de secuencia de nivel *componentes y clases* son instancias de estas clases:

| Caso de uso | Clases que intervienen |
|---|---|
| CU-01 Iniciar sesión | Usuario, Sesion, RegistroAuditoria |
| CU-07 Gestionar cliente | Cliente, Contacto, Suscripcion, SolicitudBaja, RegistroAuditoria |
| CU-15 Crear factura puntual | Factura, LineaFactura, Concepto, SolicitudAprobacion |
| CU-16 Generar facturas recurrentes | EjecucionProceso, Suscripcion, Factura, PeriodoFacturacion |
| CU-17 Emitir factura | Factura, SerieFacturacion, DocumentoFactura, PeriodoFacturacion |
| CU-20 Enviar factura | Envio, Contacto, PlantillaMensaje, DocumentoFactura |
| CU-22 Rectificar o anular factura | FacturaRectificativa, Factura, SolicitudAprobacion |
| CU-27 Enviar recordatorios de cobro | Factura, PausaRecordatorios, Envio, PlantillaMensaje |
| CU-32 Gestionar incidencia | Incidencia, Comentario, Adjunto, PausaRecordatorios |
| CU-34 Buscar clientes y facturas | VistaGuardada, Cliente, Factura |
| CU-37 Consultar dashboard financiero | Factura, Cobro, Conciliacion, ObjetivoFinanciero |
| CU-39 Exportar informes y datos | Exportacion, ProgramacionInforme |
