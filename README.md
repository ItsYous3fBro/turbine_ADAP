# Gestor de facturas Turbine

Aplicación web interna para **Turbine** que permite emitir, enviar y hacer el seguimiento de facturas **puntuales y recurrentes**, y perseguir los cobros de forma automática.

Proyecto desarrollado con metodología **Scrum** en el marco de las asignatura de Análisis y Diseño de Aplicaciones (Universidad de Málaga).

## Qué hacemos


* Desarrollo en papel de manera técnica de todo lo relacionado con la arquitectura e Ingeniería del software:
- Requisitos Funcionales y No funcionales.
- Casos de Uso.
- Diagramas de casos de Uso.
- Informes de Scrum.
- Diagramas de Clases.
- Final: MicroProyecto aparte para poder enseñar una demo para el sprint 3.

## Stack

(Si fuese un proyecto real)
| Capa | Tecnología |
| --- | --- |
| Backend | Java 21 · Spring Boot 3 · Spring Security · Spring Data JPA |
| Frontend | React · TypeScript · Vite |
| Base de datos | PostgreSQL · Flyway |
| Tareas programadas | JobRunr |
| PDF y firma | OpenHTMLtoPDF · DSS (PAdES) |
| Infraestructura | Docker · GitHub Actions |

## Estructura del repositorio

```
aplicacion/   Interfaz web  (Demostración de prueba)
docs/       Requisitos, arquitectura y diagramas UML (PlantUML)

```

## Puesta en marcha (local)

> Requisitos: Git 

```
-Inicializar Git en tu carpeta con git init
-Vincular cuenta con git config --global o similar
-Hacer una pull inicial del proyecto
-Crear tu rama y comenzar a trabajar.
```

La documentación de la API está disponible en `http://localhost:8080/swagger-ui.html`.

## Forma de trabajo

- `main` está protegida: todo cambio entra mediante **pull request** revisada por otra persona.
- Ramas:  `work<tu nombre>` . Ej: workYousef
- Commits descriptivos, en presente: `EJ: Añado mi versión de los casos de uso`.

## Equipo

Néstor ---> Product Owner
Yousef ---> Scrum Master
Irene ---> Developer
Miguel ---> Developer
Adrian ---> Developer
Javier ---> Developer
Gonzalo ---> Developer
