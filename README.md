# 🌴 Asistente Turístico Inteligente de Tenerife (Agentic RAG)

Este proyecto implementa un sistema de **Generación Aumentada por Recuperación (RAG)** con arquitectura de **Agentes (Agentic Workflow)** utilizando LangChain y LangGraph. El objetivo es crear un asistente virtual experto en turismo para la isla de Tenerife que combine conocimientos estáticos (guías en PDF) con datos en tiempo real (clima, búsqueda web).

## 🚀 Características Principales

* **RAG Documental:** Ingesta y consulta de una guía turística en PDF (`Tenerife.pdf`) para responder preguntas sobre historia, cultura y lugares de interés.
* **Agente Inteligente:** Orquestación mediante **LangGraph** para decidir cuándo consultar el documento, cuándo buscar en internet o cuándo responder directamente.
* **Herramientas Personalizadas:** Integración de la API de **Tavily** para búsquedas web y una función propia (`get_weather`) validada con **Pydantic** para consultas meteorológicas.
* **Memoria Conversacional:** Persistencia del contexto entre turnos de conversación (ej. recordar de qué ciudad se está hablando).
* **Visualización de Datos:** Generación de gráficos de temperatura utilizando el LLM como extractor de datos no estructurados.
* **Modelo:** Google Gemini 2.0 Flash.

## 📂 Estructura del Proyecto

```text
.
├── data/
│   └── Tenerife.pdf          # Documento base de conocimiento
├── practica_final_llm.ipynb  # Notebook principal con todo el código
├── requirements.txt          # Dependencias del proyecto
├── .env                      # Variables de entorno (NO subir al repo)
├── .gitignore                # Archivos a ignorar por git
├──README.md                 # Documentación
└── informe_final.md         # Informe final del trabajo detallando técnicas, posibles mejoras, etc.
