# Casos de uso — Turbine

## CU01. Iniciar sesión

- **Actores:** Empleado y administrador.
- **Descripción:** Acceder al sistema con sus credenciales para utilizar las funciones permitidas según su rol.
- **Requisitos de Adrián:** FR40, FR42.

## CU02. Gestionar clientes

- **Actores:** Empleado y administrador.
- **Descripción:** Registrar, consultar y modificar los datos de los clientes y sus contactos. Los clientes se desactivan en lugar de borrarse.
- **Requisitos de Adrián:** FR25, FR26, FR27, FR29, FR46; UR1.

## CU03. Gestionar facturas

- **Actores:** Empleado y administrador.
- **Descripción:** Crear y modificar borradores de facturas, añadir conceptos y previsualizarlas antes de emitirlas. Según este diagrama, incluye el envío de la factura.
- **Requisitos de Adrián:** FR1, FR3, FR8, FR9; UR2.

## CU04. Consultar facturas

- **Actores:** Empleado y administrador.
- **Descripción:** Buscar facturas y consultar sus datos y su estado, por ejemplo, si están emitidas, vencidas o pagadas.
- **Requisitos de Adrián:** FR2, FR53; UR3, UR8.

## CU05. Enviar factura

- **Actores:** Empleado y administrador mediante la gestión de facturas; servicio de notificaciones.
- **Descripción:** Enviar la factura al cliente por correo electrónico y registrar el resultado. Si el envío falla, se puede reintentar sin duplicar la factura.
- **Requisitos de Adrián:** FR3, FR16, FR18.

## CU06. Comprobar pago

- **Actores:** Empleado, administrador y sistema de pagos.
- **Descripción:** Consultar si una factura está pagada a partir de la información recibida del sistema de pagos, evitando contabilizar dos veces un mismo evento.
- **Requisitos de Adrián:** FR19, FR20, FR21; UR3.

## CU07. Enviar recordatorio de pago

- **Actores:** Servicio de notificaciones.
- **Descripción:** Enviar avisos automáticos cuando una factura sigue pendiente de pago, respetando los plazos, la frecuencia y las pausas configuradas.
- **Requisitos de Adrián:** FR22, FR23, FR24.

## CU08. Gestionar usuarios

- **Actor:** Administrador autorizado.
- **Descripción:** Crear, invitar y desactivar usuarios. No se permite el autorregistro y se conserva el historial de los usuarios desactivados.
- **Requisitos de Adrián:** FR43, FR45; UR11.

## CU09. Consultar dashboard

- **Actor:** Administrador.
- **Descripción:** Consultar las métricas de facturación y cobros para conocer la situación económica en periodos semanales, mensuales y trimestrales.
- **Requisitos de Adrián:** FR50, FR51; UR13.

## Notas del modelo

- En este diagrama, el administrador hereda las funciones del empleado.
- La correspondencia indica los requisitos relacionados: esta versión sencilla no cubre todos sus detalles.
- La relación `<<include>>` significa que gestionar una factura implica enviarla siempre. Si también se permite guardar solo un borrador, convendría cambiar esa relación.
