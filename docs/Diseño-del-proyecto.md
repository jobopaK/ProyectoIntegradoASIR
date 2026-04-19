\# \*\*Diseño del Proyecto: Infraestructura de Alta Disponibilidad y Orquestación de Contenedores con Kubernetes sobre Proxmox\*\*



\## \*\*1. Objetivos y Alcance del Proyecto\*\* 🚀



El objetivo principal es diseñar e implementar una \*\*infraestructura \*on-premise\* virtualizada y de alta disponibilidad\*\*, capaz de orquestar servicios contenerizados mediante Kubernetes sobre un hipervisor Proxmox.



\* \*\*Virtualización Robusta:\*\* Gestión flexible de recursos físicos mediante Proxmox VE.

\* \*\*Orquestación de Contenedores:\*\* Clúster Kubernetes v1.30 con `containerd`.

\* \*\*Automatización (IaC):\*\* Despliegue mediante Ansible para garantizar la repetibilidad del entorno.

\* \*\*Persistencia y Seguridad:\*\* Implementación de almacenamiento dinámico y gestión de secretos.



\---



\## \*\*2. Arquitectura de Sistemas y Redes\*\* 🏗️



La infraestructura se basa en una segmentación lógica para garantizar el rendimiento y la seguridad del tráfico de datos.



\### \*\*2.1. Inventario de Máquinas Virtuales\*\*

| Nodo | Función | IP Estática | Recursos (CPU/RAM) |

| :--- | :--- | :--- | :--- |

| \*\*`master01`\*\* | Plano de Control (Control Plane) | `192.168.1.210` | 2 vCPU / 4GB RAM |

| \*\*`worker01`\*\* | Carga de Trabajo (Apps) | `192.168.1.211` | 4 vCPU / 8GB RAM |

| \*\*`worker02`\*\* | Carga de Trabajo (Apps) | `192.168.1.212` | 4 vCPU / 8GB RAM |

| \*\*`srv-nfs`\*\* | Almacenamiento Persistente | `192.168.1.220` | 1 vCPU / 2GB RAM |

| \*\*`srv-ansible`\*\* | Nodo de Gestión (IaC) | `192.168.1.200` | 1 vCPU / 1GB RAM |



\### \*\*2.2. Segmentación de Red (VLANs sugeridas)\*\* 🌐

Para optimizar el tráfico, se propone una arquitectura de red segmentada:

\* \*\*VLAN 10 (Gestión):\*\* Acceso SSH y panel Proxmox.

\* \*\*VLAN 20 (Cluster):\*\* Comunicación interna entre nodos K8s (Flannel CNI).

\* \*\*VLAN 30 (Almacenamiento):\*\* Tráfico dedicado NFS para evitar latencia en las bases de datos.



\---



\## \*\*3. Implementación Tecnológica y Soluciones\*\* 🛠️



En esta sección se detallan las configuraciones críticas para resolver conflictos comunes en despliegues \*bare-metal\*.



\### \*\*3.1. Optimización del Kernel y Runtime\*\* 🔧

\* \*\*Desactivación de IPv6:\*\* Forzado mediante parámetros de kernel para evitar conflictos de resolución en Flannel.

\* \*\*Cgroups v2:\*\* Configuración de `systemd` como driver de cgroups en `containerd` para una gestión eficiente de recursos.

\* \*\*Módulo `br\_netfilter`:\*\* Carga explícita para permitir que el tráfico de los puentes sea procesado por iptables.



\### \*\*3.2. Networking L2/L7 y Balanceo\*\* ⚖️

\* \*\*MetalLB:\*\* Implementación en modo Layer 2 para asignar IPs externas a los servicios de Kubernetes dentro de la red local.

\* \*\*Nginx Ingress Controller:\*\* Gestión de tráfico mediante nombres de dominio (FQDN), permitiendo el despliegue de múltiples aplicaciones en los puertos 80/443.



\### \*\*3.3. Almacenamiento Dinámico (StorageClass)\*\* 📂

\* \*\*NFS Subdirectory External Provisioner:\*\* En lugar de montajes manuales, se implementa una `StorageClass` que permite el aprovisionamiento dinámico. Kubernetes crea automáticamente los directorios en el servidor NFS según la demanda de los \*PersistentVolumeClaims\* (PVC).







\---



\## \*\*4. Seguridad, Continuidad y Automatización\*\* 🔐



\### \*\*4.1. Gestión de Secretos y Configuración\*\* 🤐

\* \*\*Kubernetes Secrets:\*\* Las credenciales de bases de datos y tokens de API no se almacenan en los YAML, sino que se gestionan mediante objetos Secret cifrados en base64.

\* \*\*Ansible Vault:\*\* Cifrado de variables sensibles (contraseñas de sudo, tokens de unión al clúster) dentro del servidor de automatización.



\### \*\*4.2. Plan de Backup y Recuperación (DRP)\*\* 💾

Para mitigar el punto único de fallo del servidor NFS, se han establecido:

\* \*\*Velero:\*\* Herramienta de recuperación ante desastres para realizar backups de los objetos del clúster y snapshots de los volúmenes persistentes.

\* \*\*Proxmox Backup Server (PBS):\*\* Copias de seguridad a nivel de bloque de las máquinas virtuales completas de forma programada.



\### \*\*4.3. Automatización con Ansible\*\* 🤖

La infraestructura se despliega íntegramente mediante Playbooks:

1\.  \*\*Preparación del OS:\*\* Actualización, instalación de dependencias y módulos de kernel.

2\.  \*\*Instalación de K8s:\*\* Despliegue de `kubeadm`, `kubectl` y `kubelet`.

3\.  \*\*Configuración de Red:\*\* Despliegue del CNI y MetalLB.

4\.  \*\*Deploy de Apps:\*\* Despliegue de aplicaciones finales (ej. WordPress + MySQL) validando la persistencia.



\---

<p align="center">

&#x20; <b>Proyecto Integrado de Grado Superior ASIR</b><br>

&#x20; © 2026 - <a href="https://github.com/jobopaK">jobopaK</a>

</p>

