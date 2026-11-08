# Home Automation GreenHouse

Este repositorio contiene la arquitectura, lógica y bitácora del proyecto de automatización para un estante abierto de plantas tropicales (Bamworld) utilizando un ESP32-S3 y una infraestructura AWS Serverless.

La carpeta `/docs` actúa como el "cerebro" y contexto principal del proyecto. Estos documentos están diseñados para inyectarse como contexto en asistentes de Inteligencia Artificial (como Gemini o GitHub Copilot) y asegurar que el código generado respete las decisiones de hardware y arquitectura previas.

## 📂 Estructura de Documentación (`/docs`)

*   `01_arquitectura_hardware.md`: Inventario de componentes (ESP32-S3, sensores BME280, ventiladores PWM, relés) y arquitectura AWS.
*   `02_logica_clima.md`: Reglas de negocio para proteger el humidificador y simular brisas para el engrosamiento de tallos.
*   `03_bitacora.md`: Registro histórico de sesiones, decisiones tomadas y próximos pasos.

## 🧠 Cómo Inyectar este Contexto en Gemini

Para no tener que explicar el proyecto desde cero en cada sesión, sigue uno de estos flujos de trabajo:

### Opción 1: Integración Directa en VS Code (Recomendada)
Para evitar copiar y pegar texto manualmente, puedes integrar Gemini directamente en tu IDE:

1.  **Instala la extensión "Continue" (Continue.dev):** Búscala en el marketplace de VS Code.
2.  **Configura tu API Key:** Obtén una clave gratuita desde Google AI Studio y configúrala en la extensión para usar el modelo Gemini.
3.  **Usa el chat de Continue:** En el panel lateral, escribe `@` seguido del nombre del archivo (ej. `@01_arquitectura_hardware.md`). La extensión leerá el archivo automáticamente.
4.  **Genera el prompt:** *"Basado en el hardware de `@01_arquitectura_hardware.md`, escribe el código ESPHome para controlar los ventiladores PWM."*

*Alternativa:* Puedes usar la extensión oficial **Gemini Code Assist** de Google, la cual indexa todo tu *workspace* automáticamente para ofrecer contexto al chat.

### Opción 2: Uso en la Interfaz Web de Gemini
Si prefieres usar el navegador:
1.  Inicia un nuevo chat.
2.  Sube los archivos `.md` requeridos utilizando el botón de adjuntar (`+`), o copia y pega su contenido.
3.  Acompaña los archivos con tu instrucción: *"Te adjunto el contexto de mi proyecto de invernadero. Ayúdame a crear el script de Terraform para la base de datos en DynamoDB."*

## 🔄 Flujo de Trabajo (Mantenimiento del Contexto)
Para que la IA nunca pierda el hilo del proyecto, sigue este ciclo en cada sesión de trabajo:
1.  **Inyecta:** Pasa los `.md` relevantes a la IA.
2.  **Desarrolla:** Escribe código, resuelve bugs o diseña nuevas funciones.
3.  **Actualiza:** Al finalizar la sesión, pide a la IA que te genere un resumen de los cambios logrados y