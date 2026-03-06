# Extracción de Información de PDFs con OCR y LLM

## Descripción general

Este proyecto implementa un **pipeline mínimo funcional (MVP)** para extraer información estructurada de documentos PDF que pueden tener **diferentes formatos y estructuras**.

Los enfoques tradicionales de parsing (regex, plantillas fijas o reglas manuales) suelen fallar cuando los documentos cambian de formato. Para abordar este problema, este proyecto combina:

* **Extracción de texto de PDFs**
* **OCR para documentos escaneados**
* **Modelos de lenguaje (LLMs)** para extraer información de manera semántica

El objetivo es convertir documentos PDF heterogéneos en **datos estructurados** que posteriormente puedan alimentar **sistemas analíticos o dashboards**.

---

# Arquitectura del pipeline

El notebook implementa un pequeño pipeline de procesamiento de documentos:

```
Carpeta de PDFs
   │
   ▼
Carga de rutas de PDFs
   │
   ▼
Detección de texto en el PDF
   │
   ├── Si hay texto → extracción directa
   │
   └── Si no hay texto → OCR con Tesseract
   │
   ▼
Texto del documento
   │
   ▼
Extracción estructurada con LLM
   │
   ▼
JSON / DataFrame estructurado
   │
   ▼
Listo para dashboards o análisis
```

---

# Tecnologías utilizadas

## Lenguaje principal

* Python

## Procesamiento de PDFs

* PyMuPDF (`fitz`)
* pdfplumber

## OCR

* Tesseract
* pytesseract
* OpenCV

## Manejo de datos

* pandas
* json

## Integración con LLM

* OpenAI API

---

# Componentes principales

## 1. Carga de PDFs

El sistema lee todos los PDFs desde una carpeta y genera una lista de documentos con metadata básica:

* nombre del archivo
* ruta del archivo

---

## 2. Detección de texto

Antes de ejecutar OCR, el sistema revisa si el PDF **ya contiene texto embebido**.

Si el documento contiene texto:

* se extrae directamente

Si el documento no contiene texto:

* se procesa con OCR.

Esto evita ejecutar OCR innecesariamente y mejora el rendimiento.

---

## 3. Extracción de texto

Se implementan dos métodos independientes:

### Extracción de texto embebido

Utiliza **PyMuPDF** para leer directamente el texto del documento.

### Extracción mediante OCR

Para documentos escaneados:

1. Cada página se convierte en imagen
2. La imagen se procesa
3. Se ejecuta **Tesseract OCR** para extraer el texto

---

## 4. Extracción automática

Una función decide automáticamente qué método utilizar.

```
extract_text_auto(pdf_path)
```

Esto permite procesar conjuntos de documentos que contienen tanto:

* PDFs digitales
* PDFs escaneados

---

## 5. Extracción estructurada con LLM

Una vez que el texto del documento ha sido extraído, se envía a un **modelo de lenguaje**.

El modelo identifica y extrae información relevante, por ejemplo:

* datos del sitio
* clasificación del suelo
* contenidos de humedad
* límites de consistencia
* grados de saturación

La salida se devuelve en formato **JSON** y se almacena en un **DataFrame de pandas**.

---

# Flujo de uso

1. Colocar los archivos PDF en una carpeta
2. Ejecutar el notebook
3. El sistema:

   * carga los PDFs
   * extrae el texto
   * aplica OCR cuando es necesario
   * envía el texto al modelo de lenguaje
4. Los resultados se almacenan en un DataFrame listo para análisis o visualización.

---

 # Ejemplo salida

| file_name                | llm_output                                                                              |
|-------------------------|-------------------------------------------------------------------------------------------|
| 2018_SE_BLEDALES_BCS.pdf | {"nombre_documento":"2018_SE_BLEDALES_BCS.pdf","datos_sitio":{...}}                      |
| 2018_SE_SANTIAGO_BCS.pdf | {"nombre_documento":"2018_SE_SANTIAGO_BCS.pdf","datos_sitio":{...}}                      |

---

# Posibles extensiones

Este MVP puede evolucionar hacia un sistema más completo incorporando:

* clasificación automática de documentos
* procesamiento de documentos largos mediante chunking
* validación de esquemas de salida
* búsqueda semántica (RAG)
* pipelines de procesamiento en lote
* integración con data warehouses
* integración directa con plataformas de dashboards

---

# Requisitos

Librerías utilizadas en este proyecto:

```
pymupdf
pdfplumber
pytesseract
opencv-python
pillow
pandas
openai
```

Además, **Tesseract OCR debe estar instalado en el sistema**.

---

# Nota

Este proyecto es un **prototipo diseñado para demostración y experimentación**.
Para llevarlo a producción sería necesario agregar manejo de errores, escalabilidad, validación de datos y monitoreo del pipeline.
