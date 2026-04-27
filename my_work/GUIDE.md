# 📘 Guía paso a paso para correr el proyecto en VS Code (Windows)

Esta guía está hecha **a la medida de tu setup**:

- ✅ Sistema operativo: **Windows** (`C:\Users\LENOVO`)
- ✅ Editor: **Visual Studio Code**
- ✅ Python: **3.10 o superior** (ya instalado)
- ✅ **Sin Docker** (usamos `deep-translator`)
- ✅ Ubicación del proyecto: **`C:\Users\LENOVO\Documents\rag-book-qa`**
- ✅ PDF del libro en: **`C:\Users\LENOVO\Downloads\computer-science-an-overview-13th-ed.pdf`**

---

## 📋 Checklist de prerrequisitos

Antes de empezar, asegúrate de tener:

- [ ] **Python 3.10+** instalado (verifica abajo)
- [ ] **VS Code** instalado
- [ ] **Extensión Python** en VS Code
- [ ] **Extensión Jupyter** en VS Code
- [ ] **API Key de Gemini** (la obtienes en el Paso 4)
- [ ] **PDF del libro** en `C:\Users\LENOVO\Downloads\`
- [ ] **Conexión a internet estable** (para deep-translator y Gemini)

---

## 🔧 PARTE 1 — Verificar Python y extensiones de VS Code

### 1.1 — Verificar versión de Python

Abre **PowerShell** (presiona `Win + X` y elige `Windows PowerShell` o `Terminal`). Ejecuta:

```powershell
python --version
```

Resultado esperado: `Python 3.10.x` o superior. Si te dice "no se reconoce" → reinstala desde <https://www.python.org/downloads/> marcando **"Add Python to PATH"**.

### 1.2 — Verificar extensiones de VS Code

Abre VS Code y presiona `Ctrl + Shift + X` para abrir el panel de extensiones. Busca e instala (si no las tienes):

1. **Python** (publicado por Microsoft) — soporte de Python
2. **Jupyter** (publicado por Microsoft) — soporte para notebooks `.ipynb`

> 💡 Estas son las dos únicas extensiones que necesitas. No instales nada más por ahora.

---

## 📂 PARTE 2 — Preparar la carpeta del proyecto

### 2.1 — Descomprimir el zip

1. Descarga el archivo `rag-book-qa.zip` que te di
2. **Click derecho → Extraer todo...**
3. Cuando te pida la ubicación, escribe (o pega):
   ```
   C:\Users\LENOVO\Documents
   ```
4. Confirma **"Extraer"**

Resultado: tendrás la carpeta `C:\Users\LENOVO\Documents\rag-book-qa\` con todos los archivos.

### 2.2 — Abrir la carpeta en VS Code

Hay dos formas. **Usa la que prefieras:**

**Forma A — Desde el Explorador de Windows:**
1. Abre `C:\Users\LENOVO\Documents\rag-book-qa\`
2. Click derecho en una zona vacía → **"Abrir con Code"**

**Forma B — Desde VS Code:**
1. Abre VS Code
2. Menú **File → Open Folder** (`Ctrl + K, Ctrl + O`)
3. Navega a `C:\Users\LENOVO\Documents\rag-book-qa`
4. Click en **"Seleccionar carpeta"**

> 💡 Si VS Code te pregunta *"Do you trust the authors of the files in this folder?"* → click **"Yes, I trust the authors"**.

### 2.3 — Verificar el contenido

En el panel izquierdo (Explorador) deberías ver:

```
📁 RAG-BOOK-QA
├── 📄 .env.example
├── 📄 .gitignore
├── 📄 GUIDE.md
├── 📄 LICENSE
├── 📔 RAG_book_qa.ipynb
├── 📄 README.md
└── 📄 requirements.txt
```

> ⚠️ Si NO ves `.env.example` y `.gitignore`: presiona `Ctrl + Shift + P` → escribe **"Toggle Excluded Files"** → Enter. Esto hace visibles los archivos ocultos.

---

## 🐍 PARTE 3 — Crear el entorno virtual (venv)

> 💡 **¿Qué es un venv?** Es un Python aislado solo para este proyecto. Así las librerías que instales aquí no afectan otros proyectos.

### 3.1 — Abrir terminal integrada de VS Code

Presiona `` Ctrl + ` `` (la tecla del acento grave, debajo del Esc) o ve a **Terminal → New Terminal**.

Verifica que estás en la carpeta correcta. Tu prompt debería verse así:

```
PS C:\Users\LENOVO\Documents\rag-book-qa>
```

### 3.2 — Crear el entorno virtual

En la terminal de VS Code, ejecuta:

```powershell
python -m venv venv
```

Espera ~10 segundos. Se creará una carpeta nueva `venv\` (la verás en el explorador izquierdo).

### 3.3 — Activar el entorno virtual

```powershell
venv\Scripts\activate
```

Verás `(venv)` al inicio de tu prompt:

```
(venv) PS C:\Users\LENOVO\Documents\rag-book-qa>
```

> 🚨 **Si te da error "ejecución de scripts está deshabilitada":**
> 
> Ejecuta este comando una sola vez:
> ```powershell
> Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
> ```
> Te pedirá confirmación → escribe `S` y Enter. Luego vuelve a intentar `venv\Scripts\activate`.

### 3.4 — Instalar todas las dependencias

```powershell
pip install -r requirements.txt
```

⏱️ Tarda **3-5 minutos**. Verás muchas líneas de descarga e instalación.

Al final debería decir algo como:
```
Successfully installed chromadb-... deep-translator-... google-genai-... ...
```

### 3.5 — Verificar que se instalaron

```powershell
pip list | Select-String "chromadb|google-genai|deep-translator|pypdf"
```

Deberías ver las 4 librerías listadas con sus versiones.

---

## 🔑 PARTE 4 — Obtener y configurar tu API Key de Gemini

### 4.1 — Generar la API Key

1. Abre tu navegador y ve a <https://aistudio.google.com/app/apikey>
2. Inicia sesión con tu cuenta de Google
3. Click en **"Create API key"** (botón azul)
4. Selecciona o crea un proyecto nuevo
5. Copia la clave que empieza con `AIzaSy...` (39 caracteres aprox.)

> 🔒 **Guárdala bien.** No la compartas, no la subas a GitHub.

### 4.2 — Crear tu archivo `.env`

En VS Code, en el panel izquierdo:

1. Click derecho sobre el archivo `.env.example`
2. Click en **"Copy"**
3. Click derecho en una zona vacía del explorador
4. Click en **"Paste"** → se crea `.env.example - Copy`
5. Click derecho en `.env.example - Copy` → **"Rename"**
6. Renombra a `.env` (solo `.env`, sin nada más) → Enter

> 💡 **Forma alternativa más rápida:** en la terminal de VS Code:
> ```powershell
> copy .env.example .env
> ```

### 4.3 — Editar el `.env` con tu API Key

1. En VS Code, click sobre el archivo `.env` para abrirlo
2. Verás:
   ```
   GEMINI_API_KEY=tu_clave_aqui
   PDF_PATH=C:\Users\LENOVO\Downloads\computer-science-an-overview-13th-ed.pdf
   ```
3. Reemplaza `tu_clave_aqui` con tu key real:
   ```
   GEMINI_API_KEY=AIzaSyA1B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6Q
   PDF_PATH=C:\Users\LENOVO\Downloads\computer-science-an-overview-13th-ed.pdf
   ```
4. Guarda con `Ctrl + S`

> ⚠️ **NO** pongas comillas alrededor de la clave. **NO** dejes espacios alrededor del `=`. El formato correcto es exactamente: `GEMINI_API_KEY=AIzaSy...`

---

## 📔 PARTE 5 — Abrir el notebook en VS Code

### 5.1 — Abrir el notebook

En el explorador izquierdo de VS Code, **doble click** sobre `RAG_book_qa.ipynb`. Se abrirá en el editor con celdas interactivas.

### 5.2 — Seleccionar el kernel correcto

Esto es **crítico**. El notebook tiene que usar el Python del `venv` que creaste, no el global.

1. En la **esquina superior derecha** del notebook verás un botón que dice algo como **"Select Kernel"** o muestra una versión de Python
2. Click ahí
3. Aparecerá un menú desplegable. Click en **"Python Environments..."**
4. **Busca el que tenga `venv` en el path:**
   ```
   venv (Python 3.10.x)
   .\venv\Scripts\python.exe
   ```
5. **Selecciónalo.**

> 💡 Si NO aparece la opción `venv`: presiona `Ctrl + Shift + P` → escribe **"Python: Select Interpreter"** → busca y selecciona el que esté dentro de tu carpeta `venv`. Luego vuelve a intentar seleccionar el kernel del notebook.

### 5.3 — Verificación rápida del kernel

En la primera celda de código (Paso 1) presiona el botón ▶️ a la izquierda (o `Shift + Enter` con la celda seleccionada).

Si te pide instalar `ipykernel`, dale **"Install"**. VS Code lo instala automáticamente en el venv.

---

## ▶️ PARTE 6 — Ejecutar el notebook celda por celda

> 🎯 **Atajo clave:** `Shift + Enter` ejecuta la celda actual y se mueve a la siguiente.

Vas a ejecutar los **11 pasos en orden**, de arriba hacia abajo. Aquí lo que esperar de cada uno:

### Paso 0 — Instalación
Está comentado. **No hagas nada** (ya instalaste con `pip install -r requirements.txt`).

### Paso 1 — API Key
**Lo que ves:** `✅ API Key cargada correctamente (longitud: 39 caracteres)`

> 🚨 Si te aparece un cuadro pidiendo la API Key (en lugar del mensaje de éxito), es porque tu `.env` no se está cargando. Verifica que está en `C:\Users\LENOVO\Documents\rag-book-qa\.env` y que el formato es correcto.

### Paso 2 — Probar deep-translator
**Lo que ves:**
```
✅ deep-translator funciona correctamente.
   Prueba: 'Hello world' → 'Hola Mundo'
```

> 🚨 Si falla con error de conexión: verifica tu internet. Si tu red bloquea Google Translate (algunas universidades/empresas lo hacen), no funcionará.

### Paso 3 — Extraer texto del PDF
⏱️ ~30 segundos

**Lo que ves:**
```
📄 Páginas en el PDF: 624
📊 Caracteres extraídos: 1,800,000+
📊 Palabras aprox.: 290,000+
🔍 Primeros 500 caracteres:
[texto del libro en inglés]
```

> 🚨 Si da `FileNotFoundError`: la ruta del PDF está mal. Verifica que existe `C:\Users\LENOVO\Downloads\computer-science-an-overview-13th-ed.pdf`. Si tiene otro nombre, edita la variable `PDF_PATH` en esa celda.

### Paso 4 — Tokenización
**Lo que ves:**
```
🔢 Total de tokens en el libro: ~400,000
   (gemini-embedding-001 acepta hasta 8 192 tokens por request,
    por eso necesitamos hacer chunking)
```

### Paso 5 — Chunking
⏱️ ~10-15 segundos

**Lo que ves:**
```
✂️  Total de chunks generados: ~1500
🔍 Ejemplo — chunk #50 (en inglés, antes de traducir):
[fragmento de texto en inglés]
```

### Paso 6 — Traducción 🌍

⏱️ **~10-15 minutos** (la parte más larga después de los embeddings)

**Lo que ves:** una barra de progreso `tqdm` que avanza chunk por chunk.

```
🌍 Traduciendo EN→ES: 100%|████████| 1500/1500 [12:30<00:00]
💾 Guardadas 1500 traducciones en translated_chunks.pkl
🔍 Ejemplo — chunk #50 traducido al español:
[fragmento traducido]
```

> 💾 **Importante:** se guarda automáticamente en `translated_chunks.pkl` cada 50 chunks. Si el kernel se reinicia, la próxima vez que ejecutes esta celda **continuará desde donde se quedó** sin volver a empezar.
>
> 🚨 Si Google Translate da muchos errores o frena: detén la celda con el botón cuadrado ⏹️, espera 5 minutos y vuelve a ejecutar. Continuará desde donde quedó.

### Paso 7 — Configurar embeddings
⏱️ ~1 segundo

**Lo que ves:**
```
✅ Embedding functions configuradas con Gemini.
   Modelo: gemini-embedding-001 | Dimensiones: 768
   Rate limit: 1 request cada 1.1s (~54/min)
```

### Paso 8 — Crear ChromaDB
⏱️ ~2 segundos

**Lo que ves:**
```
📦 Colección lista: 'computer_science_book_es'
   Documentos ya indexados: 0
   Carpeta de persistencia: ./chroma_db
```

### Paso 9 — Indexación con embeddings 📥

⏱️ **~25-30 minutos** (la parte más larga)

Este paso llama a Gemini para convertir cada chunk en un vector de 768 dimensiones. Por el rate limit del free tier (60 req/min), tarda lo suyo.

**Lo que ves:**
```
📥 Indexando 1500 chunks (empezando desde el #0)...
   Esto tomará ~27.5 minutos.
📥 Indexando: 100%|████████| 150/150 [27:32<00:00]
✅ Indexación completa: 1500 chunks en ChromaDB.
```

> 💡 **Mientras esperas:** ve por un café ☕. Esta es la única parte donde tienes que ser paciente.
> 
> 🛑 **Si necesitas parar a la mitad:** click en el botón cuadrado ⏹️. Cuando regreses, ChromaDB ya guardó lo que se indexó hasta ahí. Solo vuelve a ejecutar la celda y continúa desde donde quedó.
>
> 🚨 **Si te da error `RESOURCE_EXHAUSTED` o `429`:** llegaste al rate limit. El código hace 3 reintentos automáticos. Si insisten los errores, espera 1 minuto y vuelve a ejecutar la celda.

### Paso 10 — Probar el pipeline RAG
⏱️ ~5 segundos

**Lo que ves:**
```
🧪 Prueba rápida del pipeline RAG:

📝 RESPUESTA:
 [respuesta sobre qué es un sistema operativo, generada por Gemini]

📚 FUENTES (top 3):
   1. (distancia=0.35) [fragmento del libro]
   2. (distancia=0.41) [fragmento del libro]
   3. (distancia=0.46) [fragmento del libro]
```

### Paso 11 — UI bonita con ipywidgets 🎨

**Lo que ves:** un cuadro morado que dice *"Pregunta al libro de Ciencias de la Computación"* con un campo de texto, un slider, y dos botones (🚀 Preguntar y 🧹 Limpiar).

> 🚨 **Si NO ves el widget interactivo y solo aparece `VBox(children=...)` o nada**: VS Code a veces requiere reiniciar el kernel. Presiona `Ctrl + Shift + P` → busca **"Jupyter: Restart Kernel"** → confirma. Luego ejecuta todas las celdas otra vez (botón **"Run All"** arriba del notebook). Como los caches están guardados en disco, los pasos 6 y 9 saltarán y llegarás al Paso 11 en ~2 minutos.

---

## 💬 PARTE 7 — Hacer preguntas al libro

Una vez que ves la caja morada del Paso 11:

1. **Click** dentro del campo de texto
2. **Escribe** tu pregunta. Ejemplos:
   - *¿Qué es un sistema operativo?*
   - *Explica qué es la complejidad algorítmica*
   - *Diferencia entre RAM y memoria caché*
   - *¿Cómo funciona un compilador?*
   - *¿Qué es la programación orientada a objetos?*
3. **Ajusta el slider** "Fragmentos a recuperar" (recomiendo dejarlo en 5)
4. **Click en 🚀 Preguntar**
5. Espera 2-3 segundos
6. Verás:
   - Tu pregunta
   - La respuesta generada por Gemini
   - Los fragmentos del libro que se consultaron (puedes expandir cada uno haciendo click en la flecha)

> 💡 **Las preguntas son ilimitadas** una vez indexado todo. Solo cuestan ~2 requests de Gemini por pregunta (búsqueda + generación), bien dentro del free tier.

---

## 🔄 PARTE 8 — Cómo retomar el trabajo otro día

Cuando vuelvas mañana o más tarde:

1. **Abre VS Code**
2. **File → Open Recent** → click en `rag-book-qa`
3. Abre la terminal integrada (`` Ctrl + ` ``)
4. Activa el venv: `venv\Scripts\activate`
5. Abre `RAG_book_qa.ipynb`
6. Verifica el kernel (esquina superior derecha) → debe ser el del `venv`
7. Click en **"Run All"** (botón ▶️▶️ arriba del notebook)
   - Pasos 0-5 corren en ~30 segundos
   - Paso 6 detecta `translated_chunks.pkl` → instantáneo
   - Paso 9 detecta que ChromaDB ya está poblado → instantáneo
   - Llegas al Paso 11 en ~1 minuto y ya puedes preguntar

---

## 🩺 PARTE 9 — Troubleshooting (errores comunes)

### ❌ "python: command not found" o "no se reconoce python"
Python no está en el PATH. Reinstálalo desde python.org marcando **"Add Python to PATH"**.

### ❌ "venv\Scripts\activate : execution policy"
```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```
Confirma con `S` + Enter.

### ❌ Kernel no aparece en VS Code
Tu venv no está siendo detectado.
1. `Ctrl + Shift + P` → **"Python: Select Interpreter"**
2. Click en **"Enter interpreter path..."**
3. Pega: `C:\Users\LENOVO\Documents\rag-book-qa\venv\Scripts\python.exe`
4. Reinicia VS Code

### ❌ "Module not found: google.genai"
El kernel del notebook no es el del venv. Verifica en la esquina superior derecha del notebook que dice algo como `venv (Python 3.10.x)`.

### ❌ Paso 3: "FileNotFoundError" con el PDF
La ruta del PDF está mal. Abre el `.ipynb`, ve al Paso 3, y verifica la línea:
```python
PDF_PATH = r"C:\Users\LENOVO\Downloads\computer-science-an-overview-13th-ed.pdf"
```
Asegúrate de que el archivo existe en esa ruta exacta. La `r` antes de la cadena es importante (raw string para Windows).

### ❌ Paso 6: Google Translate no responde / errores constantes
- Tu IP puede estar temporalmente bloqueada por hacer demasiadas peticiones. Espera 10-15 minutos.
- O cambia el `TRANSLATE_SLEEP` en el Paso 6 de `0.3` a `1.0` (más lento pero más seguro).

### ❌ Paso 9: "RESOURCE_EXHAUSTED" / "429 Too Many Requests"
Excediste el rate limit de Gemini. El código tiene reintentos. Si insiste, espera 1 minuto y vuelve a ejecutar la celda — continuará donde quedó.

### ❌ Paso 9: "Quota exceeded for the day"
Llegaste al límite diario del free tier. Espera 24h o usa otra cuenta de Google. Lo ya indexado **se quedó guardado**.

### ❌ Paso 11: La UI aparece como `VBox(children=...)` o no se ve
ipywidgets no se está renderizando. En VS Code:
1. `Ctrl + Shift + P` → **"Jupyter: Restart Kernel"**
2. Click en **"Run All"** arriba del notebook
3. Como hay cache, llegas rápido al Paso 11

Si persiste, verifica que tienes la **extensión Jupyter** de Microsoft instalada y actualizada.

### ❌ "Permission denied" al instalar paquetes
Significa que activaste el venv mal o no lo activaste. Verifica que tu prompt empieza con `(venv)`. Si no, ejecuta:
```powershell
venv\Scripts\activate
```

---

## ✅ Checklist final antes de empezar

Antes de dar `Run All` al notebook, confirma:

- [ ] Estás en VS Code con la carpeta `C:\Users\LENOVO\Documents\rag-book-qa\` abierta
- [ ] Ves el prefijo `(venv)` en la terminal
- [ ] `pip list` muestra `google-genai`, `chromadb`, `deep-translator`, `pypdf`
- [ ] El archivo `.env` tiene tu `GEMINI_API_KEY` real (no `tu_clave_aqui`)
- [ ] El PDF está en `C:\Users\LENOVO\Downloads\computer-science-an-overview-13th-ed.pdf`
- [ ] El kernel del notebook (esquina sup. derecha) apunta al `venv`
- [ ] Tienes conexión estable a internet

Si todos están marcados → **dale `Run All`** y disfruta. ☕

---

## 📊 Tiempo total estimado (primera vez)

| Etapa | Tiempo |
|---|---|
| Setup (Partes 1-5) | ~10 minutos |
| Pasos 0-5 del notebook | ~2 minutos |
| Paso 6 (traducción) | ~10-15 minutos |
| Pasos 7-8 | ~30 segundos |
| Paso 9 (indexación con embeddings) | ~25-30 minutos |
| Pasos 10-11 + primera pregunta | ~10 segundos |
| **TOTAL (primera vez)** | **~50-60 minutos** |
| **Veces siguientes** | **~2 minutos** (todo cacheado) |

---

¿Algo no funcionó? Copia el mensaje de error completo y lo resolvemos. 🚀
