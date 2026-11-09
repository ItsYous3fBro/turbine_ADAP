# Casos de uso del sistema de facturación

Los identificadores FR y UR corresponden a los requisitos funcionales y de usuario de Adrián.

## CU01 Iniciar sesión

**Actores:** Usuario operativo, administrador y superadministrador; Microsoft si se confirma

**Descripción:** Introducir credenciales; comprobar la identidad; abrir una sesión con los permisos del rol.

**Reglas y alternativas:** Rechazar credenciales incorrectas; aplicar las restricciones de sesión de NFR11. Microsoft es una alternativa pendiente, no una obligación confirmada.

**Requisitos de Adrián:** FR40, FR41, FR42

## CU02 Gestionar clientes y contactos

**Actores:** Usuario operativo sobre sus asignados; administrador y superadministrador según permisos

**Descripción:** Crear o abrir una empresa; registrar datos fiscales y contactos; consultar su ficha y suscripción; guardar cambios operativos o desactivar.

**Reglas y alternativas:** Avisar de NIF duplicado sin fusionar; conservar el historial al desactivar; registrar las consecuencias de cada cambio de estado. La facultad de alta del usuario operativo debe concretarse.

**Requisitos de Adrián:** FR25, FR26, FR27, FR28, FR29, FR46; UR1

## CU03 Importar clientes desde CSV

**Actores:** Personal interno autorizado; permiso concreto pendiente de definir

**Descripción:** Cargar el archivo; mapear columnas; previsualizar y revisar duplicados; confirmar la importación y consultar el resumen.

**Reglas y alternativas:** Identificar filas rechazadas y errores; no fusionar clientes automáticamente.

**Requisitos de Adrián:** FR25, FR29, FR31

## CU04 Gestionar suscripciones

**Actores:** Usuario operativo dentro de su ámbito; responsables autorizados para altas y cambios

**Descripción:** Consultar o preparar la suscripción; seleccionar el nivel según facturación; mostrar el prorrateo del alta; confirmar o programar una pausa entre fechas.

**Reglas y alternativas:** Calcular con días reales y documentar el último día del mes; si se altera un importe extraordinariamente, tramitar aprobación; emitir el cargo proporcional al alta. Avisar al terminar la pausa. Opcionalmente, avisar al administrador al superar el nivel en la revisión anual.

**Requisitos de Adrián:** FR5, FR6, FR7, FR27, FR28, FR32, FR33, FR35, FR39; UR1

## CU05 Tramitar la baja de un cliente

**Actores:** Personal interno autorizado; sistema externo mediante CU24

**Descripción:** Registrar la solicitud; consultar deuda; aplicar la fecha efectiva de baja; detener nuevas mensualidades desde esa fecha.

**Reglas y alternativas:** La deuda pendiente no bloquea la baja: las facturas previas y su recobro permanecen activos. Solicitar la baja por API no equivale necesariamente a aprobarla.

**Requisitos de Adrián:** FR28, FR29, FR30; UR17

## CU06 Buscar y consultar clientes y facturas

**Actores:** Usuario operativo, administrador y superadministrador según permisos

**Descripción:** Buscar por nombre, razón social, NIF, contacto o número de factura; abrir un resultado; consultar datos, estados y situación de cobro.

**Reglas y alternativas:** La búsqueda global respeta los permisos: el usuario operativo solo accede a sus clientes. Mostrar ausencia de resultados cuando corresponda.

**Requisitos de Adrián:** FR2, FR27, FR30, FR46, FR53; UR1, UR3, UR8

## CU07 Preparar una factura puntual

**Actores:** Usuario operativo; administrador y superadministrador autorizados

**Descripción:** Crear un borrador; añadir líneas o conceptos del catálogo; ajustar cantidades, precios, descuentos e impuestos; previsualizar y guardar.

**Reglas y alternativas:** El catálogo no cambia el histórico. Una edición extraordinaria se remite a aprobación; un borrador no consume numeración. Tras emisión, los cambios relevantes siguen CU11.

**Requisitos de Adrián:** FR1, FR2, FR3, FR8, FR9, FR10, FR11, FR13; UR2

## CU08 Generar facturas recurrentes

**Actores:** Cliente destinatario; disparo temporal interno el día 1

**Descripción:** El día 1, identificar suscripciones facturables; calcular líneas; generar facturas; tramitar aprobaciones cuando proceda; emitir y enviar las que estén habilitadas.

**Reglas y alternativas:** Respetar pausas y bajas. Un fallo de cliente no detiene al resto; los reintentos no duplican facturas y cada ejecución queda registrada.

**Requisitos de Adrián:** FR3, FR4, FR8, FR10, FR30, FR32, FR55

## CU09 Emitir una factura

**Actores:** Personal interno autorizado; también invocado por el proceso recurrente

**Descripción:** Comprobar datos fiscales; mostrar la vista previa; asignar número ordenado y único en la serie; firmar con certificado; conservar el documento localmente y en la nube.

**Reglas y alternativas:** Bloquear si faltan datos y decir cuáles. Evitar colisiones al emitir simultáneamente. La ubicación exacta de la aprobación antes de emitir o antes de enviar debe cerrarse: se propone resolverla antes de emitir.

**Requisitos de Adrián:** FR1, FR2, FR9, FR10, FR11, FR12, FR13, FR15, FR17; UR2

## CU10 Enviar o reintentar el envío de una factura

**Actores:** Personal interno autorizado; servicio de correo y cliente destinatario

**Descripción:** Enviar por email; registrar las personas implicadas, canal, destinatario, fecha y resultado; distinguir envío original y reintentos.

**Reglas y alternativas:** Un reintento reutiliza la misma factura. Si el correo rebota, no marcarla como notificada y avisar al responsable.

**Requisitos de Adrián:** FR2, FR3, FR16, FR17, FR18, FR26

## CU11 Rectificar una factura emitida

**Actores:** Personal interno autorizado; límites de rol pendientes de concretar

**Descripción:** Seleccionar la original; preparar la rectificativa; vincular ambas; aplicar los controles de aprobación, emisión y envío que correspondan.

**Reglas y alternativas:** Conservar el PDF original sin regenerarlo con nuevas plantillas; registrar los cambios. La anulación solo procede en los casos admitidos.

**Requisitos de Adrián:** FR1, FR2, FR11, FR14

## CU12 Aprobar facturas extraordinarias

**Actores:** Administrador y superadministrador

**Descripción:** Consultar factura y motivo; revisar la operación; registrar la aprobación e identidad de quien decide; habilitar la continuación.

**Reglas y alternativas:** Si no se aprueba, no habilitar el envío. El umbral de importe alto y el tratamiento de rechazo o devolución quedan por concretar.

**Requisitos de Adrián:** FR2, FR10, FR11, FR56; UR9

## CU13 Proponer y aprobar excepciones comerciales

**Actores:** Usuario operativo propone; administrador o superadministrador aprueba

**Descripción:** Registrar oferta, precio anormal o descuento; presentar la propuesta al administrador; revisar y aprobar; aplicar la excepción autorizada con trazabilidad.

**Reglas y alternativas:** También se aplica en el alta. El usuario operativo no aprueba su propia propuesta por el mero hecho de crearla; sin aprobación la excepción no se aplica.

**Requisitos de Adrián:** FR37, FR38, FR39, FR56; UR4, UR9

## CU14 Configurar y aprobar tarifas por nivel

**Actores:** Superadministrador; persona aprobadora autorizada por definir

**Descripción:** Modificar la configuración de tarifas; someter el cambio a aprobación humana; registrar quién aprueba; aplicar la tarifa a las operaciones que correspondan.

**Reglas y alternativas:** No alterar facturas históricas. No se exige en el texto que quien aprueba sea distinto de quien propone; ese control debe acordarse si se quiere.

**Requisitos de Adrián:** FR33, FR34, FR36, FR44; UR14

## CU15 Registrar y conciliar pagos

**Actores:** Pasarela de pagos simulada u otras fuentes; administrador y superadministrador para revisión

**Descripción:** Identificar el evento sin duplicarlo; asociar el movimiento con la factura; actualizar el estado de cobro; registrar el resultado.

**Reglas y alternativas:** Sin referencia, con dudas o con importe distinto, enviar a revisión manual. El administrador resuelve o revierte una conciliación dejando rastro; reintentos sin duplicados. Stripe es probable y se simula.

**Requisitos de Adrián:** FR2, FR19, FR20, FR21, FR55; UR3, UR12

## CU16 Gestionar recordatorios de impago

**Actores:** Usuario operativo para pausas; cliente y servicio de correo para avisos; disparo temporal interno

**Descripción:** Comprobar impago y plazo pactado; enviar avisos según la cadencia; continuar el seguimiento periódico; registrar ejecuciones.

**Reglas y alternativas:** Por defecto, avisos los días 3 y 5 y plazo de 5 días. Permitir pausa por disputa con motivo y fecha de revisión; respetar configuración y evitar duplicados. La referencia exacta para contar esos días debe confirmarse.

**Requisitos de Adrián:** FR22, FR23, FR24, FR30, FR55; UR3, UR6

## CU17 Gestionar incidencias de facturación

**Actores:** Usuario operativo, administrador y superadministrador según ámbito

**Descripción:** Abrir la incidencia; asignar responsable, estado y prioridad; añadir comentarios internos; registrar su resolución.

**Reglas y alternativas:** Si la disputa lo requiere, pausar los recordatorios indicando motivo y fecha de revisión mediante CU16.

**Requisitos de Adrián:** FR48; UR5

## CU18 Consultar notificaciones y pendientes

**Actores:** Usuario operativo, administrador y superadministrador según permisos

**Descripción:** Abrir la pantalla de trabajo diario; revisar avisos por prioridad; consultar la tarea relacionada; marcar notificaciones como leídas o resueltas.

**Reglas y alternativas:** Incluir pagos, vencimientos, rebotes y aprobaciones; avisar al finalizar pausas y, si se activa la opción, al superar límites de nivel.

**Requisitos de Adrián:** FR18, FR32, FR35, FR49; UR7

## CU19 Gestionar usuarios

**Actores:** Administrador con permiso y superadministrador

**Descripción:** Crear o invitar con email corporativo; habilitar el acceso correspondiente; desactivar cuando proceda.

**Reglas y alternativas:** No hay autorregistro; conservar acciones históricas del desactivado e invalidar sus sesiones según NFR11.

**Requisitos de Adrián:** FR43, FR44, FR45; UR11

## CU20 Asignar y delegar clientes

**Actores:** Administrador y superadministrador

**Descripción:** Asignar o desasignar un cliente; si procede, definir una delegación temporal; transferir las tareas pendientes al nuevo responsable.

**Reglas y alternativas:** Registrar quién reasigna y limitar la visibilidad al ámbito resultante. El comportamiento exacto al terminar una delegación debe concretarse.

**Requisitos de Adrián:** FR46, FR47, FR56; UR10

## CU21 Configurar permisos y parámetros globales

**Actores:** Superadministrador

**Descripción:** Configurar permisos de administradores, series de facturas y plazos y cadencia de recordatorios; guardar los valores aplicables.

**Reglas y alternativas:** Respetar plazos pactados por cliente y unicidad de series; las tarifas se gestionan en CU14. Los límites del administrador siguen pendientes; UR15 establece expresamente la gestión de permisos.

**Requisitos de Adrián:** FR13, FR23, FR44, FR45, FR46; UR14, UR15

## CU22 Consultar métricas y exportar informes

**Actores:** Administrador y superadministrador; otras exportaciones solo si se autorizan

**Descripción:** Consultar dashboard semanal, mensual o trimestral; filtrar por periodo, cliente, responsable, estado y tipo; rastrear cifras hasta sus facturas; exportar a CSV o PDF.

**Reglas y alternativas:** Respetar permisos en consultas y exportaciones; ejecutar informes grandes en segundo plano conforme a NFR12.

**Requisitos de Adrián:** FR50, FR51, FR52; UR13

## CU23 Consultar la auditoría

**Actores:** Superadministrador

**Descripción:** Consultar quién creó, modificó, aprobó, emitió o reasignó; revisar los cambios de facturas y decisiones económicas.

**Reglas y alternativas:** Los usuarios normales no pueden editar la auditoría. Registrar acciones es una obligación transversal, aunque nadie consulte esta pantalla.

**Requisitos de Adrián:** FR11, FR21, FR36, FR43, FR56; UR16

## CU24 Consultar datos y solicitar bajas por API

**Actores:** Sistema externo autorizado

**Descripción:** Solicitar datos de clientes, facturas o deuda, o enviar una solicitud de baja; validar permisos y reglas; devolver el resultado o registrar la solicitud.

**Reglas y alternativas:** No saltarse restricciones de la interfaz. Una baja con deuda conserva recobro; la API registra la solicitud y aplica el flujo de CU05 cuando corresponda.

**Requisitos de Adrián:** FR30, FR54; UR17
