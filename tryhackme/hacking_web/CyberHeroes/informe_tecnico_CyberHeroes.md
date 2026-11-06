<div align="center">

# 🛡️ INFORME TÉCNICO: VALIDACIÓN DE AUTENTICACIÓN EN CLIENTE
**TryHackMe — Análisis de Código Fuente y Evasión de Mecanismo Client-Side**

![Vulnerabilidad](https://img.shields.io/badge/VULNERABILIDAD-CLIENT--SIDE_AUTHENTICATION-critical?style=for-the-badge)
![Nivel](https://img.shields.io/badge/DIFICULTAD-FÁCIL-green?style=for-the-badge)
![Técnica](https://img.shields.io/badge/TÉCNICA-SOURCE_CODE_AUDIT-blue?style=for-the-badge)

</div>

---

### 📋 Ficha Técnica del Objetivo

| Parámetro | Detalle | Parámetro | Detalle |
| :--- | :--- | :--- | :--- |
| **IP Atacante** | `10.129.123.29` | **Auditor** | Daniel Espinal |
| **IP Objetivo** | `10.129.162.26` | **Protocolo** | HTTP (Puerto 80) |
| **Punto de Entrada** | `/login.html` | **Fallo Crítico** | Hardcoded Credentials & Client Logic |

---

## 1. Fase de Reconocimiento y Detección de Entrada

Se inicia navegando hacia la interfaz raíz del host objetivo (`http://10.129.162.26`), donde se visualiza el portal corporativo general:

<div align="center">
  <img width="700" alt="Página de Inicio" src="https://github.com/user-attachments/assets/ed361e9f-1f85-4485-8737-fe389a13ff79" />
</div>

Durante la inspección de los elementos de navegación y enlaces del sitio web, se localiza un botón de inicio de sesión (*Login*):

<div align="center">
  <img width="700" alt="Localización de Acceso Login" src="https://github.com/user-attachments/assets/80a3c9f8-8e85-4c00-bb96-a80e9a32f680" />
</div>

Al acceder al enlace, se presenta el formulario de autenticación ubicado en el recurso `http://10.129.162.26/login.html`:

<div align="center">
  <img width="700" alt="Formulario de Login" src="https://github.com/user-attachments/assets/2920afb8-efcd-49ca-997e-4840a877ab70" />
</div>

---

## 2. Intento Inicial e Intercepción de Tráfico

En primera instancia, se realiza una prueba básica con credenciales estándar (`admin` / `admin`). El formulario no recarga la página y genera de inmediato un mensaje de error visual en pantalla:

<div align="center">
  <img width="700" alt="Fallo de Autenticación Básica" src="https://github.com/user-attachments/assets/9685159b-db33-4a3f-adc6-6ac0a376d2dd" />
</div>

Para verificar si los datos se están procesando hacia un endpoint backend a través de peticiones asíncronas, se configura y activa el proxy de intercepción en **Burp Suite**:

<div align="center">
  <img width="500" alt="Configuración Burp Suite" src="https://github.com/user-attachments/assets/c8faad3d-f696-4f93-bfdb-1e372bf4ee84" />
  <br><br>
  <img width="700" alt="Habilitación del Interceptor" src="https://github.com/user-attachments/assets/349f9504-264f-41ab-a77c-20d0a45407a9" />
</div>

Al pulsar de nuevo el botón de envío con el interceptor encendido, no se genera tráfico de red saliente hacia el servidor:

<div align="center">
  <img width="700" alt="Sin Tráfico Interceptado" src="https://github.com/user-attachments/assets/3c462ecf-1537-48d0-a301-7257dee324f6" />
</div>

> [!WARNING]
> **Hallazgo Analítico:** La ausencia de peticiones HTTP salientes al disparar el evento de autenticación confirma que la validación se está realizando de forma local dentro del navegador mediante scripts de JavaScript.

---

## 3. Auditoría de Código Fuente en Cliente

Se procede a inspeccionar el código fuente del recurso `login.html` mediante la función de visualización del navegador (`Ctrl + U`):

<div align="center">
  <img width="700" alt="Código Fuente HTML" src="https://github.com/user-attachments/assets/d133e135-b3b0-408a-9faf-023a8ac51de6" />
</div>

Se realiza una búsqueda de cadenas clave vinculadas a los campos del formulario (`username`, `password`), localizando los identificadores del DOM: `uname` para el usuario y `pass` para la contraseña:

<div align="center">
  <img width="700" alt="Búsqueda de Identificadores del Formulario" src="https://github.com/user-attachments/assets/80afec68-d356-4270-8601-c121b249d8c4" />
</div>

Al rastrear el uso del identificador `uname` dentro del código, se detecta un bloque `<script>` que implementa la función encargada de autenticar directamente al usuario. En el código se observan credenciales en texto plano:

* **Usuario esperado:** `h3ck3rBoi`
* **Contraseña ofuscada:** `54321@terceSrepuS`

<div align="center">
  <img width="700" alt="Script de Autenticación con Credenciales Expuestas" src="https://github.com/user-attachments/assets/8e6c0949-47fd-46c3-9b6c-fa434007658f" />
</div>

---

## 4. Desofuscación y Obtención de Acceso

La lógica del script ejecuta una función auxiliar denominada `reverseString()`, lo que implica que la cadena de contraseña almacenada se encuentra invertida.

Se utiliza la utilidad `rev` en terminal Linux para invertir la secuencia de caracteres:

```bash
echo "54321@terceSrepuS" | rev
```

<div align="center">
  <img width="700" alt="Inversión de Cadena con rev" src="https://github.com/user-attachments/assets/b56f8723-65dc-4544-99a7-33fb331b3ae0" />
</div>

* **Contraseña descifrada:** `SuperSecret@12345`

Con el par de credenciales válidas obtenidas (`h3ck3rBoi` : `SuperSecret@12345`), se completan los campos del formulario:

<div align="center">
  <img width="700" alt="Introducción de Credenciales Válidas" src="https://github.com/user-attachments/assets/0bbe51d1-9987-497e-a148-a9e64bcdf2df" />
</div>

Al validar el formulario, la condición se cumple en el navegador y la aplicación renderiza el panel con la clave/flag final:

<div align="center">
  <img width="700" alt="Flag Obtenida Exitosamente" src="https://github.com/user-attachments/assets/315a8387-c344-409d-af34-9fae64c8faa0" />
</div>

---

## 5. Medidas de Mitigación y Remediación

> [!TIP]
> 1. **Trasladar la autenticación al lado del servidor:** La validación de credenciales nunca debe realizarse en scripts que se ejecutan en el navegador del cliente. Todo proceso de autenticación debe enviarse mediante HTTPS y resolverse contra una base de datos o directorio seguro en el backend.
> 2. **Eliminar credenciales embebidas (Hardcoded Secrets):** Nunca se deben almacenar usuarios, contraseñas, tokens o secretos en código fuente HTML/JS accesible públicamente.
> 3. **Almacenamiento seguro de contraseñas:** Las contraseñas en el backend deben tratarse mediante algoritmos de derivación de claves y hashing robusto (como bcrypt, Argon2 o PBKDF2) con salt único por usuario, evitando métodos de ofuscación débiles como inversiones de cadena o codificación simple.