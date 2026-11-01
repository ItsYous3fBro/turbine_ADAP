# Requisitos Funcionales (RF)

## 1. Gestión de Acceso y Roles

- **RF-01 (Autenticación corporativa):** El sistema debe permitir el inicio de sesión exclusivamente mediante cuenta de correo corporativo de la organización (@turbine). El login será con usuario y contraseña.
- **RF-02 (Control de acceso por roles):** El sistema debe gestionar tres niveles de permisos:
  - **SuperAdministrador:** Control total, auditoría y configuración global.
  - **Administrador:** Gestión operativa, métricas avanzadas y configuración delegada.
  - **Lector / Usuario operativo:** Gestión y consulta restringida únicamente a las empresas asignadas.
- **RF-03 (Asignación de carteras):** El sistema debe permitir vincular clientes o empresas específicas a trabajadores concretos para limitar su ámbito de facturación.

## 2. Gestión de Clientes y Planes

- **RF-04 (Registro fiscal de clientes):** El sistema debe registrar y mantener los datos fiscales completos de clientes particulares y empresas (nombre/razón social, NIF/CIF, dirección, contacto).
- **RF-05 (Asignación y revisión de planes):** El sistema debe asociar a cada cliente un plan de precios dependiente de su volumen de facturación anual, permitiendo su revisión periódica anual.
- **RF-06 (Condición de baja de clientes):** El sistema debe bloquear la baja o desactivación de un cliente mientras existan facturas pendientes o en estado impagado.

## 3. Emisión y Gestión del Ciclo de Vida de Facturas

- **RF-07 (Generación de facturas estándar y recurrentes):** El sistema debe generar facturas puntuales y recurrentes; la facturación recurrente se emitirá el día 1 de cada mes.
- **RF-08 (Prorrateo de alta inicial):** Si un cliente contrata el servicio antes del día 1, el sistema debe emitir automáticamente una factura puntual por la parte proporcional de los días restantes del mes. (???)
- **RF-09 (Firma digital):** El sistema debe permitir la firma electrónica de los documentos de factura mediante certificado digital antes de su emisión.
- **RF-10 (Edición temporal de facturas):** El sistema debe permitir la modificación de datos de una factura exclusivamente dentro del trimestre natural en el que fue emitida.
- **RF-11 (Máquina de estados y filtrado):** Las facturas deben transitar entre los estados Emitida (Pendiente de cobro), Pagada e Impagada, permitiendo filtrar y segmentar la vista por estos estados.

## 4. Cobros, Notificaciones y Pasarela

- **RF-12 (Integración de pasarela de pago):** El sistema debe conectarse con Stripe para procesar los cobros y actualizar automáticamente el estado de la factura a "Pagada" tras recibir la confirmación de la transacción.
- **RF-13 (Envío por correo electrónico):** El sistema debe despachar las facturas emitidas por email a las direcciones de contacto del cliente.
- **RF-14 (Automatización de recordatorios de cobro):** El sistema debe enviar recordatorios automáticos de pago en los días 3 y 5 posteriores a la emisión.
- **RF-15 (Flujo recurrente de impagos):** Si una factura supera el plazo de 5 días sin saldarse, el sistema debe cambiar su estado a "Impagada" e iniciar un ciclo automatizado de notificaciones periódicas de reclamación.

## 5. Almacenamiento, Métricas y Extensibilidad

- **RF-16 (Almacenamiento híbrido):** El sistema debe almacenar copias de las facturas generadas tanto en almacenamiento local como en un repositorio en la nube.
- **RF-17 (Dashboard e informes analíticos):** El sistema debe proporcionar a los administradores métricas e informes de facturación desglosados en ventanas semanales, mensuales, trimestrales y anuales.
- **RF-18 (Exposición de API):** El sistema debe exponer una API documentada para permitir la interoperabilidad y consulta de clientes, facturas y cobros.

---

# Requisitos No Funcionales (RNF)

- **RNF-01 (Diseño adaptativo - Usabilidad):** La interfaz debe ser completamente responsive y operar de forma fluida en dispositivos móviles, tablets y ordenadores portátiles.
- **RNF-02 (Línea gráfica corporativa):** La interfaz de usuario debe respetar fielmente el diseño, paleta de colores y componentes visuales de la identidad corporativa de TurbineH.
- **RNF-03 (Eficiencia de interacción - Usabilidad):** El flujo de emisión y consulta de facturas debe diseñarse para completarse en el menor número posible de clics.
- **RNF-04 (Cifrado de datos - Seguridad):** Los datos fiscales, registros contables y documentos de facturación deben estar cifrados en reposo (bases de datos y almacenamiento cloud) y en tránsito (TLS 1.3). (????)
- **RNF-05 (Documentación técnica - Mantenibilidad):** Los endpoints de la API deben estar especificados bajo un estándar abierto junto a documentación técnica de integración.
- **RNF-06 (Concurrencia y persistencia - Fiabilidad):** La base de datos debe garantizar transacciones ACID para evitar inconsistencias contables o dobles cobros durante el procesamiento concurrente de eventos de pago.
