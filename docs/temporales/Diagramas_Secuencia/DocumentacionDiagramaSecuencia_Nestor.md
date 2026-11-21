# Diagramas de secuencia · Gestor de facturas Turbine

Documentación de los diagramas de secuencia de tres casos de uso de los diagramas de casos de uso **D3 (Facturación y aprobaciones)** y **D4 (Cobros)**:

| Caso de uso | Nombre | Diagrama de origen | Actores |
|---|---|---|---|
| CU-17 | Emitir factura | D3 | Servicio de Facturación (componente interno) |
| CU-20 | Enviar factura | D3 | Servicio de Emisión (componente interno), Cliente, Proveedor de correo |
| CU-27 | Enviar recordatorios de cobro | D4 | Temporizador, Cliente, Proveedor de correo |



## Participantes internos introducidos

Para no usar otros casos de uso como origen de las llamadas, se han definido dos componentes internos que agrupan esa lógica:

- **Servicio de Facturación:** orquesta la creación de facturas puntuales y recurrentes, las rectificaciones/anulaciones y la resolución de aprobaciones (lo que en D3 son CU-15, CU-16, CU-22 y CU-19). Lo activan el Usuario estándar, el Administrador o el Temporizador.
- **Servicio de Emisión:** solicita el envío de facturas ya emitidas, tanto en la emisión como en los reenvíos (lo que en D3 son CU-17 y CU-21 apuntando a CU-20).
- **Módulo de Envío:** en el diagrama de CU-17 representa la funcionalidad de CU-20 que se invoca al emitir.

---

## 1. CU-17 · Emitir factura

**Descripción:** convierte una factura en borrador (o aprobada) en una factura emitida, con numeración definitiva, y dispara su envío (CU-20).

**Precondición:** existe una factura en estado *Borrador* o *Aprobada* (creada o rectificada por el Servicio de Facturación, o con su aprobación ya concedida).

**Postcondición:** la factura queda *Emitida*, con número correlativo, y se ha lanzado su envío al cliente.

**Flujo principal:**
1. El Servicio de Facturación solicita emitir la factura.
2. El sistema valida los datos de la factura.
3. El sistema asigna el número correlativo y marca la factura como *Emitida*.
4. El sistema persiste los cambios.
5. El sistema incluye CU-20 (Enviar factura).


---

## 2. CU-20 · Enviar factura

**Descripción:** envía por correo electrónico la factura emitida al cliente mediante el proveedor de correo.

**Actores:** Cliente, Proveedor de correo.

**Precondición:** la factura está *Emitida* (o se solicita su reenvío) y el cliente tiene un correo de contacto.

**Postcondición:** el envío queda registrado. Si el proveedor informa de un rebote, se registra mediante CU-26.

**Flujo principal:**
1. El Servicio de Emisión solicita el envío de la factura.
2. El sistema genera el PDF de la factura.
3. El sistema compone el mensaje y lo entrega al proveedor de correo.
4. El proveedor de correo envía el mensaje al cliente.
5. El sistema registra el envío como *Enviada*.

**Flujo alternativo:** el proveedor notifica un rebote → se ejecuta CU-26 (Registrar rebote de envío) y la factura queda marcada como *Envío fallido*.


---

## 3. CU-27 · Enviar recordatorios de cobro

**Descripción:** de forma automática y periódica, el sistema recuerda a los clientes las facturas pendientes de cobro o vencidas.

**Actores:** Temporizador (inicia), Cliente, Proveedor de correo.

**Precondición:** existen facturas emitidas con cobro pendiente que cumplen la regla de recordatorio (por ejemplo, vencidas).

**Postcondición:** cada recordatorio enviado queda registrado.

**Flujo principal:**
1. El Temporizador lanza la ejecución periódica.
2. El sistema busca las facturas pendientes de cobro que requieren recordatorio.
3. Para cada factura, el sistema compone el recordatorio y lo envía mediante el proveedor de correo.
4. El proveedor de correo lo entrega al cliente.
5. El sistema registra el recordatorio enviado.

---

