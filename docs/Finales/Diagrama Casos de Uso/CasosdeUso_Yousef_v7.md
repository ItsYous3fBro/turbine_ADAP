# Especificación de Casos de Uso

**Proyecto:** Gestor de facturas Turbine
**Grupo:** Ramón Llull · 3º B
**Documento base:** Documento de Requisitos v3 (BR1–BR9, UR1–UR18, FR1–FR28, NFR1–NFR13)

---

## 1. Introducción y convenciones

El documento contiene los actores (2), los diagramas UML con sus relaciones (3), las fichas de los 42 casos de uso agrupadas por diagrama (4), la trazabilidad con los requisitos (5) y las decisiones de modelado y cuestiones abiertas (6).

**Formato de las fichas** 

- **Identificador y nombre** en el título (verbo + objeto). **Ámbito:** siempre el sistema *Gestor de facturas Turbine*. Las fichas describen **qué** hace el sistema, sin elementos de interfaz.
- **Nivel:** todos los casos son de *objetivo de usuario* salvo los marcados como *subfunción* en la fila *Actores* (los ejecuta solo el sistema, se reutilizan mediante `<<include>>`/`<<extend>>` y no tienen actor propio). Un caso que extiende a otro y en el que el actor interactúa (CU-02, CU-33, CU-35) es de objetivo de usuario. En la fila *Actores* figura primero el actor principal.
- **Precondiciones:** se asumen ciertas y el sistema no las comprueba; lo que sí comprueba está en los flujos.
- **Flujos alternativos:** variantes válidas. **Flujos de excepción:** errores que terminan el caso o devuelven a un paso anterior para corregir. Se numeran con el paso en que ocurren (`3a`, `3a1`; `*a` = cualquier paso). Los pasos internos de un flujo alternativo extenso se numeran `1a.1`, `1a.2`…, y sus excepciones `1a.2a`. Las extensiones comunes (E1, E2, A1) están en R5 y no se repiten.
- **Relaciones:** fila presente solo si el caso participa en un `<<include>>` o `<<extend>>` dibujado en el diagrama. «(véase CU-XX)» es una referencia de navegación, no una relación UML.

**Reglas transversales** (se aplican a todos los casos y no se repiten en las fichas)

- **R1 · Sesión y autorización.** Todo caso iniciado por un empleado (salvo CU-01, CU-02 y CU-03) tiene como precondición que el empleado ha iniciado sesión (CU-01). El control de acceso por roles (FR2) se aplica en todos los pasos: un usuario estándar solo ve y actúa sobre los clientes que tiene asignados; un administrador, sobre todos. La sesión expira tras 30 minutos de inactividad y, al desactivar una cuenta o retirarle un rol, sus sesiones se invalidan en menos de 1 minuto (NFR5).
- **R2 · Notificaciones internas (FR21).** Cuando un flujo indica que «el sistema notifica al responsable», el destinatario es el responsable vigente del cliente, o el delegado si hay una delegación activa (FR4). Si la notificación no se refiere a un cliente concreto, se dirige a los administradores. Las aprobaciones se notifican a los administradores (excepto al creador) y los fallos técnicos al superadministrador. Toda notificación aparece en el trabajo diario del destinatario (CU-31) y se envía por los canales configurados (CU-42).
- **R3 · Auditoría (FR26).** Toda acción que crea, modifica o cambia el estado de un dato económico, de un cliente o de un permiso queda registrada con usuario (o proceso), fecha, valor anterior y valor nuevo. Ninguna factura emitida ni ningún registro de auditoría puede modificarse ni borrarse por ninguna vía (interfaz de usuario o API); todo intento se rechaza y se registra (FR15, FR26).
- **R4 · Estados de la factura (FR14).** *Borrador → (pendiente de aprobación → aprobada) → emitida → enviada / no notificada → vencida → pagada*. Estados finales alternativos: *rectificada* y *anulada*. Una factura *enviada* o *no notificada* pasa a *vencida* cuando supera su fecha de vencimiento sin pagarse (CU-27). *Incobrable* es una marca administrativa sobre una factura vencida (FR19) y *migrada*, la marca de las facturas importadas del histórico (FR8); ninguna de las dos genera recordatorios.
- **R5 · Flujos comunes (válidos en cualquier paso de cualquier caso; no se repiten en las fichas).**
  - **E1 · Sin autorización o sesión caducada (FR2, NFR5).** Si el actor no tiene permiso sobre el recurso, su sesión ha expirado o, en la API, sus credenciales no son válidas, el sistema deniega la acción sin revelar datos ajenos y no aplica ningún cambio.
  - **E2 · Fallo técnico durante un cambio (NFR7).** Si una operación falla antes de confirmarse, el sistema la deshace por completo, conserva el estado anterior e informa del error; nunca presenta como completa una operación parcial y un reintento no duplica facturas ni cobros. En los procesos por lotes, el fallo de un elemento no detiene a los demás.
  - **A1 · Cancelar antes de confirmar.** El actor puede abandonar el caso antes de confirmar; el sistema no aplica ningún cambio. Cancelar nunca deshace una emisión o un cobro ya registrados.
- **R6 · Baja pendiente (FR11).** Cuando la deuda de un cliente en *baja pendiente* (CU-07, 1c.2a) queda a cero, por un cobro (CU-30, CU-29), una anulación (CU-22) o una declaración de incobrable (CU-23), el sistema notifica a su responsable que puede completar la baja (CU-07, 1c).

---

## 2. Actores

| Actor | Tipo | Descripción | Requisitos |
|---|---|---|---|
| **Usuario estándar** | Principal | Empleado que gestiona los clientes que tiene asignados: clientes, facturas, cobros e incidencias. | UR1–UR8 |
| **Administrador** | Principal | Especialización del usuario estándar con acceso a todos los clientes. Aprueba operaciones, asigna clientes, gestiona usuarios estándar, concilia cobros y analiza la información financiera. | UR9–UR14 |
| **Superadministrador** | Principal | Especialización del administrador. Gestiona los roles de administración, configura el sistema y consulta la auditoría y la salud técnica. | UR15–UR17 |
| **Empleado invitado** | Principal | Persona invitada que aún no ha activado su cuenta. | FR3 |
| **Sistema externo** | Principal | Software autorizado que usa la API con el rol técnico de FR2 (p. ej., Brain en el futuro). | UR18 |
| **Temporizador** | Principal | Reloj del sistema. Inicia los procesos programados por fecha (facturación del día 1, recordatorios, revisión anual, vencimientos diarios, envíos programados). | FR10, FR13, FR19, FR25 |
| **Pasarela de pago (Stripe)** | Principal / Secundario | Servicio de cobro (simulado en el proyecto). Comunica los pagos recibidos y cobra el prorrateo inmediato. | FR13, FR20 |
| **Proveedor de identidad (Microsoft)** | Secundario | Autentica a los empleados con cuenta Microsoft corporativa. | FR1 |
| **Proveedor de correo** | Principal / Secundario | Servicio que entrega los correos del sistema (simulado en el proyecto). Comunica los rebotes de los envíos. | FR18 |
| **Cliente** | Secundario | Empresa cliente de Turbine. Recibe facturas y recordatorios; no accede a la aplicación. | FR18, FR19 |
| **Gestoría** | Secundario | Recibe el paquete periódico de documentación; no tiene usuario. | FR25 |

**Generalización de actores.** `Superadministrador → Administrador → Usuario estándar`. Cada rol hereda los casos de uso del anterior (FR2), por lo que cada caso se asocia solo al rol mínimo que puede iniciarlo.

---

## 3. Diagramas de casos de uso

El modelo se divide en seis diagramas por área funcional. Todos comparten actores y numeración, y cada uno dibuja el límite del sistema.

| Diagrama | Archivo PlantUML | Casos de uso |
|---|---|---|
| D1 · Acceso, usuarios y permisos | `DiagramaCU_D1_Acceso.puml` | CU-01 a CU-06 |
| D2 · Clientes y suscripciones | `DiagramaCU_D2_Clientes.puml` | CU-07 a CU-14 |
| D3 · Facturación y aprobaciones | `DiagramaCU_D3_Facturacion.puml` | CU-15 a CU-26 |
| D4 · Cobros | `DiagramaCU_D4_Cobros.puml` | CU-27 a CU-30 |
| D5 · Seguimiento y trabajo diario | `DiagramaCU_D5_Seguimiento.puml` | CU-31 a CU-36 |
| D6 · Información y configuración | `DiagramaCU_D6_Control.puml` | CU-37 a CU-42 |

**Relaciones entre casos de uso**

| Relación | Origen → destino | Punto de extensión / condición |
|---|---|---|
| `<<extend>>` | CU-02 → CU-01 | *recuperación* (paso 1): el empleado no recuerda su contraseña |
| `<<extend>>` | CU-10 → CU-09 | *prorrateo* (paso 6): alta de la suscripción a mitad de periodo |
| `<<include>>` | CU-10 → CU-17 | La factura de prorrateo se emite con el proceso común |
| `<<include>>` | CU-11 → CU-18 | Toda excepción comercial se retiene para aprobación |
| `<<include>>` | CU-12 → CU-18 | Toda propuesta de cambio de nivel se retiene para aprobación |
| `<<include>>` | CU-15, CU-16, CU-22 → CU-17 | Toda factura (puntual, recurrente o rectificativa) se emite con el proceso común |
| `<<extend>>` | CU-18 → CU-15 y CU-22 | *aprobación* (paso 7 de CU-15 y paso 6 de CU-22): se cumple la condición de retención definida en CU-18 |
| `<<extend>>` | CU-17 → CU-19 | *aprobación concedida* (paso 4): la operación aprobada es una factura o una rectificativa |
| `<<include>>` | CU-17 → CU-20 | Toda factura emitida se envía al cliente |
| `<<include>>` | CU-21 → CU-20 | El reenvío usa el mismo proceso de envío |
| `<<include>>` | CU-28, CU-29 → CU-30 | Todo cobro, venga de Stripe o de un movimiento bancario, se vincula igual |
| `<<extend>>` | CU-33 → CU-32 | *pausa* (paso 1): la incidencia afecta al cobro de una factura |
| `<<extend>>` | CU-35 → CU-34 | *acción masiva* (paso 3): el empleado selecciona varios resultados |

---

## 4. Especificación de los casos de uso

### 4.1 Acceso, usuarios y permisos (D1)

#### CU-01 · Iniciar sesión

| **Objetivo** | El empleado accede de forma segura a la aplicación para trabajar con lo que su rol le permite. |
|---|---|
| **Actores** | Usuario estándar · secundarios: Proveedor de identidad (Microsoft) |
| **Relaciones** | Extendido por CU-02 · Punto de extensión *recuperación* (paso 1) |
| **Requisitos** | FR1, FR2 · NFR4, NFR5 · BR8 |
| **Interesados** | Empleado: entrar rápido desde cualquier dispositivo. Turbine: que solo accedan empleados autorizados y que los datos fiscales estén protegidos. |
| **Precondiciones** | Ninguna. |
| **Disparador** | El empleado solicita acceder a la aplicación. |
| **Postcondición** | Existe una sesión activa asociada al empleado y a los permisos de su rol. |
| **Garantía mínima** | Ningún acceso se concede sin autenticación válida; todo intento, correcto o fallido, queda registrado. |

**Escenario principal**

1. El empleado introduce su correo corporativo y su contraseña. Punto de extensión *recuperación*.
2. El sistema comprueba que el correo pertenece al dominio corporativo de Turbine.
3. El sistema verifica las credenciales.
4. El sistema comprueba que la cuenta está activa y no bloqueada.
5. El sistema crea la sesión con los permisos del rol del empleado y le presenta su trabajo diario.

**Flujos alternativos**

- **1a. Acceso con cuenta Microsoft corporativa:** el empleado elige acceder con Microsoft. El sistema delega la autenticación en el proveedor de identidad, recibe la identidad verificada, comprueba que pertenece a la organización de Turbine y continúa en el paso 4.

**Flujos de excepción**

- **1a1. El proveedor de identidad rechaza la autenticación o no responde:** el sistema informa de que no ha sido posible validar la cuenta Microsoft y no crea la sesión.
- **2a. Dominio no autorizado:** el sistema rechaza el acceso con un mensaje genérico.
- **3a. Credenciales incorrectas:** el sistema informa del error sin indicar qué dato falla y contabiliza el intento. Al quinto intento fallido consecutivo bloquea temporalmente la cuenta (FR1, NFR5).
- **4a. Cuenta desactivada o bloqueada:** el sistema deniega el acceso e informa de que la cuenta no está disponible.

---

#### CU-02 · Recuperar contraseña

| **Objetivo** | El empleado restablece su contraseña sin intervención de un administrador. |
|---|---|
| **Actores** | Usuario estándar |
| **Relaciones** | Extiende CU-01 (punto *recuperación*) |
| **Requisitos** | FR1 · NFR4 |
| **Interesados** | Empleado: recuperar el acceso. Turbine: que nadie más pueda apropiarse de la cuenta. |
| **Precondiciones** | Ninguna. |
| **Disparador** | El empleado indica en CU-01 que no recuerda su contraseña. |
| **Postcondición** | La contraseña ha cambiado, el enlace usado queda invalidado y las sesiones anteriores del empleado se cierran. |
| **Garantía mínima** | El sistema no revela si un correo está registrado; los enlaces caducan aunque no se usen. |

**Escenario principal**

1. El empleado introduce su correo corporativo.
2. El sistema comprueba que el correo corresponde a una cuenta activa con contraseña y le envía un enlace de recuperación de un solo uso que caduca en 15 minutos (NFR4).
3. El empleado abre el enlace e introduce una nueva contraseña.
4. El sistema comprueba que la contraseña cumple la política de seguridad, la guarda de forma segura, invalida el enlace y cierra las sesiones anteriores.

**Flujos alternativos:** ninguno.

**Flujos de excepción**

- **2a. Correo no registrado o cuenta Microsoft:** el sistema muestra el mismo mensaje de confirmación que en el caso normal, pero no envía ningún enlace.
- **3a. Enlace caducado o ya usado:** el sistema rechaza el enlace y ofrece solicitar uno nuevo (vuelve al paso 1).
- **4a. Contraseña no válida:** el sistema indica los requisitos incumplidos y vuelve al paso 3.

---

#### CU-03 · Activar cuenta

| **Objetivo** | La persona invitada activa su cuenta para poder acceder al sistema. |
|---|---|
| **Actores** | Empleado invitado · secundarios: Proveedor de identidad (Microsoft) |
| **Requisitos** | FR3, FR1 |
| **Interesados** | Empleado invitado: empezar a trabajar. Administrador: que solo se activen las cuentas que ha invitado. |
| **Precondiciones** | Ninguna. |
| **Disparador** | El empleado invitado abre el enlace de invitación recibido por correo. |
| **Postcondición** | La cuenta queda activa con el rol asignado y el empleado puede iniciar sesión. |
| **Garantía mínima** | Una invitación caducada o revocada nunca activa una cuenta. |

**Escenario principal**

1. El empleado invitado abre el enlace de invitación.
2. El sistema comprueba que la invitación existe, no ha caducado y no ha sido revocada, y solicita una contraseña.
3. El empleado invitado define su contraseña.
4. El sistema comprueba la política de seguridad, activa la cuenta y confirma que ya puede iniciar sesión.

**Flujos alternativos**

- **3a. Cuenta Microsoft corporativa:** el empleado invitado vincula su cuenta Microsoft en lugar de definir una contraseña; el sistema verifica la identidad con el proveedor y continúa en el paso 4.

**Flujos de excepción**

- **2a. Invitación caducada o revocada:** el sistema lo informa y no activa la cuenta.
- **4a. Contraseña no válida:** el sistema indica los requisitos incumplidos y vuelve al paso 3.

---

#### CU-04 · Gestionar usuarios estándar

| **Objetivo** | El administrador mantiene las cuentas de los usuarios estándar: alta con invitación, invitaciones pendientes y desactivación. |
|---|---|
| **Actores** | Administrador · secundarios: Empleado invitado (recibe la invitación) |
| **Requisitos** | FR3, FR1 · UR11, UR16 |
| **Interesados** | Turbine: que solo trabajen empleados actuales y que se conserve el historial de quien se va. |
| **Precondiciones** | R1. |
| **Disparador** | Se incorpora o se marcha un empleado. |
| **Postcondición** | La cuenta queda creada e invitada, o desactivada con su historial conservado. |
| **Garantía mínima** | Nunca se borra físicamente una cuenta; este caso no modifica cuentas de administración (véase CU-05). |

**Escenario principal (alta)**

1. El administrador solicita dar de alta un usuario estándar.
2. El sistema solicita nombre y correo corporativo.
3. El administrador introduce los datos.
4. El sistema comprueba que el correo es del dominio corporativo y que no existe otra cuenta con él.
5. El sistema crea la cuenta en estado *invitada* con rol *Usuario estándar* y envía al empleado una invitación con caducidad.

**Flujos alternativos**

- **1a. Reenviar una invitación pendiente:** el administrador selecciona una cuenta *invitada*; el sistema invalida la invitación anterior y envía una nueva con caducidad renovada.
- **1b. Revocar una invitación pendiente:** el administrador selecciona una cuenta *invitada*; el sistema invalida la invitación y marca la cuenta como *desactivada*.
- **1c. Desactivar un usuario estándar:** el administrador selecciona el usuario y confirma; el sistema marca la cuenta como *desactivada*, invalida sus sesiones (R1) y conserva su historial (FR1, FR3).

**Flujos de excepción**

- **1c1. El usuario tiene clientes asignados:** el sistema impide la desactivación hasta que se reasignen (véase CU-06), para que ningún cliente quede sin responsable.
- **4a. Correo de otro dominio o ya registrado:** el sistema rechaza el alta e indica el motivo.

---

#### CU-05 · Gestionar roles de administración

| **Objetivo** | El superadministrador decide quién tiene permisos de administración y supervisa su uso. |
|---|---|
| **Actores** | Superadministrador |
| **Requisitos** | FR2, FR3 · UR16 |
| **Interesados** | Turbine: control centralizado de quién puede aprobar operaciones económicas y gestionar usuarios. |
| **Precondiciones** | R1. |
| **Disparador** | El superadministrador decide revisar o cambiar los permisos de administración. |
| **Postcondición** | Los roles quedan actualizados en el modelo de autorización centralizado y se aplican a las sesiones activas afectadas. |
| **Garantía mínima** | Siempre existe al menos un superadministrador activo. |

**Escenario principal**

1. El superadministrador solicita la relación de usuarios con rol de administración.
2. El sistema la presenta con su rol, su estado y su actividad reciente relevante.
3. El superadministrador concede o retira el rol de administrador o de superadministrador a un usuario.
4. El sistema aplica el cambio en el modelo de autorización y en las sesiones activas del usuario afectado.

**Flujos alternativos**

- **3a. Desactivar un administrador:** el superadministrador desactiva la cuenta; el sistema invalida sus sesiones (R1) y conserva su historial.

**Flujos de excepción**

- **3b. El cambio dejaría el sistema sin superadministradores activos:** el sistema lo impide.

---

#### CU-06 · Asignar y delegar clientes

| **Objetivo** | El administrador decide qué empleado es responsable de cada cliente, de forma permanente o temporal. |
|---|---|
| **Actores** | Administrador |
| **Requisitos** | FR4 · UR10 |
| **Interesados** | Responsables: recibir solo lo suyo. Turbine: que ningún cliente quede sin responsable. |
| **Precondiciones** | R1. |
| **Disparador** | Cambio de cartera, cliente nuevo sin responsable o ausencia de un empleado. |
| **Postcondición** | El cliente tiene un responsable vigente, que hereda sus tareas pendientes y recibe sus notificaciones futuras. |
| **Garantía mínima** | Un cliente nunca tiene dos responsables vigentes a la vez. |

**Escenario principal (asignación o reasignación)**

1. El administrador selecciona uno o varios clientes.
2. El administrador elige el usuario estándar que será su responsable.
3. El sistema comprueba que el usuario está activo, lo asigna como responsable y le transfiere las tareas pendientes de esos clientes.

**Flujos alternativos**

- **2a. Delegación temporal:** el administrador indica además una fecha de inicio y una de fin. El sistema registra la delegación; su inicio y su fin los aplica CU-36, que devuelve el cliente al responsable original al terminar.

**Flujos de excepción**

- **2a1. La fecha de fin es anterior a la de inicio:** el sistema rechaza la delegación.
- **3a. El usuario destino está desactivado:** el sistema rechaza la asignación.

---

### 4.2 Clientes y suscripciones (D2)

#### CU-07 · Gestionar cliente

| **Objetivo** | El empleado mantiene los clientes de su cartera durante todo su ciclo de vida: alta, consulta y actualización de la ficha y sus contactos, reactivación y baja. |
|---|---|
| **Actores** | Usuario estándar |
| **Requisitos** | FR4, FR5, FR6, FR7, FR11, FR12, FR26 · UR1 · BR5, BR7 · NFR1, NFR6 |
| **Interesados** | Empleado: dar de alta rápido y tener datos correctos para facturar. Turbine: datos fiscales válidos de cualquier país y sin duplicados, sin perder el histórico ni los ingresos pendientes y conservando los datos que exige la ley. Cliente: dejar de recibir mensualidades tras su baja. Contactos: que se respeten sus datos personales. |
| **Precondiciones** | R1. |
| **Disparador** | Llega un cliente nuevo, el empleado necesita consultar o cambiar los datos de un cliente, un cliente dado de baja vuelve, o el cliente comunica su baja (directamente o mediante una tarea de baja creada por CU-14). |
| **Postcondición** | Alta: la ficha queda registrada, con sus contactos y un responsable asignado. Modificación: la ficha refleja los datos vigentes y el cambio aparece en la línea de tiempo del cliente. Reactivación: la ficha vuelve a estar activa con su historial. Baja: la suscripción está cancelada y la ficha queda *dada de baja* (borrado lógico), o en *baja pendiente* si el cliente tiene deuda. |
| **Garantía mínima** | Nunca existen dos clientes con el mismo identificador fiscal; ningún cambio altera facturas ya emitidas ni el historial de destinatarios; nunca se borran físicamente datos ni facturas del cliente. |

**Escenario principal (alta)**

1. El empleado solicita dar de alta un cliente.
2. El sistema solicita los datos de la empresa: nombre comercial, razón social, identificador fiscal, domicilio, país, moneda y representante legal.
3. El empleado introduce los datos.
4. El sistema comprueba que no existe otro cliente con ese identificador fiscal, sin asumir formato español (FR5, FR7).
5. El empleado añade una o varias personas de contacto con su función (comercial, administración o impagos).
6. El sistema registra la ficha y asigna como responsable al empleado que la ha creado.

**Flujos alternativos**

- **1a. Consultar y actualizar la ficha:**
  - 1a.1 El empleado selecciona un cliente de su cartera.
  - 1a.2 El sistema presenta la ficha: datos fiscales, contactos, suscripción, facturas y línea de tiempo con su historial comercial (FR26).
  - 1a.3 El empleado modifica datos de la empresa o de sus contactos, desactiva un contacto (pasa a *inactivo* y se conserva el historial de destinatarios de las facturas ya enviadas, FR6) o lo anonimiza (sus datos personales se sustituyen por valores anónimos sin alterar las facturas en las que aparece, NFR6).
  - 1a.4 El sistema valida los datos y guarda la nueva versión.
- **1b. Reactivar un cliente dado de baja:** el empleado selecciona la ficha; si la ley permite conservar sus datos, el sistema recupera la ficha y su historial y la marca como activa (FR5, FR11).
- **1c. Dar de baja:**
  - 1c.1 El empleado selecciona un cliente e indica el motivo.
  - 1c.2 El sistema comprueba que no tiene facturas emitidas pendientes de pago, presenta el resumen de la baja y pide confirmación.
  - 1c.3 El empleado confirma.
  - 1c.4 El sistema cancela la suscripción y marca la ficha como *dada de baja*.
- **6a. Alta realizada por un administrador:** el administrador elige el usuario estándar responsable antes de registrar la ficha.
- **6b. Datos fiscales incompletos:** el sistema registra la ficha marcada como *incompleta*; no se podrá emitir ninguna factura a ese cliente hasta completarla (FR12).
- **6c. Alta encadenada:** el empleado continúa directamente con el alta de la suscripción del cliente (véase CU-09), para completar el proceso en pocos pasos (NFR1).

**Flujos de excepción**

- **1a.4a. Datos no válidos o identificador fiscal que ya usa otro cliente:** el sistema rechaza el cambio, indica el motivo y vuelve a 1a.3.
- **1b1. La ley no permite conservar los datos del cliente:** el sistema no reactiva la ficha e informa de que debe darse de alta como cliente nuevo.
- **1c.2a. El cliente tiene deuda pendiente:** el sistema informa de que la baja definitiva no es posible mientras exista deuda (FR11) y ofrece la baja pendiente; si el empleado la confirma (1c.3), cancela la suscripción para que no se emitan nuevas mensualidades, mantiene los recordatorios de la deuda y deja el cliente en *baja pendiente* (R6).
- **4a. Identificador fiscal duplicado:** el sistema alerta, impide el alta y ofrece abrir la ficha existente o, si estaba dada de baja, reactivarla (1b).

---

#### CU-08 · Importar datos desde CSV

| **Objetivo** | El administrador carga de forma masiva clientes o el histórico de facturas. |
|---|---|
| **Actores** | Administrador |
| **Requisitos** | FR8, FR7, FR4 · NFR12 |
| **Interesados** | Turbine: migrar los datos existentes sin teclearlos y sin introducir duplicados. |
| **Precondiciones** | R1. |
| **Disparador** | El administrador dispone de un archivo CSV con clientes o facturas históricas. |
| **Postcondición** | Las filas válidas quedan importadas y se dispone de un informe con las filas fallidas y su motivo. |
| **Garantía mínima** | Ninguna fila errónea o duplicada se importa; las facturas migradas nunca generan cobros ni recordatorios. |

**Escenario principal**

1. El administrador aporta el archivo CSV e indica el tipo de datos (clientes o histórico de facturas).
2. El sistema analiza el archivo y propone un mapeo de columnas, incluida la columna del responsable de cada cliente.
3. El administrador revisa y ajusta el mapeo.
4. El sistema presenta una previsualización con las filas válidas y las erróneas (incluidos los duplicados por identificador fiscal, FR7).
5. El administrador confirma la importación.
6. El sistema importa en segundo plano las filas válidas, informando del progreso, y presenta un resumen con un informe de errores descargable.

**Flujos alternativos**

- **1a. Histórico de facturas:** las facturas se importan en estado *migrada*; conservan su número original y no generan cobros ni recordatorios (FR8).
- **2a. El archivo no indica responsable:** el administrador elige un responsable para todos los clientes importados.

**Flujos de excepción**

- **2b. Archivo ilegible o con codificación distinta de UTF-8:** el sistema lo rechaza e indica el motivo.

---

#### CU-09 · Gestionar suscripción

| **Objetivo** | El empleado establece la relación comercial del cliente (nivel de tarifa y fechas) y la pausa o la reactiva. |
|---|---|
| **Actores** | Usuario estándar |
| **Relaciones** | Extendido por CU-10 · Punto de extensión *prorrateo* (paso 6) |
| **Requisitos** | FR9, FR10, FR12, FR13 · UR2 · BR5 · NFR1, NFR9 |
| **Interesados** | Turbine: facturar cada mes el importe correcto sin trabajo manual. Cliente: pagar lo acordado. |
| **Precondiciones** | R1. El cliente está registrado (CU-07). |
| **Disparador** | El cliente contrata, pausa o reactiva el servicio. |
| **Postcondición** | La suscripción queda registrada con su historial de cambios y la facturación recurrente la tendrá en cuenta. |
| **Garantía mínima** | Ningún cambio de suscripción altera facturas ya emitidas. |

**Escenario principal (alta)**

1. El empleado solicita crear la suscripción del cliente.
2. El sistema comprueba que los datos fiscales del cliente están completos (FR12) y propone el nivel de tarifa que corresponde a su facturación anual según la tabla vigente (FR10).
3. El empleado confirma el nivel e indica la fecha de inicio.
4. El sistema presenta la vista previa de la primera factura y de la mensualidad recurrente, con el prorrateo si el alta es a mitad de periodo (FR13, UR2).
5. El empleado confirma el alta.
6. El sistema registra la suscripción como *activa*. Punto de extensión *prorrateo*.

**Flujos alternativos**

- **1a. Pausar:** el empleado selecciona una suscripción activa e indica motivo y fecha de reactivación; el sistema la excluye de la facturación recurrente hasta esa fecha, en la que CU-36 la reactiva automáticamente y avisa al responsable.
- **1b. Reactivar antes de tiempo:** el empleado reactiva una suscripción pausada; el sistema la vuelve a facturar desde el siguiente periodo.
- **2a. Cliente sin facturación anual registrada:** el empleado indica el volumen anual estimado y el sistema propone el nivel a partir de él.
- **3a. Inicio futuro (el día 1 de un mes próximo, véase P4):** el sistema registra la suscripción como *programada*, sin prorrateo; no genera facturas hasta su fecha de inicio, que aplica CU-36 (FR9, FR13).
- **3b. Condición distinta de la tarifa estándar:** el empleado registra la suscripción con la tarifa estándar y propone la excepción (véase CU-11); no se aplica hasta su aprobación.

**Flujos de excepción**

- **2b. Datos fiscales del cliente incompletos:** el sistema impide crear la suscripción e indica los datos que faltan.

---

#### CU-10 · Facturar prorrateo

| **Objetivo** | Facturar y cobrar de inmediato la parte proporcional del primer mes de una suscripción dada de alta a mitad de periodo. |
|---|---|
| **Actores** | — (*subfunción*) · secundarios: Pasarela de pago (Stripe) |
| **Relaciones** | Extiende CU-09 (punto *prorrateo*); incluye CU-17 |
| **Requisitos** | FR13 · NFR9 |
| **Interesados** | Turbine: cobrar desde el primer día sin esperar al día 1. Cliente: pagar solo los días contratados. |
| **Precondiciones** | La suscripción se ha registrado en CU-09 con fecha de inicio a mitad de periodo. |
| **Disparador** | Condición del punto de extensión *prorrateo* de CU-09. |
| **Postcondición** | La factura de prorrateo está emitida y enviada, y se ha solicitado su cobro inmediato a la pasarela. |
| **Garantía mínima** | El importe prorrateado es exacto al céntimo, también en fin de mes y en años bisiestos (NFR9). |

**Escenario principal**

1. El sistema calcula el importe proporcional desde la fecha de inicio hasta el fin del mes.
2. El sistema prepara la factura de prorrateo con ese importe.
3. El sistema incluye CU-17 (emitir factura).
4. El sistema solicita a la pasarela de pago el cobro inmediato de la factura.

**Flujos alternativos:** ninguno.

**Flujos de excepción**

- **4a. La pasarela rechaza o no procesa el cobro:** la factura queda *enviada* sin cobrar; su cobro sigue el flujo normal de recordatorios (CU-27).

---

#### CU-11 · Proponer excepción comercial

| **Objetivo** | El empleado solicita un descuento o un ajuste de tarifa para un cliente. |
|---|---|
| **Actores** | Usuario estándar |
| **Relaciones** | Incluye CU-18 |
| **Requisitos** | FR10, FR16 · UR4 · BR3 |
| **Interesados** | Empleado: cerrar acuerdos con el cliente. Turbine: que ninguna rebaja se aplique sin control (BR3). |
| **Precondiciones** | R1. El cliente tiene una suscripción. |
| **Disparador** | Se negocia con el cliente una condición distinta de la tarifa estándar. |
| **Postcondición** | La propuesta queda pendiente de aprobación. |
| **Garantía mínima** | La excepción no se aplica a ninguna factura hasta que se aprueba. |

**Escenario principal**

1. El empleado selecciona el cliente e indica el tipo de excepción (descuento o ajuste de tarifa), el importe o porcentaje, la vigencia y el motivo.
2. El sistema presenta el efecto previsto sobre las próximas facturas.
3. El empleado confirma la propuesta.
4. El sistema incluye CU-18 (retener para aprobación).

**Flujos alternativos:** ninguno.

**Flujos de excepción**

- **3a. Falta el motivo o la vigencia:** el sistema exige completarlos y vuelve al paso 1.

---

#### CU-12 · Proponer revisión anual de tarifa

| **Objetivo** | El sistema propone una vez al año el nivel de tarifa que corresponde a cada cliente según su facturación anual. |
|---|---|
| **Actores** | Temporizador |
| **Relaciones** | Incluye CU-18 |
| **Requisitos** | FR10 · BR3 |
| **Interesados** | Turbine: cobrar según el tamaño real de cada cliente sin revisarlo a mano. |
| **Precondiciones** | Existe una tabla de tarifas configurada (CU-42). |
| **Disparador** | Llega la fecha anual de revisión configurada. |
| **Postcondición** | Por cada cliente cuyo rango ha cambiado existe una propuesta de cambio de nivel pendiente de aprobación. |
| **Garantía mínima** | Ningún nivel cambia sin aprobación humana (FR10). |

**Escenario principal**

1. El temporizador inicia la revisión anual.
2. El sistema compara la facturación anual de cada cliente activo con los rangos de la tabla vigente.
3. Para cada cliente cuyo rango ha cambiado, el sistema crea una propuesta de cambio de nivel e incluye CU-18 (retener para aprobación).
4. El sistema registra la ejecución y el número de propuestas generadas.

**Flujos alternativos:** ninguno.

**Flujos de excepción**

- **2a. Falta la facturación anual de algún cliente:** el sistema lo excluye de la revisión y notifica a su responsable para que la complete.

---

#### CU-13 · Consultar deuda de cliente

| **Objetivo** | Un sistema externo sabe si un cliente tiene facturas pendientes de pago y por qué importe. |
|---|---|
| **Actores** | Sistema externo |
| **Requisitos** | FR28, FR2, FR11 · UR18 · BR9 · NFR4 |
| **Interesados** | Sistema externo: decidir con datos fiables. Turbine: no exponer más datos de los necesarios. |
| **Precondiciones** | Ninguna. |
| **Disparador** | El sistema externo consulta la deuda de un cliente por su identificador. |
| **Postcondición** | El solicitante conoce si existe deuda, su importe y el número de facturas pendientes. |
| **Garantía mínima** | La respuesta no incluye datos personales de los contactos; toda petición queda registrada. |

**Escenario principal**

1. El sistema externo envía la consulta con el identificador del cliente y sus credenciales.
2. El sistema autentica y autoriza la petición con el rol técnico de API (FR2).
3. El sistema calcula el importe y el número de facturas emitidas no pagadas del cliente.
4. El sistema devuelve el resultado.

**Flujos alternativos:** ninguno.

**Flujos de excepción**

- **3a. Cliente inexistente:** el sistema responde que no se encuentra.

---

#### CU-14 · Solicitar baja por API

| **Objetivo** | Un sistema externo comunica que un cliente quiere darse de baja, sin que la baja se ejecute automáticamente. |
|---|---|
| **Actores** | Sistema externo |
| **Requisitos** | FR11, FR28 · UR18 |
| **Interesados** | Sistema externo: que la solicitud se atienda. Turbine: que ninguna baja se ejecute sin revisión humana. |
| **Precondiciones** | Ninguna. |
| **Disparador** | El sistema externo envía una solicitud de baja. |
| **Postcondición** | Existe una tarea de revisión de baja para el responsable del cliente, que la tramitará con CU-07 (baja, 1c). |
| **Garantía mínima** | No se modifica ni se borra ningún dato del cliente. |

**Escenario principal**

1. El sistema externo envía la solicitud de baja con el identificador del cliente, el motivo y sus credenciales.
2. El sistema autentica y autoriza la petición.
3. El sistema crea una tarea de revisión de baja y notifica al responsable del cliente (R2).
4. El sistema confirma la recepción al sistema externo.

**Flujos alternativos**

- **3a. Ya existe una solicitud de baja abierta para ese cliente:** el sistema no duplica la tarea y responde con la existente.

**Flujos de excepción**

- **3b. Cliente inexistente:** el sistema responde que no se encuentra y no crea ninguna tarea.

---

### 4.3 Facturación y aprobaciones (D3)

#### CU-15 · Crear factura puntual

| **Objetivo** | El empleado factura a un cliente un servicio no recurrente (p. ej., una instalación). |
|---|---|
| **Actores** | Usuario estándar |
| **Relaciones** | Incluye CU-17; extendido por CU-18 · Punto de extensión *aprobación* (paso 7) |
| **Requisitos** | FR12, FR14, FR16 · UR2 · BR1, BR4 · NFR1, NFR9 |
| **Interesados** | Empleado: facturar en pocos pasos. Turbine: facturas correctas y legales. Cliente: una factura clara. |
| **Precondiciones** | R1. |
| **Disparador** | Se presta al cliente un servicio que debe facturarse aparte de su suscripción. |
| **Postcondición** | La factura está emitida y enviada, o retenida pendiente de aprobación. |
| **Garantía mínima** | Ninguna factura se emite sin pasar por CU-17; si el proceso se interrumpe, el borrador se conserva. |

**Escenario principal**

1. El empleado solicita crear una factura para un cliente de su cartera.
2. El sistema crea un borrador con los datos fiscales del cliente.
3. El empleado añade líneas (concepto, cantidad, precio, descuento e impuesto), eligiéndolas del catálogo de conceptos o redactándolas.
4. El sistema calcula los totales y el vencimiento según el plazo configurado.
5. El sistema presenta la vista previa de la factura tal como se emitirá (FR12).
6. El empleado confirma la emisión.
7. El sistema incluye CU-17 (emitir factura). Punto de extensión *aprobación* (antes de incluir CU-17).

**Flujos alternativos**

- **1a. Retomar una factura *aprobada* pendiente de emisión (CU-19 4c):** el empleado la selecciona y el caso continúa en el paso 7 sin nueva aprobación.
- **4a. Importe fuera del patrón habitual del cliente:** el sistema muestra un aviso de importe atípico antes de la vista previa (FR12).
- **6a. Guardar como borrador:** el sistema conserva el borrador sin emitirlo; el empleado puede retomarlo o borrarlo más tarde (solo los borradores pueden borrarse, FR14).
- **7a. La factura cumple la condición de retención:** CU-18 extiende este caso; la factura queda *pendiente de aprobación* y el caso termina sin emitirla. La emisión se reanudará desde CU-19 cuando se apruebe.

**Flujos de excepción**

- **7b. CU-17 no puede emitir la factura (p. ej., datos fiscales incompletos o periodo cerrado):** la factura se conserva en *borrador* y el sistema indica al empleado qué debe corregir.

---

#### CU-16 · Generar facturas recurrentes

| **Objetivo** | El sistema emite automáticamente la factura mensual de cada suscripción activa. |
|---|---|
| **Actores** | Temporizador |
| **Relaciones** | Incluye CU-17 |
| **Requisitos** | FR13, FR10 · BR1 · NFR7, NFR10, NFR11, NFR13 |
| **Interesados** | Turbine: facturar todos los meses sin trabajo manual ni errores. Cliente: una sola factura por periodo. |
| **Precondiciones** | Hay una serie de numeración configurada (CU-42). El día 1, CU-36 ya ha activado las suscripciones *programadas* que empiezan ese día. |
| **Disparador** | Llega el día 1 del mes, o existe un periodo que no se completó (por una caída o por facturas no emitidas). |
| **Postcondición** | Cada suscripción activa tiene exactamente una factura emitida para el periodo y la ejecución queda registrada. |
| **Garantía mínima** | Nunca se emiten dos facturas para la misma suscripción y periodo, aunque el proceso se interrumpa. |

**Escenario principal**

1. El temporizador inicia la facturación del periodo.
2. El sistema registra el inicio de la ejecución.
3. El sistema selecciona las suscripciones *activas* que aún no tienen factura en el periodo.
4. Para cada suscripción, el sistema calcula el importe con la tarifa vigente y las excepciones aprobadas, prepara la factura e incluye CU-17 (emitir factura).
5. El sistema registra el fin de la ejecución con el número de facturas emitidas.

**Flujos alternativos**

- **1a. Periodo incompleto:** el sistema, que lo comprueba a diario, detecta el periodo no completado y lo procesa desde el paso 3; las suscripciones ya facturadas no se vuelven a facturar (FR13, NFR11).

**Flujos de excepción**

- **4a. CU-17 no emite la factura de una suscripción (fallo técnico o datos fiscales incompletos):** el sistema continúa con las demás y marca el periodo como *incompleto*; esa suscripción se factura en la recuperación diaria (1a).

---

#### CU-17 · Emitir factura

| **Objetivo** | Convertir una factura preparada en una factura oficial, numerada, firmada y almacenada, y enviarla. |
|---|---|
| **Actores** | — (*subfunción*) |
| **Relaciones** | Incluido por CU-10, CU-15, CU-16 y CU-22; extiende CU-19 (punto *aprobación concedida*); incluye CU-20 |
| **Requisitos** | FR12, FR14, FR15, FR17 · BR3 · NFR7, NFR8, NFR9, NFR10 |
| **Interesados** | Turbine y gestoría: numeración legal sin huecos ni duplicados y documento inalterable (BR3). |
| **Precondiciones** | Existe una factura preparada que no está pendiente de aprobación. |
| **Disparador** | El caso de uso que la incluye solicita la emisión, o CU-19 la reanuda tras su aprobación. |
| **Postcondición** | La factura tiene número único de su serie y PDF firmado almacenado en local y en la nube, y queda *enviada* o *no notificada* (CU-20). |
| **Garantía mínima** | Si la emisión falla, la factura no recibe número y vuelve a su estado anterior; nunca hay dos facturas con el mismo número ni huecos en la serie. |

**Escenario principal**

1. El sistema comprueba que los datos fiscales del cliente están completos (FR12).
2. El sistema comprueba que la fecha de la factura pertenece a un periodo de facturación abierto.
3. En una única transacción, el sistema asigna el siguiente número de la serie, genera el PDF con la identidad corporativa, lo firma con el certificado electrónico de Turbine y lo almacena en local y en la nube (FR14, FR17).
4. El sistema cambia la factura a *emitida*.
5. El sistema incluye CU-20 (enviar factura).

**Flujos alternativos**

- **4a. La factura es una rectificativa:** el sistema marca además la factura original como *rectificada*, o como *anulada* si la rectificativa es por el importe total (FR15).

**Flujos de excepción**

- **1a. Datos fiscales incompletos:** el sistema no emite la factura e indica los datos que faltan; si el caso que lo incluye es un proceso automático, notifica al responsable del cliente (R2).
- **2a. Periodo cerrado:** el sistema no emite la factura e informa del motivo (FR15).
- **3a. Falla la firma o uno de los almacenamientos (E2):** la factura no recibe número y el sistema notifica además al superadministrador (R2).

---

#### CU-18 · Retener para aprobación

| **Objetivo** | Dejar una operación económica sensible pendiente hasta que un administrador la resuelva. |
|---|---|
| **Actores** | — (*subfunción*) |
| **Relaciones** | Extiende CU-15 y CU-22 (punto *aprobación*); incluido por CU-11 y CU-12 |
| **Requisitos** | FR16, FR15 · BR3 · NFR8 |
| **Interesados** | Turbine: control sobre descuentos, importes atípicos, cambios de tarifa y rectificaciones (BR3). |
| **Precondiciones** | Existe una operación sensible que aún no está *aprobada*. |
| **Disparador** | Inclusión desde CU-11 (excepción comercial) o CU-12 (cambio de nivel), o condición de retención en el punto de extensión *aprobación* de CU-15 o CU-22. **Condición de retención:** la factura tiene un descuento o un importe que superan los umbrales configurados, o un vencimiento o un importe modificados a mano; o la rectificativa supera el umbral de rectificación (véase P12); y la operación no está ya *aprobada*. |
| **Postcondición** | La operación está *pendiente de aprobación* en la bandeja de validación, con su motivo de retención, y los administradores han sido notificados. |
| **Garantía mínima** | La operación no produce ningún efecto mientras esté retenida. |

**Escenario principal**

1. El sistema marca la operación como *pendiente de aprobación* y registra el motivo de retención y su creador.
2. El sistema la añade a la bandeja de validación.
3. El sistema notifica a los administradores, excepto al creador (R2).
4. El sistema devuelve el control al caso que lo ha incluido o extendido, indicando que la operación está retenida.

**Flujos alternativos:** ninguno.

**Flujos de excepción:** ninguno.

---

#### CU-19 · Resolver aprobación pendiente

| **Objetivo** | El administrador aprueba, rechaza o reasigna una operación retenida. |
|---|---|
| **Actores** | Administrador |
| **Relaciones** | Extendido por CU-17 · Punto de extensión *aprobación concedida* (paso 4) |
| **Requisitos** | FR16, FR10, FR15 · UR9 · NFR8 |
| **Interesados** | Turbine: que ninguna operación sensible se aplique sin una segunda persona (principio de los cuatro ojos). Creador: saber pronto la decisión. |
| **Precondiciones** | R1. |
| **Disparador** | El administrador atiende un aviso de aprobación pendiente o revisa la bandeja de validación. |
| **Postcondición** | La operación queda *aprobada* y aplicada, o *rechazada*; el creador conoce la decisión. |
| **Garantía mínima** | El creador de una operación nunca puede aprobarla. |

**Escenario principal**

1. El administrador solicita la bandeja de validación.
2. El sistema presenta las operaciones pendientes con su motivo de retención, importe y creador.
3. El administrador selecciona una operación y la aprueba.
4. El sistema comprueba que el administrador no es el creador y marca la operación como *aprobada*. Punto de extensión *aprobación concedida*: si es una factura o rectificativa, CU-17 extiende este caso y la emite.
5. El sistema notifica la decisión al creador (R2).

**Flujos alternativos**

- **3a. Rechazar:** el administrador indica el motivo; la factura vuelve a *borrador* o la propuesta queda *rechazada*, y el sistema continúa en el paso 5.
- **3b. Reasignar:** el administrador traslada la validación a otro administrador, al que el sistema notifica (R2).
- **4b. La operación aprobada es una excepción comercial o un cambio de nivel:** el sistema la aplica a las facturas futuras (FR10) y continúa en el paso 5.

**Flujos de excepción**

- **4a. El administrador es el creador de la operación:** el sistema impide la aprobación y ofrece reasignarla (FR16).
- **4c. CU-17 no puede emitir la factura aprobada (datos fiscales incompletos o periodo cerrado):** la factura conserva el estado *aprobada* y el sistema notifica al creador qué debe corregir (R2); la emisión se retomará desde CU-15 (factura puntual) o desde CU-22 (rectificativa) sin nueva aprobación.

---

#### CU-20 · Enviar factura

| **Objetivo** | Hacer llegar una factura emitida a los contactos adecuados del cliente y dejar constancia del resultado. |
|---|---|
| **Actores** | — (*subfunción*) · secundarios: Cliente, Proveedor de correo |
| **Relaciones** | Incluido por CU-17 y CU-21 |
| **Requisitos** | FR18, FR6, FR28 · NFR7 |
| **Interesados** | Cliente: recibir la factura. Turbine: saber con certeza a quién se envió y si llegó. |
| **Precondiciones** | La factura está emitida y su PDF firmado está almacenado. |
| **Disparador** | Inclusión desde CU-17 (envío inicial) o desde CU-21 (reenvío). |
| **Postcondición** | La factura se ha entregado al proveedor de correo y queda *enviada* (o conserva su estado, 4a); quedan registrados destinatarios, fecha, canal y resultado. |
| **Garantía mínima** | Enviar o reenviar nunca crea una factura ni un número nuevos; un fallo del proveedor de correo no altera la factura (FR28). |

**Escenario principal**

1. El sistema toma los destinatarios indicados por el caso que lo incluye o, si no los hay, los contactos activos del cliente con función de administración.
2. El sistema envía al cliente la factura por correo electrónico con el PDF y los datos de soporte de Turbine.
3. El sistema registra destinatarios, fecha, canal y resultado en el historial de destinatarios (FR6).
4. El sistema marca la factura como *enviada*.

**Flujos alternativos**

- **2a. Canal complementario:** si el cliente lo tiene configurado, el sistema envía además un aviso por WhatsApp (simulado).
- **4a. Reenvío de una factura *vencida* o *pagada*:** el sistema conserva su estado; solo una factura *emitida* o *no notificada* pasa a *enviada*.

**Flujos de excepción**

- **1a. El cliente no tiene contactos activos de administración:** el sistema marca la factura como *no notificada* y notifica al responsable (R2).
- **2b. Proveedor de correo no disponible:** el sistema reintenta según la política configurada; si se agotan los intentos, marca la factura como *no notificada* y notifica al responsable (R2).

---

#### CU-21 · Reenviar factura

| **Objetivo** | El responsable vuelve a enviar una factura emitida, por ejemplo tras un rebote o a petición del cliente. |
|---|---|
| **Actores** | Usuario estándar |
| **Relaciones** | Incluye CU-20 |
| **Requisitos** | FR18, FR6 |
| **Interesados** | Cliente: disponer de la factura. Turbine: no duplicar facturas. |
| **Precondiciones** | R1. |
| **Disparador** | Rebote de un envío anterior (CU-26) o petición del cliente. |
| **Postcondición** | La factura se ha vuelto a enviar y el nuevo envío consta en su historial. |
| **Garantía mínima** | No se crea ninguna factura nueva. |

**Escenario principal**

1. El empleado selecciona una factura emitida de su cartera y solicita reenviarla.
2. El sistema presenta los contactos activos del cliente y los destinatarios del envío anterior.
3. El empleado confirma o cambia los destinatarios.
4. El sistema incluye CU-20 (enviar factura) con los destinatarios elegidos.

**Flujos alternativos:** ninguno.

**Flujos de excepción**

- **1a. La factura no está emitida:** el sistema lo indica; un borrador no puede enviarse.

---

#### CU-22 · Rectificar o anular factura

| **Objetivo** | El administrador corrige o deja sin efecto una factura emitida mediante una factura rectificativa vinculada. |
|---|---|
| **Actores** | Administrador |
| **Relaciones** | Incluye CU-17; extendido por CU-18 · Punto de extensión *aprobación* (paso 6) |
| **Requisitos** | FR15, FR14, FR16 · UR14 · BR3 · NFR8 |
| **Interesados** | Turbine y gestoría: cumplir la normativa (una factura emitida no se modifica). Cliente: tener la factura correcta. |
| **Precondiciones** | R1. |
| **Disparador** | Se detecta un error en una factura emitida o se emitió por error. |
| **Postcondición** | Existe una rectificativa con número único vinculada a la original, que queda *rectificada* o *anulada* sin modificarse; o la rectificativa está retenida pendiente de aprobación. |
| **Garantía mínima** | La factura original nunca se edita ni se borra (R3). |

**Escenario principal**

1. El administrador selecciona una factura emitida y solicita rectificarla indicando el motivo.
2. El sistema crea una rectificativa en borrador vinculada a la original, con fecha del periodo abierto en curso.
3. El administrador indica las correcciones (importes, conceptos o datos).
4. El sistema presenta la vista previa con la diferencia respecto a la original.
5. El administrador confirma.
6. El sistema incluye CU-17 (emitir factura). Punto de extensión *aprobación* (antes de incluir CU-17).

**Flujos alternativos**

- **1a. Anular:** el administrador indica que la factura se emitió por error; el sistema prepara una rectificativa por el importe total y continúa en el paso 4.
- **1c. Retomar una rectificativa *aprobada* pendiente de emisión (CU-19 4c):** el administrador la selecciona y el caso continúa en el paso 6 sin nueva aprobación.
- **6a. La rectificativa cumple la condición de retención:** CU-18 extiende este caso; la rectificativa queda *pendiente de aprobación* y el caso termina sin emitirla. La emisión se reanudará desde CU-19 cuando la apruebe otro administrador.

**Flujos de excepción**

- **1b. La factura no está emitida:** el sistema lo indica; un borrador se corrige directamente o se borra (véase CU-15).

---

#### CU-23 · Declarar factura incobrable

| **Objetivo** | El administrador da por perdida una factura vencida y detiene su recobro de forma controlada. |
|---|---|
| **Actores** | Administrador |
| **Requisitos** | FR19, FR14, FR11 · NFR8 |
| **Interesados** | Turbine: reflejar la situación real de cobro sin borrar documentos. |
| **Precondiciones** | R1. |
| **Disparador** | El recobro de una factura se da por agotado. |
| **Postcondición** | La factura queda marcada como *incobrable* y sus recordatorios se detienen. |
| **Garantía mínima** | El documento original se conserva íntegro. |

**Escenario principal**

1. El administrador selecciona una factura vencida y solicita declararla incobrable indicando el motivo.
2. El sistema comprueba que la factura está vencida y no pagada, y pide confirmación.
3. El administrador confirma.
4. El sistema marca la factura como *incobrable* y la excluye de los recordatorios (FR19).

**Flujos alternativos:** ninguno.

**Flujos de excepción**

- **2a. La factura no está vencida o ya está pagada:** el sistema no permite la acción e indica el motivo.

---

#### CU-24 · Cerrar periodo de facturación

| **Objetivo** | El administrador cierra un periodo para que no se emitan más facturas con fecha en él. |
|---|---|
| **Actores** | Administrador |
| **Requisitos** | FR15 · UR14 · BR3 |
| **Interesados** | Gestoría: datos estables una vez presentado el periodo. Turbine: cumplimiento legal (BR3). |
| **Precondiciones** | R1. |
| **Disparador** | Fin de un periodo contable (p. ej., un trimestre). |
| **Postcondición** | El periodo queda cerrado: CU-17 no emitirá facturas con fecha en él (véase P7). |
| **Garantía mínima** | Los totales de un periodo cerrado no cambian. |

**Escenario principal**

1. El administrador selecciona el periodo que quiere cerrar.
2. El sistema presenta un resumen: facturas emitidas, borradores y operaciones pendientes de aprobación con fecha en ese periodo.
3. El administrador confirma el cierre.
4. El sistema cierra el periodo.

**Flujos alternativos:** ninguno.

**Flujos de excepción**

- **2a. Hay borradores, operaciones pendientes de aprobación o facturas *aprobadas* sin emitir en el periodo:** el sistema impide el cierre hasta que se resuelvan o descarten.

---

#### CU-25 · Consultar facturas y estado de cobro

| **Objetivo** | El empleado sigue el estado de cobro de las facturas de sus clientes y obtiene sus documentos. |
|---|---|
| **Actores** | Usuario estándar |
| **Requisitos** | FR14, FR17, FR6, FR20 · UR3 · NFR4, NFR12 |
| **Interesados** | Empleado: saber qué está pendiente. Cliente: recibir copias exactas cuando las pida. |
| **Precondiciones** | R1. |
| **Disparador** | El empleado necesita revisar facturas o el cliente pide una copia. |
| **Postcondición** | El empleado obtiene la información o el documento solicitado. |
| **Garantía mínima** | La consulta no modifica ninguna factura y el PDF entregado es idéntico al emitido. |

**Escenario principal**

1. El empleado solicita las facturas de su cartera.
2. El sistema las presenta agrupadas por estado (emitida, enviada, no notificada, vencida, pagada…) con su vencimiento e importe pendiente.
3. El empleado selecciona una factura.
4. El sistema presenta su detalle: líneas, historial de estados, destinatarios a los que se envió (FR6) y cobros vinculados.
5. El empleado solicita el PDF y el sistema lo entrega exactamente como se emitió.

**Flujos alternativos**

- **5a. Compartir con el cliente:** el sistema genera un enlace temporal al PDF que caduca en 15 minutos (FR17, NFR4).
- **5b. El documento no está disponible en el almacenamiento principal:** el sistema lo recupera de la copia redundante (FR17).

**Flujos de excepción:** ninguno.

---

#### CU-26 · Registrar rebote de envío

| **Objetivo** | Registrar que un correo enviado por el sistema (factura o recordatorio) no ha llegado a su destinatario. |
|---|---|
| **Actores** | Proveedor de correo |
| **Requisitos** | FR18, FR19, FR6 · NFR7 |
| **Interesados** | Turbine: no dar por notificada una factura que el cliente no ha recibido. Responsable: saber qué contacto debe corregir. |
| **Precondiciones** | Ninguna. |
| **Disparador** | El proveedor de correo comunica el rebote de un envío. |
| **Postcondición** | El rebote consta en el historial de destinatarios, la factura *enviada* cuyo envío ha rebotado queda *no notificada* y el responsable ha sido notificado. |
| **Garantía mínima** | Un mismo aviso de rebote nunca se registra dos veces (NFR7). |

**Escenario principal**

1. El proveedor de correo comunica el rebote con la referencia del envío y el motivo.
2. El sistema identifica el envío y la factura a la que corresponde.
3. El sistema registra el rebote en el historial de destinatarios (FR6).
4. El sistema marca la factura *enviada* como *no notificada* (FR18).
5. El sistema notifica al responsable para que corrija el contacto (véase CU-07, 1a) y reenvíe la factura (véase CU-21) (R2).

**Flujos alternativos**

- **4a. El rebote corresponde a un recordatorio, o la factura ya está *vencida* o *pagada*:** el sistema conserva su estado y continúa en el paso 5.

**Flujos de excepción**

- **2a. El envío no se puede identificar:** el sistema registra el aviso y notifica al superadministrador (R2).

---

### 4.4 Cobros (D4)

#### CU-27 · Enviar recordatorios de cobro

| **Objetivo** | El sistema persigue automáticamente el cobro de las facturas pendientes. |
|---|---|
| **Actores** | Temporizador · secundarios: Cliente, Proveedor de correo |
| **Requisitos** | FR19, FR14, FR21 · BR2 |
| **Interesados** | Turbine: reducir impagos sin escribir recordatorios a mano (BR2). Cliente: avisos claros y con datos de contacto de soporte. |
| **Precondiciones** | La cadencia de recordatorios está configurada (CU-42). |
| **Disparador** | Ejecución diaria programada. |
| **Postcondición** | Cada factura pendiente ha recibido el recordatorio que le corresponde según su antigüedad y el envío queda registrado. |
| **Garantía mínima** | No se envían recordatorios de facturas pagadas, pausadas, anuladas, rectificadas, migradas o incobrables. |

**Escenario principal**

1. El temporizador inicia la revisión diaria de cobros.
2. El sistema marca como *vencida* toda factura *enviada* o *no notificada* sin pagar cuya fecha de vencimiento ha pasado, tenga o no los recordatorios pausados (R4).
3. El sistema selecciona las facturas *enviadas*, *no notificadas* o *vencidas* sin pagar, excepto las que tienen los recordatorios pausados (CU-33) o están marcadas como *incobrables* o *migradas*.
4. Para cada factura, el sistema determina si hoy le corresponde un recordatorio según la cadencia configurada: día 3 tras la emisión, día del vencimiento (por defecto, el 5) y, después, cada 2–3 días.
5. El sistema envía el recordatorio a los contactos de impagos del cliente (o, si no hay, a los de administración) con los datos de la factura y del soporte de Turbine.
6. El sistema registra el envío.

**Flujos alternativos**

- **4a. Hoy no le corresponde recordatorio:** el sistema pasa a la siguiente factura.
- **5a. La factura está *vencida*:** el sistema notifica también al responsable del cliente (R2).

**Flujos de excepción**

- **5b. Falla el envío del recordatorio:** el sistema notifica al responsable (R2) y lo reintenta en la siguiente ejecución. Los rebotes posteriores llegan por CU-26.

---

#### CU-28 · Registrar cobro de la pasarela

| **Objetivo** | Registrar automáticamente un pago comunicado por la pasarela de pago. |
|---|---|
| **Actores** | Pasarela de pago (Stripe) |
| **Relaciones** | Incluye CU-30 |
| **Requisitos** | FR20, FR28 · BR1 · NFR7 |
| **Interesados** | Turbine: saber al instante qué está cobrado sin revisarlo a mano (BR1). Cliente: dejar de recibir recordatorios al pagar. |
| **Precondiciones** | Ninguna. |
| **Disparador** | La pasarela de pago comunica un pago. |
| **Postcondición** | El cobro queda vinculado a su factura (o en la cola de revisión manual). |
| **Garantía mínima** | Un mismo aviso de pago nunca genera dos cobros (idempotencia, NFR7). |

**Escenario principal**

1. La pasarela de pago envía el aviso de pago con su identificador, referencia e importe.
2. El sistema verifica la autenticidad del aviso.
3. El sistema comprueba que ese aviso no se ha procesado antes.
4. El sistema incluye CU-30 (vincular cobro con factura).

**Flujos alternativos:** ninguno.

**Flujos de excepción**

- **2a. Aviso no auténtico:** el sistema lo rechaza y lo registra.
- **3a. Aviso ya procesado:** el sistema lo ignora sin crear un cobro nuevo (FR20).

---

#### CU-29 · Conciliar cobros

| **Objetivo** | El administrador incorpora los cobros recibidos por transferencia o Revolut y resuelve los cobros que no se han podido vincular automáticamente. |
|---|---|
| **Actores** | Administrador |
| **Relaciones** | Incluye CU-30 |
| **Requisitos** | FR20 · UR12 · NFR7, NFR8, NFR9 |
| **Interesados** | Turbine: reflejar también los cobros que no pasan por Stripe y que la contabilidad cuadre al céntimo (NFR9). |
| **Precondiciones** | R1. |
| **Disparador** | El administrador dispone del extracto de movimientos del banco o atiende la cola de revisión manual. |
| **Postcondición** | Los movimientos importados quedan registrados como cobros vinculados a su factura o en la cola de revisión manual; cada cobro revisado queda vinculado a su factura o marcado como no identificado; una conciliación revertida queda deshecha y su factura, en el estado anterior. |
| **Garantía mínima** | Un movimiento ya importado nunca genera un segundo cobro y ninguna factura pasa a *pagada* sin un cobro validado. |

**Escenario principal**

1. El administrador aporta el archivo de movimientos (Revolut o extracto bancario).
2. El sistema lo valida y presenta los movimientos de cobro que contiene.
3. El administrador confirma la importación.
4. Para cada movimiento no procesado antes, el sistema incluye CU-30 (vincular cobro con factura).
5. El sistema presenta la cola de revisión manual, con cada cobro, el motivo de la discrepancia y las facturas candidatas, precedida, si se ha importado un archivo, del resumen de la importación (cobros vinculados, enviados a revisión y ya importados).
6. El administrador vincula un cobro pendiente con la factura correcta.
7. El sistema registra la conciliación manual; si el importe cubre el pendiente, actualiza la factura y notifica al responsable como en los pasos 3 y 4 de CU-30. El caso vuelve al paso 5 mientras queden cobros que el administrador quiera resolver.

**Flujos alternativos**

- **1a. Atender solo la cola de revisión manual** (p. ej., cobros de la pasarela que CU-30 no pudo vincular): el administrador la solicita y el caso continúa en el paso 5.
- **1b. Revertir una conciliación anterior:** el administrador selecciona una vinculación errónea e indica el motivo; el sistema la deshace y la factura vuelve a su estado anterior.
- **5a. No quedan cobros en revisión:** el sistema lo indica y el caso termina.
- **6a. Marcar como no identificado:** el administrador indica que el cobro no corresponde a ninguna factura; el sistema lo registra así.
- **7a. Pago parcial:** el sistema registra el cobro y mantiene la factura pendiente por la diferencia.

**Flujos de excepción**

- **2a. Archivo con formato no reconocido:** el sistema lo rechaza e indica el motivo.

---

#### CU-30 · Vincular cobro con factura

| **Objetivo** | Asociar un cobro recibido a la factura que paga y actualizar su estado. |
|---|---|
| **Actores** | — (*subfunción*) |
| **Relaciones** | Incluido por CU-28 y CU-29 |
| **Requisitos** | FR20, FR21 · NFR7, NFR9 |
| **Interesados** | Turbine: estado de cobro exacto. Responsable: saber que su cliente ha pagado. |
| **Precondiciones** | Existe un cobro verificado y no procesado antes. |
| **Disparador** | Inclusión desde CU-28 o CU-29. |
| **Postcondición** | El cobro está registrado y vinculado; si cubre el pendiente, la factura está *pagada* y sin recordatorios. |
| **Garantía mínima** | Ninguna factura pasa a *pagada* sin un cobro que cubra su importe. |

**Escenario principal**

1. El sistema identifica la factura por la referencia del cobro.
2. El sistema comprueba que el importe coincide con el pendiente de la factura.
3. El sistema registra el cobro, cambia la factura a *pagada* y detiene sus recordatorios.
4. El sistema notifica el cobro al responsable del cliente (R2).

**Flujos alternativos:** ninguno.

**Flujos de excepción**

- **1a. Referencia dudosa o sin factura identificable:** el sistema registra el cobro en la cola de revisión manual sin cambiar el estado de ninguna factura y notifica a los administradores (véase CU-29).
- **2a. Importe distinto del pendiente:** el sistema actúa igual que en 1a.

---

### 4.5 Seguimiento y trabajo diario (D5)

#### CU-31 · Gestionar trabajo diario

| **Objetivo** | El empleado revisa y atiende en un solo lugar sus notificaciones, tareas y cobros pendientes. |
|---|---|
| **Actores** | Usuario estándar |
| **Requisitos** | FR21, FR4 · UR7 · BR4 |
| **Interesados** | Empleado: saber qué hacer hoy sin buscar. Turbine: que nada quede sin atender. |
| **Precondiciones** | R1. |
| **Disparador** | El empleado inicia su jornada o recibe una notificación. |
| **Postcondición** | Las notificaciones consultadas quedan *leídas* y las resueltas, *atendidas*. |
| **Garantía mínima** | Ningún elemento se marca como *atendido* sin que el empleado lo indique. |

**Escenario principal**

1. El empleado solicita su trabajo pendiente.
2. El sistema presenta sus notificaciones y tareas agrupadas por prioridad, junto con los cobros pendientes de su cartera.
3. El empleado abre un elemento.
4. El sistema lo marca como *leído* y presenta el cliente, la factura o la incidencia a la que se refiere.
5. El empleado resuelve el asunto e indica que está atendido.
6. El sistema marca el elemento como *atendido*.

**Flujos alternativos**

- **5a. Tarea de baja solicitada por API:** el empleado la resuelve dando de baja al cliente (véase CU-07, 1c) o descartándola con un motivo.
- **5b. Aviso de rebote (CU-26):** el empleado corrige el contacto (véase CU-07, 1a) y reenvía la factura (véase CU-21).

**Flujos de excepción:** ninguno.

---

#### CU-32 · Gestionar incidencia

| **Objetivo** | El empleado registra un problema interno de facturación y lo sigue hasta su resolución. |
|---|---|
| **Actores** | Usuario estándar |
| **Relaciones** | Extendido por CU-33 · Punto de extensión *pausa* (paso 1) |
| **Requisitos** | FR22 · UR5 |
| **Interesados** | Empleado: dejar constancia y coordinarse. Administradores: que sus notas privadas solo las vean ellos. |
| **Precondiciones** | R1. |
| **Disparador** | Aparece un problema (factura en disputa, datos erróneos, pago no reconocido…). |
| **Postcondición** | La incidencia queda registrada con responsable, prioridad, estado, comentarios y adjuntos, vinculada al cliente y, si procede, a una factura. |
| **Garantía mínima** | Las notas privadas nunca son visibles para usuarios estándar sin autorización expresa. |

**Escenario principal**

1. El empleado abre una incidencia indicando el cliente, la factura afectada (opcional), la descripción y la prioridad. Punto de extensión *pausa*.
2. El sistema registra la incidencia con el empleado como responsable.
3. El empleado añade un comentario o un adjunto.
4. El sistema comprueba el formato y el tamaño del adjunto, lo registra y notifica al responsable de la incidencia (R2).
5. El empleado cierra la incidencia cuando se resuelve.
6. El sistema registra el cierre.

**Flujos alternativos**

- **2a. Reasignar el responsable:** el empleado asigna la incidencia a otro empleado con acceso al cliente; el sistema le notifica.
- **3a. Nota privada:** un administrador añade una nota visible solo para administradores; puede autorizar su lectura a un usuario concreto.

**Flujos de excepción**

- **4a. Adjunto fuera de formato o de tamaño:** el sistema lo rechaza e indica los límites (FR22).

---

#### CU-33 · Pausar recordatorios de una factura

| **Objetivo** | El empleado detiene temporalmente los recordatorios de una factura en disputa. |
|---|---|
| **Actores** | Usuario estándar |
| **Relaciones** | Extiende CU-32 (punto *pausa*) |
| **Requisitos** | FR19, FR22 · UR6 |
| **Interesados** | Cliente: no recibir reclamaciones mientras se aclara un problema. Turbine: que toda pausa esté justificada y tenga fecha de revisión. |
| **Precondiciones** | R1. |
| **Disparador** | El cliente disputa una factura; o condición del punto de extensión *pausa* de CU-32. |
| **Postcondición** | La factura no genera recordatorios hasta su fecha de revisión, en la que CU-36 la reanuda. |
| **Garantía mínima** | Ninguna pausa queda sin motivo ni fecha de revisión. |

**Escenario principal**

1. El empleado selecciona una factura pendiente de su cartera y solicita pausar sus recordatorios.
2. El sistema solicita el motivo y la fecha de revisión.
3. El empleado los indica.
4. El sistema comprueba que la factura está pendiente de pago, pausa sus recordatorios y, si procede, vincula la pausa a la incidencia.

**Flujos alternativos**

- **1a. Reanudar antes de la fecha:** el empleado reanuda los recordatorios de una factura pausada; el sistema la vuelve a incluir en CU-27.

**Flujos de excepción**

- **4a. Motivo vacío o fecha de revisión pasada:** el sistema lo rechaza y vuelve al paso 2.
- **4b. La factura ya está pagada:** el sistema informa de que no hay recordatorios que pausar.

---

#### CU-34 · Buscar clientes y facturas

| **Objetivo** | El empleado encuentra rápidamente clientes y facturas. |
|---|---|
| **Actores** | Usuario estándar |
| **Relaciones** | Extendido por CU-35 · Punto de extensión *acción masiva* (paso 3) |
| **Requisitos** | FR23 · UR8 · BR4 · NFR12, NFR13 |
| **Interesados** | Empleado: ahorrar tiempo (BR4). Turbine: que nadie vea lo que no le corresponde. |
| **Precondiciones** | R1. |
| **Disparador** | El empleado necesita localizar uno o varios clientes o facturas. |
| **Postcondición** | El empleado obtiene los resultados que busca. |
| **Garantía mínima** | Una búsqueda no modifica ningún dato. |

**Escenario principal**

1. El empleado busca por NIF, nombre, número de factura o correo y combina filtros.
2. El sistema presenta los resultados paginados que el empleado tiene permiso para ver.
3. El empleado selecciona uno o varios resultados. Punto de extensión *acción masiva*.
4. El sistema presenta el detalle del elemento seleccionado.

**Flujos alternativos**

- **2a. Guardar la búsqueda:** el empleado guarda la combinación de filtros como vista predeterminada; el sistema la ofrece en adelante.

**Flujos de excepción**

- **2b. Sin resultados:** el sistema lo indica y conserva los filtros para ajustarlos.

---

#### CU-35 · Ejecutar acción masiva

| **Objetivo** | Aplicar una misma acción a varios elementos a la vez de forma segura. |
|---|---|
| **Actores** | Usuario estándar |
| **Relaciones** | Extiende CU-34 (punto *acción masiva*) |
| **Requisitos** | FR23 · UR8 · NFR7 |
| **Interesados** | Empleado: ahorrar tiempo. Turbine: que una acción masiva no tenga efectos inesperados. |
| **Precondiciones** | R1. |
| **Disparador** | Condición del punto de extensión *acción masiva* de CU-34: el empleado ha seleccionado varios elementos. |
| **Postcondición** | La acción se ha aplicado y el empleado conoce el número exacto de elementos procesados y fallidos. |
| **Garantía mínima** | Una acción masiva nunca se ejecuta sin confirmación previa. |

**Escenario principal**

1. El empleado elige, entre las acciones masivas permitidas (véase P8), la que quiere aplicar a la selección.
2. El sistema indica cuántos elementos se verán afectados y pide confirmación.
3. El empleado confirma.
4. El sistema aplica la acción a cada elemento e informa del número exacto de elementos procesados y de los que fallaron, con su motivo.

**Flujos alternativos:** ninguno.

**Flujos de excepción**

- **4a. Algún elemento no admite la acción** (estado incompatible o sin permiso): el sistema no lo procesa y lo incluye en el informe de fallos con su motivo (los elementos sin permiso siguen E1).

---

#### CU-36 · Aplicar vencimientos programados

| **Objetivo** | El sistema aplica cada día los cambios que dependen de una fecha acordada previamente. |
|---|---|
| **Actores** | Temporizador |
| **Requisitos** | FR3, FR4, FR9, FR13, FR19, FR21, FR25 |
| **Interesados** | Responsables: que las fechas acordadas se cumplan sin recordarlas. Turbine: que no se pierda ninguna reactivación ni delegación. |
| **Precondiciones** | Ninguna. |
| **Disparador** | Ejecución diaria programada; el día 1 se ejecuta antes que CU-16. |
| **Postcondición** | Todas las fechas vencidas ese día se han aplicado y sus responsables, avisados. |
| **Garantía mínima** | Una fecha no aplicada por un fallo se aplica en la siguiente ejecución, porque cada paso trata también las fechas anteriores aún no aplicadas. |

**Escenario principal**

1. El temporizador inicia la revisión diaria de vencimientos.
2. El sistema activa las suscripciones *programadas* cuya fecha de inicio es hoy o anterior (FR9).
3. El sistema reactiva las suscripciones *pausadas* cuya fecha de reactivación es hoy o anterior y avisa a su responsable (FR13).
4. El sistema inicia las delegaciones cuyo inicio es hoy o anterior y finaliza las vencidas, devolviendo el cliente y sus tareas al responsable original (FR4).
5. El sistema reanuda los recordatorios de las facturas cuya fecha de revisión de pausa es hoy o anterior y avisa al responsable (FR19).
6. El sistema elimina las exportaciones cuyo periodo de retención ha vencido y caduca las invitaciones no usadas (FR25, FR3).
7. El sistema registra la ejecución.

**Flujos alternativos:** ninguno.

**Flujos de excepción**

- **\*a. Falla la aplicación de un vencimiento (E2):** el sistema notifica al superadministrador (R2).

---

### 4.6 Información y configuración (D6)

#### CU-37 · Consultar dashboard financiero

| **Objetivo** | El administrador conoce la situación financiera y la analiza hasta el detalle. |
|---|---|
| **Actores** | Administrador |
| **Requisitos** | FR24 · UR13 · BR6 · NFR9, NFR12 |
| **Interesados** | Dirección de Turbine: tomar decisiones con datos fiables (BR6). |
| **Precondiciones** | R1. |
| **Disparador** | El administrador quiere revisar ingresos, cobros o deudores. |
| **Postcondición** | El administrador dispone de las métricas solicitadas. |
| **Garantía mínima** | Los totales coinciden con los de las facturas y cobros registrados (FR24). |

**Escenario principal**

1. El administrador solicita el dashboard financiero.
2. El sistema presenta la previsión de ingresos, lo cobrado, lo vencido, los mayores deudores y el grado de cumplimiento de los objetivos vigentes.
3. El administrador elige el periodo de análisis (semanal, mensual, trimestral o anual).
4. El sistema recalcula las métricas para ese periodo.
5. El administrador selecciona un agregado.
6. El sistema presenta su desglose, hasta el nivel de factura.

**Flujos alternativos:** ninguno.

**Flujos de excepción:** ninguno.

---

#### CU-38 · Definir objetivos financieros

| **Objetivo** | El administrador fija los objetivos de ingresos mensuales o anuales con los que se compara el dashboard. |
|---|---|
| **Actores** | Administrador |
| **Requisitos** | FR24 · UR13 · BR6 |
| **Interesados** | Dirección: medir el avance frente a lo previsto. Turbine: saber qué objetivo estaba vigente en cada periodo. |
| **Precondiciones** | R1. |
| **Disparador** | Inicio de un ejercicio o cambio de planificación. |
| **Postcondición** | El objetivo queda vigente desde la fecha indicada y los anteriores se conservan para sus periodos. |
| **Garantía mínima** | Cambiar un objetivo no altera el objetivo registrado en periodos anteriores. |

**Escenario principal**

1. El administrador indica el tipo de objetivo (mensual o anual), el importe y la fecha desde la que rige.
2. El sistema comprueba los datos.
3. El sistema registra el objetivo como vigente desde esa fecha y conserva el anterior para los periodos previos (FR24).

**Flujos alternativos:** ninguno.

**Flujos de excepción**

- **2a. Importe no válido o fecha anterior a un periodo cerrado:** el sistema lo rechaza e indica el motivo.

---

#### CU-39 · Exportar informes y datos

| **Objetivo** | El empleado obtiene informes o datos en CSV o PDF, limitados a lo que puede ver, y puede programar su envío. |
|---|---|
| **Actores** | Usuario estándar |
| **Requisitos** | FR25, FR8, FR2 · UR13 · NFR4, NFR6 |
| **Interesados** | Empleado: disponer de los datos fuera del sistema. Turbine: que nadie exporte lo que no puede ver y que quede constancia. |
| **Precondiciones** | R1. |
| **Disparador** | El empleado necesita un informe o una exportación. |
| **Postcondición** | El archivo está disponible durante el periodo de retención configurado y la exportación queda registrada con usuario, fecha y filtros. |
| **Garantía mínima** | El contenido se limita a los datos que el solicitante puede ver (FR2): dos usuarios con distintos permisos obtienen informes distintos con los mismos filtros. |

**Escenario principal**

1. El empleado elige el tipo de informe (p. ej., facturación por periodo, deudores o listado de clientes) y los filtros.
2. El empleado elige el formato: CSV (UTF-8) o PDF.
3. El sistema genera el archivo con los datos que el empleado tiene permiso para ver, en segundo plano si es grande.
4. El sistema registra la exportación y la pone a disposición del empleado.

**Flujos alternativos**

- **1a. Programar el envío:** el empleado define los destinatarios y la periodicidad; el sistema guarda la programación, que ejecutará CU-40.
- **1b. Regenerar una exportación caducada:** el sistema la vuelve a generar si los datos y los permisos actuales lo permiten.

**Flujos de excepción**

- **3a. La exportación no contiene datos:** el sistema lo indica y no genera el archivo.

---

#### CU-40 · Enviar informes programados

| **Objetivo** | El sistema envía automáticamente los informes programados y el paquete periódico para la gestoría. |
|---|---|
| **Actores** | Temporizador · secundarios: Gestoría |
| **Requisitos** | FR25 · BR3 |
| **Interesados** | Gestoría: recibir la documentación sin pedirla ni tener usuario. Turbine: cumplir los plazos legales (BR3). |
| **Precondiciones** | Existen envíos programados (CU-39) o el envío a la gestoría está configurado (CU-42). |
| **Disparador** | Llega la fecha de un envío programado. |
| **Postcondición** | Los destinatarios han recibido el informe o el paquete y el envío queda registrado. |
| **Garantía mínima** | Un envío fallido queda registrado y se reintenta. |

**Escenario principal**

1. El temporizador inicia los envíos programados del día.
2. Para cada envío, el sistema genera el informe con los permisos de quien lo programó.
3. El sistema envía el archivo a sus destinatarios y registra el envío.

**Flujos alternativos**

- **2a. Envío a la gestoría:** el sistema prepara un paquete ZIP con las facturas emitidas y la documentación del periodo y lo envía a la dirección de la gestoría configurada.

**Flujos de excepción**

- **3a. Fallo de envío:** el sistema lo reintenta y, si persiste, notifica al superadministrador (R2).

---

#### CU-41 · Supervisar el sistema

| **Objetivo** | El superadministrador comprueba quién hizo qué, cuándo y con qué valores, y que el sistema y sus procesos automáticos funcionan correctamente. |
|---|---|
| **Actores** | Superadministrador |
| **Requisitos** | FR26, FR27 · UR17 · NFR8, NFR11 |
| **Interesados** | Turbine: trazabilidad completa de las decisiones económicas y de permisos (BR3) y detectar fallos antes de que afecten a la facturación o al cobro. |
| **Precondiciones** | R1. |
| **Disparador** | Revisión periódica, investigación de un cambio o aviso de un fallo técnico. |
| **Postcondición** | El superadministrador obtiene la información de auditoría o el estado técnico del sistema; la consulta de auditoría queda registrada. |
| **Garantía mínima** | La supervisión no modifica ningún dato ni ningún registro de auditoría (R3). |

**Escenario principal (auditoría)**

1. El superadministrador solicita el registro de auditoría.
2. El sistema presenta las acciones (quién, qué, cuándo, valor anterior y nuevo) con filtros por usuario, entidad, tipo y fecha.
3. El superadministrador filtra y selecciona una acción.
4. El sistema presenta su detalle completo.

**Flujos alternativos**

- **1a. Consultar la salud del sistema:**
  - 1a.1 El superadministrador solicita el estado técnico.
  - 1a.2 El sistema presenta los correos atascados o fallidos, el estado de las copias de seguridad, las últimas ejecuciones de los procesos automáticos (CU-12, CU-16, CU-27, CU-36, CU-40) y los errores del servidor.
  - 1a.3 El superadministrador selecciona un elemento.
  - 1a.4 El sistema presenta su detalle.

**Flujos de excepción:** ninguno.

---

#### CU-42 · Configurar el sistema

| **Objetivo** | El superadministrador mantiene los parámetros de negocio y las plantillas de los mensajes, en varios idiomas, sin cambiar código ni alterar el histórico. |
|---|---|
| **Actores** | Superadministrador |
| **Requisitos** | FR27, FR1, FR10, FR12, FR14, FR16, FR19, FR21, FR25 · UR15 · BR7 · NFR2 |
| **Interesados** | Turbine: adaptar el sistema a su modelo de negocio (BR7) y comunicarse de forma coherente con su imagen. Cliente: mensajes claros en su idioma. |
| **Precondiciones** | R1. |
| **Disparador** | Cambia una condición del negocio (tarifas, impuestos, plazos…) o hay que crear o cambiar el texto de un mensaje. |
| **Postcondición** | La nueva configuración o plantilla se aplica a las operaciones y envíos futuros. |
| **Garantía mínima** | Ningún cambio altera facturas, mensajes ya enviados ni datos históricos (FR27). |

**Escenario principal (parámetros)**

1. El superadministrador elige el área que quiere configurar: tabla de tarifas por nivel y fecha de la revisión anual (FR10); impuestos y monedas; series de numeración (FR14); plazo de pago y cadencia de recordatorios (FR19); catálogo de conceptos (FR12); umbrales de importe atípico, de descuento y de rectificación (FR16); canales de cada tipo de notificación (FR21); retención de exportaciones y envío a la gestoría (FR25); duración del bloqueo de acceso tras intentos fallidos (FR1).
2. El sistema presenta los valores vigentes.
3. El superadministrador modifica los valores.
4. El sistema comprueba su validez y presenta el efecto del cambio.
5. El superadministrador confirma.
6. El sistema aplica la configuración desde ese momento.

**Flujos alternativos**

- **1a. Editar una plantilla de mensaje:**
  - 1a.1 El superadministrador elige la plantilla (envío de factura, recordatorio, impago, etc.) y el idioma (español o inglés).
  - 1a.2 El sistema presenta el texto vigente y las variables disponibles (cliente, importe, vencimiento…).
  - 1a.3 El superadministrador edita el texto.
  - 1a.4 El sistema presenta una vista previa con datos de ejemplo.
  - 1a.5 El superadministrador confirma.
  - 1a.6 El sistema guarda la plantilla.

**Flujos de excepción**

- **1a.4a. Variable inexistente en el texto de la plantilla:** el sistema la señala y vuelve a 1a.3.
- **4a. Valores no válidos** (p. ej., rangos de tarifa solapados o una serie con un formato incompatible con las facturas ya emitidas): el sistema rechaza el cambio e indica el motivo.

---

## 5. Trazabilidad con los requisitos

Generada a partir del campo *Requisitos* de cada ficha (los números son los CU-XX que cubren cada requisito).

| FR | Casos de uso | UR | Casos de uso |
|---|---|---|---|
| FR1 | 01, 02, 03, 04, 42 | UR1 | 07 |
| FR2 | 01, 05, 13, 39 | UR2 | 09, 15 |
| FR3 | 03, 04, 05, 36 | UR3 | 25 |
| FR4 | 06, 07, 08, 31, 36 | UR4 | 11 |
| FR5 | 07 | UR5 | 32 |
| FR6 | 07, 20, 21, 25, 26 | UR6 | 33 |
| FR7 | 07, 08 | UR7 | 31 |
| FR8 | 08, 39 | UR8 | 34, 35 |
| FR9 | 09, 36 | UR9 | 19 |
| FR10 | 09, 11, 12, 16, 19, 42 | UR10 | 06 |
| FR11 | 07, 13, 14, 23 | UR11 | 04 |
| FR12 | 07, 09, 15, 17, 42 | UR12 | 29 |
| FR13 | 09, 10, 16, 36 | UR13 | 37, 38, 39 |
| FR14 | 15, 17, 22, 23, 25, 27, 42 | UR14 | 22, 24 |
| FR15 | 17, 18, 19, 22, 24 | UR15 | 42 |
| FR16 | 11, 15, 18, 19, 22, 42 | UR16 | 04, 05 |
| FR17 | 17, 25 | UR17 | 41 |
| FR18 | 20, 21, 26 | UR18 | 13, 14 |
| FR19 | 23, 26, 27, 33, 36, 42 |  |  |
| FR20 | 25, 28, 29, 30 |  |  |
| FR21 | 27, 30, 31, 36, 42 |  |  |
| FR22 | 32, 33 |  |  |
| FR23 | 34, 35 |  |  |
| FR24 | 37, 38 |  |  |
| FR25 | 36, 39, 40, 42 |  |  |
| FR26 | 07, 41 |  |  |
| FR27 | 41, 42 |  |  |
| FR28 | 13, 14, 20, 28 |  |  |

Los 28 FR y los 18 UR están cubiertos por al menos un caso de uso. Los NFR no generan casos propios: condicionan cómo se realizan y aparecen en el campo *Requisitos*, en las garantías y en las excepciones de las fichas (p. ej., NFR5 en R1, NFR7 en E2 y CU-28, NFR9 en CU-10).

---

## 6. Decisiones de modelado y cuestiones abiertas

**Decisiones de modelado** (las demás se explican donde se aplican: generalización en el apartado 2, relaciones en el apartado 3 y reglas en R1–R6)

1. **Facturas recurrentes.** El usuario no crea las recurrentes una a una: define la suscripción (CU-09) y el sistema las genera (CU-16). Así se cubre UR2 sin duplicar casos.
2. **Notificaciones como regla transversal (R2), no como caso de uso.** Notificar no aporta por sí mismo un resultado a ningún actor; lo que el empleado hace con las notificaciones está en CU-31.

**Cuestiones abiertas** (aspectos que el documento de requisitos v3 no fija o deja ambiguos). Para no bloquear la especificación se ha adoptado la decisión indicada, que **no es un requisito aprobado**: debe confirmarse con el cliente antes de implementar o probar ese comportamiento.

| ID | Laguna en los requisitos | Decisión adoptada en este documento | Casos afectados |
|---|---|---|---|
| P1 | FR2 define tres roles pero no la matriz exacta de permisos. | Generalización acumulativa (apartado 2). La importación de datos y de movimientos bancarios es del administrador; la baja de clientes (FR11 no tiene UR asociado) y la exportación limitada a la propia cartera (UR13 solo cita al administrador), del usuario estándar; el catálogo de conceptos (FR12 no indica quién lo mantiene), del superadministrador. | CU-07, CU-08, CU-29, CU-39, CU-42 |
| P2 | FR4 exige un responsable por cliente pero UR10 habla de «desasignar». | Un cliente creado por un usuario estándar queda asignado a él; si lo crea o importa un administrador, este indica el responsable. Desasignar se hace siempre como reasignación: ningún cliente queda sin responsable. | CU-04, CU-06, CU-07, CU-08 |
| P3 | FR6 y NFR6 permiten anonimizar contactos pero no fijan quién puede hacerlo ni el plazo de conservación. | Lo hace el responsable del cliente desde su ficha; no se fija plazo legal. | CU-07 |
| P4 | FR13 no indica cómo se factura un alta con inicio futuro a mitad de mes ni la fórmula y redondeo del prorrateo. | Las altas futuras comienzan el día 1 de un mes (sin prorrateo); el prorrateo es proporcional a los días naturales con redondeo al céntimo (NFR9). | CU-09, CU-10 |
| P5 | FR13 y FR19 no dicen si una pausa de suscripción o de recordatorios termina sola. | Las pausas de suscripción terminan en su fecha de reactivación y se avisa al responsable; las de recordatorios se reanudan solas en su fecha de revisión, o antes si el empleado lo decide. | CU-09, CU-33, CU-36 |
| P6 | FR16 admite que el superadministrador apruebe, pero prohíbe autoaprobar; no fija los umbrales. | Nadie, tampoco el superadministrador, aprueba su propia operación. Los umbrales son parámetros configurables; no se inventan cifras. | CU-18, CU-19, CU-42 |
| P7 | FR14 y FR15 nombran *anulada* y el cierre de periodo, pero no definen la reapertura ni el tratamiento contable del incobrable. | *Anulada* = rectificada por el importe total; un periodo cerrado no se reabre y sus facturas se corrigen con rectificativas fechadas en el periodo abierto; *incobrable* es una marca, no un estado contable. | CU-22, CU-23, CU-24 |
| P8 | FR23 no enumera qué acciones masivas se permiten. | Solo acciones que no emiten ni borran documentos (p. ej., reenviar facturas, asignar prioridad o exportar); cada elemento pasa las mismas comprobaciones que la acción individual. | CU-35 |
| P9 | Se admiten varias monedas (FR5) pero no se definen tipos de cambio. | Los indicadores y objetivos financieros se calculan por moneda, sin conversión. | CU-37, CU-38 |
| P10 | FR28 no define cómo detectar solicitudes de baja repetidas por API. | Se considera repetida la que llega para un cliente con una solicitud de baja ya abierta; la API solo crea una tarea de revisión, nunca ejecuta la baja. | CU-14 |
| P11 | FR1 y NFR5 no fijan la duración del bloqueo tras cinco intentos fallidos. | Duración configurable. | CU-01, CU-42 |
| P12 | FR16 somete a aprobación todas las rectificaciones; FR15 dice que se aprueban «según el importe». | Se aplica FR15: la rectificativa se retiene si supera el umbral de rectificación configurable. | CU-18, CU-22, CU-42 |
