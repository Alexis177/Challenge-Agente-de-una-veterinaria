# 🐾 Agente conversacional para una veterinaria

Asistente conversacional para WhatsApp desarrollado en Python. Utiliza Llama 3.1 8B mediante Ollama y Llama Stack para atender solicitudes de citas veterinarias y mantener el contexto de cada conversación.

Proyecto realizado como parte del challenge de **Diseño de Agentes Conversacionales**.

## Funcionalidades

- Recopila información para solicitar una cita veterinaria.
- Mantiene una conversación independiente por número de teléfono.
- Conserva un máximo de 6 elementos en el historial.
- Detecta palabras de urgencia y responde con un mensaje fijo.
- Identifica solicitudes de atención humana.
- Utiliza un lock por número para evitar el procesamiento simultáneo sobre una misma conversación.
- Recibe mensajes mediante un webhook de WhatsApp.

El asistente no confirma citas, proporciona diagnósticos ni prescribe medicamentos.

## Tecnologías utilizadas

| Tecnología | Uso |
|---|---|
| Python | Lógica del asistente |
| Google Colab | Entorno de ejecución |
| Ollama | Ejecución local del modelo |
| Llama 3.1 8B | Generación de respuestas |
| Llama Stack | Gestión de conversaciones e inferencia |
| FastAPI | Endpoints del webhook |
| Uvicorn | Servidor de la aplicación |
| WhatsApp Cloud API | Recepción y envío de mensajes |
| ngrok | Exposición del servidor mediante una URL pública |

## Requisitos

- Una sesión de Google Colab; se recomienda utilizar GPU.
- Una aplicación de Meta configurada para WhatsApp Cloud API.
- Credenciales de WhatsApp y un destinatario autorizado para las pruebas.
- Una cuenta de ngrok y su token de autenticación.
- Una versión de Llama Stack compatible con las operaciones de conversaciones utilizadas.

## Configuración de credenciales

Agregar los siguientes valores al panel **Secrets** de Google Colab y habilitar su acceso desde el notebook:

| Secret | Descripción |
|---|---|
| `WHATSAPP_ACCESS_TOKEN` | Token de acceso a WhatsApp Cloud API |
| `PHONE_NUMBER_ID` | Identificador del número de WhatsApp |
| `NGROK_AUTHTOKEN` | Token de autenticación de ngrok |

También se debe definir `VERIFY_TOKEN` en el código. Su valor debe coincidir con el token de verificación configurado en Meta.

No incluir tokens ni credenciales directamente en el repositorio.

## Ejecución

1. Abrir el notebook en Google Colab.
2. Instalar e iniciar Ollama.
3. Descargar el modelo `llama3.1:8b`.
4. Instalar e iniciar Llama Stack con el proveedor Ollama.
5. Cargar las credenciales desde Colab Secrets.
6. Ejecutar las celdas de memoria, reglas y procesamiento.
7. Definir los endpoints y la función de envío de WhatsApp.
8. Iniciar Uvicorn y el túnel de ngrok.
9. Configurar en Meta la URL pública impresa, terminada en `/webhook`.
10. Verificar el webhook y suscribir la aplicación al evento de mensajes.
11. Ejecutar las pruebas.

Si el ejemplo dental y el challenge están en el mismo notebook, evitar iniciar servidores duplicados. Se recomienda ejecutar el challenge desde una sesión limpia, incluyendo su configuración inicial.

## Flujo de funcionamiento

```text
Mensaje de WhatsApp
        ↓
Webhook de FastAPI
        ↓
Procesamiento en segundo plano
        ↓
Lock por número de teléfono
        ↓
Evaluación de reglas
        ├── Urgencia → Respuesta fija
        ├── Atención humana → Respuesta fija
        └── Consulta general → Llama Stack + Llama 3.1
                                      ↓
                              Recorte del historial
        ↓
Envío de respuesta por WhatsApp
```

