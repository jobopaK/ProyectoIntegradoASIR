# Infraestructura de Alta Disponibilidad y Orquestación con Kubernetes sobre Proxmox

## 1. Descripción del Proyecto
Este proyecto consiste en el diseño e implementación de una infraestructura virtualizada *on-premise* para el despliegue de servicios contenerizados. Se busca simular un entorno de producción real garantizando **Alta Disponibilidad (HA)**, escalabilidad y gestión centralizada.

*[Véase Documento de Análisis de Requisitos y Viabilidad](docs/Documento-de-Análisis-de-Requisitos-y-Viabilidad.md)*

## 2. Arquitectura

### Infraestructura Física
* **Host:** Servidor dedicado con **Proxmox VE**.
* **Gestión:** Acceso remoto vía Web/SSH.

### Topología Lógica (Máquinas Virtuales)

| Rol                   | SO            | Función                                       | IP Estática (Sugerida) |
| :-------------------- | :------------ | :-------------------------------------------- | :--------------------- |
| **K8s Control Plane** | Ubuntu Server | Gestión del clúster (API, Etcd, Scheduler). | 192.168.1.110          |
| **K8s Worker 1** | Ubuntu Server | Ejecución de cargas de trabajo.             | 192.168.1.111          |
| **K8s Worker 2** | Ubuntu Server | Redundancia y HA.                           | 192.168.1.112          |
| **Servidor Ansible** | Ubuntu Server | Automatización y aprovisionamiento (IaC).   | 192.168.1.115          |
| **Cliente** | Linux Desktop | Pruebas de usuario final y validación de red.| DHCP                   |

## 3. Stack Tecnológico

* **Orquestación:** Kubernetes (K8s) v1.30.
* **Runtime:** containerd.
* **Red (CNI):** Flannel.
* **Red y Balanceo:** * **MetalLB:** Load Balancer Bare-Metal (Capa 2).
* **Ingress Controller:** Nginx (Gestión de tráfico HTTP/S).
* **Automatización:** Ansible.
* **Versiones:** Git + GitHub.

## 4. Hoja de Ruta (Roadmap)
- [x] Definición de arquitectura.
- [x] Estructura del repositorio y documentación inicial.
- [x] Instalación y configuración de Proxmox.
- [x] Despliegue de VMs y configuración base (Networking, IP estática).
- [x] Inicialización del Clúster Kubernetes (Control Plane).
- [x] Unión de nodos Worker (Join) y configuración de CNI (Flannel).
- [ ] Implementación de MetalLB e Ingress.
- [ ] Despliegue de servicios (Web).
- [ ] Implementación futura de monitorización con Prometheus y Grafana.

## 5. Notas de Implementación y Troubleshooting
Durante la fase de despliegue del clúster, se identificaron y resolvieron los siguientes retos técnicos críticos:

* **Gestión de Red:** Se desactivó el protocolo IPv6 a nivel de kernel para evitar conflictos de identificación de nodos en el Control Plane.
* **Configuración del Runtime:** Se ajustó el driver de Cgroup a `systemd` en `containerd` para garantizar la estabilidad del servicio `kubelet`.
* **Persistencia de Red (CNI):** Resolución de errores `NetworkPluginNotReady` mediante la limpieza manual de interfaces de red residuales (`cni0`, `flannel.1`) y reinicio del almacenamiento de `containerd`.
* **Identidad de Nodo:** Uso de la flag `--node-ip` en la configuración de `kubelet` para forzar la vinculación correcta con la interfaz de red IPv4 estática.

---
© 2026 - Proyecto Integrado de Grado Superior ASIR
