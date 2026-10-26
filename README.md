# qsoro-skills

Marketplace personal de plugins/skills para Claude Code. Un solo `marketplace add` y después instalás lo que quieras de esta lista.

| Plugin (id de instalación) | Qué hace | Origen | Licencia |
|---|---|---|---|
| `superpowers` | Metodología de desarrollo: brainstorm → plan → ejecución, TDD, debugging sistemático, code review, subagentes | [obra/superpowers](https://github.com/obra/superpowers) | MIT |
| `mem` (claude-mem) | Memoria persistente entre sesiones: registra lo que hace Claude, lo comprime y lo reinyecta después | [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | Apache-2.0 |
| `impeccable` | Diseño frontend: `/impeccable init`, `audit`, `critique`, `polish`… + anti-patrones | [pbakaus/impeccable](https://github.com/pbakaus/impeccable) | Apache-2.0 |
| `ponytail` | Fuerza la solución mínima (stdlib y lo que ya existe primero). `/ponytail-review`, `-audit`, `-debt` | [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail) | MIT |
| `find-skills` | Busca e instala otras skills de [skills.sh](https://skills.sh) con `npx skills` | [vercel-labs/skills](https://github.com/vercel-labs/skills) | MIT |

## Cómo está armado

```
.claude-plugin/marketplace.json   ← el catálogo
plugins/find-skills/              ← único plugin con código local (copia del SKILL.md upstream)
```

- **superpowers, mem, impeccable, ponytail** no están copiados: el catálogo apunta a sus repos (`github` o `git-subdir`) y Claude Code los baja de ahí. Se actualizan solos con el upstream.
- **find-skills** upstream no es un plugin (es un SKILL.md suelto), así que se envolvió en `plugins/find-skills/` con su `plugin.json`. Copia tomada de `vercel-labs/skills@65d20cb`.
- `claude-mem` figura como `mem` porque Claude Code no deja que un plugin de terceros use un nombre que empiece con `claude-` en un marketplace propio.

## Instalar

Subí este repo a GitHub (o cualquier git) y en Claude Code:

```
/plugin marketplace add <tu-usuario>/claude-skills
/plugin install superpowers@qsoro-skills
/plugin install mem@qsoro-skills
/plugin install impeccable@qsoro-skills
/plugin install ponytail@qsoro-skills
/plugin install find-skills@qsoro-skills
```

Desde la terminal es lo mismo con `claude plugin marketplace add …` y `claude plugin install …`. También funciona sin subirlo: `claude plugin marketplace add /ruta/a/claude-skills`.

Reiniciá Claude Code después de instalar.

## Requisitos por plugin

- **mem**: Node.js; levanta un worker local (puerto 37777, UI web) y guarda todo en `~/.claude-mem/`. Envolvé texto en `<private>…</private>` para que no se guarde.
- **ponytail**: `node` en el PATH (usa dos hooks chicos). Tiene opciones: `/plugin configure ponytail@qsoro-skills`.
- **find-skills**: Node.js para `npx skills`.
- **impeccable**: arrancá cada proyecto con `/impeccable init`.

## Actualizar

```
/plugin marketplace update qsoro-skills
/plugin update <plugin>@qsoro-skills
```

## Fijar versiones (opcional)

Por defecto se sigue la rama principal de cada upstream. Para congelar uno, agregá `ref` (tag/rama) y/o `sha` (commit completo de 40 caracteres) a su `source`:

```json
"source": { "source": "github", "repo": "obra/superpowers", "sha": "<commit-completo>" }
```

## Agregar otra skill

1. Si ya es un plugin en GitHub → nueva entrada en `marketplace.json` con source `github` (o `git-subdir` si vive en una subcarpeta).
2. Si es un SKILL.md suelto → `plugins/<nombre>/skills/<nombre>/SKILL.md` + `plugins/<nombre>/.claude-plugin/plugin.json`, y entrada con `"source": "./plugins/<nombre>"`.
3. Validá con `claude plugin validate .`
