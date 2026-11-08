# Lógica de Clima y Reglas de Negocio

## 1. Contexto del Entorno
Al utilizar un estante abierto (Bamworld) en lugar de una vitrina cerrada, las variables climáticas (especialmente la humedad) interactúan directamente con el ambiente general de la oficina. Las reglas de negocio implementadas en el ESP32-S3 y respaldadas por AWS Lambda están diseñadas para proteger el hardware, optimizar los recursos (agua del humidificador) y simular un entorno tropical realista.

## 2. Protección Dinámica del Humidificador (Lógica Anti-Agotamiento)
El mayor riesgo de un estante abierto durante periodos de ausencia (ej. vacaciones) es que el humidificador trabaje al 100% intentando alcanzar una humedad imposible si el aire acondicionado seca la oficina, agotando el tanque en horas.

*   **Variables Base:**
    *   `Humedad_Target_Optima`: 65% (Ideal para Alocasias y Anthuriums).
    *   `Humedad_Minima_Supervivencia`: 45%.
*   **Algoritmo de Compensación Dinámica:**
    *   **Evaluación de Rendimiento (Delta):** Si el relé del humidificador está encendido durante 15 minutos continuos y la lectura del BME280 no incrementa al menos un 5%, el sistema asume que la humedad ambiental de la oficina es demasiado baja para competir.
    *   **Ajuste de Target (Fallback):** El sistema reduce automáticamente el `Humedad_Target_Optima` al 50% y cambia a un modelo de **Ciclo de Trabajo (Duty Cycle)**: 10 minutos encendido, 20 minutos apagado.
    *   **Alarma AWS:** Se envía un payload MQTT a AWS IoT Core que dispara un Lambda para enviar una notificación push a la app: *"Advertencia: Disipación rápida de humedad detectada. Modo de conservación de agua activado."*
    *   **Detección de Tanque Vacío:** Si tras 30 minutos de relé activo la humedad decrece o se mantiene estática, se apaga el relé indefinidamente (hasta intervención manual o de app) para evitar quemar el motor ultrasónico del humidificador.

## 3. Control de Ventilación (Brisa Simulada PWM)
Los ventiladores Arctic P12 (120mm) no funcionarán por termostato estático (ya que el mueble es abierto y no acumula calor extremo). Su función principal es **evitar plagas (hongos) y provocar estrés mecánico positivo para engrosar los tallos** de las Monsteras y Philodendrons.

*   **Modo Brisa Constante (Base):** 
    *   Señal PWM al 20-30% durante el día (simulando corriente de aire continua y silenciosa).
*   **Modo Ráfaga (Gimnasio Botánico):** 
    *   Cada 2 horas, el ESP32 sube el PWM al 70-80% de manera aleatoria durante 3 a 5 minutos. Las aspas pivotantes aseguran que el aire mueva las hojas grandes sin resecar un solo punto fijo.
*   **Silencio Nocturno:** 
    *   Coincidiendo con el apagado de las luces Barrina (atenuación completa), los ventiladores bajan al 15% (flujo mínimo solo para evitar estancamiento nocturno).

## 4. Gestión de Temperatura (Tapete Calefactor)
*   **Regla de Encendido:** Si la lectura del BME280 cae por debajo de 18°C (riesgo de aletargamiento en plantas tropicales).
*   **Regla de Apagado:** Al alcanzar los 22°C en la zona de las raíces. 
*   *Nota:* Dado que la temperatura ambiente de la oficina suele estar controlada, este relé actuará estrictamente como salvavidas en fines de semana de invierno.

## 5. Telemetría y Muestreo (Interacción con AWS)
*   **Frecuencia de Lectura Local:** El ESP32 consulta el BME280 cada 10 segundos para decisiones internas.
*   **Frecuencia de Publicación MQTT (AWS IoT Core):** Promedio de las métricas enviado cada 5 minutos (Optimización de costos y lecturas en DynamoDB).
*   **Captura de Imágenes (Cámara OV2640):** 1 foto enviada a AWS S3 cada 60 minutos durante las horas de luz activa. Permite generar un Time-Lapse posterior y revisar el nivel de agua físico o el estado de las hojas desde la app.