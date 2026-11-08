## 1. Visión General del Proyecto
Sistema de automatización, monitoreo y telemetría DIY para plantas tropicales (Monsteras, Philodendrons y Alocasias) ubicadas en un estante de madera abierto (**Bamworld Soporte para Plantas, 158 cm**). Al tratarse de un ecosistema abierto, el sistema ajusta de manera dinámica la humedad y ventilación para compensar la disipación rápida en la oficina, subiendo métricas e imágenes a una infraestructura Serverless en AWS para su gestión desde una app móvil.

## 2. Controlador Central e Interfaz
*   **Microcontrolador Principal:** ESP32-S3 con soporte para cámara OV2640 (2MP). Se actualiza desde un ESP32-CAM básico para garantizar suficientes pines (GPIO) para PWM, I2C, relés y procesamiento de imágenes simultáneo.
*   **Gabinete:** Caja de conexiones plástica estanca HoHaing IP65 de ABS con tapa transparente y bisagras. Protege la electrónica de las salpicaduras de riego y la humedad del ambiente.
*   **Montaje y Sellado:** 
    *   Uso de placa de expansión con borneras (Terminal Block Breakout Board) o montaje en Riel DIN para conexiones estables y profesionales (sin protoboard).
    *   Prensaestopas (Cable Glands) PG7/PG9 en la base de la caja para pasar cables de sensores y potencia sin perder la certificación IP65.

## 3. Gestión de Clima (Entorno Abierto)
*   **Sensores:** BME280 (Temperatura, Humedad, Presión) conectado vía I2C. Altamente resistente para entornos tropicales.
*   **Ventilación (Circulación y Fuerza de Tallos):** 2x Ventiladores de PC Arctic P12 PWM PST (120mm) montados en soportes pivotantes de aluminio de 360 grados.
    *   *Control:* Mediante módulos MOSFET IRLZ44N conectados a pines PWM del ESP32-S3, permitiendo simular brisas dinámicas en lugar de ráfagas fijas.
*   **Humedad y Temperatura Adicional:**
    *   *Control:* Módulo de relés de 5V (2 a 4 canales) optoacoplado.
    *   *Actuadores:* Enchufes AC controlados por los relés para activar/desactivar 1 o 2 humidificadores y 1 tapete calefactor según las lecturas del BME280.

## 4. Iluminación y Dimming
*   **Lámparas Seleccionadas:** 
    *   Barrina OG09 (Chips LED Rojos 660nm, formato Puck).
    *   Barrina T10 Pro (Lámpara vertical de 42W, Espectro Completo).
*   **Control de Atenuación (Amanecer/Atardecer):** 
    *   *Opción A (Integración Cloud/Alexa):* Enchufes Dimmer Inteligentes (Ej. TP-Link Tapo P135 Matter o Leviton Decora) comandados por la misma lógica de rutinas.
    *   *Opción B (Hardware Local):* Módulo Dimmer AC con cruce por cero (Zero-cross) controlado directamente por PWM desde el ESP32-S3.

## 5. Gestión de Energía
*   **Fuente Principal:** Adaptador de pared 12V 2A DC (Alimenta directamente los ventiladores Arctic P12).
*   **Regulador de Voltaje:** Módulo Step-Down LM2596 (30W 3A). Reduce los 12V a 5V limpios para alimentar el ESP32-S3 y la bobina del módulo de relés optoacoplado.

## 6. Arquitectura Cloud (AWS Serverless)
Gestión completa a través de la nube con un costo proyectado de $0.00 MXN (Capa Gratuita) e Infraestructura como Código (IaC).
*   **IaC:** Terraform para aprovisionamiento de recursos.
*   **Telemetría y Comandos:** AWS IoT Core mediante protocolo MQTT (conexión bidireccional con el ESP32-S3).
*   **Procesamiento:** AWS Lambda, activado por reglas de enrutamiento de IoT Core.
*   **Base de Datos:** Amazon DynamoDB para almacenamiento ultrarrápido de series de tiempo (Humedad, Temperatura, % PWM de ventiladores, Estado de relés).
*   **Imágenes:** Amazon S3. El ESP32-S3 sube 1 foto cada hora. Se configura una Regla de Ciclo de Vida (Lifecycle Rule) en Terraform para borrar imágenes tras 24 horas (almacenamiento total < 10 MB).
*   **API y Seguridad:** Amazon API Gateway + Amazon Cognito (User Pool exclusivo para 1 usuario administrador móvil).

## 7. Decisiones de Diseño Clave y Contexto
1.  **Cambio de ESP32-CAM a ESP32-S3:** El ESP32-CAM estándar carece de pines suficientes para manejar PWM de ventiladores, bus I2C, relés múltiples y el módulo OV2640 al mismo tiempo de manera estable. El ESP32-S3 resuelve el cuello de botella de GPIOs y memoria RAM.
2.  **Estante Abierto (Bamworld) vs. Vitrina Cerrada (Milsbo):** Al no haber puertas de cristal, la humedad ambiental se disipa rápido. La lógica de software implementará un límite de seguridad: si la humedad de la oficina cae drásticamente, el sistema bajará el *target* (objetivo) de humedad relativa para evitar que el humidificador trabaje al 100% y agote el tanque de agua en menos de un día.
3.  **Ventilación de Brisa:** Los Arctic P12 no funcionarán como extractores térmicos, sino como simuladores de viento (controlados por MOSFET) para evitar el estancamiento de aire y fortalecer los tallos de las Alocasias y Monsteras.