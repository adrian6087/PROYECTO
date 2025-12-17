<div align="center">

# 🚀 Proyecto Intermodular: Mejora Red Escolar (Radar Escolar)

![Status](https://img.shields.io/badge/Estado-En_Desarrollo-yellow?style=for-the-badge)
![Curso](https://img.shields.io/badge/Curso-2%C2%BA_ASIR-blue?style=for-the-badge)

</div>

---

### 👥 Equipo de Trabajo

| Integrante |
| :--- |
| **Adrián Gil Alemán** |
| **Magdiel Novoa Suárez** |

---

## 📑 Índice

1. [🎯 Propuesta inicial](#-propuesta-inicial)
2. [⚠️ Explicación del problema](#-explicación-del-problema-detectado)
3. [🏆 Objetivos del proyecto](#-objetivos-del-proyecto)
4. [🛠️ Tecnologías seleccionadas](#-tecnologías-seleccionadas)
5. [🏗️ Arquitectura prevista](#-arquitectura-prevista)
6. [📅 Planificación temporal](#-planificación-temporal)

---

## 🎯 Propuesta inicial

El presente proyecto tiene como finalidad modernizar la gestión de la red del centro educativo mediante una infraestructura virtualizada basada en software de código abierto.

> **La Solución:** Despliegue de un servidor **Proxmox VE** que alojará una instancia de **pfSense**, centralizando la seguridad y el control de la red para transformar un modelo de administración reactivo en uno proactivo.

Esta arquitectura permitirá resolver dos desafíos críticos para la calidad educativa:

### 1. 📊 Auditoría de Rendimiento
Mediante la inspección profunda de paquetes (DPI) y herramientas como **ntopng**, se monitorizará el tráfico en tiempo real. Esto permitirá identificar qué usuarios o aplicaciones saturan el ancho de banda, facilitando la eliminación de cuellos de botella que ralentizan la red general.

### 2. 🛡️ Identificación de Activos y Seguridad
Se implementará un protocolo técnico para la desanonimización de amenazas. Ante la detección de equipos comprometidos (virus) identificados sólo por su MAC, el sistema cruzará datos de red para revelar su Nombre de Host (ej. `301-PC5`), permitiendo su localización física y desinfección inmediata.

En definitiva, el proyecto busca implementar una solución escalable, segura y de coste cero en licencias.

---

## ⚠️ Explicación del problema detectado

La infraestructura de red actual presenta deficiencias críticas de visibilidad. La problemática se divide en dos vectores principales:

| Vector | Descripción del Problema |
| :--- | :--- |
| **Saturación (Caja Negra)** | La red sufre latencia, pero el tráfico es opaco. No se puede identificar qué equipos consumen abusivamente el ancho de banda, impidiendo liberar recursos. |
| **Anonimato de Amenazas** | Se detecta malware por MAC, pero no se puede traducir a un Nombre de Host (ej. `301-PC05`). Esto impide localizar físicamente el dispositivo para desinfectarlo. |

> En resumen, la administración actual es **reactiva y ciega**, sin capacidad para optimizar rendimiento ni neutralizar amenazas eficazmente.

---

## 🏆 Objetivos del proyecto

Diseñar e implementar un sistema de monitorización y seguridad perimetral virtualizado sobre Proxmox VE, utilizando pfSense para asegurar la disponibilidad del ancho de banda y garantizar la identificación inequívoca de los dispositivos.

### Objetivos Específicos:

* ✅ **Virtualizar la infraestructura:** Desplegar servidor Proxmox VE y VM óptima para pfSense.
* ✅ **Análisis en tiempo real:** Configurar **ntopng** para visualizar flujos y detectar consumos anómalos.
* ✅ **Protocolos de identificación:** Usar **Nmap** y tablas ARP/DHCP para resolver MAC a Nombres de Host.
* ✅ **Optimización:** Detectar y limitar equipos que saturen la red injustificadamente (QoS).

---

## 🛠️ Tecnologías seleccionadas

Pila tecnológica de **Software Libre (Open Source)** para garantizar auditabilidad y coste cero:

<div align="center">

![Proxmox](https://img.shields.io/badge/Proxmox_VE-E57000?style=for-the-badge&logo=proxmox&logoColor=white)
![pfSense](https://img.shields.io/badge/pfSense-2C3E50?style=for-the-badge&logo=pfsense&logoColor=white)
![ntopng](https://img.shields.io/badge/ntopng-Monitoreo-blue?style=for-the-badge)
![Nmap](https://img.shields.io/badge/Nmap-Seguridad-green?style=for-the-badge)

</div>

* **Proxmox VE (Hipervisor Tipo 1):** Gestión eficiente de recursos y snapshots de seguridad.
* **pfSense CE (Firewall/Router):** Basado en FreeBSD, ofrece estabilidad y gestión de paquetes.
* **ntopng:** Monitorización de tráfico histórica y en tiempo real (YouTube, BitTorrent, etc.).
* **Nmap:** Escaneo de seguridad (NetBIOS/DNS reverso) para identificar equipos infectados.

---

## 🏗️ Arquitectura prevista

El diseño sigue un modelo de virtualización sobre hardware físico en 4 capas:

1.  **🖥️ Capa de Hardware (Host):** Servidor físico con virtualización (VT-x/AMD-V) y doble NIC (WAN/LAN).
2.  **🎛️ Capa de Virtualización (Hipervisor):** **Proxmox VE** instalado en bare-metal gestionando CPU/RAM.
3.  **☁️ Capa de Máquina Virtual (Guest):** MV con **pfSense** utilizando *PCI Passthrough* o *Linux Bridges* para interceptar tráfico.
4.  **📈 Capa de Aplicación:** Servicios internos de **ntopng** y scripts de diagnóstico.

---

## 📅 Planificación temporal

El proyecto se organiza en tres etapas: análisis inicial, ejecución técnica durante las prácticas, y cierre/documentación.

### Cronograma (Gantt)

<div align="center">
  <img src="img/planificacion.png" alt="Diagrama de Gantt del Proyecto" width="100%">
  <br>
  <em>Figura 1: Planificación detallada del SPRINT 1</em>
</div>
