Arquitectura de Seguridad, Diagrama Mermaid y Modelo STRIDE

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



Categoría STRIDEAmenaza IdentificadaComponente AfectadoImpactoMitigación PropuestaSpoofing(Suplantación)Robo o falsificación de tokens de sesión (JWT) para suplantar a un usuario legítimo.Cliente / API BackendAltoImplementación de cookies con atributos HttpOnly, Secure y SameSite=Strict, junto con Autenticación de Doble Factor (2FA).Tampering(Manipulación)Inyección SQL (SQLi) o alteración de parámetros HTTP en las peticiones enviadas al servidor.Web Server / Base de DatosCríticoUso estricto de consultas preparadas (Prepared Statements), validación y sanitización de entradas en el servidor.Repudiation(Repudio)Un usuario ejecuta acciones maliciosas o cambios críticos y niega haberlos realizado por falta de trazabilidad.Sistema CompletoMedioRegistro inmutable de auditoría (Logs) firmado criptográficamente y enviado en tiempo real al servidor SIEM.Information Disclosure(Fuga de Datos)Intercepción de credenciales o tráfico en texto plano por uso de protocolos inseguros en la red.Red / ClienteAltoConfiguración obligatoria de cifrado TLS 1.3/HTTPS, HSTS habilitado y cifrado de datos en reposo (AES-256 en BD).Denial of Service(DoS / DDoS)Saturación de recursos del servidor mediante peticiones masivas de fuerza bruta al endpoint de login.WAF / Web ServerAltoConfiguración de límites de tasa (Rate Limiting) en el WAF, reglas DDoS en Cloudflare y bloqueo dinámico de IPs.Elevation of Privilege(Elevación de Privilegios)Un usuario estándar modifica parámetros o rutas HTTP para acceder a funciones administrativas sin autorización.API BackendCríticoVerificación del Control de Acceso Basado en Roles (RBAC) en cada endpoint del lado del servidor antes de procesar la solicitud.
