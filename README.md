# Infraestructura de Alta Disponibilidad y Orquestación con Kubernetes sobre Proxmox

<p align="center">
  <img src="https://img.shields.io/badge/Kubernetes-v1.30-blue?logo=kubernetes&logoColor=white" alt="K8s Version">
  <img src="https://img.shields.io/badge/Proxmox-VE%209.x-orange?logo=proxmox&logoColor=white" alt="Proxmox">
  <img src="https://img.shields.io/badge/Ansible-Automated-red?logo=ansible&logoColor=white" alt="Ansible">
  <img src="https://img.shields.io/badge/Ubuntu-24.04%20LTS-E95420?logo=ubuntu&logoColor=white" alt="Ubuntu">
  <img src="https://img.shields.io/badge/License-MIT-green" alt="License">
</p>

---

## 📖 1. Descripción del Proyecto

Este proyecto consiste en el diseño e implementación de una infraestructura virtualizada *on-premise* para el despliegue de servicios contenerizados. Se simula un entorno de producción real garantizando **Alta Disponibilidad (HA)**, escalabilidad y gestión centralizada mediante un clúster de Kubernetes robusto.

> [!IMPORTANT]
> Puedes consultar el detalle estratégico en el [Documento de Análisis de Requisitos y Viabilidad](docs/Documento-de-Análisis-de-Requisitos-y-Viabilidad.md).

---

## 🏗️ 2. Arquitectura

### 🌐 Infraestructura Física
* **Host:** Servidor dedicado con **Proxmox VE**.
* **Gestión:** Acceso remoto seguro vía Web/SSH.

### 🖥️ Topología Lógica (Máquinas Virtuales)

| Icono | Rol | SO | Función | IP Estática |
| :---: | :--- | :--- | :--- | :--- |
| 🛡️ | **K8s Control Plane** | Ubuntu Server | Gestión del clúster (API, Etcd, Scheduler) | `192.168.1.110` |
| 👷 | **K8s Worker 1** | Ubuntu Server | Ejecución de cargas de trabajo | `192.168.1.111` |
| 👷 | **K8s Worker 2** | Ubuntu Server | Redundancia y HA | `192.168.1.112` |
| 🤖 | **Servidor Ansible** | Ubuntu Server | Automatización y aprovisionamiento (IaC) | `192.168.1.115` |
| 💻 | **Cliente** | Linux Desktop | Pruebas de usuario final y validación de red | `DHCP` |

---

## 📚 3. Documentación y Guías de Despliegue

Se han elaborado manuales técnicos detallados para replicar la infraestructura paso a paso:

* **Fase 1:** [🚀 Instalación de Proxmox VE](docs/01.Instalación-Proxmox.md) - Preparación del host físico y configuración de BIOS.
* **Fase 2:** [🖥️ Configuración de Proxmox e Instalación de Ubuntu Server](docs/02.Configuración-Proxmox-e-instalación-UbuntuServer.md) - Gestión de repositorios, red y creación de plantillas (*Templates*).
* **Fase 3:** [☸️ Inicialización del Clúster Kubernetes](docs/03.Inicialización-del-Cluster-Kubernetes.md) - Despliegue de nodos, CNI Flannel y resolución de errores.
* **Fase 4:** [⚖️ Instalación y Configuración de MetalLB](docs/04.Instalar-y-configurar-MetalLB.md) - Implementación de balanceo de carga en Capa 2 y gestión de IPs externas.
* **Fase 5:** [🌐 Instalación de Ingress Controller (Nginx)](docs/05.Instalar-Ingress-Controller-Nginx.md) - Gestión de tráfico HTTP/HTTPS mediante reglas de enrutamiento por dominio.

> [!TIP]
> Se recomienda seguir las guías en orden secuencial para garantizar la correcta visibilidad de red entre los componentes del clúster.

---

## 🛠️ 4. Stack Tecnológico

* **Orquestación:** `Kubernetes v1.30` ☸️
* **Runtime:** `containerd` 📦
* **Red (CNI):** `Flannel` 🕸️
* **Red y Balanceo:**
    * **MetalLB:** Load Balancer Bare-Metal (Capa 2).
    * **Ingress Controller:** Nginx (Gestión de tráfico HTTP/S).
* **Automatización:** `Ansible` 🤖
* **Control de Versiones:** `Git` + `GitHub` 🐙

---

## 🗺️ 5. Hoja de Ruta (Roadmap)

- [x] 📐 Definición de arquitectura.
- [x] 📁 Estructura del repositorio y documentación inicial.
- [x] 🌐 Instalación y configuración de Proxmox.
- [x] 🖥️ Despliegue de VMs y configuración base (Networking, IP estática).
- [x] 🛡️ Inicialización del Clúster Kubernetes (Control Plane).
- [x] 👷 Unión de nodos Worker (Join) y configuración de CNI (Flannel).
- [ ] ⚖️ Implementación de MetalLB e Ingress.
- [ ] 🚀 Despliegue de servicios (Web).
- [ ] 🤖 Automatización avanzada con Ansible.
- [ ] 📊 Monitorización con Prometheus y Grafana (Futuro).

---

## 🔧 6. Notas de Implementación y Troubleshooting

Durante el despliegue se resolvieron retos técnicos críticos documentados para futuras referencias:

> [!CAUTION]
> **Gestión de Red:** Desactivación obligatoria de **IPv6** a nivel de kernel para evitar conflictos en el Control Plane.

* **Configuración del Runtime:** Ajuste del driver de Cgroup a `systemd` en `containerd` para asegurar la estabilidad del proceso `kubelet`.
* **Vinculación de IP:** Uso estricto de la flag `--node-ip` en la configuración de `kubelet` para forzar la interfaz IPv4 correcta sobre el hardware virtualizado.
* **Conectividad Externa (Netplan):** Se detectó que los nodos perdían conectividad con internet al configurar IPs estáticas. Se corrigió definiendo explícitamente la ruta por defecto (`routes: to: default via 192.168.1.1`) en lugar del parámetro obsoleto `gateway4`.
* **Identidad de Nodo (Kubelet):** Para evitar que el clúster usara interfaces erróneas, se forzó la vinculación de IP mediante la creación del archivo `/etc/default/kubelet` con el parámetro `KUBELET_EXTRA_ARGS="--node-ip=<IP_ESTATICA>"`.
* **Módulos del Kernel (Networking):** El plugin de red Flannel entraba en `CrashLoopBackOff` por la ausencia del módulo `br_netfilter`. Se solucionó cargando el módulo manualmente y asegurando su persistencia en `/etc/modules-load.d/k8s.conf`.
* **Persistencia de Red (CNI):** Resolución de errores `NetworkPluginNotReady` mediante la limpieza manual de interfaces residuales (`cni0`, `flannel.1`) y el reinicio de `containerd`.

---
<p align="center">
  <b>Proyecto Integrado de Grado Superior ASIR</b><br>
  © 2026 - <a href="https://github.com/jobopaK">jobopaK</a>
</p>
