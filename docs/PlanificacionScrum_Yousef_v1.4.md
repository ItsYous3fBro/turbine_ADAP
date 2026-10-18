# Plan de equipo Scrum · Gestor de facturas Turbine

**Planificación del proyecto**

Equipo de 7 personas · 6 sprints · proyecto teórico de análisis y diseño de software

| **Proyecto** | Gestor de facturas Turbine (diseño teórico) |
|---|---|
| **Cliente / stakeholder principal** | Turbine H · Alejandro Ruiz |
| **Product Owner** | *Nestor Rodriguez* |
| **Scrum Master** | *Yousef AL Haidar* |
| **Fecha de inicio / fin** | *[30/09/2026]* – *[dd/mm/aaaa]* |
| **Duración del sprint** | S1–S2: 10 días (hasta la exposición del 20/10) · desde S3: 2 semanas |
| **Número de sprints** | 6 + Sprint 0 de preparación |
| **Versión del documento** | 1.2 · 14/10/2026 |

### Contenido

1. Visión y objetivos
2. Equipo y roles
3. Reparto de trabajo por miembro
4. Acuerdos de equipo (Working Agreement)
5. Definiciones: Ready, Done, historias y estimación
6. Product Backlog
7. Hoja de ruta y entregables
8. Eventos Scrum y calendario
9. Capacidad del equipo
10. Fichas de sprint (Sprint 1 a 6)
11. Seguimiento: tablero, burndown y velocidad
12. Impedimentos, riesgos y decisiones
13. Sprint Review y Retrospectiva
14. Partes interesadas y comunicación

Anexos: checklists, glosario y fuentes

# Control de versiones

| **Versión** | **Fecha** | **Autor/a** | **Cambios** |
|---|---|---|---|
| 1.0 | 30/09/2026 | Yousef Al Haidaar| Versión inicial del plan de equipo. |
| 1.1 | 07/10/2026 | Yousef AL Haidar | Adaptación a proyecto teórico de análisis y diseño: se eliminan alcance y supuestos; se adaptan roles, RACI, fichas, definiciones, backlog y hoja de ruta. |
| 1.2 | 14/10/2026 | Yousef AL Haidar | Sprints 1 y 2 ajustados al primer parcial (20/10); nueva filosofía de trabajo con versiones temporales y archivos finales; canales de comunicación; preparación de la exposición. |
|   |   |   |   |
|   |   |   |   |

# 1. Visión y objetivos


**Visión:** Turbine H necesita un gestor de facturación interno, Factugest es una aplicación web que emite, envía y hace el seguimiento de facturas puntuales y recurrentes y de sus cobros. A diferencia de soluciones genéricas como Holded, se centra en la operativa de Turbine: pocos clics, uso desde cualquier dispositivo y recordatorios y cobros automáticos. El proyecto no construye la aplicación: la analiza y la diseña.

| **Visión** | Una aplicación web capaz de emitir, gestionar y seguir facturas puntuales y recurrentes, y sus cobros. |
|---|---|
| **Product Goal actual** | Diseñar de forma teórica la aplicación siguiendo buenas prácticas de ingeniería y arquitectura del software: requisitos, casos de uso, diagramas UML, documentación e informes Scrum, más una demo ilustrativa en el Sprint 3. |

## 1.1 Objetivos y métricas de éxito

| **Nº** | **Objetivo** | **Métrica / indicador** | **Meta y fecha** |
|---|---|---|---|
| O1 | Definir los requisitos funcionales y no funcionales | Archivo final de requisitos consensuado; cada requisito trazado a la entrevista; RNF medibles | Sprint 2 (sáb. 17/10) |
| O2 | Modelar los casos de uso | Diagrama PlantUML sin errores y documento de casos de uso que indica en qué requisitos se basa cada caso | Sprint 2 (sáb. 17/10) |
| O3 | Modelar la estructura del sistema | Diagrama de clases coherente con los casos de uso | Sprint 3 |
| O4 | Documentar la arquitectura | Documento de arquitectura y decisiones aprobado por el PO | Sprint 3 |
| O5 | Presentar una demo ilustrativa (micro-proyecto) | Demo de los flujos principales mostrada en la Review | Sprint 3 |
| O6 | Aplicar Scrum y documentarlo | Un informe Scrum (Review y Retro) por sprint | Cada sprint |

## 1.2 Épicas

Seis bloques de trabajo, uno por tipo de entregable. Se descomponen en elementos del backlog durante el refinamiento.

| **ID** | **Épica** | **Descripción** | **Valor para el negocio** | **Prioridad** |
|---|---|---|---|---|
| E1 | Requisitos | Requisitos funcionales y no funcionales trazados a la entrevista | Alto | Must |
| E2 | Casos de uso | Diagrama de casos de uso y especificación textual de cada caso | Alto | Must |
| E3 | Modelado estructural | Diagrama de clases de dominio y modelo de datos | Alto | Must |
| E4 | Arquitectura y documentación | Documento de arquitectura, decisiones (ADR) y documentación del repositorio | Medio | Should |
| E5 | Gestión Scrum | Planificación, informes de sprint, Review y Retrospectiva | Medio | Must |
| E6 | Demo (micro-proyecto) | Prototipo ilustrativo de los flujos principales para el Sprint 3 | Medio | Should |

# 2. Equipo y roles

La Guía Scrum define tres responsabilidades dentro del Scrum Team: un Product Owner, un Scrum Master y los Developers. Con 7 miembros, la composición utilizada es 1 PO, 1 SM y 5 Developers.



## 2.1 Directorio del equipo

| **Nº** | **Nombre** | **Rol Scrum** | **Especialización** | **Dedic.** | **Contacto / canal** |
|---|---|---|---|---|---|
| 1 | *Nestor* | Product Owner (PO) | Visión, backlog y validación con el cliente | *[XX]* % | *git/NestorDZ* |
| 2 | *Yousef* | Scrum Master (SM) | Proceso Scrum, coordinación e informes | *[XX]* % | *git/ItsYous3fBro* |
| 3 | *Irene* | Developer | Arquitectura y estándares de modelado | *[XX]* % | *git/Irenegm06* |
| 4 | *Adrian* | Developer | Modelo de dominio: requisitos funcionales y clases | *[XX]* % | *git/AdriRodriM22* |
| 5 | *Javier* | Developer | Usabilidad: requisitos no funcionales, prototipos y demo | *[XX]* % | *git/JavirMartinez22* |
| 6 | *Miguel* | Developer | Casos de uso y calidad: revisión y trazabilidad | *[XX]* % | *git/MiguelPPA06* |
| 7 | *Gonzalo* | Developer | Casos de uso, repositorio y documentación | *[XX]* % | *git/GMerida33* |

## 2.2 Responsabilidades por rol

### Product Owner

- Maximiza el valor del trabajo y es la única persona que ordena el Product Backlog.
- Representa al cliente (Turbine): valida que cada requisito y caso de uso responda a lo dicho en la entrevista, fusiona con cada subgrupo sus versiones temporales en el archivo final y redacta el informe y la documentación de cada tarea.
- Acepta o rechaza los artefactos terminados y resuelve dudas durante el sprint.

### Scrum Master

- Vela por que Scrum se entienda y se aplique bien; facilita los eventos como líder-servidor.
- Elimina impedimentos, coordina a los subgrupos, redacta los informes Scrum, prepara la Sprint Review y mantiene una copia de seguridad del repositorio.
- No es jefe de proyecto ni asigna tareas: el equipo se autoorganiza.

### Developers (5)

- Elaboran los artefactos de análisis y diseño de cada incremento: requisitos, casos de uso, diagramas UML y documentación.
- Planifican el sprint, deciden cómo hacer el trabajo y se autoorganizan.
- Suben cada versión de su trabajo a GitHub con un commit descriptivo y la avisan en el grupo, cumpliendo la Definición de Terminado.
- Mantienen la coherencia entre artefactos: mismos actores, identificadores y terminología.

## 2.3 Matriz de responsabilidades (RACI)

*R = realiza · A = responsable último (en tareas de equipo, todos los Developers) · C = consultado · I = informado.*

| **Actividad** | **PO** | **SM** | **Irene** | **Adrian** | **Javier** | **Miguel** | **Gonzalo** |
|---|---|---|---|---|---|---|---|
| Definir visión y Product Goal | A/R | C | C | C | C | C | C |
| Ordenar y priorizar el backlog | A/R | I | C | C | C | C | C |
| Refinar elementos y criterios de aceptación | A | C | R | R | R | R | R |
| Estimar | I | I | A/R | A/R | A/R | A/R | A/R |
| Facilitar eventos y eliminar impedimentos | I | A/R | C | C | C | C | C |
| Requisitos funcionales | C | I | R | A/R | C | C | I |
| Requisitos no funcionales | C | I | R | C | A/R | C | I |
| Diagrama de casos de uso | C | R | C | C | C | A/R | R |
| Especificación de casos de uso | C | R | C | R | R | A/R | R |
| Diagrama de clases y modelo de datos | I | I | R | A/R | C | C | I |
| Arquitectura y decisiones (ADR) | I | I | A/R | R | C | C | C |
| Revisión de calidad y trazabilidad | C | I | R | R | R | A/R | R |
| Repositorio y documentación | C | I | R | R | R | R | A/R |
| Demo ilustrativa (Sprint 3) | A | I | R | R | A/R | C | R |
| Informes Scrum (Review y Retro) | C | A/R | R | R | R | R | R |
| Aceptar artefactos terminados | A/R | I | C | C | C | C | I |

## 2.4 Responsabilidades rotativas

Rotar tareas de facilitación reparte la carga y evita depender de una sola persona. Asigna un nombre por sprint.

| **Responsabilidad** | **S1** | **S2** | **S3** | **S4** | **S5** | **S6** |
|---|---|---|---|---|---|---|
| Facilitador/a de la retrospectiva | Yousef | Yousef | *[Nombre]* | *[Nombre]* | *[Nombre]* | *[Nombre]* |
| Encargado/a del acta (Review y Retro) | Yousef | Yousef | *[Nombre]* | *[Nombre]* | *[Nombre]* | *[Nombre]* |
| Guardián/a del tablero | Yousef | Yousef | *[Nombre]* | *[Nombre]* | *[Nombre]* | *[Nombre]* |
| Responsable de la exposición | Decidido Por el profesor | Decidido por el profesor | *[Nombre]* | *[Nombre]* | *[Nombre]* | *[Nombre]* |

# 3. Reparto de trabajo por miembro


Responsabilidades comunes a todos los Developers: participar en el refinamiento y las estimaciones; revisar los artefactos de otros miembros (pull request); cumplir la Definición de Terminado; asistir a todos los eventos; señalar impedimentos en el Daily; usar las plantillas y la nomenclatura comunes.

## 3.1 Miembro 1 · Product Owner (PO) · Nestor

| **Nombre** | Nestor Rodriguez |
|---|---|
| **Especialización** | Visión, backlog y validación con el cliente |
| **Dedicación al proyecto** | 16 % del tiempo semanal |
| **Responsabilidades permanentes** | Define y comunica el Product Goal y la visión del producto.<br>Crea, ordena y mantiene el Product Backlog (única persona que fija prioridades).<br>Se reúne con cada subgrupo para fusionar las versiones temporales en el archivo final de la tarea.<br>Redacta el informe y la documentación de cada tarea a partir de su archivo final.<br>Valida cada requisito y caso de uso frente a la entrevista con el cliente. |
| **Trabajo típico en cada sprint** | Antes del sprint: refina y prioriza los elementos del backlog.<br>Sprint Planning: propone el objetivo del sprint y resuelve dudas.<br>Durante el sprint: responde dudas en menos de 24 h y valida artefactos según se completan.<br>Sprint Review: presenta el objetivo, recoge feedback y actualiza el backlog. |
| **Artefactos y entregables que posee** | Product Backlog priorizado, Product Goal, informes y documentación de cada tarea, preguntas abiertas al cliente. |
| **Suplente / backup** | Yousef (solo decisiones menores; los cambios de prioridad esperan al PO) |
| **Formación o apoyo necesario** | Repaso de la entrevista y de técnicas de priorización (MoSCoW). |

## 3.2 Miembro 2 · Scrum Master (SM) · Yousef

| **Nombre** | Yousef AL Haidar |
|---|---|
| **Especialización** | Proceso Scrum, coordinación e informes |
| **Dedicación al proyecto** | 16 % del tiempo semanal |
| **Responsabilidades permanentes** | Facilita los eventos Scrum y vela por que respeten su duración máxima (timebox).<br>Detecta y elimina impedimentos; resuelve dudas y peticiones de ayuda (por privado o etiquetado en el grupo).<br>Recoge por privado las sugerencias de cada miembro para la Sprint Review.<br>Redacta los informes Scrum y plantea la presentación de cada entrega.<br>Mantiene una copia de seguridad local del repositorio. |
| **Trabajo típico en cada sprint** | Convoca y facilita Planning, Daily, Review y Retrospectiva.<br>Actualiza el registro de impedimentos y el burndown.<br>Comprueba que se cumplen la Definición de Terminado y los acuerdos de equipo.<br>Hace seguimiento de las acciones de mejora acordadas en la retrospectiva. |
| **Artefactos y entregables que posee** | Calendario de eventos, registro de impedimentos, burndown y velocidad, informes Scrum. |
| **Suplente / backup** | Miguel (facilitación de eventos) |
| **Formación o apoyo necesario** | Guía Scrum 2020; PlantUML. |

## 3.3 Miembro 3 · Developer · Irene · Arquitectura y estándares

| **Nombre** | Irene |
|---|---|
| **Especialización** | Arquitectura, estándares de modelado y coherencia técnica |
| **Dedicación al proyecto** | 16 % del tiempo semanal |
| **Responsabilidades permanentes** | Propone y documenta las decisiones de arquitectura con el equipo (sin jerarquía sobre el resto).<br>Define los estándares de modelado: notación UML, plantillas de requisitos y casos de uso, nomenclatura e identificadores.<br>Revisa que los artefactos sean coherentes entre sí (actores, clases, requisitos).<br>Colabora en la redacción de los requisitos funcionales y no funcionales. |
| **Trabajo típico en cada sprint** | Inicio del sprint: prepara las plantillas y los criterios de modelado de los artefactos del sprint.<br>Mantiene el registro de decisiones de arquitectura (ADR).<br>Revisa los diagramas más complejos antes de su aprobación. |
| **Artefactos y entregables que posee** | Documento de arquitectura, ADR, guía de estilo de modelado y plantillas. |
| **Suplente / backup** | Adrian |
| **Formación o apoyo necesario** | UML 2.5 (clases y componentes); arquitectura por capas. |

## 3.4 Miembro 4 · Developer · Adrian · Modelo de dominio

| **Nombre** | Adrian |
|---|---|
| **Especialización** | Modelo de dominio: requisitos funcionales y diagrama de clases |
| **Dedicación al proyecto** | 16 % del tiempo semanal |
| **Responsabilidades permanentes** | Redacta los requisitos funcionales a partir de la entrevista.<br>Diseña el modelo de dominio: entidades, atributos, relaciones y multiplicidades.<br>Mantiene el diagrama de clases y el modelo de datos coherentes con los casos de uso.<br>Documenta las reglas de negocio (plazos de pago, estados de factura, recurrencia). |
| **Trabajo típico en cada sprint** | Identifica en la entrevista los requisitos y reglas del área del sprint.<br>Actualiza el diagrama de clases cuando cambian requisitos o casos de uso.<br>Revisa con Miguel la trazabilidad entre requisitos y casos de uso. |
| **Artefactos y entregables que posee** | Catálogo de requisitos funcionales, reglas de negocio, diagrama de clases y modelo de datos. |
| **Suplente / backup** | Irene |
| **Formación o apoyo necesario** | Diagramas de clases UML; modelado de datos relacional. |

## 3.5 Miembro 5 · Developer · Javier · Usabilidad y requisitos no funcionales

| **Nombre** | Javier |
|---|---|
| **Especialización** | Usabilidad: requisitos no funcionales, prototipos y demo |
| **Dedicación al proyecto** | 16 % del tiempo semanal |
| **Responsabilidades permanentes** | Redacta los requisitos no funcionales, siempre medibles (usabilidad, seguridad, rendimiento, portabilidad).<br>Diseña los prototipos (wireframes) de las pantallas principales.<br>Lidera la demo ilustrativa del Sprint 3.<br>Cuida que el diseño respete la estética de Turbine y funcione en móvil, tablet y PC. |
| **Trabajo típico en cada sprint** | Antes del sprint: bocetos de las pantallas de los casos de uso candidatos.<br>Define el criterio de verificación de cada requisito no funcional.<br>Prepara el material visual para la Sprint Review. |
| **Artefactos y entregables que posee** | Catálogo de requisitos no funcionales, prototipos y demo del Sprint 3. |
| **Suplente / backup** | Gonzalo |
| **Formación o apoyo necesario** | Herramienta de prototipado (Figma o similar); calidad del software según ISO/IEC 25010. |

## 3.6 Miembro 6 · Developer · Miguel · Casos de uso y calidad

| **Nombre** | Miguel |
|---|---|
| **Especialización** | Casos de uso y calidad: revisión y trazabilidad |
| **Dedicación al proyecto** | 16 % del tiempo semanal |
| **Responsabilidades permanentes** | Elabora el diagrama de casos de uso y sus especificaciones junto a Gonzalo y Yousef.<br>Mantiene la matriz de trazabilidad entre requisitos y casos de uso.<br>Revisa los artefactos antes de su aprobación y registra las observaciones.<br>Verifica que cada elemento cumple la Definición de Terminado. |
| **Trabajo típico en cada sprint** | Inicio del sprint: define los criterios de revisión de los artefactos comprometidos.<br>Revisa cada artefacto según se completa, no al final del sprint.<br>Elabora un breve informe de calidad del sprint (observaciones abiertas y resueltas). |
| **Artefactos y entregables que posee** | Diagrama y especificaciones de casos de uso, matriz de trazabilidad, registro de observaciones. |
| **Suplente / backup** | Gonzalo |
| **Formación o apoyo necesario** | Especificación de casos de uso (flujo principal y alternativos); PlantUML. |

## 3.7 Miembro 7 · Developer · Gonzalo · Repositorio y documentación

| **Nombre** | Gonzalo |
|---|---|
| **Especialización** | Casos de uso, repositorio y documentación |
| **Dedicación al proyecto** | 16 % del tiempo semanal |
| **Responsabilidades permanentes** | Colabora en el diagrama y las especificaciones de casos de uso.<br>Administra el repositorio GitHub: ramas, pull requests y estructura de la carpeta docs/.<br>Mantiene los diagramas PlantUML compilando sin errores y sus imágenes exportadas.<br>Coordina la maquetación final de la documentación entregable. |
| **Trabajo típico en cada sprint** | Integra en main los artefactos aprobados y exporta los diagramas.<br>Mantiene el README y el índice de la documentación.<br>Prepara la versión de los documentos que se entrega al cierre del sprint. |
| **Artefactos y entregables que posee** | Repositorio, README, carpeta docs/, diagramas exportados y documento final. |
| **Suplente / backup** | Yousef |
| **Formación o apoyo necesario** | Git y GitHub (ramas y pull requests); PlantUML. |

# 4. Acuerdos de equipo (Working Agreement)


| **Área** | **Acuerdo del equipo (ejemplos editables)** |
|---|---|
| **Propósito del equipo** | *[Un diseño teorico de la aplicacion con una demo final, la prioridad son el diseño, documentacion, la arquitectura, y organizacion del equipo. Ademas, todo mientras se respetan las buenas practicas de la Ingeniería/Arquitectura del Software]* |
| **Horarios y disponibilidad** | *[Horario de reunion total: Fines de semana 10:00-12:00. Horarios de reunion parcial: 10:00 12:00 o 16:00-19:00.]*  |
| **Herramientas** | *[Repositorio: GitHub web con copia local · Chat principal: WhatsApp · Llamadas y dudas largas: Discord (creado por Javier) · Diagramas: PlantUML]* |
| **Comunicación** | *[Canal principal: grupo de WhatsApp. Cada subida a GitHub se avisa allí]*<br>*[Discord para llamadas y dudas largas; no sustituye al grupo de WhatsApp]*<br>*[Dudas, ayuda o problemas: privado al Scrum Master o etiquetarle en el grupo]* |
| **Reuniones** | *[Check-in de máx. 5 minutos a mitad de sprint: avances, obstáculos, malentendidos y quién necesita ayuda]*<br>*[Las reuniones (check-in, preparación de la exposición) se convocan con fecha y hora]*<br>*[Los temas largos se tratan después, por Discord]* |
| **Toma de decisiones** | *[Decisiones de prioridad: las toma el Scrum Master junto al subgrupo afectado]*<br>*[Decisiones sobre cómo modelar: las toman los Developers por consenso; si no hay acuerdo, votación y se acepta la decisión ≥3]*<br>*[Las decisiones relevantes se anotan en el registro de decisiones]* |
| **Repositorio y control de versiones** | *[Solo se suben archivos .md con el formato Tarea_Nombre_vN.md (p. ej., CasosdeUso_Miguel_v1.md)]*<br>*[Las versiones temporales van en su carpeta; los archivos finales, en la de la tarea]*<br>*[Cada subida lleva un commit descriptivo y se avisa en WhatsApp por si alguien va a hacer push]*<br>*[Quien trabaje en local hace siempre git pull antes de empezar y vigila las subidas de los demás]* |
| **Límites de trabajo en curso (WIP)** | *[Máx. 2 tareas en "En curso" por persona; terminar antes de empezar]* |
| **Ayuda y colaboración** | *[Si llevas más de 1 h bloqueado/a, pides ayuda; los artefactos complejos se trabajan en parejas]* |
| **Gestión de conflictos** | *[1) Hablarlo directamente entre las personas implicadas; 2) llevarlo al Scrum Master; 3) tratarlo en la retrospectiva]*<br>*[Se critica el trabajo, nunca a la persona]* |
| **Cultura y reconocimiento** | *[Celebramos los objetivos de sprint cumplidos; reconocemos la ayuda entre compañeros]* |
| **Incumplimiento de acuerdos** | *[Se recuerda en el Daily o la Retro; sin culpables, con acciones de mejora]* |

## 4.1 Flujo de trabajo de los artefactos

Desde el Sprint 2 cada tarea (requisitos, casos de uso…) sigue estos pasos:

- **Versión temporal:** cada miembro trabaja en su propia versión de su parte, en un archivo Tarea_Nombre_vN.md (p. ej., Requisitos_Irene_v1.md).
- **Iteración:** cada mejora se sube como una versión nueva (v2, v3…) con un commit descriptivo y se avisa en WhatsApp.
- **Fusión:** los responsables de la tarea se reúnen con el PO (Nestor) y crean un único archivo final con lo que consideran apto de cada versión temporal.
- **Entrega:** el subgrupo sube el archivo final de la tarea; Nestor redacta el informe y la documentación correspondientes.
- **Seguimiento:** en el check-in de mitad de sprint se revisa cómo va cada tarea y quién necesita ayuda.

**Firmado / aceptado por el equipo el** *[14/10/2026]*:

| Yousef | Nestor | Irene | Miguel |
|---|---|---|---|
| Adrian | Javier | Gonzalo |   |

# 5. Definiciones: Ready, Done, historias y estimación

## 5.1 Definición de Terminado (Definition of Done)

Es el compromiso de calidad del incremento y forma parte de la Guía Scrum: un artefacto solo cuenta como terminado si cumple todos los puntos. Si no, vuelve al Product Backlog.

| **✓** | **Criterio de terminado (ejemplos editables)** |
|---|---|
| ☐ | El artefacto cumple los criterios de aceptación de su elemento del backlog. |
| ☐ | Usa la plantilla y la nomenclatura comunes (identificadores, actores, clases). |
| ☐ | Cada requisito y caso de uso indica su fuente en la entrevista o en las notas. |
| ☐ | Los diagramas compilan en PlantUML sin errores y su imagen está exportada. |
| ☐ | Es coherente con el resto de artefactos (requisitos ↔ casos de uso ↔ clases). |
| ☐ | Consensuado por el subgrupo y el PO a partir de las versiones temporales. |
| ☐ | Sin observaciones de revisión abiertas. |
| ☐ | Archivo final subido a GitHub con el nombre acordado y anunciado en el grupo. |
| ☐ | Aceptado por el Product Owner. |
| ☐ | *[Criterio propio del proyecto]* |

## 5.2 Definición de Preparado (Definition of Ready)

Ojo: la Definition of Ready no forma parte de la Guía Scrum oficial; es una práctica opcional muy extendida. Sirve para que las historias lleguen al Sprint Planning listas para trabajarse, pero no debe convertirse en una barrera burocrática.

| **✓** | **Criterio de preparado (ejemplos editables)** |
|---|---|
| ☐ | Está redactado como elemento del backlog con un entregable claro. |
| ☐ | Tiene criterios de aceptación verificables. |
| ☐ | Es lo bastante pequeño para completarse en un sprint (idealmente ≤ 8 puntos). |
| ☐ | Ha sido estimado por los Developers. |
| ☐ | Sus dependencias están identificadas (p. ej., las clases dependen de los casos de uso). |
| ☐ | Se ha localizado la información de partida en la entrevista o las notas. |
| ☐ | El PO ha confirmado su prioridad. |

## 5.3 Formato de historia de usuario

| **Formato** | Como *[tipo de usuario]*, quiero *[acción o funcionalidad]* para *[beneficio o motivo]*. |
|---|---|
| **Criterios de aceptación** | Dado *[contexto inicial]*, cuando *[acción]*, entonces *[resultado esperado]*. |
| **Ejemplo** | Como usuario de administración, quiero crear una factura puntual en pocos clics para no hacerla a mano. Dado un cliente dado de alta, cuando completo los conceptos y confirmo, entonces se genera la factura con número correlativo y se envía por email al cliente. |
| **Calidad INVEST** | Independiente · Negociable · Valiosa · Estimable · Pequeña (Small) · Verificable (Testable). |

## 5.4 Estimación

Se estima en puntos de historia (esfuerzo relativo, no horas), usando planning poker con la escala de Fibonacci. Los Developers son quienes estiman; el PO responde dudas.

| **Puntos** | **Significado** | **Ejemplo de referencia** |
|---|---|---|
| 1 | Trivial: cambio pequeño y bien conocido. | *[Corregir una errata o un identificador]* |
| 2 | Pequeño y claro. | *[Añadir un requisito a un catálogo existente]* |
| 3 | Sencillo, con algo de trabajo en una capa. | *[Especificar un caso de uso sencillo]* |
| 5 | Mediano; afecta a varias capas. | *[Diagrama de casos de uso de un área]* |
| 8 | Grande, con incertidumbre; valorar dividirla. | *[Diagrama de clases de dominio completo]* |
| 13+ | Demasiado grande: dividir antes de comprometer. | *[Todo el catálogo de requisitos de una vez]* |

# 6. Product Backlog


| **ID** | **Épica** | **Historia de usuario** | **Prioridad** | **Pts** | **Sprint** | **Estado** |
|---|---|---|---|---|---|---|
| H-001 | E1 | Requisitos funcionales: versiones temporales (S1) y archivo final trazado a la entrevista | Must | 5 | S1–S2 | Hecho |
| H-002 | E1 | Requisitos no funcionales medibles: versiones temporales (S1) y archivo final | Must | 3 | S1–S2 | Hecho |
| H-003 | E2 | Casos de uso: versiones temporales V1 | Must | 3 | S1 | Hecho |
| H-004 | E5 | Exposición del primer parcial (20/10): requisitos, casos de uso, documento de casos de uso, documentación e informe Scrum | Must | 5 | S2 | En curso |
| H-005 | E2 | Documento de casos de uso: especificación de cada caso y requisitos en los que se basa | Must | 5 | S2 | Hecho |
| H-006 | E2 | Diagrama de casos de uso final (archivo único consensuado) | Must | 3 | S2 | Hecho |
| H-007 | E3 | Diagrama de clases de dominio | Must | 8 | S3 | Pendiente |
| H-008 | E4 | Documento de arquitectura y registro de decisiones (ADR) | Should | 5 | S3 | Pendiente |
| H-009 | E3 | Diagramas dinámicos (secuencia o actividad) de los casos críticos | Could | 5 | S4 | Pendiente |
| H-010 | E1 | Matriz de trazabilidad requisitos ↔ casos de uso ↔ clases | Should | 3 | S4 | Pendiente |
| H-011 | E4 | Documento final consolidado | Must | 5 | S5 | Pendiente |
| H-012 | E6 | Prototipos y demo ilustrativa de los flujos principales | Should | 13 | S5 | Pendiente |
| H-013 | E5 | Informe Scrum de cada sprint (Review y Retro) | Must | 2 | Todos | Recurrente |

|   |   |   |   |   |   |   |
|   |   |   |   |   |   |   |

*Estados sugeridos: Pendiente · Ready · En sprint · En curso · Hecho · Recurrente · Descartada.*

# 7. Hoja de ruta y entregables


| **Sprint** | **Fechas** | **Objetivo** | **Hito / entregable** | **Entrega** |
|---|---|---|---|---|
| S0 | 30/09 | Organización inicial: roles, acuerdos y backlog. | Equipo organizado; repositorio creado; backlog inicial. | — |
| S1 | 01/10 – 09/10 | <br>Primeras versiones (V1) de requisitos funcionales y no funcionales y de casos de uso. | Versiones V1 subidas a GitHub. | — |
| S2 | 10/10 – 19/10 | Cerrar requisitos y casos de uso a partir de las versiones temporales y preparar la exposición. | Archivos finales (sáb. 17/10); documento de casos de uso; informe Scrum; presentación. | Primer parcial (20/10) |
| S3 | 21/10 – 03/11 | Modelo de dominio, arquitectura y planteamiento demo ilustrativa. | Diagrama de clases; documento de arquitectura; demo de los flujos principales. | Demo |
| S4 | 04/11 – 17/11 | Diagramas dinámicos y trazabilidad. | Diagramas de secuencia o actividad; matriz de trazabilidad. | — |
| S5 | 18/11 – 01/12 | Revisión de calidad y documentación completa. | Documento consolidado y revisado. | Versión candidata |
| S6 | 02/12 – 15/12 | Cierre: correcciones, entrega final y retrospectiva del proyecto. | Entrega final; informe Scrum final; lecciones aprendidas. | Entrega final |

## Calendario de referencia

Anota festivos, exámenes, entregas de otras asignaturas y vacaciones que afecten a la capacidad. Después, refléjalos en la sección 9.

| **Fecha o periodo** | **Evento** | **Impacto en el equipo** |
|---|---|---|
| 17/10  · [16:00] | Check-in del equipo (máx. 5 min) | Ver avances, obstáculos y quién necesita ayuda |
| 18/10 · [10:00] | Fecha límite de los archivos finales de requisitos y casos de uso | Todo terminado antes de preparar la exposición |
| 18/10 · [11:00] | Reunión de preparación de la exposición | Cierre del Sprint 2: ensayo, Review y Retro |
|19/10 · [15:00] | Ultimo check de la exposicion, repaso y asignacion de roles representativos. | Todo a punto para la presentacion. |
| Mar. 20/10 · [08:45] | Primer parcial: exposición | Entrega fija |


# 8. Eventos Scrum y calendario

Scrum define cinco eventos, con el Sprint como contenedor de todos ellos. Las duraciones de la tabla son los máximos de la Guía Scrum, proporcionales a un sprint de 2 semanas (la guía indica 8 h de Planning, 4 h de Review y 3 h de Retro para un sprint de un mes).

| **Evento** | **Cuándo y duración máx.** | **Participantes** | **Propósito** | **Resultado** |
|---|---|---|---|---|
| **Sprint** | 10 días (S1–S2) · 2 semanas (desde S3) | Todo el equipo | Contenedor de todos los eventos; produce un incremento. | Incremento de artefactos revisados |
| **Sprint Planning** | Primer día · 4 h | PO, SM, Developers | Acordar por qué es valioso el sprint, qué se hará y cómo. | Objetivo del sprint + Sprint Backlog |
| **Check-in (Daily adaptado)** | A mitad de sprint · máx. 5 min | Todo el equipo | Ver avances, obstáculos y malentendidos, y quién necesita ayuda. El resto del seguimiento es asíncrono por WhatsApp. | Plan del día; impedimentos visibles |
| **Refinamiento del backlog** | Continuo · ≤ 10 % de la capacidad (p. ej., 2 h/semana) | PO y Developers | Detallar, dividir y estimar los elementos de sprints futuros. | Elementos "Ready" |
| **Sprint Review** | Último día · 2 h | Equipo y stakeholders | Presentar los artefactos terminados y recoger feedback; ajustar el backlog. | Backlog actualizado |
| **Sprint Retrospective** | Tras la Review · 1,5 h | Todo el equipo | Inspeccionar cómo se ha trabajado y acordar mejoras. | Acciones de mejora |

## 8.1 Agenda de Sprint Planning

Estructura recomendada (basada en la guía de TeamRetro): capacidad → objetivo → alcance → plan → confirmación. Total aproximado de 3 h 20 min, dentro del máximo de 4 h.

| **Paso** | **Actividad** | **Tiempo** | **Contenido** |
|---|---|---|---|
| 1 | Contextualizar | 10 min | El PO resume el resultado del sprint anterior, cambios de prioridad y dirección propuesta. |
| 2 | Confirmar capacidad | 10 min | Ausencias, festivos, reuniones fijas y margen para imprevistos (sección 9). |
| 3 | Acordar el objetivo | 20 min | Redactar en una frase el Sprint Goal antes de elegir trabajo. |
| 4 | Seleccionar trabajo | 60 min | Revisar historias frente al objetivo, su estado Ready y su tamaño; parar al llegar a la capacidad. |
| 5 | Planificar el trabajo | 90 min | Dividir historias en tareas, detectar dependencias y riesgos, validar estimaciones. |
| 6 | Confirmar y comprometer | 10 min | Repetir objetivo y alcance; comprobar la confianza del equipo ("puño de cinco"). |

# 9. Capacidad del equipo

> **Cómo usarlo:** Antes de cada Sprint Planning, estima las horas reales que cada miembro puede dedicar al proyecto, restando ausencias y aplicando un factor de foco. En este proyecto todos elaboran artefactos, también el PO y el SM.

**Fórmula: horas de foco = semanas del sprint × horas por semana × factor de foco. Los Sprints 1 y 2 (10 días) cuentan como 1,5 semanas. Ejemplo: 1,5 × 6 h × 80 % ≈ 7 h por persona.**

## Capacidad base por sprint

| **Nº** | **Miembro** | **Semanas** | **Ausencias (días)** | **Horas / semana** | **Factor foco** | **Horas de foco** |
|---|---|---|---|---|---|---|
| 1 | Nestor · PO | 1,5 | 0 | *[XX] h* | 80 % | *[__] h* |
| 2 | Yousef · SM | 1,5 | 0 | *[XX] h* | 80 % | *[__] h* |
| 3 | Irene · Arquitectura | 1,5 | 0 | *[XX] h* | 80 % | *[__] h* |
| 4 | Adrian · Modelo de dominio | 1,5 | 0 | *[XX] h* | 80 % | *[__] h* |
| 5 | Javier · Usabilidad | 1,5 | 0 | *[XX] h* | 80 % | *[__] h* |
| 6 | Miguel · Casos de uso y calidad | 1,5 | 0 | *[XX] h* | 80 % | *[__] h* |
| 7 | Gonzalo · Repositorio y documentación | 1,5 | 0 | *[XX] h* | 80 % | *[__] h* |
|   | TOTAL equipo |   |   |   |   | *[__] h* |

# 10. Fichas de sprint (Sprint 1 a 6)

> **Cómo usarlo:** Una ficha por sprint. Se rellena en el Sprint Planning (objetivo, capacidad y backlog) y se cierra en la Review (resultado). La columna «Responsable» es quien lidera la tarea; puede haber trabajo en parejas.

**Sprint 0 (preparación):** reunir al equipo, acordar el Working Agreement, redactar la visión y las épicas, crear el backlog inicial, crear el repositorio GitHub y las plantillas, y definir la DoR y la DoD.

## Sprint 1

| **Fechas** | 01/10/2026 – 09/10/2026 |
|---|---|
| **Objetivo del sprint (Sprint Goal)** | Organizar el equipo, crear el repositorio y elaborar las primeras versiones (V1) de los requisitos funcionales y no funcionales y de los casos de uso. |
| **Capacidad y compromiso** | *[Capacidad: ___ h · Puntos comprometidos: ___ · Velocidad de referencia: ___]* |
| **Dependencias y riesgos** | *[Personas ausentes, dependencias externas, riesgos del sprint]* |

### Sprint Backlog

| **ID** | **Historia / tarea** | **Responsable** | **Pts** | **Horas** | **Estado** |
|---|---|---|---|---|---|
| 1 | <br>Comentar la reunión y compartir información.<br>Repartido roles | TODOS |   | 2 | **COMPLETADO** |
| 2 | Crear el repositorio, dividir la tarea y plantear ligeramente los casos de uso.<br>Repasar básicos git | Repositorio -> Yousef<br>Plantear casos de uso-><br>Todos menos Yousef<br>Division de tarea -> Todos. |   | 72 | **COMPLETADO** |
| 3 | Comparar y fijar requisitos Funcionales y No funcionales.<br>Casos de Uso-> Imagen mental y comienzo de la creación.<br>Comienzo de la documentación.<br>Informes generales en cada subgrupo.<br> | Documentación -> Nestor<br>Requisitos F y No F -> Irene, Adrian y Javier<br>CD de uso -> Miguel Y Gonzalo, Yousef |   | 72 | **COMPLETADO (V1)** |
|   |   |   |   |   |   |
|   |   |   |   |   |   |

### Carga por miembro

| **Miembro (rol)** | **Historias / tareas** | **Horas plan** | **Horas disp.** | **Observaciones** |
|---|---|---|---|---|
| Nestor · PO |   |   | *[__]* |   |
| Yousef · SM |   |   | *[__]* |   |
| Irene · Arquitectura |   |   | *[__]* |   |
| Adrian · Modelo de dominio |   |   | *[__]* |   |
| Javier · Usabilidad |   |   | *[__]* |   |
| Miguel · Casos de uso y calidad |   |   | *[__]* |   |
| Gonzalo · Repositorio y documentación |   |   | *[__]* |   |

### Resultado (se rellena en la Review)

| **Objetivo cumplido** | *[Sí / Parcial / No]* |
|---|---|
| **Puntos completados** | *[___ de ___]* · Historias completadas: *[___ de ___]* |
| **Notas** | Las versiones finales de requisitos y casos de uso pasan al Sprint 2. Las sugerencias de cada miembro se recogen por privado para la Sprint Review. |

## Sprint 2

| **Fechas** | 10/10/2026 – 19/10/2026 |
|---|---|
| **Objetivo del sprint (Sprint Goal)** | Cerrar los requisitos funcionales y no funcionales y los casos de uso a partir de las versiones temporales, y preparar la exposición del primer parcial (20/10). |
| **Capacidad y compromiso** | *[Capacidad: ___ h · Puntos comprometidos: ___ · Velocidad de referencia: ___]* |
| **Dependencias y riesgos** | Archivos finales listos el sábado 17/10; reunión de preparación el lunes 19/10. Riesgo: conflictos al subir a GitHub a la vez (avisar en WhatsApp antes de cada push). |

### Sprint Backlog

| **ID** | **Historia / tarea** | **Responsable** | **Pts** | **Horas** | **Estado** |
|---|---|---|---|---|---|
| 1 | H-001 / H-002 · Requisitos F y NF: versiones temporales (Requisitos_Nombre_vN.md) y archivo final consensuado con el PO | Irene, Adrian, Javier | 8 |   | En curso |
| 2 | H-005 / H-006 · Casos de uso: versiones temporales, diagrama final y documento de casos de uso con los requisitos en que se basa cada caso | Miguel, Gonzalo, Yousef | 8 |   | En curso |
| 3 | Informe y documentación de cada tarea a partir de los archivos finales | Nestor | 3 |   | Pendiente |
| 4 | H-013 · Informe Scrum del Sprint 1 y Sprint Review (sugerencias por privado) | Yousef | 2 |   | En curso |
| 5 | H-004 · Presentación del primer parcial | Yousef (planteamiento) · Todos | 5 |   | En curso |
| 6 | Check-in (jue. 15 o vie. 16) y reunión de preparación (lun. 19/10) | Todos | — |   | Pendiente |

### Carga por miembro

| **Miembro (rol)** | **Historias / tareas** | **Horas plan** | **Horas disp.** | **Observaciones** |
|---|---|---|---|---|
| Nestor · PO |   |   | *[__]* |   |
| Yousef · SM |   |   | *[__]* |   |
| Irene · Arquitectura |   |   | *[__]* |   |
| Adrian · Modelo de dominio |   |   | *[__]* |   |
| Javier · Usabilidad |   |   | *[__]* |   |
| Miguel · Casos de uso y calidad |   |   | *[__]* |   |
| Gonzalo · Repositorio y documentación |   |   | *[__]* |   |

### Resultado (se rellena en la Review)

| **Objetivo cumplido** | *[Sí / Parcial / No]* |
|---|---|
| **Puntos completados** | *[___ de ___]* · Historias completadas: *[___ de ___]* |
| **Notas** | *[Qué se movió al siguiente sprint y por qué]* |

## Sprint 3

| **Fechas** | 21/10/2026 – 03/11/2026 |
|---|---|
| **Objetivo del sprint (Sprint Goal)** | Modelar el dominio (diagrama de clases), documentar la arquitectura y presentar la demo ilustrativa. |
| **Capacidad y compromiso** | *[Capacidad: ___ h · Puntos comprometidos: ___ · Velocidad de referencia: ___]* |
| **Dependencias y riesgos** | *[Personas ausentes, dependencias externas, riesgos del sprint]* |

### Sprint Backlog

| **ID** | **Historia / tarea** | **Responsable** | **Pts** | **Horas** | **Estado** |
|---|---|---|---|---|---|
|   |   |   |   |   |   |
|   |   |   |   |   |   |

### Carga por miembro

| **Miembro (rol)** | **Historias / tareas** | **Horas plan** | **Horas disp.** | **Observaciones** |
|---|---|---|---|---|
| Nestor · PO |   |   | *[__]* |   |
| Yousef · SM |   |   | *[__]* |   |
| Irene · Arquitectura |   |   | *[__]* |   |
| Adrian · Modelo de dominio |   |   | *[__]* |   |
| Javier · Usabilidad |   |   | *[__]* |   |
| Miguel · Casos de uso y calidad |   |   | *[__]* |   |
| Gonzalo · Repositorio y documentación |   |   | *[__]* |   |

### Resultado (se rellena en la Review)

| **Objetivo cumplido** | *[Sí / Parcial / No]* |
|---|---|
| **Puntos completados** | *[___ de ___]* · Historias completadas: *[___ de ___]* |
| **Notas** | *[Qué se movió al siguiente sprint y por qué]* |

## Sprint 4

| **Fechas** | 04/11/2026 – 17/11/2026 |
|---|---|
| **Objetivo del sprint (Sprint Goal)** | Modelar los casos críticos con diagramas dinámicos y completar la trazabilidad. |
| **Capacidad y compromiso** | *[Capacidad: ___ h · Puntos comprometidos: ___ · Velocidad de referencia: ___]* |
| **Dependencias y riesgos** | *[Personas ausentes, dependencias externas, riesgos del sprint]* |

### Sprint Backlog

| **ID** | **Historia / tarea** | **Responsable** | **Pts** | **Horas** | **Estado** |
|---|---|---|---|---|---|
|   |   |   |   |   |   |
|   |   |   |   |   |   |

### Carga por miembro

| **Miembro (rol)** | **Historias / tareas** | **Horas plan** | **Horas disp.** | **Observaciones** |
|---|---|---|---|---|
| Nestor · PO |   |   | *[__]* |   |
| Yousef · SM |   |   | *[__]* |   |
| Irene · Arquitectura |   |   | *[__]* |   |
| Adrian · Modelo de dominio |   |   | *[__]* |   |
| Javier · Usabilidad |   |   | *[__]* |   |
| Miguel · Casos de uso y calidad |   |   | *[__]* |   |
| Gonzalo · Repositorio y documentación |   |   | *[__]* |   |

### Resultado (se rellena en la Review)

| **Objetivo cumplido** | *[Sí / Parcial / No]* |
|---|---|
| **Puntos completados** | *[___ de ___]* · Historias completadas: *[___ de ___]* |
| **Notas** | *[Qué se movió al siguiente sprint y por qué]* |

## Sprint 5

| **Fechas** | 18/11/2026 – 01/12/2026 |
|---|---|
| **Objetivo del sprint (Sprint Goal)** | Revisar la calidad y la coherencia de todos los artefactos y consolidar la documentación. |
| **Capacidad y compromiso** | *[Capacidad: ___ h · Puntos comprometidos: ___ · Velocidad de referencia: ___]* |
| **Dependencias y riesgos** | *[Personas ausentes, dependencias externas, riesgos del sprint]* |

### Sprint Backlog

| **ID** | **Historia / tarea** | **Responsable** | **Pts** | **Horas** | **Estado** |
|---|---|---|---|---|---|
|   |   |   |   |   |   |
|   |   |   |   |   |   |

### Carga por miembro

| **Miembro (rol)** | **Historias / tareas** | **Horas plan** | **Horas disp.** | **Observaciones** |
|---|---|---|---|---|
| Nestor · PO |   |   | *[__]* |   |
| Yousef · SM |   |   | *[__]* |   |
| Irene · Arquitectura |   |   | *[__]* |   |
| Adrian · Modelo de dominio |   |   | *[__]* |   |
| Javier · Usabilidad |   |   | *[__]* |   |
| Miguel · Casos de uso y calidad |   |   | *[__]* |   |
| Gonzalo · Repositorio y documentación |   |   | *[__]* |   |

### Resultado (se rellena en la Review)

| **Objetivo cumplido** | *[Sí / Parcial / No]* |
|---|---|
| **Puntos completados** | *[___ de ___]* · Historias completadas: *[___ de ___]* |
| **Notas** | *[Qué se movió al siguiente sprint y por qué]* |

## Sprint 6

| **Fechas** | 02/12/2026 – 15/12/2026 |
|---|---|
| **Objetivo del sprint (Sprint Goal)** | Cierre: correcciones, entrega final y retrospectiva del proyecto. |
| **Capacidad y compromiso** | *[Capacidad: ___ h · Puntos comprometidos: ___ · Velocidad de referencia: ___]* |
| **Dependencias y riesgos** | *[Personas ausentes, dependencias externas, riesgos del sprint]* |

### Sprint Backlog

| **ID** | **Historia / tarea** | **Responsable** | **Pts** | **Horas** | **Estado** |
|---|---|---|---|---|---|
|   |   |   |   |   |   |
|   |   |   |   |   |   |

### Carga por miembro

| **Miembro (rol)** | **Historias / tareas** | **Horas plan** | **Horas disp.** | **Observaciones** |
|---|---|---|---|---|
| Nestor · PO |   |   | *[__]* |   |
| Yousef · SM |   |   | *[__]* |   |
| Irene · Arquitectura |   |   | *[__]* |   |
| Adrian · Modelo de dominio |   |   | *[__]* |   |
| Javier · Usabilidad |   |   | *[__]* |   |
| Miguel · Casos de uso y calidad |   |   | *[__]* |   |
| Gonzalo · Repositorio y documentación |   |   | *[__]* |   |

### Resultado (se rellena en la Review)

| **Objetivo cumplido** | *[Sí / Parcial / No]* |
|---|---|
| **Puntos completados** | *[___ de ___]* · Historias completadas: *[___ de ___]* |
| **Notas** | *[Qué se movió al siguiente sprint y por qué]* |

# 11. Seguimiento: tablero, burndown y velocidad

## 11.1 Tablero del sprint

Columnas del tablero con su límite de trabajo en curso (WIP). Un tablero visible y actualizado es la principal herramienta de transparencia.

| **Backlog del sprint** | **En curso** | **En revisión** | **Validación PO** | **Hecho** | **Bloqueado** |
|---|---|---|---|---|---|
| Límite WIP: — | Límite WIP: *[10]* | Límite WIP: *[4]* | Límite WIP: *[4]* | Límite WIP: — | Límite WIP: *[0-2]* |
| Elementos comprometidos y sin empezar | Alguien lo está elaborando | Pull request abierta | El PO lo valida | Cumple la DoD | Con impedimento registrado |

## 11.2 Burndown del sprint

Puntos restantes por día. La línea ideal baja de forma lineal desde los puntos comprometidos hasta 0 al día 10.

| **Sprint __** | **D1** | **D2** | **D3** | **D4** | **D5** | **D6** | **D7** | **D8** | **D9** | **D10** |
|---|---|---|---|---|---|---|---|---|---|---|
| **Ideal** |   |   |   |   |   |   |   |   |   |   |
| **Real** |   |   |   |   |   |   |   |   |   |   |

## 11.3 Velocidad del equipo

| **Sprint** | **Pts comprometidos** | **Pts completados** | **Capacidad (h)** | **% objetivo cumplido** | **Notas** |
|---|---|---|---|---|---|
| S1 |   |   |   |   |   |
| S2 |   |   |   |   |   |
| S3 |   |   |   |   |   |
| S4 |   |   |   |   |   |
| S5 |   |   |   |   |   |
| S6 |   |   |   |   |   |
| Media |   |   |   |   |   |

*Usa la velocidad para calibrar, no para comparar personas ni presionar al equipo: mide una capacidad, no un rendimiento individual.*

## 11.4 Indicadores adicionales

| **Indicador** | **Qué mide** | **Frecuencia** |
|---|---|---|
| Cumplimiento del objetivo del sprint | ¿Se logró el Sprint Goal? (Sí / Parcial / No) | Cada sprint |
| Observaciones de revisión | Observaciones abiertas y resueltas en los artefactos | Cada sprint |
| Tiempo de ciclo | Días desde "En curso" hasta "Hecho" | Cada sprint |
| Trazabilidad | % de requisitos con al menos un caso de uso asociado | Cada sprint |
| Satisfacción del equipo | Voto de 1 a 5 en la retrospectiva | Cada sprint |

# 12. Impedimentos, riesgos y decisiones

## 12.1 Registro de impedimentos

El Scrum Master lo actualiza a diario. Un impedimento es cualquier cosa que frena al equipo y que este no puede resolver solo.

| **ID** | **Fecha** | **Descripción** | **Afectado/a** | **Responsable** | **Estado** | **Resuelto** |
|---|---|---|---|---|---|---|
| I-01 | *[dd/mm]* | *[Ejemplo: falta información del cliente sobre la tabla de precios por tramo]* | *[Requisitos]* | *[PO]* | *[Abierto]* |   |
|   |   |   |   |   |   |   |
|   |   |   |   |   |   |   |

## 12.2 Registro de riesgos

Puntuación = probabilidad (1–5) × impacto (1–5). Verde 1–7: vigilar en revisiones semanales · Ámbar 8–14: mitigación activa · Rojo 15–25: escalar y vigilar a diario.

| **ID** | **Riesgo** | **Categoría** | **P** | **I** | **Punt.** | **Resp.** | **Mitigación** | **Estado** |
|---|---|---|---|---|---|---|---|---|
| R-01 | *[Ausencias por exámenes o enfermedad reducen la capacidad]* | *[Recursos]* | 4 | 3 | 12 | *[SM]* | *[Registrar disponibilidad en cada Planning; margen del 20 %]* | *[Abierto]* |
| R-02 | *[Estimaciones demasiado optimistas al principio]* | *[Planificación]* | 4 | 3 | 12 | *[PO]* | *[Calibrar en S1–S2; dividir historias grandes]* | *[Abierto]* |
| R-03 | *[Conocimiento concentrado en una sola persona (factor bus)]* | *[Equipo]* | 3 | 4 | 12 | *[Irene]* | *[Trabajo en parejas y suplentes definidos]* | *[Abierto]* |
| R-04 | *[Artefactos de distintos subgrupos que no encajan entre sí]* | *[Calidad]* | 3 | 4 | 12 | *[Miguel]* | *[Plantillas y nomenclatura comunes; revisión cruzada antes de integrar]* | *[Abierto]* |
| R-05 | *[Requisitos ambiguos o contradictorios en la entrevista]* | *[Requisitos]* | 3 | 3 | 9 | *[PO]* | *[Lista de preguntas abiertas al cliente; registrar los supuestos]* | *[Abierto]* |
|   |   |   |   |   |   |   |   |   |
|   |   |   |   |   |   |   |   |   |

## 12.3 Registro de decisiones

| **ID** | **Fecha** | **Decisión** | **Contexto y alternativas** | **Decidida por** |
|---|---|---|---|---|
| D-01 | *[dd/mm]* | Stack de referencia (teórico): Spring Boot + React + PostgreSQL | Alternativas: Django + HTMX, React + NestJS, React + Go. Se elige por la experiencia del equipo en Java y SQL. | Equipo |
| D-02 | *[dd/mm]* | Diagramas UML en PlantUML dentro de docs/ | Alternativas: draw.io, StarUML. PlantUML se versiona en Git y se revisa por pull request. | Equipo |
| D-03 | *[dd/mm]* | Una rama por persona (work<nombre>) y pull request obligatoria a main | Alternativa: ramas por tarea. | Equipo |
|   |   |   |   |   |

# 13. Sprint Review y Retrospectiva

> **Cómo usarlo:** Copia estas dos plantillas al final de cada sprint. Son eventos distintos: la Review mira el producto (con stakeholders); la Retrospectiva mira el proceso (solo el equipo).

## 13.1 Agenda de la Sprint Review (2 h)

| **Tiempo** | **Actividad** |
|---|---|
| 10 min | El PO recuerda el objetivo del sprint y qué historias estaban comprometidas. |
| 60 min | Presentación de los artefactos terminados por quienes los han elaborado: diagramas y documentos reales, no resúmenes. |
| 20 min | Feedback de los stakeholders; qué se ha aprendido. |
| 20 min | El PO revisa el backlog y la hoja de ruta; se comenta la capacidad del próximo sprint. |
| 10 min | Cierre: acuerdos, próximos pasos y fecha de la siguiente Review. |

## 13.2 Acta de la Sprint Review

| **Sprint / fecha** | *[Sprint _ · dd/mm/aaaa]* |
|---|---|
| **Asistentes** | *[Nombres del equipo y stakeholders]* |
| **Objetivo del sprint** | *[Texto]* → Cumplido: *[Sí / Parcial / No]* |
| **Historias completadas** | *[IDs y títulos]* |
| **Historias no completadas** | *[IDs y motivo]* |
| **Feedback recibido** | *[Comentarios de los stakeholders]* |
| **Cambios al backlog** | *[Historias nuevas, cambios de prioridad, descartes]* |

## 13.3 Retrospectiva (1,5 h)

Formato base: Qué fue bien · Qué no fue bien · Qué probaremos. Antes de la Review, cada miembro envía al Scrum Master por privado sus sugerencias, ideas o quejas sobre el sprint. Secuencia: revisar acciones anteriores (10 min) → recopilar datos (20 min) → generar ideas (25 min) → elegir 1–3 acciones concretas (25 min) → cerrar (10 min).

| **Qué fue bien** | **Qué no fue bien** | **Qué probaremos** |
|---|---|---|
| *[ ]* | *[ ]* | *[ ]* |
|   |   |   |
|   |   |   |

## 13.4 Acciones de mejora

Una retrospectiva sin acciones no cambia nada. Cada acción tiene responsable y fecha, y se revisa al inicio de la siguiente retro. Idealmente, se incorpora la acción más importante al Sprint Backlog.

| **ID** | **Sprint** | **Acción** | **Responsable** | **Fecha límite** | **Estado** |
|---|---|---|---|---|---|
| A-01 | *[S1]* | *[Ejemplo: revisar las PR en menos de 24 h]* | *[Nombre]* | *[dd/mm]* | *[Pendiente]* |
|   |   |   |   |   |   |
|   |   |   |   |   |   |

**Voto de satisfacción del equipo (1–5):** *[ ]*

# 14. Partes interesadas y comunicación

| **Persona / grupo** | **Rol / interés** | **Influencia** | **Interés** | **Cómo y cuándo comunicar** | **Contacto** |
|---|---|---|---|---|---|
| *Alejandro Ruiz (Turbine)* | *Cliente: aporta los requisitos y resuelve dudas* | *[Alta]* | *[Alto]* | *[Preguntas por escrito a través del profesorado]* | *[ ]* |
| *Profesorado de la asignatura* | *Evalúa los entregables y las exposiciones* | *[Alta]* | *[Alto]* | *[Sprint Review y entregas]* | *[correo]* |
|   |   |   |   |   |   |
|   |   |   |   |   |   |

## Plan de comunicación interna

| **Comunicación** | **Frecuencia** | **Canal** | **Responsable** |
|---|---|---|---|
| Check-in del equipo | A mitad de sprint (máx. 5 min) | *[Discord o WhatsApp]* | *[Facilita el SM]* |
| Aviso de cada subida a GitHub | Cada commit | *[WhatsApp]* | *[Quien sube]* |
| Informe y documentación de cada tarea | Al cerrar cada tarea | *[GitHub]* | *[Nestor (PO)]* |
| Dudas, bloqueos y peticiones de ayuda | Cuando surjan | *[Privado al SM o etiquetarle en WhatsApp]* | *[Todos]* |
| Llamadas y dudas largas | Cuando haga falta | *[Discord]* | *[Todos]* |
| Sugerencias para la Sprint Review | Al cierre de cada sprint | *[Privado al SM]* | *[Todos]* |

# Anexos

## A. Checklist de inicio (Sprint 0)

☐  Definido el equipo, los roles y las dedicaciones (sección 2).

☐  Acordado y firmado el Working Agreement (sección 4).

☐  Redactadas la visión, los objetivos y las épicas (sección 1).

☐  Creado el Product Backlog inicial con historias priorizadas y estimadas (sección 6).

☐  Definidas la Definición de Preparado y la de Terminado (sección 5).

☐  Repositorio GitHub, tablero y plantillas de artefactos operativos.

☐  Calendario de eventos convocado para todos los sprints (sección 8).

☐  Calculada la capacidad del Sprint 1 (sección 9).

## B. Checklist de cierre de sprint

☐  Todos los elementos "Hecho" cumplen la Definición de Terminado.

☐  Los archivos finales de cada tarea están subidos a GitHub y anunciados en el grupo.

☐  Sprint Review realizada y acta rellenada.

☐  Retrospectiva realizada y acciones asignadas.

☐  Velocidad y burndown actualizados (sección 11).

☐  Backlog reordenado y estimado para el siguiente sprint.

☐  Registro de riesgos e impedimentos revisado.

☐  Sprint Planning del siguiente sprint convocado.

## C. Glosario

| **Término** | **Definición** |
|---|---|
| Increment | Conjunto de artefactos terminados en el sprint; suma de todos los anteriores. |
| Product Backlog | Lista ordenada de todo lo que necesita el producto; la gestiona el PO. |
| Product Goal | Estado futuro del producto que sirve de objetivo a largo plazo. |
| Sprint Backlog | Objetivo del sprint + historias seleccionadas + plan para entregarlas. |
| Sprint Goal | Objetivo único del sprint; da coherencia y flexibilidad al trabajo. |
| Definition of Done | Estándar de calidad que debe cumplir una historia para considerarse terminada. |
| Definition of Ready | Práctica opcional: criterios para que una historia pueda entrar en un sprint. |
| Velocidad | Puntos de historia completados por sprint; referencia para planificar. |
| Capacidad | Horas realmente disponibles del equipo en un sprint. |
| Timebox | Duración máxima fija de un evento. |
| Impedimento | Obstáculo que frena al equipo y que no puede resolver por sí mismo. |
| Artefacto | Producto de trabajo del análisis y diseño: requisito, caso de uso, diagrama o documento. |
| WIP | Work in Progress: trabajo en curso; limitarlo mejora el flujo. |
| MoSCoW | Método de priorización: Must, Should, Could, Won't. |
| Requisito funcional | Lo que el sistema debe hacer (p. ej., «el sistema deberá emitir facturas recurrentes el día 1»). |
| Requisito no funcional | Cómo debe hacerlo: calidad medible (usabilidad, seguridad, rendimiento…). |
| Caso de uso | Objetivo que un actor consigue con el sistema, descrito desde fuera. |
| Trazabilidad | Relación verificable entre una fuente, sus requisitos, casos de uso y clases. |

## D. Fuentes consultadas

- [La Guía Scrum 2020 (scrumguides.org)](https://scrumguides.org/scrum-guide.html)
- [Atlassian: Roles y responsabilidades en Scrum](https://www.atlassian.com/agile/scrum/roles)
- [Smartsheet: plantillas Scrum (backlog, sprint, capacidad, roles)](https://www.smartsheet.com/content/scrum-templates)
- [Easy Agile: Team charter y Working Agreement](https://www.easyagile.com/blog/team-charter-working-agreement-social-contract-template-guide)
- [TeamRetro: Agenda de Sprint Planning](https://www.teamretro.com/guides/sprint-planning-guide/sprint-planning-agenda/)
- [Mountain Goat Software: Sprint Planning](https://www.mountaingoatsoftware.com/agile/scrum/meetings/sprint-planning-meeting)
- [Toolkit Café: Agile project charter](https://toolkitcafe.com/blog/agile-project-charter-template-guide)
- [Rocketlane: Plantilla de registro de riesgos](https://www.rocketlane.com/blogs/risk-register-template)
