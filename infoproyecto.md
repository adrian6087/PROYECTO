# Proyecto Intermodular (SPRINT)

**Integrantes del grupo:**
* Adrián Gil Alemán
* Magdiel Novoa Suárez

**Curso:** 2ª ASIR

---

## Índice

1. [Propuesta inicial](#propuesta-inicial)
2. [Explicación del problema detectado](#explicación-del-problema-detectado)
3. [Objetivos del proyecto](#objetivos-del-proyecto)
4. [Tecnologías seleccionadas](#tecnologías-seleccionadas)
5. [Arquitectura prevista](#arquitectura-prevista)
6. [Planificación temporal](#planificación-temporal)


## Propuesta inicial

El presente proyecto tiene como finalidad modernizar la gestión de la red del centro educativo mediante una infraestructura virtualizada basada en software de código abierto.

La propuesta consiste en el despliegue de un servidor **Proxmox VE** que alojará una instancia de **pfSense**, centralizando la seguridad y el control de la red para transformar un modelo de administración reactivo en uno proactivo.

Esta arquitectura permitirá resolver dos desafíos críticos para la calidad educativa:

### 1. Auditoría de Rendimiento
Mediante la inspección profunda de paquetes (DPI) y herramientas como **ntopng**, se monitorizará el tráfico en tiempo real. Esto permitirá identificar qué usuarios o aplicaciones saturan el ancho de banda, facilitando la eliminación de cuellos de botella que ralentizan la red general.

### 2. Identificación de Activos y Seguridad
Se implementará un protocolo técnico para la desanonimización de amenazas. Ante la detección de equipos comprometidos (virus) identificados sólo por su MAC, el sistema cruzará datos de red para revelar su Nombre de Host (ej. 301-PC5), permitiendo su localización física y desinfección inmediata.

En definitiva, el proyecto busca implementar una solución escalable, segura y de coste cero en licencias, capaz de garantizar la estabilidad de la conexión y facilitar la respuesta ante incidentes de seguridad.

---

## Explicación del problema detectado

La infraestructura de red actual presenta deficiencias críticas de visibilidad que comprometen tanto la eficiencia operativa como la seguridad del centro. La problemática se divide en dos vectores principales:

### 1. Saturación y "Caja Negra" del Tráfico
La red sufre episodios de alta latencia y lentitud. Debido a la falta de herramientas de monitorización, el tráfico es opaco para el administrador, lo que impide identificar qué equipos específicos están consumiendo abusivamente el ancho de banda. Esto imposibilita una gestión focalizada para liberar recursos y garantizar la conectividad.

### 2. Desconexión entre Identidad Lógica y Física
Se ha detectado malware asociado a direcciones MAC específicas, pero el sistema actual no permite traducir dichas direcciones a un Nombre de Host (ej. 301-PC05). Esta falta de resolución impide al equipo técnico localizar físicamente los dispositivos infectados para proceder a su desinfección inmediata.

En resumen, la administración de la red opera de forma reactiva y ciega, sin capacidad para optimizar el rendimiento ni para neutralizar amenazas de seguridad de manera eficaz.

---

## Objetivos del proyecto

Diseñar e implementar un sistema de monitorización y seguridad perimetral virtualizado sobre Proxmox VE, utilizando pfSense para asegurar la disponibilidad del ancho de banda y garantizar la identificación inequívoca de los dispositivos conectados a la red escolar.

### Objetivos Específicos:

* **Virtualizar la infraestructura de red:** Desplegar un servidor Proxmox VE y configurar una máquina virtual óptima para pfSense.
* **Implementar análisis de tráfico en tiempo real:** Configurar el paquete ntopng para visualizar flujos de datos y detectar consumos anómalos por protocolo.
* **Establecer protocolos de identificación:** Utilizar herramientas de escaneo activo (Nmap) y análisis de tablas ARP/DHCP para resolver direcciones MAC a Nombres de Host legibles.
* **Optimizar el rendimiento de la red:** Detectar y limitar a los equipos que saturen la red injustificadamente.

---

## Tecnologías seleccionadas

Para la ejecución del proyecto se ha optado por una pila tecnológica de **Software Libre (Open Source)**, garantizando la auditabilidad, seguridad y ausencia de costes de licencia:

* **Proxmox VE (Virtual Environment):** Seleccionado como hipervisor de tipo 1 (bare-metal). Permite una gestión eficiente de los recursos de hardware y facilita la creación de snapshots (copias de seguridad) antes de realizar cambios críticos en la configuración de la red.
* **pfSense CE (Community Edition):** Elegido como sistema operativo para el firewall y router virtual. Su base en FreeBSD ofrece gran estabilidad y su gestor de paquetes permite extender su funcionalidad fácilmente.
* **ntopng:** Herramienta de monitorización de tráfico que se integrará en pfSense. Se selecciona por su capacidad de mostrar gráficas históricas y en tiempo real, desglosando el tráfico por aplicación (YouTube, BitTorrent, Web, etc.).
* **Nmap (Network Mapper):** Utilidad de escaneo de seguridad que se utilizará para interrogar a los equipos de la red mediante protocolos NetBIOS y DNS reverso, fundamental para obtener los nombres de los equipos infectados.

---

## Arquitectura prevista

La arquitectura del proyecto se diseñará siguiendo un modelo de virtualización sobre hardware físico, estructurado en las siguientes capas:

1.  **Capa de Hardware (Host):** Un servidor físico con soporte para virtualización (CPU con instrucciones VT-x/AMD-V) y doble interfaz de red (NIC) para separar el tráfico WAN (Internet) del tráfico LAN (Red Escolar).
2.  **Capa de Virtualización (Hipervisor):** Proxmox VE se instalará directamente sobre el hardware, gestionando la memoria, CPU y almacenamiento.
3.  **Capa de Máquina Virtual (Guest):** Se desplegará una MV con pfSense que tendrá asignados, mediante PCI Passthrough o Linux Bridges, los recursos de red necesarios para interceptar y analizar el tráfico.
4.  **Capa de Aplicación:** Dentro de pfSense se ejecutarán los servicios de ntopng (para las estadísticas) y los scripts de diagnóstico para la resolución de nombres.

---

## Planificación temporal

La planificación temporal se organiza en tres etapas alineadas con las prácticas: una fase previa de análisis y despliegue inicial; un bloque central durante las prácticas para la ejecución técnica, configuración y pruebas en entorno real; y una etapa final posterior dedicada exclusivamente al cierre y documentación de la memoria.

### Cronograma (Gantt)

![Diagrama de Gantt del Proyecto](img/planificacion.png)
