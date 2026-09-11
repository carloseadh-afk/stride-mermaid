# Práctica 2: Arquitectura de Seguridad, Diagrama Mermaid y Modelo STRIDE

Este repositorio contiene la documentación técnica, el diagrama de flujo de datos y el análisis de amenazas basado en el modelo **STRIDE** para el **Escenario 1** (Aplicación Web Segura y Red Empresarial).

---

## Descripción del Escenario 1

El sistema consiste en un entorno web transaccional expuesto a Internet donde los clientes se autentican para acceder a servicios backend. La arquitectura implementa controles de seguridad perimetrales y monitoreo centralizado.

### Componentes de la Arquitectura
1. **Cliente Web / Navegador:** Origen de las peticiones HTTP/HTTPS del usuario.
2. **WAF / Firewall:** Filtro perimetral contra ataques web comunes (OWASP Top 10) y mitigación DDoS.
3. **Servidor Web / API Backend:** Procesador de lógica de negocio y gestión de sesiones.
4. **Base de Datos (DB):** Almacenamiento persistente de credenciales cifradas y datos sensibles.
5. **Servidor de Logs / SIEM:** Sistema centralizado e inmutable de auditoría y monitoreo de eventos.

---

## Diagrama de Flujo del Sistema (Mermaid)

El siguiente diagrama detalla la secuencia de interacción entre los componentes del Escenario 1:

```mermaid
graph TD
    subgraph Zona Externa
        User[Cliente / Navegador Web]
    end

    subgraph Perímetro de Seguridad
        WAF[Firewall de Aplicación Web - WAF]
    end

    subgraph Red Interna / DMZ
        WebServer[Servidor Web / API Backend]
    end

    subgraph Zona de Datos Segura
        DB[(Base de Datos SQL)]
        SIEM[Servidor SIEM / Syslog]
    end

    User -->|1. Petición HTTPS / Login| WAF
    WAF -->|2. Inspección y Filtrado de Tráfico| WebServer
    WebServer -->|3. Consulta de Credenciales| DB
    DB -->|4. Respuesta con Datos de Usuario| WebServer
    WebServer -->|5. Emisión de Token JWT Firmado| User
    WebServer -->|6. Envío de Registros de Auditoría| SIEM
