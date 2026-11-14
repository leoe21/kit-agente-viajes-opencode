---
name: Nueva skill de viaje
description: Crea una nueva skill (herramienta) para el agente de viajes Parcero siguiendo el patrón del proyecto (un archivo en skills/ con TOOL + run) y la registra en skills/__init__.py. Úsala cuando se quiera añadir una capacidad al agente, por ejemplo "recomendar restaurantes", "clima del destino" o "frases útiles".
---

## Cuándo usar esta skill
Cuando se pida crear o añadir una **skill del agente** de viajes (una nueva
herramienta que Parcero pueda usar).

## El patrón del proyecto (obligatorio)
Cada skill del agente vive en su propio archivo `skills/<nombre>.py` y expone:

- `TOOL`: un diccionario con
  - `"name"`: el nombre de la herramienta, en minúsculas con guion bajo
    (p. ej. `"recomendar_restaurantes"`). Debe coincidir con lo que use el modelo.
  - `"description"`: una frase clara en español que diga qué hace y cuándo usarla.
  - `"parameters"`: el esquema de entrada en el formato de OpenAI/Groq
    (`{"type": "object", "properties": {...}, "required": [...]}`).
- `run(...)`: la función que ejecuta la skill y **devuelve texto en español**.
  Sus argumentos deben coincidir con las `properties` del `TOOL`.

Si la skill necesita datos de destinos, importa lo que haga falta desde
`._datos` (`cargar_destinos`, `buscar_destino`, `normalizar`); no abras el JSON
directamente.

Hay un ejemplo completo del molde en `references/patron-skill.md`; léelo antes
de escribir el archivo.

## Pasos
1. Si falta información, pregunta en una frase qué debe hacer la skill y qué
   parámetros recibe (nombre y tipo de cada uno, cuáles son obligatorios).
2. Crea `skills/<nombre>.py` con `TOOL` + `run`, comentado y en español,
   siguiendo el molde de `references/patron-skill.md`.
3. Regístrala: abre `skills/__init__.py` y añade el nuevo módulo a la lista
   `_MODULOS`. **No** modifiques `TOOLS` ni `DISPATCH`: se construyen solos a
   partir de `_MODULOS`.
4. Propón una prueba rápida por consola, por ejemplo:
   `python -c "from skills.<nombre> import run; print(run(...))"`

## Reglas
- Nunca escribas claves ni API keys en el código.
- Los precios son estimaciones en pesos colombianos (COP); déjalo claro.
- No pidas datos personales sensibles.
- Mantén el código simple y bien comentado: es material de clase.
