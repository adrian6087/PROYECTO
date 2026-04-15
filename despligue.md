# Reporte de Despliegue: Proyecto de Monitorización y Seguridad en Red Escolar

**Autores:** Equipo de Administración de Redes
**Entorno de Laboratorio:** Proxmox VE (Nested), pfSense 2.4.5, Contenedores LXC.

---

## 1. Introducción y Arquitectura Base
El objetivo de este proyecto es simular la red de un colegio para detectar dos anomalías específicas: un equipo infectado por malware (del cual solo conocemos su dirección MAC) y un equipo que está generando un cuello de botella saturando el ancho de banda (el PC "Gamer").

Para optimizar recursos, optamos por una arquitectura virtualizada:
* **Hipervisor:** Proxmox VE 9 (virtualizado dentro de VirtualBox).
* **Enrutador/Firewall Core:** Máquina Virtual con pfSense 2.4.5.
* **Nodos de las aulas (PCs):** Contenedores LXC (Linux Containers) muy ligeros para no saturar la RAM del servidor.
* **Topología Capa 2:** Los routers de las aulas se configuraron en modo "Bridge" (puente transparente) para evitar el NAT y permitir que pfSense vea las direcciones MAC reales de cada alumno.

---

## 2. Preparación del Entorno (Proxmox en VirtualBox)
Dado que ejecutamos Proxmox dentro de VirtualBox (Virtualización Anidada), tuvimos que realizar ajustes de red a nivel de hipervisor base para que el tráfico fluyera correctamente hacia el mundo exterior.

1.  Se configuraron dos adaptadores de red en VirtualBox para la máquina de Proxmox:
    * **Adaptador 1 (Puente / Bridged):** Conectado a la tarjeta física para salida a Internet.
    * **Adaptador 2 (Red Interna):** Llamada `AULAS`, funciona como nuestro switch virtual LAN.
2.  **Fix Crítico:** En ambos adaptadores habilitamos el **"Modo Promiscuo: Permitir todo"**. Sin esto, VirtualBox bloqueaba los paquetes de pfSense por seguridad (ya que llevaban una MAC distinta a la de Proxmox).

A nivel de Proxmox, mapeamos estas conexiones en dos *Linux Bridges*:
* `vmbr0` -> WAN (Internet)
* `vmbr1` -> LAN (Red de aulas)

---

## 3. Despliegue del Firewall Core (pfSense)
Se creó una Máquina Virtual completa para hospedar el "cerebro" de la red.

* **Recursos:** 2 Zócalos / 2 Núcleos (Tipo: x86-64-v2-AES), 2GB de RAM.
* **Interfaces:** Se asignaron las tarjetas asociadas a `vmbr0` y `vmbr1`.
* **Configuración inicial (Consola):**
    * Habilitamos DHCP en la interfaz WAN (`vtnet0`) para obtener salida a internet.
    * Asignamos una IP estática a la LAN (`vtnet1`): `192.168.50.1/24`.
    * Configuramos el servicio DHCP local para entregar IPs dinámicas a los alumnos en ese rango.

---

## 4. Simulación de los Nodos (Aulas)
Para simular el "Aula 301" sin asfixiar la RAM del servidor, desplegamos Contenedores LXC basados en plantillas de Alpine Linux y Ubuntu (consumiendo apenas 512MB de RAM cada uno).

**Configuración especial del objetivo (El equipo infectado):**
Para poder probar la búsqueda del virus, creamos el contenedor `301-PC02` y forzamos una dirección MAC estática conocida desde Proxmox (ej: `08:00:27:E8:24:3E`). Esto simula la "firma de red" del equipo infectado.

**Equipo Administrador:**
Se desplegó una VM adicional con entorno gráfico (Ubuntu Desktop/Windows) conectada a `vmbr1` para acceder a la WebGUI de pfSense mediante la IP `192.168.50.1` y administrar las reglas visualmente.

---

## 5. Pruebas y Evidencias Técnicas (Desde la WebGUI de pfSense)

Para esta fase, decidimos centralizar la administración y usar directamente las herramientas nativas que nos ofrece pfSense, demostrando que un buen firewall tiene todo lo necesario para auditar la red.

### Escenario A: Detección del equipo infectado por MAC (Tabla ARP)
El objetivo era localizar en qué IP dinámica se escondía la máquina infectada (el `301-PC02`) basándonos únicamente en su dirección física (MAC), simulando un reporte del antivirus central.

1. **Uso de la Tabla ARP Nativa:** En lugar de hacer barridos a ciegas con Nmap desde un PC, entramos directamente a la interfaz web de pfSense y fuimos a **Diagnostics > ARP Table** (Diagnósticos > Tabla ARP). 
2. pfSense mantiene un registro en tiempo real de quién está "hablando" en la red. Simplemente usamos el buscador del navegador (`Ctrl + F`) para buscar la MAC específica que le habíamos asignado al equipo con el virus en Proxmox.
3. Inmediatamente ubicamos la fila correspondiente, revelando qué IP dinámica le había asignado el servidor DHCP en ese exacto momento, lo que nos da el objetivo claro para bloquearle el internet.

![Captura de la Tabla ARP en pfSense mostrando la MAC y la IP del equipo infectado]()

### Escenario B: Detección del "Cuello de Botella" (Traffic Graph)
Inicialmente pensábamos usar Nmap, pero como confirmamos que no analiza flujos de descarga en vivo, y no pudimos instalar `ntopng` por la versión antigua del pfSense, acudimos al "Radar" nativo del firewall.

![Captura de la Tabla Traffic en pfSense el consumo por equipos]()

