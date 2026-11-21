# **Documento de los Diagramas de Secuencia**

**Grupo:** Ramón Llull

**Curso:** 3º B

## **1. Descripción general**

Este documento explica los diagramas de secuencia de los casos de uso de emisión y generación de facturas del gestor de facturas de TurbineH y los motivos de las decisiones tomadas.

| Caso de uso | Bloque (diagrama de casos de uso) | Diagramas |
|---|---|---|
| **CU-15 · Crear factura puntual** | D3 · Facturación y aprobaciones | `DiagramaDeSecuenciaCU15a`, `CU15b` y `CU15Completo` |
| **CU-16 · Generar facturas recurrentes** | D3 · Facturación y aprobaciones | `DiagramaDeSecuenciaCU16a`, `CU16b` y `CU16Completo` |
| **CU-22 · Rectificar o anular factura** | D3 · Facturación y aprobaciones | `DiagramaDeSecuenciaCU22a`, `CU22b` y `CU22Completo` |

Cada caso de uso tiene dos partes: la **a** con el escenario principal y la **b** con los flujos alternativos y de excepción. Además, hay una versión completa que los reúne todos en un solo diagrama.

## **2. Criterios comunes**

Se siguen los mismos criterios que en el resto de diagramas de secuencia del grupo: participantes que son componentes del diagrama de componentes y objetos del diagrama de clases, interfaces entre corchetes (`[IFacturacion]`, `[IDatabase]`, `[ILog]`), numeración según los pasos del caso de uso, bloques `alt` con el código de cada flujo y registro en Auditoría de toda acción sobre datos económicos. Además:

* **CU-17 y CU-18 se representan con un fragmento `ref`.** Los tres casos incluyen CU-17 (emitir factura) y CU-15 y CU-22 son extendidos por CU-18 (retener para aprobación). Un `ref` es la forma de mostrar en un diagrama de secuencia que en ese punto se ejecuta otro caso de uso, sin copiar su contenido.
* **Facturación y aprobaciones es el componente central,** porque es el que ofrece `IFacturacion` y agrupa la creación de facturas, la numeración, la rectificación y las aprobaciones (decisión DD2 del diagrama de componentes).
* **Los datos del cliente y de las suscripciones se leen de la base de datos.** Facturación no requiere `IClientes` para evitar una dependencia circular, como explica la decisión DD10 del diagrama de componentes.
* **La aprobación se decide con `requiereAprobacion()`.** Es el método de la interfaz `Aprobable`, que realiza `Factura` (y por herencia `FacturaRectificativa`). Compara la factura con los umbrales de `Configuracion`.
* **En la parte b cada flujo va separado por una línea divisoria** con su código y el paso donde empieza, para que se lea cada uno por separado.
* **En la versión completa se usan `alt` anidados y no `break`.** Un `break` solo sale del fragmento que lo contiene, así que el flujo seguiría hacia CU-17 aunque la factura se hubiera guardado como borrador o retenido.
* **No se dibuja la comprobación de permisos.** Que un usuario estándar solo trabaje con los clientes de su cartera es una regla común (R1 y E1) que el documento de casos de uso no repite en cada ficha.

## **3. CU-15 · Crear factura puntual**

**Actor:** Usuario estándar. **Componentes:** Interfaz web, Facturación y aprobaciones, Auditoría y Base de datos. **Clases:** `f : Factura` y `l : LineaFactura` (y `Cliente`, `Concepto` y `Configuracion` como datos devueltos por la base de datos).

**Qué muestra.** El diagrama 15a recoge el escenario principal: crear el borrador con los datos fiscales del cliente, añadir las líneas, calcular los totales y el vencimiento, presentar la vista previa, confirmar, comprobar si hace falta aprobación y emitir con CU-17. El diagrama 15b recoge los flujos 1a (retomar una factura aprobada), 4a (importe atípico), 6a (guardar como borrador), 7a (retención con CU-18) y la excepción 7b (CU-17 no puede emitir).

**Decisiones y motivos**

* **El borrador se guarda al crearlo y en cada cambio.** La garantía mínima dice que si el proceso se interrumpe el borrador se conserva, así que no puede quedarse solo en memoria hasta el final.
* **Las líneas se añaden en bloque.** `anadirLineas(lineas)` lleva dentro un `loop` por cada línea y un `alt` según si el concepto sale del catálogo (`Concepto`) o lo redacta el empleado. Cada línea se crea como objeto y calcula su importe con `calcularImporte()`.
* **El vencimiento y la vista previa son operaciones del componente** (`calcularVencimiento`, `generarVistaPrevia`), porque `Factura` no tiene esos métodos. El vencimiento usa `plazoPagoDias` de `Configuracion`.
* **La vista previa no pasa por Documentos y firma.** El PDF firmado solo se genera al emitir, dentro de CU-17.
* **El importe atípico (4a) es solo un aviso.** Se comprueba con `esImporteAtipico()` y no impide seguir. Si además supera el umbral, la factura se retendrá en 7a.
* **Retomar una factura aprobada (1a) no vuelve a pedir aprobación.** Se pasa directamente al paso 7 y no se llama a `requiereAprobacion()`.
* **Si CU-17 no puede emitir (7b),** la factura no recibe número, se queda en borrador (o aprobada si venía de 1a) y se indica al empleado qué debe corregir.

## **4. CU-16 · Generar facturas recurrentes**

**Actor:** Temporizador. **Componentes:** Planificador, Facturación y aprobaciones, Auditoría y Base de datos. **Clases:** `e : EjecucionProceso`, `s : Suscripcion` y `f : Factura`.

**Qué muestra.** El diagrama 16a recoge el escenario principal: registrar el inicio de la ejecución, obtener las suscripciones activas sin factura en el periodo, preparar y emitir una factura por cada una y registrar el fin. El diagrama 16b recoge el flujo 1a (recuperación diaria de un periodo incompleto) y la excepción 4a (CU-17 no emite la factura de una suscripción).

**Decisiones y motivos**

* **Aparecen el Temporizador y el Planificador.** El Temporizador es el actor del caso de uso y el Planificador es el componente que llama a Facturación mediante `IFacturacion` (decisión DD7). Así coinciden los dos diagramas.
* **El importe se calcula con `calcularCuotaMensual()`.** Es el método de `Suscripcion`, que aplica la tarifa vigente (`NivelTarifa`) y las excepciones comerciales aprobadas (FR10). No se dibujan esas clases por dentro porque no tienen métodos propios para ello.
* **Las facturas no se duplican gracias al paso 3.** La consulta solo devuelve suscripciones activas sin factura emitida en el periodo, que es lo que dice el caso de uso. Si la ejecución se repite, las ya facturadas no vuelven a salir (NFR11). No se repite la comprobación dentro del `loop`.
* **La configuración se lee una sola vez,** antes del `loop`, para calcular el vencimiento de todas las facturas.
* **La ejecución se registra como objeto.** `EjecucionProceso` se crea al empezar y se actualiza al terminar con el resultado. Como la clase no tiene métodos, la actualización se hace en la base de datos.
* **Si CU-17 falla en una suscripción (4a), el borrador se borra.** La factura guarda una copia de los datos fiscales, así que si se reutilizara volvería a fallar. Se borra (solo los borradores se pueden borrar, FR14), se registra en Auditoría y se rehace en la recuperación con los datos ya corregidos. El objeto se destruye en el diagrama. El proceso sigue con las demás suscripciones (E2) y la ejecución queda como incompleta.
* **La recuperación (1a) reutiliza la misma ejecución.** El caso de uso dice que se procesa desde el paso 3, sin registrar un nuevo inicio. Para no repetir los pasos 3 a 5 se usa un `ref`.

## **5. CU-22 · Rectificar o anular factura**

**Actor:** Administrador. **Componentes:** Interfaz web, Facturación y aprobaciones, Auditoría y Base de datos. **Clases:** `o : Factura` (la original) y `r : FacturaRectificativa` (y `PeriodoFacturacion` como dato devuelto por la base de datos).

**Qué muestra.** El diagrama 22a recoge el escenario principal: comprobar que la factura está emitida, crear la rectificativa vinculada a la original, aplicar las correcciones, presentar la vista previa con la diferencia, confirmar y emitir con CU-17. El diagrama 22b recoge los flujos 1a (anular), 1c (retomar una rectificativa aprobada), 6a (retención con CU-18) y la excepción 1b (la factura no está emitida).

**Decisiones y motivos**

* **Rectificar y anular usan el mismo mensaje.** `solicitarRectificacion(idFactura, motivo, anular)` lleva un parámetro que se guarda en el atributo `anulaTotalmente` de `FacturaRectificativa`. Los dos caminos comparten casi todo; en la anulación no hay paso 3 porque la rectificativa es por el importe total.
* **Se puede rectificar una factura emitida, enviada, no notificada, vencida o pagada.** Una factura ya rectificada o anulada no se vuelve a tocar: se rectifica su rectificativa. Un borrador, una factura pendiente de aprobación o una aprobada dan la excepción 1b. `comprobarEmitida` es una operación del componente.
* **La rectificativa lleva fecha del periodo abierto.** Por eso se obtiene el `PeriodoFacturacion` abierto antes de crearla.
* **La original nunca se edita.** Es CU-17, en su paso 4a, quien la pasa a RECTIFICADA o ANULADA. Se indica dentro del `ref` porque es parte de CU-17 y no de este caso de uso (R3, FR15).
* **La retención se decide con el umbral de rectificación.** `requiereAprobacion()` compara la rectificativa con `umbralRectificacion` de `Configuracion`.
* **Retomar una rectificativa aprobada (1c) no vuelve a pedir aprobación.** Se pasa directamente al paso 6.
* **La diferencia y la vista previa son operaciones del componente** (`calcularDiferencia`, `generarVistaPrevia`), igual que en CU-15.

## **6. Correlación con el resto de diagramas**

| Diagrama | Cómo se relaciona con los diagramas de secuencia |
|---|---|
| **Casos de uso** | Cada secuencia sigue los pasos, flujos alternativos y excepciones del caso de uso, con sus mismos códigos (1a, 4a, 6a, 7b...). Las relaciones `<<include>>` con CU-17 y `<<extend>>` con CU-18 se dibujan como fragmentos `ref`. |
| **Componentes** | Los participantes y las interfaces son los del diagrama de componentes: Interfaz web, Facturación y aprobaciones, Planificador, Auditoría y Base de datos, con `IFacturacion`, `IDatabase` e `ILog`. |
| **Clases** | Los mensajes a objetos usan métodos reales: `Factura` (`calcularTotales`, `esImporteAtipico`, `requiereAprobacion`), `LineaFactura` (`calcularImporte`) y `Suscripcion` (`calcularCuotaMensual`). También se crean objetos de `Factura`, `LineaFactura`, `FacturaRectificativa` y `EjecucionProceso`, y se usan los estados de `EstadoFactura` y los tipos de `TipoFactura`. |
