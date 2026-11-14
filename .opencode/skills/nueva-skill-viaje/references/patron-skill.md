# Molde de una skill del agente

Ejemplo completo del patrón `TOOL` + `run`. Una skill nueva se escribe igual,
cambiando el nombre, la descripción, los parámetros y la lógica de `run`.

```python
"""Skill: <qué hace, en una frase>."""
from ._datos import buscar_destino  # solo si la skill usa datos de destinos

TOOL = {
    "name": "nombre_de_la_herramienta",          # minúsculas con guion bajo
    "description": (
        "Qué hace la skill y cuándo conviene usarla. En español, claro, "
        "porque esto es lo que lee el modelo para decidir si la llama."
    ),
    "parameters": {                               # esquema de entrada (OpenAI/Groq)
        "type": "object",
        "properties": {
            "destino": {"type": "string", "description": "Nombre del destino."},
            "dias": {
                "type": "integer", "minimum": 1, "maximum": 30,
                "description": "Número de días del viaje.",
            },
            # Para opciones cerradas se usa enum:
            # "nivel": {"type": "string", "enum": ["economico", "medio", "alto"]},
        },
        "required": ["destino", "dias"],          # los argumentos obligatorios
    },
}


def run(destino, dias):                           # los argumentos = las properties
    _, d = buscar_destino(destino)
    if not d:
        return f"No tengo información de '{destino}'. Prueba con otro destino de Colombia."
    # ... lógica de la skill ...
    return f"Resultado para {d['nombre']} ({dias} días): ..."   # SIEMPRE texto en español
```

## Registro en skills/__init__.py

Solo se añade el módulo nuevo a la lista `_MODULOS`. El resto no se toca:

```python
from . import destinos, itinerario, presupuesto, maleta, nombre_de_la_herramienta

_MODULOS = [destinos, itinerario, presupuesto, maleta, nombre_de_la_herramienta]

# TOOLS y DISPATCH se arman solos a partir de _MODULOS:
TOOLS = [{"type": "function", "function": m.TOOL} for m in _MODULOS]
DISPATCH = {m.TOOL["name"]: m.run for m in _MODULOS}
```
