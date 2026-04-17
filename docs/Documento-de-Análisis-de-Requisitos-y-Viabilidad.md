# 📄 Documento de Análisis de Requisitos y Viabilidad

<p align="center">
  <img src="https://img.shields.io/badge/Proyecto-ASIR-005571?style=for-the-badge" alt="ASIR">
  <img src="https://img.shields.io/badge/Fase-Análisis_y_Viabilidad-blue?style=for-the-badge" alt="Análisis">
  <img src="https://img.shields.io/badge/Metodología-Kanban-orange?style=for-the-badge" alt="Kanban">
</p>

<details>
<summary>📋 <strong>Tabla de Contenidos (Haz clic para desplegar)</strong></summary>
<br>

<ul>
  <li><a href="#-1-introducción-y-contexto-del-proyecto">1. Introducción y Contexto del Proyecto</a></li>
  <li><a href="#-2-análisis-de-viabilidad-y-requisitos">2. Análisis de Viabilidad y Requisitos</a></li>
  <li><a href="#-3-planificación-inicial-del-trabajo">3. Planificación Inicial del Trabajo</a></li>
  <li><a href="#-4-anexos-y-referencias">4. Anexos y Referencias</a></li>
</ul>
</details>

---

## 📖 1. Introducción y Contexto del Proyecto

### 1.1. Título y Resumen Ejecutivo
* **Título del Proyecto:** Infraestructura de Alta Disponibilidad y Orquestación de Contenedores con Kubernetes sobre Proxmox.
* **Resumen Ejecutivo:** Este proyecto consiste en el diseño e implementación de una infraestructura *on-premise* virtualizada utilizando Proxmox VE. Sobre esta base, se despliega un clúster de Kubernetes v1.30 optimizado para entornos de alta disponibilidad. El sistema garantiza la resiliencia de servicios contenerizados mediante la gestión automática de fallos y una red interna robusta, proporcionando una solución profesional a la gestión de servicios web críticos.

### 1.2. Justificación y Necesidad (El Problema)
* **Identificación del Cliente/Necesidad:** El cliente ficticio "DevOps Solutions S.L." requiere migrar de sistemas monolíticos a una arquitectura de microservicios.

* **El problema:** Tiempos de inactividad por fallos de hardware, escalado vertical costoso y falta de consistencia entre entornos.

* **Motivación Técnica:** Integración de administración Linux avanzada, virtualización de alto rendimiento y resolución de problemas reales de red (stack dual, DNS, Cgroups) que surgen en despliegues bare-metal.

### 1.3. Objetivos del Proyecto
* **Objetivo General:** Implementar un clúster de Kubernetes funcional en hardware físico propio, capaz de autorecuperarse y exponer servicios de forma balanceada.

* **Objetivos Específicos:**
  1. Configurar **Proxmox VE** como base de virtualización sólida.
  2. Desplegar un clúster **Kubernetes** (1 Master, 2 Workers) con configuración de red optimizada.
  3. Resolver conflictos de red específicos (desactivación de IPv6, gestión de DNS) para asegurar la estabilidad del CNI.
  4. Implementar balanceo de carga L2 con **MetalLB** y enrutamiento L7 con **Nginx Ingress**.
  5. Proveer almacenamiento persistente descentralizado mediante un servidor NFS dedicado.
  6. Validar la **Alta Disponibilidad** mediante pruebas de caída de nodos.
  7. Documentar todo el proceso utilizando control de versiones con **Git y GitHub**.

* **Objetivo Deseable:** Implementación de un sistema de monitorización y visualización de métricas del clúster utilizando **Prometheus y Grafana**, condicionado a la disponibilidad de tiempo tras asegurar la estabilidad del núcleo del sistema.

---

## ⚖️ 2. Análisis de Viabilidad y Requisitos

### 2.1. Viabilidad Operativa, Técnica y Económica
* **Viabilidad Técnica:** Confirmada tras la resolución exitosa de la fase de inicialización. El hardware soporta el stack propuesto y se ha validado la compatibilidad de **containerd** como runtime bajo la jerarquía de Cgroups de sistema.
* **Justificación:** Se ha elegido **Kubernetes** frente a Docker Swarm por ser el estándar de facto en la industria, y **Proxmox** por su flexibilidad frente a soluciones propietarias como VMware ESXi.
* **Viabilidad Temporal:** El proyecto se encuentra en una fase avanzada. La resolución de problemas de red en la fase inicial reduce el riesgo de retrasos en la implementación de servicios superiores (MetalLB/Ingress).
* **Viabilidad Económica:** Coste de software de **0€**. Se utiliza software Open Source con licencias permisivas (GPL, Apache 2.0).

### 2.2. Requisitos Funcionales y No Funcionales

* **Requisitos Funcionales:**
  * El sistema debe permitir el despliegue de aplicaciones web mediante ficheros YAML.
  * El sistema debe asignar automáticamente IPs de la red local a los servicios mediante MetalLB.
  * El sistema debe redirigir el tráfico HTTP basándose en nombres de dominio (Ingress).
  * El sistema debe recuperar automáticamente un contenedor (Pod) si este falla o se elimina.
  * El sistema debe garantizar la persistencia de datos de las aplicaciones independientemente del nodo físico en el que se ejecuten.
  * La automatización (Ansible) debe ser capaz de configurar un nuevo nodo desde cero.

* **Requisitos No Funcionales:** 
  * **Alta Disponibilidad:** El servicio web debe seguir respondiendo aunque uno de los nodos Worker se apague.
  * **Escalabilidad:** Debe permitir añadir nuevos nodos al clúster sin detener el servicio.
  * **Consistencia de Red:** Exclusión de tráfico IPv6 para evitar latencias y errores de direccionamiento interno.
  * **Mantenibilidad:** Documentación de incidencias técnicas y soluciones aplicadas (Troubleshooting).
  * **Rendimiento:** Uso de `systemd` como driver de Cgroup para una mejor integración con el kernel de Ubuntu 24.04.
  

### 2.3. Herramientas y Stack Tecnológico Propuesto

| Categoría | Tecnología / Herramienta |
| :--- | :--- |
| **Hipervisor** | Proxmox VE |
| **Sistemas Operativos** | Ubuntu Server 24.04 LTS |
| **Orquestación y Runtime** | Kubernetes (K8s) v1.30, Containerd |
| **Red y Balanceo** | Flannel (CNI), MetalLB, Nginx Ingress Controller |
| **Almacenamiento Persistente**| Servidor NFS (Network File System) dedicado |
| **Automatización** | Ansible |
| **Control de Versiones** | Git y GitHub |

---

## 📅 3. Planificación Inicial del Trabajo

### 3.1. Estructura del Equipo
* **Administrador del Proyecto (Alumno):** Asume todos los roles técnicos y de gestión (SysAdmin, DevOps Engineer, Documentador). 
  * Responsable de la infraestructura física y virtual.
  * Desarrollador de scripts de automatización e integración.

### 3.2. Metodología de Trabajo
* **Metodología:** Kanban adaptado a resolución de incidencias. 
* **Herramienta:** GitHub Projects.

> [!NOTE]
> **Justificación:** Al ser un proyecto individual, Kanban permite una visualización directa del flujo de trabajo ("Por hacer", "En progreso", "Hecho") sin la sobrecarga administrativa de las ceremonias de Scrum (Sprints, Dailies), permitiendo flexibilidad según la carga de trabajo de otras asignaturas. 

### 3.3. Hitos Clave de la Fase

| Fase | Tarea | Estado |
| :--- | :--- | :--- |
| **Análisis** | Definición de la arquitectura y stack. | ✅ Completado |
| **Diseño** | Configuración de Proxmox e IPs Estáticas. | ✅ Completado |
| **Implementación** | Inicialización del Control Plane (v1.30). | ✅ Completado |
| **Implementación** | Join de Workers y Configuración de Flannel CNI. | ✅ Completado |
| **Implementación** | Depuración de errores de Red y Container Runtime. | ✅ Completado |
| **Implementación** | Despliegue de MetalLB e Ingress. | ✅ Completado |
| **Implementación** | Despliegue de servidor NFS y Volúmenes Persistentes. | ✅ Completado |
| **Diseño** | Desarrollo de Playbooks de Ansible iniciales. | ✅ Completado |
| **Pruebas** | Test de Alta Disponibilidad (HA). | ⏳✅ Completado |

---

## 📚 4. Anexos y Referencias

### 4.1. Webgrafía y Documentación Oficial
A continuación se detallan las fuentes de información primarias utilizadas para el análisis, diseño e implementación de la infraestructura:

**A. Virtualización y Sistema Base**
* **[Proxmox VE Administration Guide](https://pve.proxmox.com/pve-docs/)**: Documentación oficial para la instalación, configuración de almacenamiento y gestión de redes en el hipervisor.
* **[Ubuntu Server Documentation](https://ubuntu.com/server/docs)**: Guías de referencia para la configuración del sistema operativo base de los nodos.

**B. Orquestación y Contenedores (Kubernetes)**
* **[Kubernetes Documentation](https://kubernetes.io/docs/home/)**: Fuente principal para los conceptos de arquitectura (Control Plane, Nodes), despliegue de Pods y Servicios.
* **[Containerd Getting Started](https://github.com/containerd/containerd/blob/main/docs/getting-started.md)**: Referencia para la configuración del runtime de contenedores compatible con CRI.
* **[Herramienta Kubeadm](https://kubernetes.io/docs/reference/setup-tools/kubeadm/)**: Documentación específica para la inicialización del clúster (bootstrapping).
* **[Flannel CNI Plugin](https://github.com/flannel-io/flannel)**: Repositorio oficial del plugin de red.
* **Troubleshooting Kubeadm:** Documentación interna sobre resolución de errores en el despliegue de nodos worker.

**C. Redes, Balanceo de Carga y Almacenamiento**
* **[MetalLB Documentation](https://metallb.universe.tf/)**: Guía de implementación para el balanceador de carga en entornos *bare-metal* (capa 2).
* **[Ingress Nginx Controller](https://kubernetes.github.io/ingress-nginx/)**: Documentación de la comunidad de Kubernetes para la configuración del controlador de entrada y reglas de enrutamiento.
* **[NFS Server Documentation (Ubuntu)](https://ubuntu.com/server/docs/network-file-system-nfs)**: Referencia técnica para la configuración de exportaciones seguras del servidor NFS.

**D. Automatización y Gestión**
* **[Ansible Documentation](https://docs.ansible.com/)**: Manuales de referencia para la creación de inventarios, Playbooks y uso de módulos.
* **[Proxmox Ansible Collection](https://docs.ansible.com/ansible/latest/collections/community/general/proxmox_kvm_module.html)**: Documentación de los módulos comunitarios para gestionar Proxmox vía Ansible.

---
<p align="center">
  <b>Proyecto Integrado de Grado Superior ASIR</b><br>
  © 2026 - <a href="https://github.com/jobopaK">jobopaK</a>
</p>
