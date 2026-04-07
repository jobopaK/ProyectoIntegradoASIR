<details>
<summary>📋 <strong>Tabla de Contenidos (Haz clic para desplegar)</strong></summary>
<br>

<ul>
  <li><a href="#documento-de-análisis-de-requisitos-y-viabilidad">Documento de Análisis de Requisitos y Viabilidad</a>
    <ul>
      <li><a href="#1-introducción-y-contexto-del-proyecto">1. Introducción y Contexto del Proyecto</a>
        <ul>
          <li><a href="#11-título-y-resumen-ejecutivo">1.1. Título y Resumen Ejecutivo</a></li>
          <li><a href="#12-justificación-y-necesidad-el-problema">1.2. Justificación y Necesidad (El Problema)</a></li>
          <li><a href="#13-objetivos-del-proyecto">1.3. Objetivos del Proyecto</a></li>
        </ul>
      </li>
      <li><a href="#2-análisis-de-viabilidad-y-requisitos">2. Análisis de Viabilidad y Requisitos</a>
        <ul>
          <li><a href="#21-viabilidad-operativa-técnica-y-económica">2.1. Viabilidad Operativa, Técnica y Económica</a></li>
          <li><a href="#22-requisitos-funcionales-y-no-funcionales">2.2. Requisitos Funcionales y No Funcionales</a></li>
          <li><a href="#23-herramientas-y-stack-tecnológico-propuesto">2.3. Herramientas y Stack Tecnológico Propuesto</a></li>
        </ul>
      </li>
      <li><a href="#3-planificación-inicial-del-trabajo">3. Planificación Inicial del Trabajo</a>
        <ul>
          <li><a href="#31-estructura-del-equipo">3.1. Estructura del Equipo</a></li>
          <li><a href="#32-metodología-de-trabajo">3.2. Metodología de Trabajo</a></li>
          <li><a href="#33-hitos-clave-de-la-fase">3.3. Hitos Clave de la Fase</a></li>
        </ul>
      </li>
      <li><a href="#4-anexos-y-referencias">4. Anexos y Referencias</a>
        <ul>
          <li><a href="#41-webgrafía-y-documentación-oficial">4.1. Webgrafía y Documentación Oficial</a></li>
        </ul>
      </li>
    </ul>
  </li>
</ul>

</details>

------

## Documento de Análisis de Requisitos y Viabilidad

### 1. Introducción y Contexto del Proyecto

#### 1.1. Título y Resumen Ejecutivo:

- **Título del Proyecto:** Infraestructura de Alta Disponibilidad y Orquestación de Contenedores con Kubernetes sobre Proxmox.
- **Resumen Ejecutivo:** Este proyecto consiste en el diseño e implementación de una infraestructura *on-premise* virtualizada utilizando Proxmox VE. Sobre esta base, se despliega un clúster de Kubernetes v1.30 optimizado para entornos de alta disponibilidad. El sistema garantiza la resiliencia de servicios contenerizados mediante la gestión automática de fallos y una red interna robusta, proporcionando una solución profesional a la gestión de servicios web críticos.

#### 1.2. Justificación y Necesidad (El Problema):

- **Identificación del Cliente/Necesidad:** El cliente ficticio "DevOps Solutions S.L." requiere migrar de sistemas monolíticos a una arquitectura de microservicios.
- **El problema:** Tiempos de inactividad por fallos de hardware, escalado vertical costoso y falta de consistencia entre entornos. 
- **Motivación Técnica:** Integración de administración Linux avanzada, virtualización de alto rendimiento y resolución de problemas reales de red (stack dual, DNS, Cgroups) que surgen en despliegues bare-metal.

#### 1.3. Objetivos del Proyecto:

- **Objetivo General:** Implementar un clúster de Kubernetes funcional en hardware físico propio, capaz de autorecuperarse y exponer servicios de forma balanceada.
- **Objetivos Específicos:**
  1. Configurar **Proxmox VE** como base de virtualización sólida.
  2. Desplegar un clúster **Kubernetes** (1 Master, 2 Workers) con configuración de red optimizada.
  3. Resolver conflictos de red específicos (desactivación de IPv6, gestión de DNS) para asegurar la estabilidad del CNI.
  4. Implementar balanceo de carga L2 con **MetalLB** y enrutamiento L7 con **Nginx Ingress**.
  5. Validar la **Alta Disponibilidad** mediante pruebas de caída de nodos.
  6. Documentar todo el proceso utilizando control de versiones con **Git y GitHub**.
- **Objetivo Deseable:** Implementación de un sistema de monitorización y visualización de métricas del clúster utilizando **Prometheus y Grafana**, condicionado a la disponibilidad de tiempo tras asegurar la estabilidad del núcleo del sistema.

------

### 2. Análisis de Viabilidad y Requisitos

#### 2.1. Viabilidad Operativa, Técnica y Económica:

- **Viabilidad Técnica:** Confirmada tras la resolución exitosa de la fase de inicialización. El hardware soporta el stack propuesto y se ha validado la compatibilidad de **containerd** como runtime bajo la jerarquía de Cgroups de sistema.
- **Justificación:** Se ha elegido **Kubernetes** frente a Docker Swarm por ser el estándar de facto en la industria, y **Proxmox** por su flexibilidad frente a soluciones propietarias como VMware ESXi.
- **Viabilidad Temporal:** El proyecto se encuentra en una fase avanzada. La resolución de problemas de red en la fase inicial reduce el riesgo de retrasos en la implementación de servicios superiores (MetalLB/Ingress).
- **Viabilidad Económica:** Coste de software de **0€**. Se utiliza software Open Source con licencias permisivas (GPL, Apache 2.0).

#### 2.2. Requisitos Funcionales y No Funcionales:

- **Requisitos Funcionales:**
  - El sistema debe permitir el despliegue de aplicaciones web mediante ficheros YAML.
  - El sistema debe asignar automáticamente IPs de la red local a los servicios mediante MetalLB.
  - El sistema debe redirigir el tráfico HTTP basándose en nombres de dominio (Ingress).
  - El sistema debe recuperar automáticamente un contenedor (Pod) si este falla o se elimina.
  - La automatización (Ansible) debe ser capaz de configurar un nuevo nodo desde cero.
- **Requisitos No Funcionales:** 
  - **Alta Disponibilidad:** El servicio web debe seguir respondiendo aunque uno de los nodos Worker se apague.
  - **Escalabilidad:** Debe permitir añadir nuevos nodos al clúster sin detener el servicio.
  - **Consistencia de Red:** Exclusión de tráfico IPv6 para evitar latencias y errores de direccionamiento interno.
  - **Mantenibilidad:** Documentación de incidencias técnicas y soluciones aplicadas (Troubleshooting).
  - **Rendimiento:** Uso de `systemd` como driver de Cgroup para una mejor integración con el kernel de Ubuntu 24.04.

#### 2.3. Herramientas y Stack Tecnológico Propuesto:

- **Selección Tecnológica:** 
  - **Hipervisor:** Proxmox VE.
  - **Sistemas Operativos:** Ubuntu Server 24.04 LTS.
  - **Orquestación:** Kubernetes (K8s) v1.30, Containerd.
  - **Red y Balanceo:** Flannel (CNI), MetalLB, Nginx Ingress Controller.
  - **Automatización:** Ansible.
  - **Control de Versiones:** Git y GitHub.

------

### 3. Planificación Inicial del Trabajo

#### 3.1. Estructura del Equipo:

- **Administrador del Proyecto (Alumno):** Asume todos los roles técnicos y de gestión (SysAdmin, DevOps Engineer, Documentador). 
  - Responsable de la infraestructura física y virtual.
  - Desarrollador de scripts de automatización e integración.

#### 3.2. Metodología de Trabajo:

- **Metodología:** Kanban adaptado a resolución de incidencias. 
- **Herramienta:** GitHub Projects.
- **Justificación:** Al ser un proyecto individual, Kanban permite una visualización directa del flujo de trabajo ("Por hacer", "En progreso", "Hecho") sin la sobrecarga administrativa de las ceremonias de Scrum (Sprints, Dailies), permitiendo flexibilidad según la carga de trabajo de otras asignaturas. 

#### 3.3. Hitos Clave de la Fase:

| **Fase** | **Tarea** | **Estado** |
| ------------------ | ------------------------------------------------ | ---------- |
| **Análisis** | Definición de la arquitectura y stack.           | Completado |
| **Diseño** | Configuración de Proxmox e IPs Estáticas.        | Completado |
| **Implementación** | Inicialización del Control Plane (v1.30).        | Completado |
| **Implementación** | Join de Workers y Configuración de Flannel CNI.  | Completado |
| **Implementación** | Depuración de errores de Red y Container Runtime.| Completado |
| **Implementación** | Despliegue de MetalLB e Ingress.                 | Pendiente  |
| **Diseño**         | Desarrollo de Playbooks de Ansible iniciales.    | Pendiente  |
| **Pruebas** | Test de Alta Disponibilidad (HA).                | Pendiente  |

------

### 4. Anexos y Referencias

#### 4.1. Webgrafía y Documentación Oficial

-A continuación se detallan las fuentes de información primarias utilizadas para el análisis, diseño e implementación de la infraestructura:

**A. Virtualización y Sistema Base**

- **Proxmox VE Administration Guide:** Documentación oficial para la instalación, configuración de almacenamiento y gestión de redes en el hipervisor.
  - *Enlace:* https://pve.proxmox.com/pve-docs/
- **Ubuntu Server Documentation:** Guías de referencia para la configuración del sistema operativo base de los nodos.
  - *Enlace:* https://ubuntu.com/server/docs

**B. Orquestación y Contenedores (Kubernetes)**

- **Kubernetes Documentation:** Fuente principal para los conceptos de arquitectura (Control Plane, Nodes), despliegue de Pods y Servicios.
  - *Enlace:* https://kubernetes.io/docs/home/
- **Containerd Getting Started:** Referencia para la configuración del runtime de contenedores compatible con CRI.
  - *Enlace:* https://github.com/containerd/containerd/blob/main/docs/getting-started.md
- **Herramienta Kubeadm:** Documentación específica para la inicialización del clúster (bootstrapping).
  - *Enlace:* https://kubernetes.io/docs/reference/setup-tools/kubeadm/
- **Flannel CNI Plugin:** https://github.com/flannel-io/flannel
- **Troubleshooting Kubeadm:** Documentación interna sobre resolución de errores en el despliegue de nodos worker.

**C. Redes y Balanceo de Carga**

- **MetalLB Documentation:** Guía de implementación para el balanceador de carga en entornos *bare-metal* (capa 2).
  - *Enlace:* https://metallb.universe.tf/
- **Ingress Nginx Controller:** Documentación de la comunidad de Kubernetes para la configuración del controlador de entrada y reglas de enrutamiento.
  - *Enlace:* https://kubernetes.github.io/ingress-nginx/

**D. Automatización y Gestión**

- **Ansible Documentation:** Manuales de referencia para la creación de inventarios, Playbooks y uso de módulos.
  - *Enlace:* https://docs.ansible.com/
- **Proxmox Ansible Collection:** Documentación de los módulos comunitarios para gestionar Proxmox vía Ansible.
  - *Enlace:* https://docs.ansible.com/ansible/latest/collections/community/general/proxmox_kvm_module.html

