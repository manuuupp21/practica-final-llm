# Informe Final: Asistente Turístico Inteligente con Arquitectura RAG Agéntica

## 1. Introducción y Objetivo

El presente proyecto tiene como objetivo desarrollar un sistema conversacional avanzado capaz de actuar como guía turístico de la isla de Tenerife. A diferencia de los chatbots tradicionales o los sistemas RAG (Retrieval-Augmented Generation) lineales, este proyecto implementa una **arquitectura agéntica**.

El sistema no solo responde preguntas, sino que **razona** sobre la naturaleza de la consulta para decidir dinámicamente qué herramienta utilizar: consultar una base de conocimiento (PDF), buscaar datos en tiempo real (Clima/Web) o realizar extracción de datos para visualización.

## 2. Metodología y Técnicas Empleadas

Para la implementación se ha utilizado las herramientas de **LangChain** y **LangGraph**, aprovechando la potencia del modelo **Google Gemini 2.0 Flash**.

### 2.1. Ingesta de Conocimiento (RAG Documental)
Se implementó un pipeline de ingestión de datos para dotar al modelo de conocimiento específico sobre historia y turismo:
* **Carga:** Uso de `PyPDFLoader` para procesar la guía `Tenerife.pdf`.
* **Fragmentación:** Aplicación de `RecursiveCharacterTextSplitter` con *chunks* de 1000 caracteres y solapamiento (*overlap*) de 200 para mantener el contexto entre fragmentos.
* **Vectorización:** Uso de `GoogleGenerativeAIEmbeddings` (modelo `embedding-001`) y almacenamiento en `InMemoryVectorStore` para búsquedas de similitud semántica.

### 2.2. Orquestación Agéntica con LangGraph
En lugar de una cadena secuencial, se diseñó un **Grafo de Estado (`StateGraph`)** que define el flujo de razonamiento:
* **Nodo `reasoner`:** Evalúa el historial de mensajes y decide si invocar una herramienta o responder al usuario.
* **Memoria (`MemorySaver`):** Se integró un sistema de persistencia (`checkpointer`) que permite mantener el hilo conversacional (multi-turno), recordando entidades mencionadas anteriormente (por ejemplo, recordar la ciudad "Garachico" en una segunda pregunta).

### 2.3. Herramientas Personalizadas (Custom Tools)
Se desarrollaron herramientas específicas para extender las capacidades del LLM:
* **`retrieve`:** Herramienta de recuperación vectorial para acceder al contenido del PDF.
* **`get_weather`:** Herramienta estructurada con **Pydantic**. Esto asegura que el modelo extraiga correctamente los parámetros necesarios (nombre de la ciudad) antes de realizar la búsqueda externa.
* **Búsqueda Web:** Integración de **Tavily Search API** para obtener información actualizada que no existe en el PDF.

### 2.4. Extracción de Datos y Visualización (LLM-as-a-Parser)
Se implementó una técnica híbrida para generar gráficos de temperatura:
1.  El agente consulta el clima de múltiples municipios.
2.  El LLM procesa el texto no estructurado de la web para extraer únicamente el valor numérico (entero).
3.  Se utiliza **Matplotlib** para generar una visualización gráfica de los datos extraídos.

## 3. Resultados Obtenidos

El sistema resultante es capaz de:
1.  Responder preguntas históricas y culturales con alta precisión basándose en el documento proporcionado.
2.  Proporcionar datos meteorológicos en tiempo real interactuando con APIs externas.
3.  Mantener una conversación fluida y coherente gracias a la gestión de memoria.
4.  Generar visualizaciones de datos (gráficos de barras) a petición, demostrando capacidades multimodales y de estructuración de datos.

## 4. Limitaciones del Proyecto

* **Volatilidad de Datos:** Al usar `InMemoryVectorStore`, la base de datos vectorial se reconstruye en cada ejecución. En un entorno de producción, se debería migrar a una solución persistente como ChromaDB o Pinecone.
* **Dependencia de APIs:** El funcionamiento correcto depende de la latencia y disponibilidad de las APIs de Google (Gemini) y Tavily.
* **Extracción de Datos:** Aunque se utilizan prompts robustos, la extracción de temperaturas de texto web no estructurado puede presentar errores ocasionales si la fuente de datos tiene un formato muy atípico.

## 5. Posibles Mejoras y Trabajo Futuro

* **Interfaz de Usuario:** Implementar una interfaz gráfica (Streamlit o Gradio) para facilitar la interacción fuera del entorno de desarrollo (Notebook).
* **Sistema de Recomendación:** Integrar un perfil de usuario para que las sugerencias del RAG se adapten a los gustos personales (ej. turismo de aventura vs. cultural).
* **Multimodalidad de Entrada:** Permitir al usuario subir imágenes de lugares para que el agente los identifique y busque información sobre ellos.