# Casos de Uso - Sistema de Facturación "Turbine"

Este documento resume los flujos de interacción principales entre los usuarios (actores) y el sistema de facturación independiente de Turbine.

## Actores del Sistema

* **Empleado:** Gestiona el día a día (clientes, facturas, incidencias).

* **Admin / SuperAdmin:** Realiza cargas masivas y aprueba operaciones financieras críticas.

* **Dirección:** Consulta el estado financiero de la empresa.

* **Sistema:** Ejecuta cobros automáticos, valida registros y genera respaldos.

## Listado de Casos de Uso

### 1. Gestionar Ficha de Clientes (CU-01)

* **Actor:** Empleado

* **Descripción:** Permite dar de alta clientes, asociar múltiples contactos (comercial, administración, impagos) y controlar su estado (Activo, Pausado, Inactivo).

* **Validación:** El sistema bloquea automáticamente la creación de identificadores fiscales duplicados.

* **Requisitos asociados:** 
  * UR-01 (Crear fichas de clientes y contactos)
  * RF1 (Controlar estados del cliente)
  * RF2 (Detectar clientes duplicados)
  * RF3 (Ficha comercial multi-contacto)

### 2. Importar y Exportar Datos Masivos (CU-02)

* **Actores:** Admin, Empleado, Sistema

* **Descripción:**

  * *Importación:* El Admin puede cargar miles de clientes vía CSV, visualizando errores antes del guardado.

  * *Exportación:* Los empleados generan reportes en PDF/CSV. El sistema compila periódicamente un `.ZIP` automático para la gestoría.

* **Requisitos asociados:** 
  * BR-02 (Gestionar miles de clientes eficientemente)
  * UR-02 (Importar clientes vía CSV con vista previa)
  * UR-06 (Exportar información en CSV/PDF)
  * RF8 (Importar/exportar datos y generar ZIP para gestoría)

### 3. Aprobar Operaciones Críticas (CU-03)

* **Actor:** Admin / SuperAdmin

* **Descripción:** Toda modificación sensible (descuentos, cambios de precio, alteración de vencimientos o facturas de alto importe) queda bloqueada en estado "Pendiente" hasta que un administrador la valida.

* **Requisitos asociados:** 
  * UR-03 (Gestionar proceso de aprobación)
  * RF4 (Gestor de aprobaciones de modificaciones e importes)

### 4. Conciliar Pagos y Movimientos (CU-04)

* **Actor:** Empleado

* **Descripción:** Cruza los ingresos recibidos (Stripe, Revolut, transferencias) con las facturas emitidas. Si los importes no coinciden al céntimo, el sistema levanta una alerta para su revisión manual.

* **Requisitos asociados:** 
  * UR-04 (Conciliar pagos y revisión manual de descuadres)
  * RF7 (Conciliar pagos de múltiples fuentes como Stripe o banco)

### 5. Gestionar Incidencias de Facturación (CU-05)

* **Actor:** Empleado

* **Descripción:** Permite abrir tickets internos ante quejas o problemas. Esta acción **pausa automáticamente** los intentos de cobro y los correos de recordatorio hasta que se resuelva la incidencia.

* **Requisitos asociados:** 
  * UR-05 (Crear y gestionar incidencias internas)
  * RF5 (Abrir incidencias para parar flujos de cobro)

### 6. Gestionar el Ciclo de Vida de Facturas (CU-06)

* **Actores:** Empleado, Sistema

* **Descripción:** Cubre desde la creación de "Borradores" hasta la emisión final. Las facturas emitidas son inalterables y llevan numeración única. Cualquier error posterior se corrige obligatoriamente mediante una **factura rectificativa**.

* **Requisitos asociados:** 
  * BR-04 (Cumplir normativa de datos y conservación)
  * RF5 (Guardar borradores de facturas)
  * RF6 (Inmutabilidad y facturas rectificativas)
  * RNF3 (Control de fallos y reanudación automática sin duplicidades)

### 7. Visualizar Panel Financiero / Dashboard (CU-07)

* **Actores:** Dirección, Admin

* **Descripción:** Proporciona un panel de control limpio y unificado con métricas en tiempo real sobre la salud financiera, flujo de caja y estado general de cobros.

* **Requisitos asociados:** 
  * BR-05 (Visión clara del dinero y estado de clientes para dirección)

### 8. Previsualizar Notificaciones - Sandbox (CU-08)

* **Actores:** Empleado, Admin

* **Descripción:** Entorno seguro de pruebas que permite simular el envío de correos, recordatorios y facturas para verificar su formato sin impactar a los clientes reales.

* **Requisitos asociados:** 
  * RNF2 (Modo de pruebas y previsualización)

---
*Nota: Requisitos transversales como la **independencia del sistema frente a la IA (BR-01)**, la **automatización eficiente del trabajo (BR-03)**, el **registro de auditoría inmutable (RNF1)** y la **gestión segura de sesiones de usuario (RNF4)** aplican a nivel global sobre todos los casos de uso descritos.*