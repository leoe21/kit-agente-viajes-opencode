# Instalación y preparación de la demo

Paso a paso para dejar todo listo ANTES de clase. En clase solo se abre OpenCode
y se van pegando los prompts de `00 Prompts para armar el proyecto (OpenCode).txt`.

> Los comandos se dan para **Windows, macOS y Linux**, porque los equipos de los
> estudiantes pueden ser de cualquiera de los tres. Cada bloque está marcado con
> el sistema. Se usa solo el del sistema propio.

---

## 1. Requisito previo: Node.js 18 o superior

OpenCode se instala con Node. Verificar si ya está:

```
node --version
```

Si no está, instalar Node 18+ desde https://nodejs.org (los tres sistemas) o con
el gestor de paquetes del sistema.

---

## 2. Instalar OpenCode

**La forma que sirve en los tres sistemas (recomendada):**

```
npm install -g opencode-ai
```

(El paquete se llama `opencode-ai`, no `opencode`.)

Alternativas según el sistema:

- **macOS:** `brew install sst/tap/opencode`
  o el script: `curl -fsSL https://opencode.ai/install | bash`
- **Linux:** `curl -fsSL https://opencode.ai/install | bash`
  o `npm install -g opencode-ai`
- **Windows:** `npm install -g opencode-ai`,
  o `scoop install opencode`, o `choco install opencode`.
  (El script `curl ... | bash` en Windows requiere WSL.)

Verificar la instalación (en este equipo ya está OpenCode 2.2.6):

```
opencode --version
```

Si tras instalar con npm el comando `opencode` no aparece:
- **Windows:** agregar la carpeta de binarios globales de npm (normalmente
  `%APPDATA%\npm`) al PATH.
- **macOS/Linux:** asegurarse de que `$(npm prefix -g)/bin` esté en el PATH.

Doc oficial: https://opencode.ai/docs

---

## 3. Crear la API key de Groq (gratis)

Groq da acceso gratuito a modelos abiertos (se usa `openai/gpt-oss-120b`) con el
estándar OpenAI. Es igual en los tres sistemas (se hace en el navegador):

1. Entrar a https://console.groq.com y crear una cuenta.
2. Ir a **API Keys** → **Create API Key**.
3. Copiar la clave (empieza por `gsk_...`). Se muestra una sola vez.

La clave **nunca** se pega en el chat, ni en el código, ni se sube a git.

### Conectar OpenCode a un modelo con esa misma llave

Ojo: hay **dos usos** de la llave de Groq y es fácil confundirlos.

1. **OpenCode** (la herramienta donde se escriben los prompts) también es una IA
   y necesita un modelo para poder construir.
2. **La app** que construimos (Parcero) necesita el modelo para responder → eso
   es el `secrets.toml` del paso 4.

Se puede usar la **misma llave de Groq** para las dos. Para OpenCode, una sola
vez, en una terminal:

```
opencode auth login
```

Elegir **Groq** en la lista y pegar la llave (`gsk_...`). Queda guardada en el
equipo (en `~/.local/share/opencode/auth.json`), no hay que repetirlo.

Después, al abrir OpenCode, elegir el modelo con el comando `/models`. Para
**construir** conviene un modelo de **Google Gemini** (por ejemplo `Gemini 3.5
Flash Lite`): el nivel gratis de Groq tiene un límite de tokens por minuto muy
bajo para OpenCode y se queda corto. Groq se usa después dentro de la app ya
construida, no para construirla.

---

## 4. Guardar la key para que la app la use

La llave hay que dejarla en un sitio del que la app la pueda leer, **sin
escribirla en el código**. Hay dos formas; para la app de Streamlit se usa la **b)**.

### a) Variable de entorno — solo para probar el cerebro por consola

Sirve para `python agent.py "..."`. Dura **solo mientras esa ventana de terminal
siga abierta**; al cerrarla hay que volver a ponerla.

- **Windows (PowerShell):** `$env:GROQ_API_KEY = "gsk_tu_key_aqui"`
- **Windows (CMD):** `set GROQ_API_KEY=gsk_tu_key_aqui`
- **macOS / Linux (bash o zsh):** `export GROQ_API_KEY="gsk_tu_key_aqui"`

### b) Archivo de secretos — es la que usa la app de Streamlit

Streamlit busca la llave en un archivo llamado **`secrets.toml`**, que debe estar
dentro de una carpeta llamada **`.streamlit`**. Esa carpeta va **en la misma
carpeta donde está `app.py`**, es decir, dentro de `Kit_demo_OpenCode\proyecto\`
(ahí construye OpenCode). Queda así:

```
Kit_demo_OpenCode\
└── proyecto\
    ├── app.py
    ├── agent.py
    ├── skills\
    └── .streamlit\
        └── secrets.toml   ← aquí va la llave
```

(El punto al inicio de `.streamlit` es normal: hace que la carpeta quede "oculta".
No es un error ni una extensión.)

**Paso a paso para crearlo.** Abrir una terminal, entrar a la carpeta del kit y
crear la carpeta y el archivo de una vez (cambiar la ruta por la de tu equipo):

- **Windows (PowerShell):**
  ```powershell
  cd "D:\OneDrive\Desktop\Microdencial en creación de agentes de IA\05_Modulo 4.9_ Demo Open code\Kit_demo_OpenCode\proyecto"
  New-Item -ItemType Directory -Force .streamlit | Out-Null
  Set-Content -Path .streamlit\secrets.toml -Value 'GROQ_API_KEY = "gsk_tu_key_aqui"' -Encoding utf8
  ```
- **macOS / Linux (Terminal):**
  ```bash
  cd ~/ruta/a/Kit_demo_OpenCode/proyecto
  mkdir -p .streamlit
  echo 'GROQ_API_KEY = "gsk_tu_key_aqui"' > .streamlit/secrets.toml
  ```

Luego **abrir `.streamlit/secrets.toml` y reemplazar `gsk_tu_key_aqui`** por la
llave real copiada de Groq (la que empieza por `gsk_`). El archivo final es una
sola línea:

```toml
GROQ_API_KEY = "gsk_laLlaveRealDeGroq"
```

**Para comprobar que quedó bien:**

- **Windows:** `Get-Content .streamlit\secrets.toml`
- **macOS / Linux:** `cat .streamlit/secrets.toml`

**Cómo la usa la app (no hay que hacer nada más).** Al correr `streamlit run
app.py`, Streamlit lee solo ese archivo y el código `st.secrets["GROQ_API_KEY"]`
toma la llave. No se pasa por ningún comando ni se escribe en el código.

Ese archivo está en `.gitignore`: **nunca** se sube a git.

---

## 5. Preparar el entorno de Python

Requiere Python 3.10 o superior (`python --version` / `python3 --version`).

- **Windows (PowerShell):**
  ```powershell
  python -m venv .venv
  .\.venv\Scripts\Activate.ps1
  pip install streamlit openai
  ```
- **macOS / Linux:**
  ```bash
  python3 -m venv .venv
  source .venv/bin/activate
  pip install streamlit openai
  ```

(En clase, `requirements.txt` lo genera OpenCode en el Paso 9; después basta
`pip install -r requirements.txt`.)

---

## 6. Abrir OpenCode en la subcarpeta `proyecto/`

El proyecto se construye **dentro de `proyecto/`**, no en la raíz del kit. Así lo
que crea OpenCode (data/, skills/, agent.py, app.py, etc.) queda separado de la
semilla y de los archivos de OpenCode, y reiniciar es solo vaciar esa carpeta.

Desde `Kit_demo_OpenCode\`, entrar a la subcarpeta y abrir OpenCode (los tres
sistemas):

```
cd proyecto
opencode
```

Aunque OpenCode se abre parado en `proyecto/`, lee el `AGENTS.md` del kit (el
contexto del proyecto) y descubre la skill `nueva-skill-viaje` de
`.opencode/skills/`, porque busca esos archivos en las **carpetas superiores**
(la raíz del repositorio git). Para comprobarlo, escribir `@` y ver que aparece
`nueva-skill-viaje` en la lista. Todo lo que construya caerá dentro de `proyecto/`.

### Nota de aislamiento (recomendada)
OpenCode también carga skills personales (de `~/.config/opencode/skills`,
`~/.claude/skills` y `.agents/`) y el `AGENTS.md` de carpetas superiores. Para que
en clase **solo** aparezca la skill del proyecto y la demo sea limpia, conviene
que por encima del kit no haya otro `AGENTS.md` ni skills personales que estorben.
(Es el mismo cuidado de aislamiento del Módulo 5.)

---

## 7. Correr la app ya construida

Desde la subcarpeta `proyecto/` (donde quedó `app.py`), en los tres sistemas:

```
cd proyecto
streamlit run app.py
```

(Si OpenCode ya estaba abierto parado en `proyecto/`, se corre desde ahí mismo en
otra terminal.)

---

## 8. Reiniciar la demo (dejar el kit como semilla)

El `.gitignore` ignora todo lo que OpenCode genera dentro de `proyecto/` y conserva
la "semilla": este `INSTALACION.md`, el `AGENTS.md`, la carpeta `.opencode/`, el
`00 Prompts...txt`, el `README.md` y la carpeta `proyecto/` vacía (con su `.gitkeep`).

Para volver a empezar de cero (si la carpeta es un repositorio git), en los tres
sistemas:

```
git clean -fdX
```

Esto borra solo los archivos ignorados —es decir, todo lo que OpenCode creó dentro
de `proyecto/`, incluido el `secrets.toml`— y deja la semilla intacta: la carpeta
`proyecto/` queda vacía (solo con su `.gitkeep`), lista para rearmar en vivo.

> Ojo: al vaciar `proyecto/` también se borra `proyecto/.streamlit/secrets.toml`.
> Si se quiere conservar la llave, copiarla aparte antes de reiniciar y volver a
> dejarla después (o rehacer el paso 4b).

### Volver al estado original (rama `base`)

El repositorio tiene una rama llamada **`base`**: es una foto congelada del kit
tal como se comparte. Si se modificó algún archivo de la semilla por error, se
restaura **todo** al original con:

```
git fetch origin
git reset --hard origin/base
git clean -fdX
```

- `git fetch origin` trae la rama base.
- `git reset --hard origin/base` devuelve los archivos de la semilla a su estado original.
- `git clean -fdX` borra el proyecto que generó OpenCode.

El kit queda como recién descargado, listo para empezar de cero.
