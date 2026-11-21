# Gestor de facturas Turbine — Diagramas de secuencia (CU-32, CU-34, CU-37)

Documentación conjunta de los diagramas de secuencia de los casos de uso **CU-32**, **CU-34** y **CU-37**.


Archivos incluidos:

| Caso de uso | Imagen | Código fuente PlantUML |
|-------------|--------|------------------------|
| CU-32 | `diagramas/CU-32.png` | `diagramas/CU-32.puml` |
| CU-34 | `diagramas/CU-34.png` | `diagramas/CU-34.puml` |
| CU-37 | `diagramas/CU-37.png` | `diagramas/CU-37.puml` |

## Índice

1. [CU-32 Gestionar incidencia](#1-cu-32-gestionar-incidencia)
2. [CU-34 Buscar clientes y facturas](#2-cu-34-buscar-clientes-y-facturas)
3. [CU-37 Consultar dashboard financiero](#3-cu-37-consultar-dashboard-financiero)

---

## 1. CU-32 Gestionar incidencia

### 1.1 Ficha

| Campo | Descripción |
|-------|-------------|
| **Identificador** | CU-32 |
| **Nombre** | Gestionar incidencia |
| **Diagrama de origen** | D5 · Seguimiento y trabajo diario |
| **Actor principal** | Usuario estándar |
| **Relaciones** | `«extend»` desde **CU-33 Pausar recordatorios de una factura**. Condición: *la incidencia afecta al cobro de una factura*. Punto de extensión: *pausa (paso 1)*. |
| **Precondiciones** | El usuario está autenticado con rol de usuario estándar o superior. |
| **Postcondiciones** | La incidencia queda registrada o actualizada y se anota en el historial. Si afectaba al cobro, los recordatorios de la factura quedan pausados. |

### 1.2 Participantes

| Participante | Tipo | Función |
|--------------|------|---------|
| Usuario estándar | Actor | Consulta, registra y actualiza incidencias. |
| Gestor de facturas (Turbine) | Sistema | Valida los datos, aplica las reglas de negocio y orquesta el caso de uso. |
| Base de datos | Almacén | Guarda facturas, incidencias, recordatorios e historial. |

### 1.3 Diagrama de secuencia

**CU-32. Se encuentra en Archivos finales/Diagramas de Secuencia**

### 1.4 Descripción de los mensajes

1. `consultarIncidencias(filtros)`: el usuario solicita el listado. El sistema lo obtiene de la base de datos (`obtenerIncidencias`) y lo devuelve.
2. **Alternativa «registra una incidencia nueva»**: el sistema comprueba que la factura existe (`obtenerFactura`), valida los datos (`validarIncidencia`) y guarda la incidencia con estado `ABIERTA`, devolviendo su identificador.
3. **Alternativa «actualiza una incidencia existente»**: el sistema recupera la incidencia, valida el cambio de estado (`validarCambio`) y la actualiza.
4. **Fragmento opcional (extensión CU-33)**: solo si la incidencia afecta al cobro de una factura, se invoca `CU-33 pausarRecordatorios(idFactura, idIncidencia)`. Si no afecta, los recordatorios siguen activos.
5. `registrarHistorial(...)`: se deja constancia de la acción y el usuario recibe la confirmación «incidencia gestionada».

### 1.5 Casos especiales

| Caso | Tratamiento |
|------|-------------|
| Datos inválidos o factura inexistente | `validarIncidencia` falla: no se guarda nada y se informa al usuario. |
| Transición de estado no permitida | `validarCambio` falla: la incidencia no se modifica. |
| Factura sin recordatorios activos | CU-33 termina sin cambios. |

---

## 2. CU-34 Buscar clientes y facturas

### 2.1 Ficha

| Campo | Descripción |
|-------|-------------|
| **Identificador** | CU-34 |
| **Nombre** | Buscar clientes y facturas |
| **Diagrama de origen** | D5 · Seguimiento y trabajo diario |
| **Actor principal** | Usuario estándar |
| **Relaciones** | `«extend»` desde **CU-35 Ejecutar acción masiva**. Condición: *se seleccionan varios resultados*. Punto de extensión: *acción masiva (paso 3)*. |
| **Precondiciones** | El usuario está autenticado con rol de usuario estándar o superior. |
| **Postcondiciones** | El usuario ve los clientes y facturas que cumplen los criterios. Si lanzó una acción masiva, esta queda aplicada sobre la selección. |

### 2.2 Participantes

| Participante | Tipo | Función |
|--------------|------|---------|
| Usuario estándar | Actor | Introduce criterios y selecciona resultados. |
| Gestor de facturas (Turbine) | Sistema | Valida criterios, combina resultados y coordina la acción masiva. |
| Base de datos | Almacén | Consulta de clientes y facturas y aplicación de acciones masivas. |

### 2.3 Diagrama de secuencia

**CU-34. Se encuentra en Archivos finales/Diagramas de Secuencia**

### 2.4 Descripción de los mensajes

1. `buscar(texto, filtros)`: el usuario lanza la búsqueda y el sistema valida los criterios (`validarCriterios`).
2. El sistema consulta clientes (`consultarClientes`) y facturas (`consultarFacturas`) y los une con `combinarResultados`. Devuelve el listado al usuario.
3. `seleccionarResultados(ids)` (**paso 3**): el usuario marca uno o varios resultados.
4. **Fragmento opcional (extensión CU-35)**: solo si hay varios resultados seleccionados, se ejecuta `CU-35 ejecutarAccionMasiva(accion, ids)`. El sistema aplica la acción sobre el lote y devuelve un resumen por elemento.
5. Si se selecciona un único resultado, no se activa la extensión.

### 2.5 Casos especiales

| Caso | Tratamiento |
|------|-------------|
| Búsqueda sin coincidencias | El listado se devuelve vacío y se informa al usuario. |
| Criterios inválidos | `validarCriterios` los rechaza antes de consultar la base de datos. |
| Acción masiva con errores parciales | El resumen distingue los elementos correctos de los fallidos. |

---

## 3. CU-37 Consultar dashboard financiero

### 3.1 Ficha

| Campo | Descripción |
|-------|-------------|
| **Identificador** | CU-37 |
| **Nombre** | Consultar dashboard financiero |
| **Diagrama de origen** | D6 · Información y configuración |
| **Actor principal** | Administrador (el Superadministrador lo hereda por generalización) |
| **Relaciones** | Sin `«include»` ni `«extend»`. Consulta los objetivos definidos en **CU-38 Definir objetivos financieros**. |
| **Precondiciones** | El usuario está autenticado con rol Administrador o Superadministrador. |
| **Postcondiciones** | Se muestran los indicadores del periodo y el cumplimiento de objetivos. No se modifica ningún dato. |

### 3.2 Participantes

| Participante | Tipo | Función |
|--------------|------|---------|
| Administrador | Actor | Consulta el dashboard y cambia el periodo. |
| Gestor de facturas (Turbine) | Sistema | Verifica el rol, calcula los indicadores y los compara con los objetivos. |
| Base de datos | Almacén | Facturación, cobros y objetivos financieros. |

### 3.3 Diagrama de secuencia

**CU-37. Se encuentra en Archivos finales/Diagramas de Secuencia**

### 3.4 Descripción de los mensajes

1. `consultarDashboard(periodo)`: el administrador accede al dashboard y el sistema comprueba su rol (`verificarRol`).
2. **Alternativa «rol autorizado»**: el sistema obtiene el resumen financiero del periodo (facturado, cobrado, pendiente y vencido) y los objetivos vigentes (definidos en CU-38). Después ejecuta `calcularIndicadores` y devuelve el dashboard con el cumplimiento de objetivos.
3. **Fragmento opcional «cambia el periodo»**: se repiten las consultas y el cálculo para el nuevo periodo y se devuelve el dashboard actualizado.
4. **Alternativa «rol no autorizado»**: el sistema responde «acceso denegado» sin consultar datos financieros.

### 3.5 Casos especiales

| Caso | Tratamiento |
|------|-------------|
| Usuario estándar | Acceso denegado, por la dirección de la herencia de actores en D6. |
| Periodo sin datos | Los indicadores se muestran a cero. |
| Periodo sin objetivos definidos | Se muestran los datos reales sin comparativa. |

---
