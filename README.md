# 🎙️ IR-VOICE — Control por Voz de Dispositivos IR con ESP32 + Alexa

> Controla tu **TV, Radio y Proyector** con comandos de voz de Alexa, usando un ESP32 como emisor de señales infrarrojas.

---

## 📋 Descripción

**IR-VOICE** es un sistema domótico que conecta Amazon Alexa con dispositivos electrónicos a través de señales infrarrojas (IR). El ESP32 actúa como puente entre la nube de **SinricPro** (compatible con Alexa) y los aparatos del hogar, emitiendo los códigos IR correspondientes al recibir un comando de voz.

El repositorio incluye dos programas:

| Archivo | Función |
|---|---|
| `CODIGO GENERAL` | Programa principal: recibe comandos de Alexa y emite señales IR |
| `CAPTURADOR IR` | Herramienta auxiliar: captura códigos IR de cualquier control remoto |

---

## ⚙️ Dispositivos Compatibles

| Dispositivo | Marca | Protocolo IR |
|---|---|---|
| 📺 TV | TCL | Nikai |
| 📻 Radio | Panasonic | PANASONIC |
| 📽️ Proyector | Genérico | NEC |

---

## 🗣️ Comandos de Voz Disponibles

"Alexa, enciende el TV"
"Alexa, apaga el TV"
"Alexa, enciende el radio"
"Alexa, apaga el radio"
"Alexa, enciende el proyector"
"Alexa, apaga el proyector"

> **Nota:** El proyector usa una secuencia de dos pulsos IR para apagarse (menú + confirmación), con un delay de 1.5 s entre ambos.

---

## 🛒 Hardware Requerido

| Componente | Detalle |
|---|---|
| ESP32 | Cualquier variante (DevKit recomendado) |
| LED IR emisor | Conectado al **GPIO 19** |
| Receptor IR (TSOP) | Conectado al **GPIO 18** *(solo para captura)* |
| Resistencia 100Ω | En serie con el LED IR emisor |
| Transistor NPN (opcional) | Para mayor alcance del emisor IR |
| Cable USB | Para programar el ESP32 |

### Diagrama de conexión básico

ESP32 GPIO 19 ──[100Ω]──► LED IR (ánodo)
                              └── GND (cátodo)

ESP32 GPIO 18 ◄── TSOP OUT
TSOP VCC      ── 3.3V
TSOP GND      ── GND

---

## 📦 Librerías Necesarias

Instálalas desde el **Library Manager** de Arduino IDE:

| Librería | Instalación |
|---|---|
| `SinricPro` | Buscar: `SinricPro` |
| `IRremoteESP8266` | Buscar: `IRremoteESP8266` |
| `WiFi` | Incluida con el core de ESP32 |

---

## 🚀 Configuración y Uso

### 1. Clonar el repositorio

git clone https://github.com/adanaqueantony3-sys/IR-VOICE.git
cd IR-VOICE/ESP32

### 2. Crear una cuenta en SinricPro

1. Ir a https://sinric.pro y crear una cuenta gratuita.
2. Crear **3 dispositivos** de tipo **Switch** (TV, Radio, Proyector).
3. Copiar el `APP_KEY`, `APP_SECRET` y los **Device IDs**.

### 3. Configurar credenciales en el código

Edita `CODIGO GENERAL` y reemplaza:

#define WIFI_SSID     "TU_RED_WIFI"
#define WIFI_PASSWORD "TU_CONTRASEÑA"

#define APP_KEY    "tu-app-key-de-sinricpro"
#define APP_SECRET "tu-app-secret-de-sinricpro"

#define DEVICE_TV         "id-del-dispositivo-tv"
#define DEVICE_RADIO      "id-del-dispositivo-radio"
#define DEVICE_PROYECTOR  "id-del-dispositivo-proyector"

### 4. Subir el código al ESP32

- Abrir `CODIGO GENERAL` en Arduino IDE.
- Seleccionar la placa: **ESP32 Dev Module**.
- Seleccionar el puerto COM correcto.
- Clic en **Subir** ▶️.

### 5. Vincular con Alexa

1. En la app de **Amazon Alexa**, ir a: `Más → Skills y Juegos`
2. Buscar **"SinricPro"** y activar el skill.
3. Iniciar sesión con tu cuenta de SinricPro.
4. Los dispositivos aparecerán automáticamente en Alexa.

---

## 🔍 Capturar Códigos IR de Nuevos Dispositivos

Si quieres agregar un nuevo aparato, usa `CAPTURADOR IR`:

1. Conectar el receptor TSOP al **GPIO 18**.
2. Subir `CAPTURADOR IR` al ESP32.
3. Abrir el **Monitor Serial** a **115200 baudios**.
4. Apuntar el control remoto al receptor y presionar cualquier botón.
5. Anotar el protocolo, código HEX y bits mostrados.
6. Usar esos valores en la función IR correspondiente de `CODIGO GENERAL`.

Ejemplo de salida:
====== CÓDIGO CAPTURADO ======
Protocolo : NEC
HEX       : 0xFF28D7
Bits      : 32
==============================

---

## 📁 Estructura del Repositorio

IR-VOICE/
└── ESP32/
    ├── CODIGO GENERAL      # Programa principal
    ├── CAPTURADOR IR       # Herramienta de captura IR
    └── README.md

---

## 🔧 Personalización

Para agregar un nuevo dispositivo:

1. Captura su código IR con `CAPTURADOR IR`.
2. Crea un nuevo Switch en SinricPro y copia el Device ID.
3. Agrega la función IR y el callback en `CODIGO GENERAL`:

#define DEVICE_NUEVO "tu-nuevo-device-id"

void IR_Nuevo() {
  irsend.sendNEC(0xTU_CODIGO, 32);
}

bool onNuevo(const String &deviceId, bool &state) {
  IR_Nuevo();
  return true;
}

4. Registra el dispositivo en setup():

SinricProSwitch &sw_nuevo = SinricPro[DEVICE_NUEVO];
sw_nuevo.onPowerState(onNuevo);

---

## ⚠️ Notas Importantes

- **No compartas** tus credenciales de WiFi ni de SinricPro públicamente.
- El alcance del LED IR depende del hardware; un transistor NPN amplificador puede duplicar la distancia de control.
- SinricPro tiene un plan gratuito con límite de dispositivos. Consulta https://sinric.pro/pricing

---

## 📄 Licencia

Este proyecto es de uso libre para fines educativos y personales.

---

*Desarrollado con ❤️ usando ESP32 + SinricPro + IRremoteESP8266*
