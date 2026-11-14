# Proyecto: "Parcero" — planificador de viajes por Colombia

Este archivo lo lee OpenCode al abrir la carpeta. Son las reglas del proyecto.

## Objetivo
Construir, paso a paso y con fines educativos, un agente de IA sencillo que
conversa con el usuario y usa "skills" (herramientas) para:
recomendar destinos, armar un itinerario, estimar el presupuesto y hacer la
lista de maleta. Es una demostración de clase.

Responde siempre en **español** y explica en frases cortas lo que haces.

## Pila técnica
- Python.
- Interfaz de chat con **Streamlit** (`app.py`, se corre con `streamlit run app.py`).
- El modelo se llama con la librería oficial de **OpenAI**, pero apuntando a
  **Groq** (es compatible con el estándar OpenAI). Para cambiar de proveedor solo
  se cambian la URL base, el modelo y la API key.
  - URL base: `https://api.groq.com/openai/v1`
  - Modelo: `openai/gpt-oss-120b` (modelo gratis de Groq con soporte de herramientas)

## Estructura del proyecto
```
data/destinos.json      # el "conocimiento": destinos de Colombia
skills/                 # una skill (herramienta) por archivo
  _datos.py             # utilidades para leer los datos
  destinos.py           # recomendar_destinos
  itinerario.py         # armar_itinerario
  presupuesto.py        # estimar_presupuesto
  maleta.py             # lista_maleta
  __init__.py           # junta TOOLS y DISPATCH a partir de _MODULOS
prompts/sistema.md      # la personalidad y las reglas del agente
agent.py                # el cerebro: el bucle de uso de herramientas
app.py                  # la cara: la interfaz en Streamlit
requirements.txt
.streamlit/             # config.toml y secrets.toml.example
```

## Convenciones (obligatorias)
- **Una skill del agente por archivo** en `skills/`. Cada archivo expone:
  - `TOOL`: diccionario con `name`, `description` y `parameters` en el formato
    de herramientas de OpenAI/Groq.
  - `run(...)`: la función que ejecuta la skill y devuelve **texto en español**.
- Las skills se registran en `skills/__init__.py` añadiéndolas a la lista
  `_MODULOS`. `TOOLS` y `DISPATCH` se arman solos a partir de esa lista: no se
  tocan a mano.
- Para leer datos de destinos se usan `cargar_destinos`, `buscar_destino` y
  `normalizar` de `skills/_datos.py` (no se abre el JSON directamente en cada skill).
- **Nunca** se escriben claves ni API keys en el código. La key se lee de los
  secretos de Streamlit (`st.secrets`) o de la variable de entorno `GROQ_API_KEY`.
- Los precios son **estimaciones** en pesos colombianos (COP); se deja claro que
  son aproximados y varían con la temporada.
- El agente **no** pide ni maneja datos personales sensibles (documentos,
  tarjetas, contraseñas).
- Código **sencillo y bien comentado**: es material para enseñar.

## Ayuda disponible
Hay una skill de OpenCode llamada **`nueva-skill-viaje`** que crea una skill del
agente con el molde correcto (TOOL + run) y la registra en `skills/__init__.py`.
Se invoca escribiendo `@nueva-skill-viaje` en el prompt.
