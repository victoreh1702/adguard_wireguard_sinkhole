# AdGuard Home DNS Sinkhole & WireGuard VPN Integration 🛡️🌐

Un servidor DNS privado y sumidero de publicidad (*sinkhole*) desplegado sobre una infraestructura cloud (Oracle Cloud) e integrado de forma nativa con una red privada virtual WireGuard. Diseñado para bloquear anuncios, rastreadores y malware a nivel de red en todos los dispositivos conectados al túnel, garantizando privacidad absoluta y un consumo de recursos mínimo.

## 🚀 Características Principales

*   **Bloqueo de Publicidad a Nivel de Red:** Intercepta y bloquea peticiones DNS de banners, pop-ups, scripts de rastreo y dominios maliciosos antes de que lleguen a los dispositivos del usuario.
*   **Integración Cifrada con WireGuard:** Todo el tráfico DNS de los *peers* (móviles, ordenadores, smart TVs) se canaliza de forma privada a través de la IP interna de la VPN (`X.X.X.X`).
*   **Arquitectura Privada y Segura:** El panel de administración web y el servidor DNS están confinados exclusivamente a la interfaz de la VPN (`wg0`), evitando exposiciones innecesarias a internet mediante restricciones estrictas en el cortafuegos (`iptables`).
*   **Eficiencia Cloud:** Configurado en una instancia Always Free de Oracle Cloud (Ubuntu), operando con un impacto de CPU y RAM imperceptible.

## 🛠 Stack Tecnológico

*   **Servidor DNS / Bloqueador:** AdGuard Home
*   **Redes y Túneles:** Protocolo WireGuard (`wg0`)
*   **Sistema Operativo:** Linux Ubuntu 24.04 LTS (Oracle Cloud Infrastructure)
*   **Seguridad / Red:** Iptables, Netfilter-persistent

## 📋 Pasos de Despliegue e Instalación

Para replicar este entorno en un servidor Linux/Ubuntu remoto:

1. **Instalación de AdGuard Home:**
   Ejecuta el script oficial de instalación automatizada para configurar el binario en `/opt/AdGuardHome`:
   ```bash
   curl -s -S -L [https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh](https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh) | sh -s -- -v

2. **Configuración de Interfaces (Asistente Web):**
   * Accede al panel inicial (puedes usar un túnel SSH temporal `-L 3000:localhost:3000` para la configuración inicial).
   * Vincula la interfaz web de administración **exclusivamente** a la IP del túnel WireGuard (`X.X.X.X`) en el puerto `80`.
   * Vincula el servidor DNS **exclusivamente** a la IP `X.X.X.X` en el puerto por defecto `53`.

3. **Configuración del Cortafuegos (Firewall):**
   Aplica las reglas en `iptables` para permitir el tráfico de red exclusivamente a través de la interfaz de la VPN y hazlo persistente ante reinicios:
   ```bash
   sudo iptables -I INPUT -i wg0 -j ACCEPT
   sudo netfilter-persistent save

4. **Configuración de los Clientes (Peers):**
   Edita los parámetros de red en tus dispositivos (móviles, Smart TVs, etc.) o en el archivo de configuración `.conf` de WireGuard para redirigir las peticiones al sumidero DNS:
   ```ini
   [Interface]
   ...
   DNS = X.X.X.X