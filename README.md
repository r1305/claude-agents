# claude-agents

Subagentes de Claude Code para orquestar un flujo de desarrollo por roles: un **orquestador** que solo delega, y agentes especializados en implementar, validar, revisar, explorar y mantener herramientas. Cada rol tiene su modelo por defecto para controlar el consumo.

Este repositorio es un **marketplace de plugins** con un único plugin: `dev-agents`.

> Son ejemplos de un flujo concreto, no una plantilla universal. Léelos, adáptalos a tu proyecto y quítales lo que no uses.

## Agentes incluidos

| Agente | Modelo por defecto | Cuándo usarlo |
|--------|--------------------|---------------|
| `orchestrator` | Hereda el modelo de la sesión (`inherit`) | Es el punto de entrada. Recibe la petición, decide el rol y delega. No implementa, valida ni revisa por sí mismo. |
| `dev` | Sonnet | Implementar funcionalidades, arreglar bugs y refactorizar. Respeta las convenciones del proyecto. Al terminar ofrece validación con `qa`. |
| `qa` | Haiku | Validación posterior a un cambio: compilación, tests, arranque y checklist de despliegue. Solo lectura y comprobaciones en shell; si todo pasa, puede hacer commit y push. |
| `review` | Opus | Revisión de código en solo lectura: bugs, seguridad, rendimiento y restricciones de despliegue. Informa por severidad con `archivo:línea`. |
| `explorer` | Sonnet | Lee un proyecto y guarda un mapa de su estructura para que los demás agentes no tengan que re-explorarlo. Solo escribe en la carpeta de mapas. |
| `tooling` | Sonnet | Crea y actualiza skills, agentes y comandos de Claude Code. Nunca edita código de la aplicación. |

Flujo típico: `orchestrator` → `explorer` (si el proyecto no tiene mapa) → `dev` → `qa` → `review` si hace falta una revisión adicional.

## Instalación

Desde una terminal con `claude` (no funciona en la pestaña Code de la aplicación de escritorio):

```
/plugin marketplace add r1305/claude-agents
/plugin install dev-agents@claude-agents
```

Si tu versión de Claude Code muestra otra forma en `/plugin`, usa la que indique. También puedes instalarlo directamente con la CLI:

```
claude plugin marketplace add r1305/claude-agents
claude plugin install dev-agents@claude-agents
```

Tras instalarlo, reinicia la sesión o ejecuta `/reload-plugins`.

### Alternativa: copiar los archivos

Si prefieres no usar plugins, clona el repositorio y copia los agentes a tu carpeta personal:

```bash
git clone https://github.com/r1305/claude-agents.git
mkdir -p ~/.claude/agents
cp claude-agents/plugins/dev-agents/agents/*.md ~/.claude/agents/
```

En Windows (PowerShell), la carpeta destino es `%USERPROFILE%\.claude\agents\`.

## Cómo adaptarlos

- **`AGENTS.md` del proyecto.** `dev`, `qa`, `review` y `tooling` leen un `AGENTS.md` en la raíz del proyecto (o del repositorio que toquen) para conocer convenciones: zona horaria, formato de commits, comandos de verificación, restricciones de despliegue. Si no lo tienes, créalo; los agentes seguirán funcionando con reglas genéricas, pero con menos contexto.
- **Mapas de proyecto.** `explorer` guarda sus mapas en una carpeta `project-maps/` de tu workspace (por ejemplo `<workspace>/.claude/project-maps/<proyecto>.md`). Ajusta la ruta en los `.md` si usas otra convención.
- **Placeholders.** Los agentes usan marcadores como `<workspace>`, `<proyecto>` o `<app>`. Sustitúyelos por tus rutas y nombres reales.
- **Modelos y límite de uso.** Las secciones *Modelos por tarea* y *Límite de uso* describen una tabla de decisión que el orquestador espera encontrar en las instrucciones de tu proyecto (por ejemplo en un `CLAUDE.md`). Adapta esa tabla a tus planes y límites.
- **Restricciones de hosting.** Los agentes incluyen comprobaciones pensadas para hosting compartido (una sola instancia, pool de base de datos reutilizado, sin procesos hijos, sin timers sin limpieza, etc.). Quita o cambia las que no apliquen a tu entorno.
- **Pruebas contra datos reales.** Si tu `qa` ejecuta pruebas contra una base de datos real, revisa con cuidado las reglas de datos de prueba antes de usarlo.

## Estructura del repositorio

```
.claude-plugin/marketplace.json       catálogo del marketplace
plugins/dev-agents/
  .claude-plugin/plugin.json          manifiesto del plugin
  agents/*.md                         los seis subagentes
```

## Validación

```bash
claude plugin validate .
claude plugin validate plugins/dev-agents
```

## Licencia

MIT. Consulta [LICENSE](LICENSE).
