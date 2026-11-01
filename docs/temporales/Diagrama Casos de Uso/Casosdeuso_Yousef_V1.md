# Gestor de facturas Turbine — Casos de uso V1

Oct 14, 2026 · @Yousef

Primera versión, básica, de los casos de uso del gestor de facturas: 4 actores y 10 casos de uso (clientes, facturas, envío y cobro). 

## Actores

La V1 tiene dos actores que usan la aplicación y dos externos. El Administrador es un Usuario con más permisos, así que hereda todos sus casos de uso.

| Actor | Tipo | Quién es |
| --- | --- | --- |
| Usuario | Principal | Empleado de Turbine que gestiona clientes y facturas |
| Administrador | Principal | Usuario con permisos extra: gestiona usuarios y ve el dashboard |
| Sistema (temporizador) | Secundario | Las tareas automáticas que se ejecutan por fecha, sin que nadie pulse nada |
| Cliente | Secundario | La empresa que recibe las facturas y los recordatorios; no entra en la app |

## Diagrama

El Usuario cubre el día a día (clientes y facturas), el Sistema automatiza la recurrencia, los recordatorios y los cobros, y el Cliente solo recibe.
Crear una factura y emitir las recurrentes **incluyen** siempre enviarla al cliente (`<<include>>`). 
La flecha entre Administrador y Usuario es una **generalización**: el Administrador hereda todos los casos del Usuario.

## Lista de casos de uso

| ID | Caso de uso | Actor | Descripción |
| --- | --- | --- | --- |
| CU-01 | Iniciar sesión | Usuario | Entrar en la app con email corporativo y contraseña |
| CU-02 | Dar de alta cliente | Usuario | Registrar una empresa con su persona de contacto y datos fiscales |
| CU-03 | Consultar clientes | Usuario | Ver el listado de clientes y la ficha de cada uno |
| CU-04 | Crear factura | Usuario | Crear una factura puntual o marcarla como recurrente |
| CU-05 | Consultar facturas | Usuario | Ver las facturas y su estado (emitida, pagada, impagada) |
| CU-06 | Enviar factura | (incluido) | Generar el PDF y enviarlo por email al cliente |
| CU-07 | Emitir facturas recurrentes | Sistema | Crear y enviar el día 1 de cada mes las facturas recurrentes |
| CU-08 | Enviar recordatorio de pago | Sistema | Avisar al cliente de una factura pendiente o vencida |
| CU-09 | Registrar cobro | Sistema | Detectar el pago en Stripe y marcar la factura como pagada |
| CU-10 | Gestionar usuarios | Administrador | Crear, editar y desactivar usuarios de la app |


### CU-01 · Iniciar sesión

1. El usuario introduce email y contraseña.
2. El sistema comprueba que el email es del dominio de Turbine y que la contraseña es correcta.
3. El sistema muestra la pantalla principal.


### CU-02 · Dar de alta cliente

1. El usuario pulsa «Nuevo cliente».
2. Introduce los datos de la empresa (nombre, NIF, dirección), de la persona de contacto y si la facturación es recurrente.
3. El sistema valida los datos y guarda el cliente.


### CU-03 · Consultar clientes

1. El usuario abre el listado de clientes.
2. Puede buscar por nombre o NIF.
3. Selecciona un cliente y el sistema muestra su ficha con sus facturas.

### CU-04 · Crear factura

1. El usuario elige un cliente y pulsa «Nueva factura».
2. Introduce los conceptos e importes; el sistema calcula IVA y total.
3. Indica si es puntual o recurrente.
4. El sistema guarda la factura con número correlativo y estado Emitida.
5. Se ejecuta **CU-06 Enviar factura**.


### CU-05 · Consultar facturas

1. El usuario abre el listado de facturas.
2. Filtra por estado (emitida, pagada, impagada) o por cliente.
3. Abre una factura y puede descargar su PDF.

### CU-06 · Enviar factura

1. El sistema genera el PDF de la factura.
2. El sistema lo envía por email al cliente, con los datos de contacto de soporte.


### CU-07 · Emitir facturas recurrentes

1. El día 1 de cada mes, el sistema busca los clientes con facturación recurrente activa.
2. Para cada uno crea la factura del mes.
3. Se ejecuta **CU-06 Enviar factura**.

### CU-08 · Enviar recordatorio de pago

1. Cada día, el sistema revisa las facturas no pagadas.
2. Si es el día 3 o 5 desde la emisión, envía un recordatorio al cliente.
3. Si han pasado más de 5 días, marca la factura como Impagada y sigue enviando recordatorios periódicos hasta el cobro.

### CU-09 · Registrar cobro

1. Stripe informa de un pago.
2. El sistema identifica la factura y la marca como Pagada.
3. El sistema deja de enviar recordatorios de esa factura.

### CU-10 · Gestionar usuarios

1. El administrador abre la lista de usuarios.
2. Crea, edita o desactiva un usuario.
3. El sistema guarda los cambios.


