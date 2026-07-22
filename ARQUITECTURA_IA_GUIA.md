# Arquitectura de Inteligencia Artificial para Asistentes Guía (Descarga de Documentos)

Para crear un asistente virtual (IA) cuya función principal sea actuar como un "guía" que recomiende y entregue documentos para descargar, no es necesaria una arquitectura compleja de búsqueda vectorial profunda (RAG). El objetivo aquí es **enrutar la intención del usuario hacia un archivo específico**.

Existen dos formas muy elegantes de lograr esto en el ecosistema Enterprise:

## 1. El Enfoque Sencillo: "System Prompts" con formato Markdown

Si tu empresa tiene una lista manejable de documentos (por ejemplo, entre 10 y 50 formatos clave), puedes simplemente inyectar el catálogo completo en las instrucciones ocultas (System Prompts) de la IA antes de que interactúe con el usuario.

*   **Tu Instrucción Oculta:** *"Eres un asistente guía. Aquí tienes nuestro catálogo de documentos: 1. Política de Vacaciones (URL: /assets/docs/vacaciones.pdf), 2. Formato de Préstamos (URL: /assets/docs/prestamos.pdf). Si te piden alguno, debes responder obligatoriamente entregando un enlace Markdown de descarga."*
*   **El Resultado:** Cuando el usuario diga *"Necesito pedir vacaciones"*, la IA responderá: *"¡Claro! Aquí tienes la guía. Puedes [Descargar el Documento Aquí](/assets/docs/vacaciones.pdf)."* (El Frontend, como Angular, renderizará ese enlace para que el usuario haga clic y lo descargue directamente).

## 2. El Enfoque Profesional: "Function Calling" (Llamada a Herramientas)

Los modelos modernos como Gemini y OpenAI tienen una característica brillante llamada **Function Calling**. En lugar de darle la lista estática de documentos a la IA, le das un "poder" (una función programada por ti).

1.  **Definición:** Le dices a Gemini: *"Oye, si el usuario te pide un documento, tienes permiso de ejecutar la función `buscarDocumento(palabraClave)`"*.
2.  **Interacción:** El usuario escribe en el chat: *"Necesito el formato de reembolso de viáticos"*.
3.  **Invocación:** Gemini detecta la intención y, en lugar de intentar adivinar o responder con texto, **detiene su respuesta y llama a tu código Angular/Backend** solicitando ejecutar `buscarDocumento("viáticos")`.
4.  **Respuesta Final:** Tu base de datos tradicional (SQL o Backend) busca el archivo, devuelve la URL estructurada a la IA, y Gemini ahora sí le responde al usuario final: *"¡Lo encontré en el sistema corporativo! Haz clic en este botón para descargarlo."*

### ¿Por qué es mejor usar Function Calling?

Porque si el día de mañana agregas 1,000 documentos nuevos a tu base de datos, **no tienes que enseñarle nada a la IA ni actualizar su Prompt**. Ella simplemente seguirá extrayendo la palabra clave de lo que el usuario pide, ejecutará la herramienta de búsqueda en tu sistema, y tú podrás dibujar hermosos botones de descarga nativos (ej. Angular Material) en lugar de simples enlaces de texto.

Esta técnica garantiza que la IA sea escalable y actúe como un verdadero agente de enrutamiento.
