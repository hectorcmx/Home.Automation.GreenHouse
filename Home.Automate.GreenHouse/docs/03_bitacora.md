# Bitácora del Proyecto y Registro de Sesiones

## Sesión 1: Definición de la Arquitectura Base y Nube (Contexto Inicial)
*   **Concepto:** Se estableció la idea de un invernadero automatizado y monitoreado mediante una app móvil para la oficina.
*   **Hardware Base:** Selección inicial de ESP32-CAM, ventiladores Arctic P12, sensor BME280 y módulo de relés optoacoplado.
*   **Arquitectura de Software (AWS Serverless):** Se decidió utilizar Terraform (IaC) para desplegar una infraestructura de costo $0.00 MXN en AWS.
    *   *Componentes Cloud:* IoT Core (MQTT), Lambda, DynamoDB (series de tiempo), API Gateway + Cognito y S3 (con regla de expiración de 24 hrs para fotos).

## Sesión 2: Refinamiento de Hardware y Adaptación a Entorno Abierto (Noviembre 2026)
*   **Corrección de Entorno:** Se cambió el contenedor del proyecto de una vitrina cerrada (IKEA Milsbo) a un estante de madera abierto (**Bamworld Soporte para Plantas 158 cm**).
*   **Actualización de Microcontrolador:** Se descartó el ESP32-CAM a favor de un **ESP32-S3** con módulo de cámara OV2640, debido a la necesidad de mayor cantidad de pines (GPIO) para PWM, I2C y relés.
*   **Gestión de Iluminación:** 
    *   Se confirmó el uso de las lámparas Barrina OG09 y T10 Pro.
    *   Se investigaron opciones para atenuar (dimmer) la T10 Pro. Se acordó que la vía más segura y compatible con el ecosistema actual es el uso de un **Enchufe Inteligente Dimmer** (ej. TP-Link Tapo P135, Leviton, Dewenwils), aunque se mantiene abierta la opción de usar un Dimmer AC Zero-Cross físico controlado por el ESP32.
*   **Definición de Lógica de Negocio (Clima):**
    *   Se estableció un algoritmo de protección para el humidificador, el cual entrará en ciclos de trabajo (duty cycle) o se apagará si detecta que la humedad de la oficina es demasiado baja para ser compensada, evitando así quemar el motor o agotar el tanque de agua prematuramente.
    *   Se definieron modos de ventilación PWM (Brisa Constante y Modo Ráfaga) para promover el estrés mecánico positivo y fortalecer los tallos de las plantas.
*   **Estructuración del Proyecto:** Se crearon los documentos fundacionales en Markdown (`01_arquitectura_hardware.md` y `02_logica_clima.md`) para inicializar el repositorio y servir como contexto inyectable en futuras sesiones de programación (VS Code / Copilot / Gemini).

---
*Nota: Agrega las futuras decisiones de diseño de interfaz móvil, creación de scripts de Terraform y módulos de código C++/ESPHome debajo de esta línea en las próximas sesiones.*