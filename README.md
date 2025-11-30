# Infraestructura de Alta Disponibilidad y Orquestación con Kubernetes sobre Proxmox

## 1. Descripción del Proyecto
Este proyecto consiste en el diseño e implementación de una infraestructura virtualizada *on-premise* para el despliegue de servicios contenerizados. Se busca simular un entorno de producción real garantizando **Alta Disponibilidad (HA)**, escalabilidad y gestión centralizada.

*[Véase Documento de Análisis de Requisitos y Viabilidad](docs/Documento-de-análisis-de-Requisitos-y-Viabilidad.md)*

## 2. Arquitectura

### Infraestructura Física
* **Host:** Servidor dedicado con **Proxmox VE**.
* **Gestión:** Acceso remoto vía Web/SSH.

### Topología Lógica (Máquinas Virtuales)
| Rol                   | SO            | Función                                       |
| :-------------------- | :------------ | :-------------------------------------------- |
| **K8s Control Plane** | Linux Server  | Gestión del clúster (API, Etcd, Scheduler).   |
| **K8s Worker 1**      | Linux Server  | Ejecución de cargas de trabajo.               |
| **K8s Worker 2**      | Linux Server  | Redundancia y HA.                             |
| **Servidor Ansible**  | Linux Server  | Automatización y aprovisionamiento (IaC).     |
| **Cliente**           | Linux Desktop | Pruebas de usuario final y validación de red. |

## 3. Stack Tecnológico
* **Orquestación:** Kubernetes (K8s).
* **Runtime:** containerd.
* **Red y Balanceo:** 
    * **MetalLB:** Load Balancer Bare-Metal (Capa 2).
    * **Ingress Controller:** Nginx (Gestión de tráfico HTTP/S).

* **Automatización:** Ansible.
* **Versiones:** Git + GitHub.

## 4. Hoja de Ruta (Roadmap)
- [x] Definición de arquitectura.
- [x] Estructura del repositorio y documentación inicial.
- [ ] Instalación y configuración de Proxmox.
- [ ] Despliegue de VMs y configuración con Ansible.
- [ ] Inicialización del Clúster Kubernetes.
- [ ] Implementación de MetalLB e Ingress.
- [ ] Despliegue de servicios (Web).
- [ ] Implementación futura de monitorización con Prometheus y Grafana.
