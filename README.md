# Sistema automatizado de diagnóstico técnico y análisis de vulnerabilidades en redes

**Proyecto de innovación SENATI 2026 · Aplicado en CMC Technology S.A.C. (Trujillo, Perú)**

Aplicación de escritorio en **Python + Nmap** que automatiza el diagnóstico del área de soporte técnico: descubre los equipos de la red, detecta puertos y servicios expuestos, clasifica el riesgo y genera un **reporte PDF profesional** para el cliente.

![Python](https://img.shields.io/badge/Python-3.13-3776AB?logo=python&logoColor=white)
![Nmap](https://img.shields.io/badge/Nmap-escaneo%20de%20red-0E83CD)
![CustomTkinter](https://img.shields.io/badge/GUI-CustomTkinter-1F2329)
![ReportLab](https://img.shields.io/badge/Reportes-ReportLab%20PDF-B91C1C)
![Estado](https://img.shields.io/badge/estado-validado%20en%20la%20empresa-15803D)

📄 **[Ver el caso de estudio completo (PDF)](docs/caso-de-estudio.pdf)**

![Diagnóstico de red](img/02-diagnostico-red.png)

---

## El problema en la empresa

CMC Technology vende equipos informáticos y da soporte técnico a usuarios, emprendedores y pequeñas empresas. Durante mis prácticas en el área de soporte encontré cuatro problemas:

| Problema | Consecuencia |
|---|---|
| **Sin monitoreo del estado de los equipos** (CPU, RAM, disco, red) | La lentitud o la saturación se detectaban cuando el cliente ya estaba afectado |
| **Sin análisis preventivo de red** | Nadie sabía qué equipos estaban conectados ni qué puertos y servicios estaban expuestos |
| **Diagnóstico manual y dependiente del técnico** | Resultados variables, más tiempo de atención y ningún procedimiento estándar |
| **Sin reportes técnicos estructurados** | Los hallazgos se comunicaban de palabra o en documentos de Word hechos a mano |

**Causa raíz:** faltaban herramientas automatizadas, procesos estandarizados y políticas de seguridad, lo que mantenía un enfoque reactivo y con poca visibilidad técnica.

![Antes y después](img/antes-vs-despues.jpg)

## La solución

| Módulo | Qué hace |
|---|---|
| **Monitoreo del sistema** | Obtiene SO, procesador, uso de CPU, RAM y disco, IP e interfaces. Alerta si hay saturación, un disco crítico o una IP APIPA (fallo de DHCP) |
| **Escaneo de red** | Nmap portable: descubre hosts (`-sn`) y luego escanea cada host con el perfil rápido (`-F`), básico (`-sV`) o completo (`-sV -sC`) |
| **Análisis técnico** | Interpreta el XML de Nmap, clasifica cada servicio como **ALTO / MEDIO / BAJO / POR VALIDAR** y redacta recomendaciones |
| **Reportes** | Genera un PDF con código de atención, resumen ejecutivo, tablas por host con colores según el riesgo, recomendaciones y conclusión |

Además:
- **3 modos de escaneo:** red sugerida (se detecta sola según la interfaz), IP específica o rango manual.
- **Portable:** corre desde una USB con Nmap incluido, sin instalar nada en el equipo del cliente.
- **Interfaz fluida:** el escaneo corre en un hilo aparte, así que la ventana no se congela y muestra el avance en tiempo real.

### Clasificación de riesgo (extracto)

| Servicio | Riesgo | Recomendación |
|---|---|---|
| Telnet (23), RDP (3389), VNC (5900), bases de datos (3306/5432/1433) | 🔴 ALTO | Deshabilitar o restringir a IPs autorizadas |
| SMB (445), NetBIOS (139), RPC (135), HTTP (80), FTP (21) | 🟠 MEDIO | Validar si son necesarios, revisar recursos compartidos y firewall |
| HTTPS (443), SSH (22), DNS (53) | 🟢 BAJO | Suelen ser legítimos; validar quién tiene acceso |
| Puertos no estándar / `tcpwrapped` | ⚪ POR VALIDAR | Confirmar con el cliente el origen del servicio |

## Capturas

| Diagnóstico de equipo | Panel de ejecución |
|---|---|
| ![Diagnóstico de equipo](img/01-diagnostico-equipo.png) | ![Panel de ejecución](img/03-panel-ejecucion.png) |

## Validación en la red de la empresa

Se probó el 26/05/2026 en la red local de CMC Technology (IPs anonimizadas):

| Host | Equipo | Servicios abiertos | Riesgo |
|---|---|---|---|
| 192.168.10.1 | Router | DNS (53), HTTP (80) | 🟠 MEDIO |
| 192.168.10.142 | Dispositivo móvil | 49152, 62078 (tcpwrapped) | ⚪ POR VALIDAR |
| 192.168.10.133 | PC Windows | RPC, NetBIOS, SMB, 7070 | 🟠 MEDIO |
| 192.168.10.152 | PC Windows | RPC, NetBIOS, SMB, 7070 | 🟠 MEDIO |

**Resultado:** 4 hosts, 12 servicios detectados y riesgo general **MEDIO**. El reporte PDF se generó solo, sin intervención manual.

## Reportes de ejemplo

| Reporte de red | Reporte de equipo |
|---|---|
| [![Reporte de red](img/reporte-red-ejemplo-p1.png)](docs/reporte-red-ejemplo.pdf) | [![Reporte de equipo](img/reporte-equipo-ejemplo-p1.png)](docs/reporte-equipo-ejemplo.pdf) |

## Tecnologías

- **Python 3.13** con arquitectura modular (`scanner`, `parser`, `analyzer`, `network_analyzer`, `report_generator`, `gui_app`…)
- **Nmap** ejecutado mediante `subprocess`, con salida en XML y TXT
- **ElementTree** para leer el XML de Nmap
- **psutil / platform / socket** para obtener la información del sistema y de la red
- **CustomTkinter**, con `threading` y `queue`, para la interfaz gráfica
- **ReportLab** para los reportes PDF
- Configuración en **JSON** (perfiles de escaneo, rutas y objetivos)

![Flujo del sistema](img/flujo-sistema.jpg)

## Próximos pasos

- Historial de atenciones en una base de datos centralizada
- Monitoreo periódico con alertas cuando aparezca un servicio de riesgo alto
- Panel web de reportes integrado con el inventario de activos de TI
- Cruce de las versiones detectadas con bases de vulnerabilidades (CVE)

## Mi rol

Me encargué del proyecto de principio a fin: detecté el problema en el área de soporte, diseñé la solución y desarrollé la aplicación con apoyo de asistentes de IA. También hice las pruebas en la red de la empresa y la documentación, que presenté como proyecto de innovación en SENATI (asesor: Luis Cristofer Mostacero Pérez).

---

> ⚖️ **Uso ético:** la herramienta se usó solo en redes y equipos con autorización de la empresa. Las IPs de este repositorio están anonimizadas y se omitieron los nombres de equipos y usuarios.
>
> 🔒 **Código fuente:** este repositorio es una vitrina del proyecto. El código no se publica, pero puedo mostrarlo y hacer una demostración en una entrevista.

**Bryan Jeremy Espinoza Herrera** · Profesional Técnico en Ingeniería de Ciberseguridad · Trujillo, Perú
