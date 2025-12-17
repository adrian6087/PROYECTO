# Proyecto Intermodular (SPRINT)

**Integrantes del grupo:**
* Adrián Gil Alemán
* Magdiel Novoa Suárez

**Curso:** 2ª ASIR

---

## Índice

| Sección | Página |
| :--- | :--- |
| Propuesta inicial | 3 |
| Explicación del problema detectado | 3 |
| Objetivos del proyecto | 4 |
| Tecnologías seleccionadas | 4 |
| Arquitectura prevista | 5 |
| Planificación temporal | 5 |

---

## Propuesta inicial

[cite_start]El presente proyecto tiene como finalidad modernizar la gestión de la red del centro educativo mediante una infraestructura virtualizada basada en software de código abierto[cite: 8]. [cite_start]La propuesta consiste en el despliegue de un servidor **Proxmox VE** que alojará una instancia de **pfSense**, centralizando la seguridad y el control de la red para transformar un modelo de administración reactivo en uno proactivo[cite: 9].

[cite_start]Esta arquitectura permitirá resolver dos desafíos críticos para la calidad educativa[cite: 10]:

### 1. Auditoría de Rendimiento
[cite_start]Mediante la inspección profunda de paquetes (DPI) y herramientas como **ntopng**, se monitorizará el tráfico en tiempo real[cite: 12]. [cite_start]Esto permitirá identificar qué usuarios o aplicaciones saturan el ancho de banda, facilitando la eliminación de cuellos de botella que ralentizan la red general[cite: 13].

### 2. Identificación de Activos y Seguridad
[cite_start]Se implementará un protocolo técnico para la desanonimización de amenazas[cite: 15]. [cite_start]Ante la detección de equipos comprometidos (virus) identificados sólo por su MAC, el sistema cruzará datos de red para revelar su Nombre de Host (ej. 301-PC5), permitiendo su localización física y desinfección inmediata[cite: 16].

[cite_start]En definitiva, el proyecto busca implementar una solución escalable, segura y de coste cero en licencias, capaz de garantizar la estabilidad de la conexión y facilitar la respuesta ante incidentes de seguridad[cite: 17].

---

## Explicación del problema detectado

[cite_start]La infraestructura de red actual presenta deficiencias críticas de visibilidad que comprometen tanto la eficiencia operativa como la seguridad del centro[cite: 19]. [cite_start]La problemática se divide en dos vectores principales[cite: 20]:

### 1. Saturación y "Caja Negra" del Tráfico
La red sufre episodios de alta latencia y lentitud. [cite_start]Debido a la falta de herramientas de monitorización, el tráfico es opaco para el administrador, lo que impide identificar qué equipos específicos están consumiendo abusivamente el ancho de banda[cite: 22]. [cite_start]Esto imposibilita una gestión focalizada para liberar recursos y garantizar la conectividad[cite: 23].

### 2. Desconexión entre Identidad Lógica y Física
[cite_start]Se ha detectado malware asociado a direcciones MAC específicas, pero el sistema actual no permite traducir dichas direcciones a un Nombre de Host (ej. 301-PC05)[cite: 25]. [cite_start]Esta falta de resolución impide al equipo técnico localizar físicamente los dispositivos infectados para proceder a su desinfección inmediata[cite: 26].

[cite_start]En resumen, la administración de la red opera de forma reactiva y ciega, sin capacidad para optimizar el rendimiento ni para neutralizar amenazas de seguridad de manera eficaz[cite: 27].

---

## Objetivos del proyecto

[cite_start]Diseñar e implementar un sistema de monitorización y seguridad perimetral virtualizado sobre Proxmox VE, utilizando pfSense para asegurar la disponibilidad del ancho de banda y garantizar la identificación inequívoca de los dispositivos conectados a la red escolar[cite: 29].

### Objetivos Específicos:

* [cite_start]**Virtualizar la infraestructura de red:** Desplegar un servidor Proxmox VE y configurar una máquina virtual óptima para pfSense[cite: 31, 32].
* [cite_start]**Implementar análisis de tráfico en tiempo real:** Configurar el paquete ntopng para visualizar flujos de datos y detectar consumos anómalos por protocolo[cite: 33, 34].
* [cite_start]**Establecer protocolos de identificación:** Utilizar herramientas de escaneo activo (Nmap) y análisis de tablas ARP/DHCP para resolver direcciones MAC a Nombres de Host legibles[cite: 35, 36].
* [cite_start]**Optimizar el rendimiento de la red:** Detectar y limitar a los equipos que saturen la red injustificadamente[cite: 37, 38].

---

## Tecnologías seleccionadas

[cite_start]Para la ejecución del proyecto se ha optado por una pila tecnológica de **Software Libre (Open Source)**, garantizando la auditabilidad, seguridad y ausencia de costes de licencia[cite: 40, 41]:

* **Proxmox VE (Virtual Environment):** Seleccionado como hipervisor de tipo 1 (bare-metal). [cite_start]Permite una gestión eficiente de los recursos de hardware y facilita la creación de snapshots (copias de seguridad) antes de realizar cambios críticos en la configuración de la red[cite: 42, 43].
* **pfSense CE (Community Edition):** Elegido como sistema operativo para el firewall y router virtual. [cite_start]Su base en FreeBSD ofrece gran estabilidad y su gestor de paquetes permite extender su funcionalidad fácilmente[cite: 44, 45].
* **ntopng:** Herramienta de monitorización de tráfico que se integrará en pfSense. [cite_start]Se selecciona por su capacidad de mostrar gráficas históricas y en tiempo real, desglosando el tráfico por aplicación (YouTube, BitTorrent, Web, etc.)[cite: 46, 47].
* [cite_start]**Nmap (Network Mapper):** Utilidad de escaneo de seguridad que se utilizará para interrogar a los equipos de la red mediante protocolos NetBIOS y DNS reverso, fundamental para obtener los nombres de los equipos infectados[cite: 48].

---

## Arquitectura prevista

[cite_start]La arquitectura del proyecto se diseñará siguiendo un modelo de virtualización sobre hardware físico, estructurado en las siguientes capas[cite: 50]:

1.  [cite_start]**Capa de Hardware (Host):** Un servidor físico con soporte para virtualización (CPU con instrucciones VT-x/AMD-V) y doble interfaz de red (NIC) para separar el tráfico WAN (Internet) del tráfico LAN (Red Escolar)[cite: 51].
2.  [cite_start]**Capa de Virtualización (Hipervisor):** Proxmox VE se instalará directamente sobre el hardware, gestionando la memoria, CPU y almacenamiento[cite: 52].
3.  [cite_start]**Capa de Máquina Virtual (Guest):** Se desplegará una MV con pfSense que tendrá asignados, mediante PCI Passthrough o Linux Bridges, los recursos de red necesarios para interceptar y analizar el tráfico[cite: 53].
4.  [cite_start]**Capa de Aplicación:** Dentro de pfSense se ejecutarán los servicios de ntopng (para las estadísticas) y los scripts de diagnóstico para la resolución de nombres[cite: 54].

---

## Planificación temporal

[cite_start]La planificación temporal se organiza en tres etapas alineadas con las prácticas: una fase previa de análisis y despliegue inicial; un bloque central durante las prácticas para la ejecución técnica, configuración y pruebas en entorno real; y una etapa final posterior dedicada exclusivamente al cierre y documentación de la memoria[cite: 56, 57, 58].

### Cronograma (Gantt)

![Diagrama de Gantt del Proyecto](imagenes/gantt_chart.png)

* [cite_start]**Auditoría de Hardware:** 17 de Diciembre[cite: 67, 68].
* [cite_start]**Despliegue del Hipervisor:** 20 - 26 de Enero[cite: 70, 71].
* [cite_start]**Instalación de pfSense:** 31 de Enero - 2 de Febrero[cite: 72, 73].
* [cite_start]**Configuración de Red:** 10 - 26 de Febrero[cite: 74, 75].
* [cite_start]**Integración de ntopng:** 20 - 26 de Febrero[cite: 76, 77].
* [cite_start]**Gestión de Ancho de Banda:** 28 de Febrero - 2 de Marzo[cite: 78, 79].
* [cite_start]**Identificación de Hosts:** 8 - 26 de Marzo[cite: 80, 81].
* [cite_start]**Respuesta a Incidentes:** 15 - 26 de Marzo[cite: 82, 83].
* [cite_start]**Validación y Tuning:** 31 de Marzo - 2 de Abril[cite: 84, 85].
* [cite_start]**Documentación Final:** 16 - 26 de Abril[cite: 86, 87].
