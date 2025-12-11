# Структура проекта

## Корень

- `package.json`, `pnpm-lock.yaml` — управление зависимостями и версиями.
- `.prettierrc.cjs` — настройки форматирования.
- `.gitignore`, `.cursorignore` — исключения для git и Cursor.
- `.vscode/` — локальные настройки редактора.
- `.cursor/` — правила и команды для Cursor/агентов.
- `docs/` — документация проекта (см. ниже).
- `src/` — тематические заметки и материалы (см. ниже).
- `readme.md` — краткое описание репозитория.

## Документация `docs/`

- `docs/common/` — общие правила и договорённости, переиспользуемые между проектами:
  - `dev-environment.md` — настройка окружения.
  - `git-commit-description.md` — правила описания коммитов.
  - `naming-conventions.md` — соглашения по именованию.
- `docs/project/` — конкретные материалы для этого проекта:
  - `project-structure.md` — описание структуры (этот файл).
  - `run-and-build.md` — команды разработки и сборки.
  - `tech-stack.md` — используемые технологии.
  - `testing-guidelines.md` — правила тестирования.

## Заметки и материалы `src/`

`src/` содержит тематические разделы с файлами заметок:

- `AI/AI-помощник/` — материалы по различным AI-ассистентам (Cody, Claude, Copilot, и др.).
- `AI/MCP/` — заметки по Context7-MCP.
- `Deno (среда выполнения TypeScript, JavaScript)/` — статьи о Deno.
- `Electron/` — заметки и решения для Electron.
- `git/` — практики и советы по Git.
- `Markdown/` — приёмы работы с Markdown.
- `nodejs/pnpm/` — материалы по pnpm и экосистеме Node.js.
- `React/` — заметки по React.
- `ReactNative/`:
  - `CLI/`, `Expo/` (включая `Expo Router/`, `Expo EAS/`) — материалы по React Native и Expo.
- `Vite/` — заметки по Vite.
- `VSCode/` — настройки и советы по VS Code.
- `Тестирование/` — заметки по TDD, WebdriverIO, Vitest и другим инструментам тестирования.
