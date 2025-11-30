- [Documento de Análisis de Requisitos y Viabilidad](#documento-de-análisis-de-requisitos-y-viabilidad)
  - [1. Introducción y Contexto del Proyecto](#1-introducción-y-contexto-del-proyecto)
    - [1.1. Título y Resumen Ejecutivo:](#11-título-y-resumen-ejecutivo)
    - [1.2. Justificación y Necesidad (El Problema):](#12-justificación-y-necesidad-el-problema)
    - [1.3. Objetivos del Proyecto:](#13-objetivos-del-proyecto)
  - [2. Análisis de Viabilidad y Requisitos](#2-análisis-de-viabilidad-y-requisitos)
    - [2.1. Viabilidad Operativa, Técnica y Económica:](#21-viabilidad-operativa-técnica-y-económica)
    - [2.2. Requisitos Funcionales y No Funcionales:](#22-requisitos-funcionales-y-no-funcionales)
    - [2.3. Herramientas y Stack Tecnológico Propuesto:](#23-herramientas-y-stack-tecnológico-propuesto)
  - [3. Planificación Inicial del Trabajo](#3-planificación-inicial-del-trabajo)
    - [3.1. Estructura del Equipo:](#31-estructura-del-equipo)
    - [3.2. Metodología de Trabajo:](#32-metodología-de-trabajo)
    - [3.3. Hitos Clave de la Fase:](#33-hitos-clave-de-la-fase)
  - [4. Anexos y Referencias](#4-anexos-y-referencias)

## Documento de Análisis de Requisitos y Viabilidad

### 1. Introducción y Contexto del Proyecto

#### 1.1. Título y Resumen Ejecutivo:

- **Título del Proyecto:** Infraestructura de Alta Disponibilidad y Orquestación de Contenedores con Kubernetes sobre Proxmox.
- **Resumen Ejecutivo:** Diseñar e implementar una infraestructura virtualizada *on-premise* capaz de desplegar servicios contenerizados garantizando **Alta Disponibilidad (HA)**, escalabilidad y gestión centralizada. El sistema simulará un entorno de producción real utilizando prácticas de *Infraestructura como Código* (IaC) y control de versiones.



#### 1.2. Justificación y Necesidad (El Problema):

- **Identificación del Cliente/Necesidad:** Describan el cliente (ficticio o real) y el problema o carencia operativa que el proyecto busca resolver (ej: "La empresa X necesita un sistema de copias de seguridad automatizado y monitorizado").
- **Motivación Técnica y Profesional:** Expliquen cómo el proyecto consolida las competencias de ASIR (Sistemas Operativos, Redes, Seguridad) y por qué es relevante para el mercado actual.




#### 1.3. Objetivos del Proyecto:

- **Objetivo General:** El resultado final que se espera conseguir.
- **Objetivos Específicos:** Listado de metas concretas, medibles, alcanzables, relevantes y con plazos definidos (ej. "Instalar y configurar un servidor de monitorización Zabbix para 5 máquinas virtuales").



------



### 2. Análisis de Viabilidad y Requisitos ###

#### 2.1. Viabilidad Operativa, Técnica y Económica: ####

- **Viabilidad Técnica:** ¿El equipo tiene el know-how para realizarlo? ¿El hardware y software del centro educativo (virtualización, licencias, etc.) es adecuado? Justifica por qué la tecnología seleccionada es la más apropiada (ej. ¿Por qué Docker y no máquinas virtuales dedicadas?).

- **Viabilidad Operativa y Temporal:** ¿El proyecto se puede completar con los recursos humanos del equipo y dentro del tiempo establecido (fin de curso).

- **Viabilidad Económica (FOSS):** Dado que se usará Software Libre (FOSS), la viabilidad se centrará en los costes de implantación, mantenimiento y recursos (horas/persona).



#### 2.2. Requisitos Funcionales y No Funcionales: ####

- **Requisitos Funcionales:** ¿Qué debe hacer el sistema? (ej. El sistema debe autenticar usuarios contra LDAP; debe generar copias de seguridad diarias; debe alertar por correo electrónico).

- **Requisitos No Funcionales:** ¿Cómo debe ser el sistema? (ej. Debe ser seguro -Hardening-, debe ser rápido -Rendimiento-, debe ser fácil de usar -Usabilidad-).



#### 2.3. Herramientas y Stack Tecnológico Propuesto: ####

- **Selección Tecnológica:** Detalla el stack de tecnologías que se va a utilizar (ej. Ubuntu Server 22.04, Nginx, MariaDB, Docker, Jenkins, Ansible, Wekan, DokuWiki).

- **Justificación de la Elección:** Explica brevemente porqué estas herramientas son las mejores para el proyecto, destacando sus beneficios como Software Libre (FOSS) y su relevancia profesional.



------



### 3. Planificación Inicial del Trabajo

#### 3.1. Estructura del Equipo:

- Identificación de los miembros del equipo y asignación de roles iniciales (ej. Scrum Master/Coordinador, Responsable de Seguridad, Responsable de Documentación).



#### 3.2. Metodología de Trabajo:

- Define la metodología ágil que van a usar (Scrum o Kanban). Justifiquen la elección (ej. "Usaremos Kanban en Wekan por su sencillez y seguimiento visual continuo").



#### 3.3. Hitos Clave de la Fase:

- Incluye una tabla de tareas realizadas en esta fase de Análisis y las tareas pendientes para el próximo hito (Diseño y Planificación).



------



### 4. Anexos y Referencias

- **Búsqueda de Información:** Incluye las fuentes consultadas para el análisis de viabilidad (documentación oficial, blogs técnicos, comparativas de software, etc.).
