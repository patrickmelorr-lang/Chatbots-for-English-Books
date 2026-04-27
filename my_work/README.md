# 📚 RAG Book Q&A — Computer Science: An Overview (13th ed.)

> Sistema de preguntas y respuestas (RAG) sobre un libro técnico en inglés, **traducido al español al vuelo** y consultable mediante búsqueda semántica.

[![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white)](https://www.python.org/)
[![Gemini](https://img.shields.io/badge/LLM-Gemini%202.5%20Flash-4285F4?logo=google&logoColor=white)](https://ai.google.dev/)
[![ChromaDB](https://img.shields.io/badge/Vector%20DB-ChromaDB-FF6B35)](https://www.trychroma.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

---

## 🎯 Demo

El usuario escribe una pregunta en español sobre cualquier tema del libro, y el sistema:

1. Busca los fragmentos más relevantes del libro (en español).
2. Pasa esos fragmentos como contexto a Gemini.
3. Genera una respuesta clara, basada **únicamente en el libro** (anti-alucinación).

---

## 🏗️ Arquitectura

```
┌─────────────────────────────────────────────────────────────┐
│                    PIPELINE DE INDEXACIÓN                    │
└─────────────────────────────────────────────────────────────┘

  📄 PDF (inglés)
       │
       ▼
   ┌────────────┐
   │   pypdf    │  Extrae texto plano (ignora imágenes y fórmulas)
   └────────────┘
       │
       ▼
   ┌────────────┐
   │ tiktoken + │  Divide en ~1500 chunks de ~256 tokens
   │  langchain │  con chunk_overlap=30 (evita perder contexto)
   └────────────┘
       │
       ▼
   ┌──────────────────────┐
   │  deep-translator     │  Traduce cada chunk EN→ES
   │  (Google Translate)  │  Sin Docker, sin API key
   └──────────────────────┘
       │
       ▼
   ┌──────────────────────┐
   │ Gemini Embeddings    │  Convierte cada chunk en vector de 768 dim
   │ gemini-embedding-001 │  task_type=RETRIEVAL_DOCUMENT
   └──────────────────────┘
       │
       ▼
   ┌────────────┐
   │  ChromaDB  │  Almacena {texto, vector, metadata} en disco
   │ persistent │  (./chroma_db)
   └────────────┘

┌─────────────────────────────────────────────────────────────┐
│                    PIPELINE DE CONSULTA                      │
└─────────────────────────────────────────────────────────────┘

  ❓ Pregunta del usuario (ipywidgets)
       │
       ▼
   ┌──────────────────────┐
   │ Gemini Embeddings    │  Embedding de la pregunta
   │ task=RETRIEVAL_QUERY │
   └──────────────────────┘
       │
       ▼
   ┌────────────┐
   │  ChromaDB  │  top-k chunks más cercanos (cosine similarity)
   │  query()   │
   └────────────┘
       │
       ▼
   ┌──────────────────────┐
   │  Gemini 2.5 Flash    │  Recibe: contexto + pregunta + reglas
   │  generate_content()  │  Genera: respuesta en español
   └──────────────────────┘
       │
       ▼
   💡 Respuesta + fuentes citadas
```

---

## 🛠️ Stack tecnológico

| Componente | Tecnología | Por qué |
|---|---|---|
| **Lectura PDF** | `pypdf` | Standard, ligero, ignora gráficos automáticamente |
| **Tokenización** | `tiktoken` (`cl100k_base`) | Rápido, cuenta tokens fielmente |
| **Chunking** | `langchain-text-splitters` (`RecursiveCharacterTextSplitter`) | Respeta saltos naturales del texto |
| **Traducción** | `deep-translator` (Google Translate) | Gratis, sin API key, sin Docker, ilimitado |
| **Embeddings** | `gemini-embedding-001` (768 dim) | Multilingüe, free tier, calidad SOTA |
| **Vector DB** | `ChromaDB` PersistentClient | Local, sin servicios externos, persiste en disco |
| **LLM** | `gemini-2.5-flash` | Rápido, gratis, buena calidad |
| **UI** | `ipywidgets` | Funciona dentro del notebook (también en VS Code) |

---

## 📋 Requisitos previos

1. **Python 3.10+** instalado
2. **VS Code** con las extensiones **Python** y **Jupyter**
3. Una **API key gratuita de Gemini** → <https://aistudio.google.com/app/apikey>
4. El **PDF del libro** descargado localmente
5. **Conexión a internet** (para deep-translator y Gemini)

---

## ⚙️ Instalación rápida

```bash
git clone https://github.com/TU_USUARIO/rag-book-qa.git
cd rag-book-qa

python -m venv venv
venv\Scripts\activate        # Windows
# source venv/bin/activate   # macOS / Linux

pip install -r requirements.txt

cp .env.example .env         # Edita .env y pega tu GEMINI_API_KEY
```

Luego abre `RAG_book_qa.ipynb` en VS Code y ejecuta celda por celda.

> Para una guía paso a paso ultra-detallada en VS Code, mira `GUIDE.md`.

---

## ⏱️ Tiempos estimados

| Paso | Tiempo aprox. | Notas |
|---|---|---|
| 0–2 | < 1 min | Setup |
| 3–5 | 1–2 min | Extracción + chunking |
| 6 | 10–15 min | Traducción de 1500 chunks (Google Translate) |
| 7–8 | < 30 s | Configurar embeddings y ChromaDB |
| 9 | **25–30 min** | Indexación con Gemini (60 req/min × 1500 chunks) |
| 10–11 | Instantáneo | Cada pregunta tarda ~2-3 segundos |

> 💾 **Pasos 6 y 9 se cachean en disco**: si el kernel se reinicia, no hay que volver a traducir ni embebir.

---

## 📁 Estructura del proyecto

```
rag-book-qa/
├── RAG_book_qa.ipynb        # 📔 Notebook principal (todo el pipeline)
├── requirements.txt          # 📦 Dependencias de Python
├── .env.example              # 🔑 Plantilla de variables de entorno
├── .gitignore                # 🚫 Archivos a no subir a Git
├── LICENSE                   # ⚖️ Licencia MIT
├── README.md                 # 📖 Este archivo
├── GUIDE.md                  # 📘 Guía paso a paso para VS Code
│
├── chroma_db/                # 🗃️ (generado) Base de datos vectorial persistente
├── translated_chunks.pkl     # 💾 (generado) Cache de chunks traducidos
└── .env                      # 🔒 (NO se sube) API keys reales
```

---

## 🎓 Decisiones técnicas

### ¿Por qué chunks de 256 tokens y no más grandes?

Chunks pequeños → **búsqueda semántica más precisa**: cuando recuperas el top-5, todos los fragmentos están muy enfocados en la pregunta. Chunks grandes meten ruido.

### ¿Por qué `chunk_overlap=30`?

Para que ideas que cruzan la frontera entre dos chunks **no se pierdan**: ambos chunks comparten 30 tokens de contexto.

### ¿Por qué deep-translator y no LibreTranslate?

`deep-translator` con `GoogleTranslator` no requiere Docker, ni instalación adicional, ni API key. Solo `pip install` y funciona. Para un proyecto de portafolio es ideal: **cualquier persona que clone tu repo puede correrlo en 5 minutos**.

### ¿Por qué `task_type=RETRIEVAL_DOCUMENT` y `RETRIEVAL_QUERY`?

Gemini optimiza el embedding según el rol del texto: un documento que se va a buscar usa una representación distinta a una consulta. Esta distinción **mejora notablemente el recall**.

### ¿Por qué Gemini 2.5 Flash y no Pro?

Flash es **gratis en el free tier**, mucho más rápido, y para Q&A sobre texto recuperado es más que suficiente.

---

## ⚠️ Limitaciones

- **El plan gratuito de Gemini limita a 60 req/min de embeddings**, por eso indexar 1500 chunks toma ~25-30 min la primera vez.
- **deep-translator usa el motor web de Google**: si haces muchas peticiones consecutivas Google puede banear tu IP temporalmente. Por eso el código tiene `time.sleep(0.3)` y reintentos con backoff.
- **Las fórmulas matemáticas se pierden** porque `pypdf` solo extrae texto plano.
- **El LLM solo responde con base en lo recuperado**: si la pregunta es sobre un tema que no está en el libro, dirá "No encontré esa información".

---

## 🌐 Cómo publicar este proyecto en GitHub

### Paso 1 — Crear repositorio

1. Ve a <https://github.com/new>
2. Nombre: `rag-book-qa`
3. Descripción: *"RAG system for Q&A over a translated CS textbook using Gemini + ChromaDB"*
4. **Public** (para portafolio)
5. **NO** marques "Initialize with README"
6. Clic en **Create repository**

### Paso 2 — Inicializar Git localmente

```bash
cd ruta\al\proyecto

git init
git add .
git commit -m "feat: initial commit — RAG pipeline with Gemini and ChromaDB"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/rag-book-qa.git
git push -u origin main
```

### Paso 3 — Verifica qué se está subiendo

Antes de hacer `git push` corre `git status` y asegúrate de que **NO** aparecen:

- ❌ `.env` (contiene tu API key)
- ❌ `chroma_db/` (puede pesar cientos de MB)
- ❌ `translated_chunks.pkl` (cache pesado)
- ❌ `*.pdf` (el libro tiene copyright)

### Paso 4 — Mejoras de presentación

- **Topics:** `rag`, `retrieval-augmented-generation`, `gemini`, `chromadb`, `nlp`, `llm`, `python`, `jupyter-notebook`
- **Screenshot:** captura del notebook en `docs/screenshot.png` y agrégalo al README
- **Demo GIF:** graba la UI con [LICEcap](https://www.cockos.com/licecap/) (Win) → `docs/demo.gif`
- **Release v1.0.0**: desde la pestaña Releases

---

## ⚖️ Licencia

MIT — ver [LICENSE](LICENSE).

> ⚠️ **Nota sobre el libro:** *Computer Science: An Overview (13th ed.)* tiene copyright. Este proyecto **no incluye ni redistribuye el libro** — el usuario debe aportar su propia copia legalmente adquirida.

---

<p align="center">
  Hecho con ☕ y mucha curiosidad por aprender Ciencias de la Computación.
</p>
