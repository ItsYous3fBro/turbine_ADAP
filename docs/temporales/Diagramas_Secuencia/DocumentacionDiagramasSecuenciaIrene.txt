# **Documento de los Diagramas de Secuencia**

**Grupo:** Ramón Llull

**Curso:** 3º B

## **1. Descripción general**

Este documento explica los diagramas de secuencia de los casos de uso del gestor de facturas y clientes de TurbineH y los motivos de las decisiones tomadas.

| Caso de uso | Bloque (diagrama de casos de uso) | Diagramas |
|---|---|---|
| **CU-01 · Iniciar sesión** | D1 · Acceso, usuarios y permisos | `DiagramaDeSecuenciaCU1a`, `CU1b` y `CU1Completo` |
| **CU-07 · Gestionar cliente** | D2 · Clientes y suscripciones | `DiagramaDeSecuenciaCU7a`, `CU7b`, `CU7c`, `CU7d` y `CU7Completo` |
| **CU-39 · Exportar informes y datos** | D6 · Información y configuración | `DiagramaDeSecuenciaCU39a`, `CU39b` y `CU39Completo` |

Cada caso de uso se ha dividido en partes para que cada diagrama se lea con facilidad. Además, hay una versión completa que reúne todos los fragmentos divididos unificados.

## **2. Criterios comunes**

* **Se mezclan componentes y clases.** Los participantes son componentes del diagrama de componentes (Interfaz web, Autenticación y permisos, Clientes y suscripciones, Dashboard e informes, Auditoría, Base de datos...) y objetos de clases del diagrama de clases (`u : Usuario`, `c : Cliente`, `exp : Exportacion`...). Los componentes muestran qué parte del sistema hace cada cosa y por qué interfaz. Las clases permiten que los métodos sean los del diagrama de clases, como pide el profesor.
* **Las interfaces se indican entre corchetes.** Cada mensaje entre componentes lleva su interfaz (`[IAuth]`, `[IClientes]`, `[IInformes]`, `[IFacturacion]`, `[IIdentidad]`, `[ILog]`, `[IDatabase]`), igual que en el diagrama de componentes.
* **Los mensajes a clases usan sus métodos.** Por ejemplo, `autenticar(contrasena : String)`, `asignarResponsable(...)`, `cancelar()` o `generar()`. Los objetos nuevos se crean con un mensaje de creación.
* **Las operaciones propias de un componente** (como `comprobarDominio` o `comprobarEstado`) no están en las clases porque no son responsabilidad de ningún objeto de dominio, sino del componente que gestiona el caso de uso.
* **La numeración sigue el caso de uso.** El escenario principal va con los números de sus pasos (1, 2, 3...), cada flujo alternativo con su código (1a, 1b, 1c, 6a...) y las llamadas internas que cuelgan de un paso con decimales (por ejemplo, 4.1).
* **Las alternativas y excepciones son bloques `alt`.** Cada uno lleva el código del caso de uso (Excepción 2a, Excepción 3a...) para localizarlo rápido.
* **Todo queda registrado en Auditoría.** Cada operación importante, correcta o fallida, llama a Auditoría mediante `ILog`, y esta guarda el registro con `IDatabase`. Así se cumple el registro de las acciones.
* **Los componentes de negocio llaman directamente a Auditoría y a la Base de datos.** Es coherente con el diagrama de componentes, donde el paquete Negocio se conecta como conjunto a `IDatabase` e `ILog`.

## **3. CU-01 · Iniciar sesión**

**Actores:** Usuario estándar y, como secundario, Microsoft (proveedor de identidad). **Componentes:** Interfaz web, Autenticación y permisos, Microsoft, Auditoría y Base de datos. **Clases:** `u : Usuario` y `s : Sesion`.

**Qué muestra.** El diagrama 1a recoge el escenario principal (correo corporativo y contraseña): comprueba el dominio, obtiene el usuario, verifica las credenciales, comprueba el estado de la cuenta y crea la sesión. El diagrama 1b recoge el flujo alternativo 1a: el acceso con cuenta Microsoft.

**Decisiones y motivos**

* **Dos diagramas.** El acceso con Microsoft sigue un camino distinto (delega en `IIdentidad`) y tiene su propia excepción, así que se dibuja aparte para no mezclar ambos caminos y mejorar la lectura.
* **Dominio, organización y estado son operaciones del componente** (`comprobarDominio`, `comprobarOrganizacion`, `comprobarEstado`). Son reglas de acceso del componente de Autenticación y permisos, no métodos de `Usuario`.
* **La verificación y el bloqueo están en `Usuario`.** `autenticar(contrasena : String)` y `registrarIntentoFallido()` son métodos de la clase. La nota recuerda que al 5.º intento fallido consecutivo la cuenta pasa a BLOQUEADA.
* **La sesión la crea `crear(u)`** a partir del usuario, como indica la composición `Usuario *-- Sesion` del diagrama de clases. La nota indica que expira a los 30 minutos de inactividad.
* **Los errores son genéricos.** Ante un dominio no autorizado o unas credenciales incorrectas, el usuario recibe un mensaje de error.
* **Se registra siempre el intento,** sea correcto o fallido.
* **En el acceso con Microsoft también se comprueba la organización.** Si la cuenta no pertenece a Turbine, se deniega con un mensaje genérico, igual que en el dominio no autorizado.

## **4. CU-07 · Gestionar cliente**

**Actor:** Usuario estándar (en el alta, un administrador puede elegir el responsable). **Componentes:** Interfaz web, Clientes y suscripciones, Facturación y aprobaciones (solo en la baja), Auditoría y Base de datos. **Clases:** `c : Cliente`, `ct : Contacto` y `s : Suscripcion`.

**Qué muestra.** Cuatro diagramas, uno por cada parte del ciclo de vida del cliente:

* **7a · Alta.** Comprueba que el identificador fiscal no esté duplicado, crea el cliente `c` y sus contactos `ct`, asigna el responsable y comprueba con `datosFiscalesCompletos()` si queda INCOMPLETO o ACTIVO. Después registra la auditoría.
* **7b · Consultar y actualizar la ficha.** Muestra la ficha completa y su línea de tiempo, valida los cambios y permite desactivar o anonimizar un contacto con `desactivar()` y `anonimizar()`.
* **7c · Reactivar.** Antes de llamar a `reactivar()`, comprueba si la ley permite conservar los datos (excepción 1b1).
* **7d · Baja.** Consulta la deuda a Facturación mediante `IFacturacion` y `calcularDeuda()`. Con deuda pendiente ofrece la baja pendiente: cancela la suscripción con `cancelar()`, mantiene los recordatorios y deja al cliente en BAJA_PENDIENTE. Sin deuda, cancela la suscripción y llama a `darDeBaja(motivo)`, que es un borrado lógico (DADO_DE_BAJA).

**Decisiones y motivos**

* **Clientes y suscripciones es el componente central,** porque es el que ofrece `IClientes` y agrupa las fichas, los contactos y las suscripciones (decisión DD2 del diagrama de componentes).
* **La baja consulta a Facturación.** Es la dependencia que el diagrama de componentes recoge en DD10: Clientes y suscripciones usa Facturación para comprobar las facturas pendientes antes de una baja.
* **La baja es un borrado lógico.** Nunca se borran datos ni facturas del cliente (garantía mínima del caso de uso).
* **Las validaciones previas son mensajes propios** (consultar el NIF, comprobar la conservación legal, consultar la deuda). Así se ve en qué punto se decide cada flujo alternativo o de excepción.
* **Dos confirmaciones en la baja (1c.3).** La baja pendiente y la baja definitiva piden confirmación al empleado, como dice el caso de uso.

## **5. CU-39 · Exportar informes y datos**

**Actor:** Usuario estándar. **Componentes:** Interfaz web, Dashboard e informes, Auditoría y Base de datos. **Clases:** `exp : Exportacion` y `prog : ProgramacionInforme`.

**Qué muestra.** El diagrama 39a recoge el escenario principal: elegir informe y filtros, elegir formato (CSV en UTF-8 o PDF), generar el archivo y ponerlo a disposición del empleado. El diagrama 39b recoge los dos flujos alternativos: programar el envío periódico y regenerar una exportación caducada.

**Decisiones y motivos**

* **El contenido se limita a los permisos del usuario.** `consultarDatosPermitidos(tipoInforme, filtros, permisosUsuario)` es la forma de cumplir la garantía mínima: dos usuarios con distintos permisos obtienen informes distintos con los mismos filtros.
* **Primero se consultan los datos y después se crea la exportación.** Si no hay datos, se informa y no se genera ningún archivo.
* **La exportación es un objeto.** `crear(...)` y `generar()` son los métodos de `Exportacion`, y se guarda en la base de datos. Así se conservan la fecha, los filtros y el usuario de cada exportación.
* **La descarga es opcional (`opt`).** El empleado puede descargar el archivo cuando quiera, con `descargar()`.
* **Programar solo guarda la programación.** El flujo 1a crea una `ProgramacionInforme` y la guarda. No envía nada, porque eso lo hace CU-40 más adelante.
* **Regenerar comprueba los datos y permisos actuales.** Si los datos y los permisos lo permiten, el sistema vuelve a generar el archivo. Si no, informa del motivo.

## **6. Correlación con el resto de diagramas**

| Diagrama | Cómo se relaciona con los diagramas de secuencia |
|---|---|
| **Casos de uso** | Cada secuencia sigue los pasos, flujos alternativos y excepciones del caso de uso, con sus mismos códigos (1a, 6b, 3a...). |
| **Componentes** | Los participantes y las interfaces son los del diagrama de componentes: Interfaz web, Autenticación y permisos, Clientes y suscripciones, Facturación y aprobaciones, Dashboard e informes, Base de datos, Auditoría y Microsoft. |
| **Clases** | Los mensajes a objetos usan métodos reales: `Usuario` (`autenticar`, `registrarIntentoFallido`), `Cliente` (`asignarResponsable`, `datosFiscalesCompletos`, `calcularDeuda`, `darDeBaja`, `reactivar`), `Contacto` (`desactivar`, `anonimizar`), `Suscripcion` (`cancelar`) y `Exportacion` (`generar`, `descargar`). También se crean objetos de `Sesion` y `ProgramacionInforme`. |