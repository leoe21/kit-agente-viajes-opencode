# Kit de la demo · Construir un agente con OpenCode

Kit autocontenido para armar **en vivo**, con OpenCode, el agente de viajes
"Parcero" (planificador de viajes por Colombia). El código de referencia
(solucionario) está en `C:\Git_Hub_leoe21\agente-viajes`; este kit es la
**semilla** desde la que se reconstruye el proyecto delante de la clase.

## Dos sentidos de "skill" (la idea de la demo)
| Nivel | Qué es | Dónde vive |
|---|---|---|
| **Skill del agente** | Herramienta que usa Parcero: recomendar destinos, itinerario, presupuesto, maleta (patrón `TOOL` + `run`) | `skills/*.py` (lo genera OpenCode) |
| **Skill de OpenCode** | Capacidad que se le da a OpenCode *para construir* esas skills bien y parejas | `.opencode/skills/nueva-skill-viaje/SKILL.md` |

## Qué hay en la semilla
- `INSTALACION.md` — instalar OpenCode, crear la API key de Groq, variables,
  cómo correr y cómo reiniciar la demo.
- `AGENTS.md` — el contexto del proyecto que OpenCode lee al abrir la carpeta.
- `.opencode/skills/nueva-skill-viaje/` — la skill de OpenCode que arma una skill
  del agente (con su `references/patron-skill.md`).
- `00 Prompts para armar el proyecto (OpenCode).txt` — los prompts para pegar
  uno por uno, en orden, siguiendo el mapa de 10 pasos.
- `.gitignore` — ignora el proyecto generado; conserva la semilla.

## Cómo se usa en clase
1. Seguir `INSTALACION.md` (antes de clase).
2. Abrir OpenCode en esta carpeta: `opencode`.
3. Ir pegando los prompts de `00 Prompts...txt` en orden, con el ciclo
   **Explico → Pido a OpenCode → Revisamos → Probamos**.
4. En las skills del agente se puede usar el atajo `@nueva-skill-viaje` para que
   OpenCode las arme con el molde correcto.
5. Al final: `streamlit run app.py` y, si se quiere, desplegar en Streamlit.

## Seguridad
- La API key de Groq nunca va en el chat ni en el código; solo en
  `.streamlit/secrets.toml` (ignorado por git) o en el panel de Streamlit Cloud.
- Solo datos ficticios/públicos de turismo. El agente no pide datos personales.
