<div align="center">

# 🛡️ INFORME TÉCNICO: LABORATORIO WEB SSRF
**TryHackMe — Explotación de Server-Side Request Forgery & Acceso Local**

![Vulnerabilidad](https://img.shields.io/badge/VULNERABILIDAD-SSRF-critical?style=for-the-badge)
![Nivel](https://img.shields.io/badge/DIFICULTAD-FÁCIL-green?style=for-the-badge)
![Herramienta](https://img.shields.io/badge/HERRAMIENTA-BURP_SUITE-orange?style=for-the-badge)

</div>

---

### 📋 Ficha Técnica del Objetivo

| Parámetro | Detalle | Parámetro | Detalle |
| :--- | :--- | :--- | :--- |
| **IP Atacante** | `10.128.96.115` | **Auditor** | Daniel Espinal |
| **IP Objetivo** | `10.128.164.193` | **Protocolo** | HTTP (Puerto 80) |
| **Vector Inicial** | Credenciales por Defecto (`admin:admin`) | **Vulnerabilidad Crítica** | SSRF (Acceso a `127.0.0.1`) |

---

## 1. Reconocimiento y Acceso Inicial

Se navega hacia la dirección IP del objetivo (`http://10.128.164.193`), identificando un panel de autenticación web corporativo.

<div align="center">
  <img width="700" alt="Panel de Login" src="https://github.com/user-attachments/assets/8a0aed79-499f-4b55-ab9d-e0d4f6c04e27" />
</div>

### Configuración del Entorno de Intercepción

Para auditar el tráfico HTTP entre el navegador y el servidor, se configura **Burp Suite** como proxy intermedio (`127.0.0.1:8080`) en los ajustes del navegador y se habilita la escucha en la pestaña de proxy.

<div align="center">
  <img width="700" alt="Configuración de Proxy en Navegador" src="https://github.com/user-attachments/assets/f4e700c0-7a11-4d18-aa0f-8b15bdb7e0f9" />
  <br><br>
  <img width="700" alt="Activación de Proxy en Burp Suite" src="https://github.com/user-attachments/assets/fc9750d3-7ff6-49f5-88ee-daa0fb8cd25c" />
</div>

### Prueba de Credenciales Débiles

Se realiza un intento de inicio de sesión utilizando pares de credenciales por defecto comunes en entornos administrativos:

* **Usuario:** `admin`
* **Contraseña:** `admin`

<div align="center">
  <img width="700" alt="Intento de Login con Credenciales por Defecto" src="https://github.com/user-attachments/assets/d1bf5888-f507-4361-922f-ac83dea4f439" />
</div>

La aplicación valida las credenciales sin requerir doble factor ni bloqueo por fuerza bruta, concediendo acceso al panel de administración.

<div align="center">
  <img width="700" alt="Acceso Concedido al Panel Admin" src="https://github.com/user-attachments/assets/21729082-c11b-46e0-9e7f-6e1760dd6726" />
</div>

---

## 2. Análisis del Sistema y Detección de Restricción

Dentro del dashboard se expone una indicación en el panel lateral derecho: el recurso sensible con la clave/flag se encuentra ubicado en el endpoint `/internal/admin.php`.

<div align="center">
  <img width="450" alt="Ruta Interna Revelada" src="https://github.com/user-attachments/assets/4b4ae3e1-17ac-42ef-a1ba-2ad90f80c631" />
</div>

Al intentar consultar directamente dicho endpoint desde la máquina atacante (`http://10.128.164.193/internal/admin.php`), el servidor responde con un error de control de acceso:

> [!WARNING]
> **Control de Acceso Perimetral Activo:** La página restringe las conexiones directas externas y solo permite solicitudes originadas desde la dirección de loopback local (`127.0.0.1`).

<div align="center">
  <img width="700" alt="Acceso Denegado - Solo Localhost" src="https://github.com/user-attachments/assets/11b56421-3214-4946-9433-f5deba65183c" />
</div>

---

## 3. Explotación: Server-Side Request Forgery (SSRF)

Al explorar las funciones del panel, se localiza un botón denominado **Export PDF**.

<div align="center">
  <img width="700" alt="Funcionalidad de Exportación a PDF" src="https://github.com/user-attachments/assets/ae0f8330-1c24-49e5-a78e-cf06d8003ad1" />
</div>

Al ejecutar la exportación, el backend genera un documento tomando como fuente interna la URL:  
`http://127.0.0.1/server-info.php`

<div align="center">
  <img width="700" alt="Reporte Generado desde Localhost" src="https://github.com/user-attachments/assets/e6b4b604-f72a-4427-80d5-16074bd7e44d" />
</div>

Esto evidencia que **el servidor web realiza peticiones HTTP hacia sí mismo para renderizar el documento**, confiando ciegamente en el parámetro recibido del cliente.

### Intercepción y Manipulación de la Petición

1. Se activa la intercepción de peticiones en **Burp Suite** (`Intercept is on`).
2. Se pulsa de nuevo el botón de exportación.
3. Se identifica el parámetro en el cuerpo/URL de la petición HTTP que contiene la ruta de destino (`server-info.php`).

<div align="center">
  <img width="700" alt="Petición Interceptada en Burp Suite" src="https://github.com/user-attachments/assets/fb56b751-858a-4047-beb7-583d8eaa88b3" />
</div>

4. Se modifica el valor del parámetro para apuntar hacia el endpoint restringido:
   ```http
   /internal/admin.php
   ```

<div align="center">
  <img width="700" alt="Modificación del Payload SSRF" src="https://github.com/user-attachments/assets/9f053101-fe1c-4bf6-b94a-6d59a39867ce" />
</div>

5. Se envía la solicitud modificada pulsando en **Forward**.

<div align="center">
  <img width="700" alt="Envío de Petición Manipulada" src="https://github.com/user-attachments/assets/b5ef6a2d-3050-46df-95aa-b032ec8f49d5" />
</div>

---

## 4. Resultado e Impacto

El servidor procesó la petición internamente desde la interfaz `127.0.0.1`, evadiendo la restricción perimetral y renderizando el contenido protegido. Se obtiene con éxito la clave/flag del laboratorio:

<div align="center">
  <img width="700" alt="Flag Obtenida Exitosamente" src="https://github.com/user-attachments/assets/57947fbb-9726-482d-8727-5a5f209c2eda" />
</div>

---

## 5. Medidas de Mitigación y Remediación

> [!TIP]
> 1. **Lista Blanca de URLs (Whitelisting):** El conversor a PDF no debe permitir entradas arbitrarias de los usuarios. Solo debe consultar endpoints estáticos y estrictamente autorizados.
> 2. **Deshabilitar Esquemas y Redes Internas:** Bloquear a nivel de aplicación peticiones salientes dirigidas a `127.0.0.1`, `localhost` y rangos privados RFC 1918 (`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`).
> 3. **Gestión de Identidades:** Forzar el cambio obligatorio de contraseñas por defecto tras el primer arranque de la aplicación y habilitar autenticación multifactor (MFA).