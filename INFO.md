# Agent Skills for Context Engineering — установка и использование

15 скиллов (инструкций) для AI-агентов по **context engineering** и **harness engineering**: как проектировать контекст, память, инструменты, мульти-агентные системы и оценку агентов. Ставятся через [skills CLI](https://github.com/vercel-labs/skills), копированием папок или как Claude/Cursor-плагин.

**Важно:** скиллы работают с AI-агентом, не с редактором. VS Code сам по себе скиллы не читает — нужен Copilot, Cline, Continue и т.д.

**Автор:** [Muratcan Koylan](https://x.com/koylanai). Upstream: `muratcankoylan/Agent-Skills-for-Context-Engineering`.

**Фундамент:** начни с `context-fundamentals` — базовые модели контекста и attention. Остальные скиллы ссылаются друг на друга и «маршрутизируют» смежные задачи.

---

## Что такое context engineering

**Context engineering** — дисциплина управления **контекстным окном** LLM: system prompt, история, tool definitions, retrieved docs, выводы инструментов. Это не prompt engineering (формулировка одной инструкции), а **курация всего**, что попадает в ограниченный attention budget.

Ключевая идея: важнее **качество и плотность** токенов, чем их количество. Длинный контекст деградирует (lost-in-the-middle, poisoning, clash).

---

## Скиллы по категориям

### Фундамент (начни с этого)

| Скилл | Что делает |
|-------|------------|
| `context-fundamentals` | Что такое контекст, anatomy окна, U-shaped attention, mental models |
| `context-degradation` | Диагностика деградации: lost-in-middle, poisoning, clash, confusion |
| `context-compression` | Сжатие длинных сессий: summarization, compaction, handoff summaries |

**Промпты:**
```
context-fundamentals: объясни, почему агент «забывает» инструкцию из середины контекста
```
```
context-degradation: агент путает старые и новые требования — что сломалось?
```
```
context-compression: сожми 50-turn сессию в handoff для другого агента
```

### Архитектура агентных систем

| Скилл | Что делает |
|-------|------------|
| `multi-agent-patterns` | Orchestrator, swarm, handoffs, изоляция контекста между агентами |
| `memory-systems` | Кратко-/долгосрочная память, vector/graph, Mem0, Zep, Cognee и др. |
| `tool-design` | Описания tools, схемы, ошибки, MCP, консолидация tool surface |
| `filesystem-context` | Scratchpads, offloading tool output, JIT discovery, handoff-файлы |
| `hosted-agents` | Remote sandboxes, warm pools, background coding agents, Modal-style infra |

**Промпты:**
```
multi-agent-patterns: нужен ли мне второй агент или хватит одного с tools?
```
```
tool-design: перепиши descriptions tools — агент путает search и fetch
```
```
filesystem-context: tool output раздувает контекст — вынеси в файлы
```
```
hosted-agents: спроектируй background agent с pre-warmed sandbox
```

### Эксплуатация и оптимизация

| Скилл | Что делает |
|-------|------------|
| `context-optimization` | Бюджет токенов, masking, KV/prefix cache, partitioning, retrieval scope |
| `latent-briefing` | Передача orchestrator state воркерам через KV cache compaction |
| `evaluation` | Детерминированные checks, rubrics, regression suites, quality gates |
| `advanced-evaluation` | LLM-as-judge, pairwise compare, bias mitigation, calibration |
| `harness-engineering` | Автономные harness: locked metrics, logs, novelty gates, rollback, approval |

**Промпты:**
```
context-optimization: сократи token cost без потери качества ответов
```
```
evaluation: спроектируй regression suite для coding agent
```
```
advanced-evaluation: LLM-as-judge с rubric для code review
```
```
harness-engineering: автономный research loop с human approval на merge
```

### Методология разработки

| Скилл | Что делает |
|-------|------------|
| `project-development` | Нужен ли LLM вообще, shape pipeline, cost estimation, structured output |

**Промпты:**
```
project-development: стоит ли делать этот проект на LLM или rule-based pipeline?
```
```
project-development: оцени token cost для 10k документов в batch pipeline
```

### Когнитивная архитектура

| Скилл | Что делает |
|-------|------------|
| `bdi-mental-states` | Beliefs, desires, intentions; RDF → mental states; BDI ontology |

**Промпты:**
```
bdi-mental-states: смоделируй beliefs/desires/intentions агента из RDF-контекста
```

---

## Маршрутизация между скиллами

Скиллы явно указывают, куда направлять смежные задачи:

| Задача | Скилл |
|--------|-------|
| Концепции, onboarding | `context-fundamentals` |
| Агент «тупеет» в длинной сессии | `context-degradation` → `context-compression` / `context-optimization` |
| Файлы как память | `filesystem-context` (не `memory-systems`) |
| Cross-session semantic memory | `memory-systems` |
| Один tool или набор tools | `tool-design` |
| Shape всего проекта / pipeline | `project-development` |
| Несколько агентов | `multi-agent-patterns` |
| Базовые eval gates | `evaluation` |
| LLM-судья | `advanced-evaluation` |

---

## Установка

### Структура репозитория

```
Agent-Skills-for-Context-Engineering/
├── skills/                    # 15 публичных скиллов (то, что ставишь)
│   ├── context-fundamentals/
│   │   ├── SKILL.md
│   │   ├── references/
│   │   └── scripts/           # опционально: демо на Python
│   └── ...
├── examples/                  # готовые системы (не скиллы)
├── researcher/                # OS для maintainers репо (не для конечных пользователей)
├── .plugin/plugin.json        # Open Plugins manifest (Cursor и др.)
└── .claude-plugin/            # Claude Code marketplace
```

При ручной установке копируй **целые папки** из `skills/` — вместе с `references/` и `scripts/`.

### Только в проект (рекомендуется)

```bash
cd /path/to/your-project

# все 15 скиллов
npx skills add muratcankoylan/Agent-Skills-for-Context-Engineering -a cursor -y

# или твой форк
npx skills add Shirobokov-aa/Agent-Skills-for-Context-Engineering -a cursor -y

# выборочно
npx skills add muratcankoylan/Agent-Skills-for-Context-Engineering \
  --skill context-fundamentals --skill tool-design --skill multi-agent-patterns \
  -a cursor -y

# список доступных скиллов
npx skills add muratcankoylan/Agent-Skills-for-Context-Engineering --list
```

| Область | Флаг | Куда |
|---------|------|------|
| Проект | без `-g` | `.agents/skills/` |
| Глобально | `-g` | `~/.agents/skills/` |

### Из локального клона (без GitHub)

```bash
cd /path/to/your-project

mkdir -p .agents/skills
cp -r /path/to/Agent-Skills-for-Context-Engineering/skills/* .agents/skills/

# один скилл
cp -r /path/to/Agent-Skills-for-Context-Engineering/skills/context-fundamentals .agents/skills/
```

### Cursor + VS Code сразу

```bash
cd /path/to/your-project

npx skills add muratcankoylan/Agent-Skills-for-Context-Engineering \
  -a cursor -a github-copilot -y
```

| Агент в VS Code | Флаг `--agent` | Папка |
|-----------------|----------------|-------|
| GitHub Copilot | `github-copilot` | `.agents/skills/` |
| Cline | `cline` | `.agents/skills/` |
| Continue | `continue` | `.continue/skills/` |
| Roo Code | `roo` | `.roo/skills/` |

Continue и Roo — отдельные папки, добавь `-a continue` или `-a roo` к команде.

### Cursor Plugin Directory

Репозиторий в [Cursor Plugin Directory](https://cursor.directory/plugins/context-engineering). Можно установить плагин `context-engineering` через UI Cursor — все 15 скиллов разом. Manifest: `.plugin/plugin.json` (стандарт [Open Plugins](https://open-plugins.com)).

### Claude Code (плагин)

```text
/plugin marketplace add muratcankoylan/Agent-Skills-for-Context-Engineering
/plugin install context-engineering@context-engineering-marketplace
```

### Глобально (на все проекты)

```bash
npx skills add muratcankoylan/Agent-Skills-for-Context-Engineering -g -a cursor -y
```

### Флаги

| Флаг | Значение |
|------|----------|
| `-y` / `--yes` | Без вопросов в терминале |
| `-g` / `--global` | На все проекты |
| `-a` / `--agent` | Целевой агент (`cursor`, `github-copilot`, …) |
| `--skill` | Конкретный скилл (без флага — все) |
| `--list` | Показать список скиллов без установки |

### Удалить

```bash
# через CLI (перечисли нужные или все 15)
npx skills remove --global \
  context-fundamentals context-degradation context-compression \
  context-optimization latent-briefing multi-agent-patterns \
  memory-systems tool-design filesystem-context hosted-agents \
  evaluation advanced-evaluation harness-engineering \
  project-development bdi-mental-states \
  -a cursor -y

# вручную — все скиллы разом
rm -rf .agents/skills/{context-fundamentals,context-degradation,context-compression,context-optimization,latent-briefing,multi-agent-patterns,memory-systems,tool-design,filesystem-context,hosted-agents,evaluation,advanced-evaluation,harness-engineering,project-development,bdi-mental-states}
```

### Обновить

```bash
npx skills update
npx skills list
```

---

## Использование

1. Открой проект, где установлены скиллы.
2. **Сначала** при необходимости — `context-fundamentals` для mental models.
3. В чате агента опиши задачу и **назови скилл** в начале или в тексте.
4. Агент подхватывает скилл по `description` в `SKILL.md` и читает `references/` по необходимости.
5. Скрипты в `scripts/` — опциональные демо; для работы скилла Python не обязателен.

**Типичный порядок:**
```
context-fundamentals     →  понять ограничения контекста
project-development      →  нужен ли LLM, shape pipeline
tool-design / multi-agent-patterns →  архитектура
context-optimization     →  снизить token cost
evaluation               →  quality gates
```

**С другими наборами скиллов:**
```
absolute-work            →  разработка фичи
tool-design              →  спроектировать tools для агента
context-optimization     →  не раздувать контекст в длинной сессии
remotion-best-practices  →  если задача — видео, не context
stop-slop                →  тексты без AI-slop (отдельно от context engineering)
```

При похожих задачах явно указывай скилл: `@filesystem-context` для file-backed context, `@memory-systems` для cross-session памяти, `@tool-design` для описаний tools.

`.agents/skills/` можно закоммитить в git — команда получит те же скиллы.

---

## Примеры (`examples/`)

Готовые системы, показывающие скиллы в связке (не устанавливаются как скиллы):

| Пример | О чём |
|--------|-------|
| `digital-brain-skill` | Personal OS: бренд, контент, knowledge, network |
| `x-to-book-system` | Multi-agent: X → ежедневная «книга» |
| `llm-as-judge-skills` | TypeScript LLM-as-judge, 19 тестов |
| `book-sft-pipeline` | Fine-tune под стиль автора (LoRA, $2) |
| `interleaved-thinking` | Оптимизатор reasoning traces → skills |

---

## `researcher/` — не для конечных пользователей

Папка `researcher/` — **внутренняя OS maintainers** репозитория: rubrics, benchmarks, continuous loop, валидация скиллов. Нужна, если ты **развиваешь сам этот репозиторий**, а не просто используешь скиллы в своём проекте.

Ключевые команды (для maintainers):

```bash
python3 researcher/scripts/validate_repo.py --strict
python3 researcher/scripts/skill_health.py --strict --no-history
python3 researcher/scripts/check_activation_cases.py
```

---

## Ссылки

- Репозиторий: https://github.com/muratcankoylan/Agent-Skills-for-Context-Engineering
- DeepWiki: https://deepwiki.com/muratcankoylan/Agent-Skills-for-Context-Engineering
- Cursor Plugin: https://cursor.directory/plugins/context-engineering
- Шаблон нового скилла: `template/SKILL.md`
