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


## 2. Matriz de Amenazas (Metodología STRIDE)

Para analizar los riesgos en las fronteras de confianza, aplicamos el modelo STRIDE (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege).

| ID | Componente / Flujo afectado | Amenaza (STRIDE) | Descripción del Riesgo | Mitigación Propuesta | Estado |
| :--- | :--- | :--- | :--- | :--- | :--- |
| *T-01* | Flujo 1 (Usuario -> API) | *S*poofing (Suplantación) | Un atacante intercepta la red y suplanta la identidad del usuario enviando credenciales falsas. | Implementar HTTPS obligatorio con TLS 1.3 y cookies seguras (HttpOnly, Secure). | 🟢 Mitigado |
| *T-02* | Flujo 2 (API -> BD) | *Tampering (Alteración) | El usuario inyecta código malicioso en el formulario de login para alterar la consulta SQL (SQL Injection*). | Uso estricto de ORM (como Prisma o Sequelize) y consultas preparadas (Prepared Statements). | 🟢 Mitigado |
| *T-03* | Componente: Base de Datos | *Information Disclosure | Acceso directo no autorizado a la base de datos que expone las contraseñas de los usuarios. | Encriptar las contraseñas usando **Argon2id* o *bcrypt* antes de guardarlas. Encriptar la BD en reposo. | 🟢 Mitigado |
| *T-04* | Flujo 1 (Usuario -> API) | *Denial of Service | Un botnet inunda la API con millones de peticiones HTTP POST de login, saturando el servidor de red. | Configurar un **Rate Limiter* en el API Gateway (máximo 5 peticiones de login por minuto por IP). | 🟡 En Progreso |
| *T-05* | Flujo 5 (Admin -> BD) | *Elevation of Privilege | Un atacante local accede al puerto SSH de la base de datos e intenta adivinar la contraseña por fuerza bruta. | Cerrar el puerto SSH al público (0.0.0.0/0). Solo permitir accesos mediante una **VPN* empresarial o Bastion Host. | ❌ Pendiente |

## 3. Lista de Control de Mitigaciones Pendientes (Checklist)

A medida que el equipo de desarrollo escribe el código y configura la red, debe marcar el progreso aquí:
- [x] Configurar TLS 1.3 en el servidor web.
- [x] Implementar hashing de contraseñas con Argon2id.
- [ ] Implementar Rate Limiting en la API.
- [ ] Configurar las reglas de Firewall (Security Groups) para aislar la Base de Datos.
