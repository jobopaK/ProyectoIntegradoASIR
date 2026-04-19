# **Diseño del Proyecto: Infraestructura de Alta Disponibilidad y Orquestación de Contenedores con Kubernetes sobre Proxmox**

## **1\. Objetivos**

El objetivo principal de este proyecto es diseñar, implementar y documentar una **infraestructura *on-premise* virtualizada y de alta disponibilidad**, capaz de orquestar servicios contenerizados mediante Kubernetes sobre un hipervisor Proxmox. Se busca resolver las problemáticas comunes de los sistemas monolíticos, como los tiempos de inactividad por fallos de hardware y los altos costes del escalado vertical, ofreciendo una solución moderna orientada a microservicios.

Para alcanzar este fin, se han establecido los siguientes **objetivos específicos**, los cuales abarcan desde la configuración física hasta la capa de aplicación:

* **Virtualización robusta:** Configurar Proxmox VE como hipervisor base, permitiendo una gestión flexible de los recursos físicos frente a alternativas propietarias.  
* **Orquestación de contenedores:** Desplegar un clúster de Kubernetes v1.30 (compuesto por 1 nodo Master y 2 Workers) utilizando `containerd` como entorno de ejecución (*runtime*).  
* **Resolución avanzada de red (Networking):** Optimizar la red del clúster resolviendo conflictos específicos del despliegue en entornos *bare-metal* (servidores físicos/locales). Esto incluye la desactivación de IPv6 a nivel de kernel para evitar latencias, y la correcta gestión de los Cgroups del sistema utilizando `systemd`.  
* **Gestión de tráfico externo e interno:** Implementar MetalLB para proveer balanceo de carga en Capa 2 (L2), asignando dinámicamente IPs locales a los servicios. A esto se le suma Nginx Ingress Controller para el enrutamiento de Capa 7 (L7), permitiendo el acceso a las aplicaciones mediante nombres de dominio.  
* **Almacenamiento persistente desacoplado:** Garantizar que los datos de las aplicaciones no se pierdan si un contenedor (Pod) falla, mediante la configuración de un servidor NFS dedicado que provea Volúmenes Persistentes (PV/PVC) independientemente del nodo Worker en el que se ejecute la carga de trabajo.  
* **Automatización e Infraestructura como Código (IaC):** Desarrollar *Playbooks* de Ansible capaces de aprovisionar y configurar nuevos nodos desde cero, garantizando la escalabilidad y consistencia del entorno.  
* **Tolerancia a fallos:** Validar la Alta Disponibilidad (HA) del entorno mediante pruebas controladas de caída de nodos, asegurando que los servicios web sigan respondiendo ante incidencias.

Como **objetivo deseable**, y condicionado al tiempo tras asegurar la estabilidad del núcleo, se plantea la integración de un *stack* de monitorización utilizando Prometheus y Grafana para visualizar las métricas del clúster.

---

## **2\. Arquitectura del Sistema**

La arquitectura del sistema está diseñada bajo un enfoque de **microservicios y alta disponibilidad**, dividiendo la infraestructura en capas bien definidas que interactúan entre sí. Esta separación garantiza la modularidad, facilita el mantenimiento y permite el escalado horizontal.

### **2.1. Infraestructura Física e Hipervisor**

La base del proyecto reside en un servidor dedicado físico (*Host*) sobre el cual se ha instalado **Proxmox VE**. La elección de Proxmox frente a otras soluciones como VMware ESXi se justifica por su naturaleza Open Source y su enorme flexibilidad para gestionar tanto máquinas virtuales (KVM) como contenedores LXC, actuando como una base de virtualización sólida y sin costes de licenciamiento.

### **2.2. Topología Lógica (Máquinas Virtuales)**

Sobre Proxmox, se ha desplegado una topología lógica utilizando **Ubuntu Server 24.04 LTS** como sistema operativo base para todas las máquinas, garantizando la compatibilidad con las herramientas modernas de Linux. El direccionamiento de red está configurado de forma estática (salvo el cliente) y se distribuye de la siguiente manera:

* **K8s Control Plane (192.168.1.110):** Actúa como el cerebro del clúster de Kubernetes. Ejecuta los componentes críticos: el API Server (punto de entrada de comandos), el `etcd` (base de datos clave-valor con el estado del clúster) y el *Scheduler* (decide dónde se ejecutan los Pods).  
* **K8s Worker 1 (192.168.1.111) y Worker 2 (192.168.1.112):** Son los nodos de trabajo donde se ejecutan realmente las aplicaciones contenerizadas. Disponer de dos nodos garantiza la redundancia requerida para cumplir el requisito de Alta Disponibilidad.  
* **Servidor Ansible (192.168.1.115):** Máquina dedicada a la automatización mediante Infraestructura como Código (IaC).  
* **Servidor NFS (192.168.1.116):** Nodo centralizado que exporta directorios en red para el almacenamiento persistente de las bases de datos y aplicaciones.  
* **Cliente (DHCP):** Un entorno Linux de escritorio simulando al usuario final que consume los servicios.

### **2.3. Stack Tecnológico de Orquestación y Red**

A nivel lógico y de software, la arquitectura de Kubernetes v1.30 hace uso de herramientas específicas para entornos locales (*on-premise*):

* **Runtime y CNI:** Los nodos utilizan `containerd` configurado con el *driver* de Cgroup de `systemd`, lo que asegura una mejor integración con el kernel de Ubuntu y previene caídas del proceso `kubelet`. La red de *overlay* (comunicación entre Pods) la proporciona el CNI **Flannel**, el cual se comunica a través del módulo del kernel `br_netfilter`.  
* **Tráfico Externo:** En entornos en la nube, servicios como AWS o Google Cloud proveen balanceadores de carga automáticos. En este entorno *bare-metal*, se emplea **MetalLB** configurado en modo Capa 2 (con el modo `strictARP: true` activado) para anunciar IPs de la red local (ej. 192.168.1.200) y asignarlas a los servicios. Este tráfico es recogido por el **Nginx Ingress Controller**, que analiza las cabeceras HTTP y redirige las peticiones al contenedor correspondiente según el nombre de dominio solicitado.

---

## **3\. Diagrama (Red / Servidores)**

El diseño del sistema se ha modelado visualmente mediante un diagrama estructurado en bloques lógicos, los cuales definen el ciclo de vida, la automatización y la jerarquía del tráfico en el entorno. A continuación, se detalla en profundidad cada uno de los bloques y su flujo de comunicación:

1. **Bloque de Automatización (Automation):** Es el origen de la configuración. Una máquina virtual con Ansible (`Ansible node automation vm`) utiliza un inventario dinámico o estático (`hosts.ini`) para dirigir y aplicar la configuración sobre el clúster de Kubernetes. Ejecuta de forma secuencial dos conjuntos de instrucciones principales: el *playbook* de pre-requisitos (`pre-requisitos.yml`), que prepara las máquinas base, y el *playbook* de configuración de nodos (`Node setup ansible playbook`), que orquesta el despliegue de las dependencias.  
2. **Bloque Base (Foundation):** Representa la infraestructura virtual. El hipervisor `Proxmox VE` actúa como anfitrión (*host*) alojando la capa de máquinas virtuales (`Ubuntu VMs vm layer`). Estas máquinas virtuales son preparadas por Ansible y se dividen conceptualmente en dos grupos que formarán el clúster: el conjunto de trabajadores (`Workers k8s vm set`) y la máquina de control (`Control plane k8s vm`).  
3. **Bloque del Clúster (Cluster):** Este es el núcleo operativo de la red de contenedores. El plano de control (*Control plane*) arranca el sistema de red interna utilizando **Flannel CNI** (`kube-flannel.yml`), creando una red superpuesta (overlay) que conecta directamente a los Workers. Para la salida y entrada de red hacia el exterior, se expone el tráfico a través de **MetalLB** (el balanceador de carga bare-metal), el cual es alimentado y configurado por los ficheros de configuración de balanceo (`LB config`). Finalmente, MetalLB entrega el tráfico de red al **Nginx Ingress Controller** (`deploy.yaml`), que se encargará del enrutamiento final a nivel de aplicación.  
4. **Bloque de Cargas de Trabajo (Workloads):** Representa las aplicaciones productivas. El tráfico HTTP/HTTPS gestionado por el Ingress Controller es enrutado hacia dos aplicaciones principales de demostración técnica: una pila de aplicación de WordPress (`wordpress.yml`) y una aplicación web de prueba (`web-demo.yml`).  
5. **Bloque de Almacenamiento y Operaciones (Storage & Ops):** Para evitar la pérdida de estado en la aplicación de WordPress, las cargas de trabajo (*Workloads*) persisten sus datos y dependen directamente del aprovisionador de almacenamiento NFS (`NFS storage provisioner`), que a su vez escribe físicamente la información en la máquina virtual dedicada al Servidor NFS (`NFS server storage vm`). Paralelamente, el bloque de operaciones incluye documentación técnica y *tokens* vitales (como `token_k8s.txt`) que permiten a los Workers unirse al clúster de forma segura.

![Diagrama](./images/Diagrama_small.png)

---

## **4\. Plan de Trabajo**

El desarrollo de este proyecto se ha estructurado utilizando una metodología **Kanban** gestionada a través de *GitHub Projects*. Dado que el estudiante asume la totalidad de los roles (Administrador de Sistemas, Ingeniero DevOps y Documentador), Kanban resulta ideal para visualizar el flujo de trabajo sin la sobrecarga burocrática de otras metodologías ágiles como Scrum.

El flujo de trabajo se ha dividido en un *Roadmap* secuencial compuesto por 10 fases iterativas que garantizan la visibilidad de red entre los componentes y la superación de retos técnicos en un entorno *bare-metal*:

* **Fase 1 y 2 (Análisis y Diseño Inicial):** Definición de la arquitectura, instalación del servidor físico con Proxmox VE, gestión de repositorios, creación de plantillas de Ubuntu Server y configuración manual de red estática con Netplan (corrigiendo explícitamente las rutas por defecto `routes: to: default` para no perder salida a internet).  
* **Fase 3 (Despliegue del Core):** Inicialización del *Control Plane* de Kubernetes v1.30. En esta fase se superaron importantes obstáculos técnicos o *troubleshooting*, destacando la desactivación forzosa del protocolo IPv6 en el kernel y el forzado de vinculación de las direcciones IPv4 de los nodos mediante el parámetro `--node-ip` en la configuración de `kubelet`. Asimismo, se solventaron los errores de `CrashLoopBackOff` cargando el módulo `br_netfilter` para estabilizar el plugin CNI Flannel y realizando la unión exitosa de los Workers.  
* **Fase 4 y 5 (Redes Externas):** Implementación de la capa de acceso a los servicios. Despliegue del balanceador de carga L2, MetalLB, e instalación del controlador Nginx Ingress para gestionar peticiones por nombre de dominio en los puertos 80/443.  
* **Fase 6 (Persistencia de Datos):** Configuración del Servidor NFS externo y su respectivo aprovisionador dinámico en Kubernetes, asegurando que aplicaciones críticas conserven su estado.  
* **Fase 7 y 8 (Automatización y Aplicación):** Desarrollo de todo el código de automatización (Ansible) logrando la infraestructura como código (IaC). Despliegue de aplicaciones reales utilizando *Dockerfiles* personalizados y validando la lectura/escritura en volúmenes persistentes de bases de datos.  
* **Fase 9 y 10 (Validación y Monitorización):** La última etapa (actualmente en curso/pendientes de confirmación final) involucra las pruebas de estrés simulando caídas de nodos (*Alta Disponibilidad*) para corroborar que los Pods se replanifican en nodos sanos automáticamente. Opcionalmente, se finalizará con la puesta en marcha de un panel de observabilidad utilizando Prometheus y Grafana.

