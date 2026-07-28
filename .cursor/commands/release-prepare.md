# release-prepare — Подготовка релиза

**Назначение:** При вызове `/release-prepare` подготовь следующий релиз на ветке
`develop`: собери изменения с прошлого тега, сформируй секцию в `CHANGELOG.md`,
синхронизируй версию в манифестах и создай коммит `chore(release):`. Merge в
`main`, тег и push выполняются **отдельно** (см. шаг 9) — часто **вне Cursor**
из‑за блокировки каталога `.cursor/`.

**Источники правил:** `docs/common/git-commit-description.md`,
`docs/common/git-workflow.md`, `CHANGELOG.md` (формат Keep a Changelog, язык —
русский).

**Связанные команды:** `/close-task` (опциональное накопление в `[Unreleased]`),
`/git-commit`.

---

## Алгоритм

### Шаг 1. Проверить окружение

1. Выполни `git branch --show-current`.
   - Если не `develop` — выведи предупреждение и предложи `git checkout develop`.
     Продолжай только после переключения или явного подтверждения пользователя.
2. Выполни `git status --porcelain`.
   - Если рабочая директория не чистая — сообщи и заверши (или попроси
     закоммитить/спрятать изменения).
3. Выполни `git fetch origin` и `git pull origin develop`.

### Шаг 2. Определить версию

1. Прочитай текущую версию из `package.json` (источник правды для SemVer).
2. Выполни `git describe --tags --abbrev=0` (последний тег, например `v1.0.0`).
   - Если тегов нет — диапазон коммитов с начала истории `develop`.
3. Спроси пользователя **новую версию** (`X.Y.Z`) или тип bump:
   - `patch` — исправления (`1.0.0` → `1.0.1`)
   - `minor` — новая функциональность (`1.0.0` → `1.1.0`)
   - `major` — breaking changes (`1.0.0` → `2.0.0`)
4. Проверь: новая версия **строго больше** текущей и тега `vX.Y.Z` ещё не
   существует (`git tag -l "v<новая-версия>"`).

### Шаг 3. Собрать источники изменений

**A. Git-коммиты** (диапазон `<последний-тег>..develop`, без merge-коммитов):

```bash
git log <последний-тег>..develop --no-merges --pretty=format:"%h %s"
```

**Включать в анализ:**

- `feat`, `fix`, `perf` — пользовательские изменения

**Исключать из итогового CHANGELOG:**

- `docs(memory-bank):`, `docs(plan):`, прочие чисто документационные коммиты
- `chore`, `ci`, `test`, `refactor`, `style`, `build` — если изменение **не
  заметно** конечному пользователю приложения
- дубликаты одной и той же правки (несколько коммитов с одним смыслом)

**B. Completed-задачи** — файлы в `memory-bank/completed-tasks/` с датой
завершения **после даты последнего релиза** (из заголовка последней секции
`CHANGELOG.md` или даты тега).

Для каждой задачи читай **## Результат** — это предпочтительный источник
формулировок на русском.

**C. Секция `[Unreleased]`** в `CHANGELOG.md` — черновик из `/close-task`.

### Шаг 4. Сформировать секцию CHANGELOG

1. Объедини источники A + B + C: дедуплицируй, сгруппируй по категориям Keep a
   Changelog:
   - **Добавлено** — `feat`, новые возможности
   - **Изменено** — изменения существующего поведения
   - **Исправлено** — `fix`
   - **Удалено**, **Устарело**, **Безопасность** — при наличии
2. Язык: **русский**, формулировки для **пользователя приложения**, не для
   разработчика (без упоминаний Memory Bank, E2E harness, specta, dev-debug-panel
   и т.п.).
3. Одна задача / одна логическая фича = один пункт списка.
4. Подготовь блок:

```markdown
## [X.Y.Z] - YYYY-MM-DD

[краткое вводное предложение о релизе — опционально]

### Добавлено

- ...

### Изменено

- ...

### Исправлено

- ...
```

5. Дата релиза — **текущая** (`YYYY-MM-DD`), если пользователь не указал другую.

### Шаг 5. Обновить CHANGELOG.md

1. Вставить новую секцию **сразу после** `## [Unreleased]`.
2. Очистить содержимое `[Unreleased]` (оставить заголовок пустым).
3. Обновить ссылки внизу файла (Keep a Changelog):
   - `[Unreleased]: .../compare/vX.Y.Z...HEAD`
   - `[X.Y.Z]: .../releases/tag/vX.Y.Z`
   - сохранить ссылки на прошлые версии

### Шаг 6. Синхронизировать версию

Обновить **одинаковое** значение `X.Y.Z` в:

| Файл                        | Поле        |
| --------------------------- | ----------- |
| `package.json`              | `"version"` |
| `src-tauri/Cargo.toml`      | `version`   |
| `src-tauri/tauri.conf.json` | `"version"` |

После правки `Cargo.toml` обновить `src-tauri/Cargo.lock`:

```bash
cargo generate-lockfile --manifest-path src-tauri/Cargo.toml
```

(или `pnpm build:rust` / сборка, если lockfile обновляется автоматически в вашем
окружении — зафиксировать изменение lockfile в том же коммите.)

### Шаг 7. Показать план и запросить подтверждение

Покажи:

- текущая → новая версия
- последний тег и диапазон коммитов
- полный diff или текст новой секции CHANGELOG
- список изменяемых файлов

Спроси: «Применить подготовку релиза X.Y.Z?» Дождись явного подтверждения.

### Шаг 8. Коммит (только после подтверждения)

1. Создай `.git-commit-msg.txt` через инструмент `Write` (UTF-8 без BOM):

```
chore(release): версия X.Y.Z

- обновлён CHANGELOG.md
- синхронизирована версия X.Y.Z в package.json, Cargo.toml,
  tauri.conf.json и Cargo.lock
```

2. `git add CHANGELOG.md package.json src-tauri/Cargo.toml src-tauri/tauri.conf.json src-tauri/Cargo.lock`
3. `git commit -F .git-commit-msg.txt`
4. Удали `.git-commit-msg.txt`.

### Шаг 9. Инструкции после коммита (вывести пользователю)

Merge в `main`, тег и push **не выполнять автоматически** из Cursor — каталог
`.cursor/` может быть заблокирован IDE.

**Рекомендуемый порядок (Git Bash, Cursor закрыт):**

```bash
cd /h/edu/edu.tauri/pet-ai.bot-bench

git push origin develop

git checkout main
git pull origin main

# при ошибке untracked Cargo.lock: rm src-tauri/Cargo.lock

cat > .git-commit-msg.txt << 'EOF'
chore(release): влить develop в main — версия X.Y.Z

- релиз BotBench X.Y.Z
- обновлён CHANGELOG.md
EOF

git merge --no-ff develop -F .git-commit-msg.txt
rm .git-commit-msg.txt

git tag -a vX.Y.Z -m "BotBench X.Y.Z"

git push origin main
git push origin vX.Y.Z

git checkout develop
```

Подставь фактическую `X.Y.Z` в команды и сообщения.

---

## Не делать

- **Release без подтверждения:** не менять версию и CHANGELOG до явного «да».
- **Push/merge/tag из Cursor:** не выполнять merge в `main` и push, если
  пользователь не просит явно и не закрыл Cursor.
- **CHANGELOG:** не включать dev-инфраструктуру, Memory Bank, внутренние
  рефакторинги без пользовательского эффекта.
- **Версия:** не оставлять рассинхрон между `package.json`, `Cargo.toml`,
  `tauri.conf.json` и `Cargo.lock`.
- **Кодировка:** для `.git-commit-msg.txt` — только инструмент `Write`, не
  PowerShell `Out-File`.

---

## Чек-лист выполнения

- [ ] Ветка `develop`, рабочая директория чистая
- [ ] Последний тег определён, новая версия согласована
- [ ] Источники собраны: git log + completed-tasks + [Unreleased]
- [ ] Секция CHANGELOG сформирована на русском (Keep a Changelog)
- [ ] [Unreleased] очищен, ссылки внизу обновлены
- [ ] Версия синхронизирована в четырёх файлах
- [ ] План показан, подтверждение получено
- [ ] Коммит `chore(release): версия X.Y.Z` создан
- [ ] Пользователю выведены инструкции merge → tag → push (Git Bash)
