# Guía: OpenVPN (Proton VPN) + LINE en Debian con GNOME

Guía paso a paso para usar **LINE** en **Debian (GNOME)** cuando hay restricciones por ubicación,
usando **OpenVPN** con el plan gratuito de **Proton VPN** y la extensión de LINE en un navegador
basado en Chromium (Brave, Chrome o Chromium).

> Probado en Debian 13 con GNOME, conexión por cable (RJ45).

---

## Índice

1. [Instalar OpenVPN](#1-instalar-openvpn)
2. [Crear cuenta en Proton VPN y descargar el archivo .ovpn](#2-crear-cuenta-en-proton-vpn-y-descargar-el-archivo-ovpn)
3. [Obtener el usuario y contraseña de OpenVPN](#3-obtener-el-usuario-y-contraseña-de-openvpn)
4. [Importar la VPN en GNOME](#4-importar-la-vpn-en-gnome)
5. [Configurar usuario y contraseña en la VPN](#5-configurar-usuario-y-contraseña-en-la-vpn)
6. [Conectarse y comprobar](#6-conectarse-y-comprobar)
7. [Usar LINE en el navegador](#7-usar-line-en-el-navegador)
8. [Problemas comunes](#8-problemas-comunes)
9. [Seguridad](#9-seguridad)

---

## 1. Instalar OpenVPN

Abrir una terminal y ejecutar (pide la contraseña de tu usuario de Debian; al escribirla no se ven los caracteres, es normal):

```bash
sudo apt update && sudo apt install -y openvpn network-manager-openvpn network-manager-openvpn-gnome
```

```bash
sudo systemctl restart NetworkManager
```

Paquetes instalados:

| Paquete | Para qué sirve |
|---|---|
| `openvpn` | El programa de VPN |
| `network-manager-openvpn` | Permite a NetworkManager usar OpenVPN |
| `network-manager-openvpn-gnome` | Agrega OpenVPN a la configuración de red de GNOME |

> **¿Hace falta Wi-Fi?** No. La VPN funciona sobre cualquier conexión a internet, incluido el cable de red (RJ45).

---

## 2. Crear cuenta en Proton VPN y descargar el archivo .ovpn

OpenVPN es solo el programa; se necesita un servidor. Proton VPN tiene un plan gratuito.

1. Entrar a **protonvpn.com** → **Get Proton VPN** → elegir el plan **Free** y registrarse.
2. Entrar a **account.protonvpn.com**.
3. En el menú izquierdo: **Downloads** (Descargas).
4. Bajar hasta **OpenVPN configuration files** y elegir:
   - **Platform:** Linux
   - **Protocol:** UDP
   - **Free server configs** (servidores gratuitos)
5. Elegir un país y hacer clic en **Download**.
   - **Japón (JP)**: buena opción para LINE (es una app muy usada allá), pero más lejos → algo más lento.
   - **Estados Unidos (US)**: más cerca de Chile → más rápido.
   - Se recomienda descargar ambos y probar.

El archivo se llama, por ejemplo, `jp-free-16.protonvpn.udp.ovpn`.

> ⚠️ El plan gratuito **no tiene servidores en Chile**. Si necesitas aparecer en otra ciudad de Chile,
> hace falta un plan de pago (Proton VPN Plus u otro proveedor con servidores en Chile).

---

## 3. Obtener el usuario y contraseña de OpenVPN

Estos datos **no son** tu correo ni tu contraseña de Proton.

1. En **account.protonvpn.com** → menú izquierdo → **Account**.
2. Buscar el recuadro **OpenVPN / IKEv2 username**.
3. Ahí aparecen:
   - **OpenVPN username** → botón de copiar al lado.
   - **OpenVPN password** → botón de copiar; el ícono del ojo solo la muestra u oculta.

**Reset credentials** (restablecer credenciales): genera un usuario y contraseña **nuevos** y los
anteriores dejan de funcionar. Úsalo si la conexión falla por autenticación o si alguien vio tus datos.

---

## 4. Importar la VPN en GNOME

Con el archivo `.ovpn` guardado (en este ejemplo en `~/Documents`):

```bash
nmcli connection import type openvpn file ~/Documents/jp-free-16.protonvpn.udp.ovpn
```

Opcional, ponerle un nombre más claro:

```bash
nmcli connection modify jp-free-16.protonvpn.udp connection.id "ProtonVPN Japon"
```

También se puede hacer de forma gráfica: **Configuración → Red → VPN (+) → Importar desde archivo…**

---

## 5. Configurar usuario y contraseña en la VPN

Si la ventana **"Authentication required"** aparece una y otra vez, es porque falta el **nombre de usuario**.

1. **Configuración → Red →** engranaje ⚙️ junto a **ProtonVPN Japon**.
2. Pestaña **Identity**, sección **Authentication**:
   - **Type:** `Password`
   - **User name:** pegar el **OpenVPN username**.
   - **Password:** hacer clic en el ícono de la derecha del campo → **Store the password only for this user**, y pegar el **OpenVPN password**.
3. No tocar **Gateway** ni **CA certificate**.
4. Clic en **Apply**.

---

## 6. Conectarse y comprobar

1. Clic en la **esquina superior derecha** (íconos de red/volumen/batería).
2. **VPN → ProtonVPN Japon** → activar.
3. Aparece un ícono de VPN/candado en la barra superior.
4. Comprobar en **whatismyipaddress.com** que el país sea **Japan**.

Para desconectar: mismo menú → apagar la VPN.

También desde la terminal:

```bash
nmcli connection up "ProtonVPN Japon"
```

```bash
nmcli connection down "ProtonVPN Japon"
```

---

## 7. Usar LINE en el navegador

LINE no tiene app oficial para Linux, pero sí una **extensión oficial** para navegadores basados en
Chromium (**Brave**, Chrome, Chromium). No funciona en Firefox.

### 7.1 Preparar el celular

En la app de LINE: **Inicio → Configuración ⚙️ → Cuenta**:

- Activar **Permitir inicio de sesión** (*Allow login*).
- (Opcional) Registrar un **correo electrónico** para entrar con correo y contraseña.

### 7.2 Instalar la extensión

1. Abrir **Brave** (o Chrome) → **chromewebstore.google.com**.
2. Buscar **LINE** y verificar que el desarrollador sea **LINE Corporation / LY Corporation** (hay copias falsas).
3. **Agregar a Brave / Add to Chrome**.

### 7.3 Iniciar sesión

1. Clic en el ícono de LINE (o dentro del ícono de extensiones 🧩).
2. Entrar con **código QR** (escanear desde el celular: Inicio → escáner QR) o con **correo y contraseña**.
3. Escribir en el celular el **código de verificación** que muestra el computador.
4. Listo: aparecen los contactos y chats.

> **Nota:** *LINE Developers* (developers.line.biz) es para programadores que crean bots y apps.
> **No** sirve para chatear con tus contactos.

Orden recomendado: **conectar la VPN primero → luego abrir LINE**.

---

## 8. Problemas comunes

| Problema | Solución |
|---|---|
| La ventana de contraseña aparece todo el tiempo | Falta el *User name* en la VPN (ver paso 5). |
| Error de autenticación | Verificar que usas el usuario/contraseña de **OpenVPN**, no los de la cuenta Proton. Revisar que no se copiaron espacios. Si sigue, usar **Reset credentials** y poner los nuevos datos. |
| No aparece la opción VPN en GNOME | Instalar `network-manager-openvpn-gnome` y reiniciar NetworkManager (paso 1). |
| Conexión lenta | Probar un servidor más cercano (US). |
| LINE pide permiso para iniciar sesión | Activar *Permitir inicio de sesión* en el celular (paso 7.1). |

---

## 9. Seguridad

- **Nunca** compartas contraseñas en chats, capturas de pantalla ni repositorios.
- Si tus credenciales de OpenVPN se vieron en algún lado, usa **Reset credentials** en Proton.
- No subas archivos `.ovpn` con credenciales a GitHub.
- Instala la extensión de LINE solo si el desarrollador es el oficial.
