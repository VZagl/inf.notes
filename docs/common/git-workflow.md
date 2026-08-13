> **Правила для ИИ:** работа с Git (branching, merge, rebase)

# Правила работы с Git

## Стратегия ветвления: Feature-Branch Workflow

Использовать **feature-branch workflow** с короткоживущими ветками. Вся разработка ведётся в отдельных ветках.

**Структура веток:**

- **`main`**: готова к продакшену / релизу, защищена. Push в `main` запускает деплой на GitHub Pages (см. `.github/workflows/deploy.yml`)
- **`develop`**: интегрирует завершённые фичи, защищена (если отсутствует — использовать `main`). Push в `develop` деплой **не** запускает
- **`build`**: долгоживущая ветка **стенда** (проверка нововведений на Pages). Не релиз и не замена `main`. Push в `build` запускает деплой. Когда нужна пересборка для проверки — merge текущей ветки (часто `develop`) в `build` через `/git-merge-to-build` или вручную, затем push. Команда не удаляет и не предлагает удалять ветки
- **Feature-ветки**: создаются из `develop`, короткоживущие, для одной фичи/бага

### Именование веток

Именовать ветки понятно и последовательно:

```bash
# Новая фича
git checkout -b feat/FEAT-123-add-user-profile

# Исправление бага
git checkout -b fix/BUG-456-auth-redirect-loop

# Рефакторинг
git checkout -b refactor/improve-logging-middleware
```

### Задачи Memory Bank и feature-ветки

При старте задачи через `/van` (Memory Bank System) каждая задача получает отдельную короткоживущую feature-ветку. Операционные шаги — в `docs/common/memory-bank-usage.md` → «Git-ветка».

**Соглашение об именовании (из `task_id`):**

| Тип задачи                 | Шаблон                                     | Пример (`task_id`: `step-base-ui-layout`) |
| -------------------------- | ------------------------------------------ | ----------------------------------------- |
| Шаг плана / фича           | `feat/<task_id>`                           | `feat/step-base-ui-layout`                |
| Исправление бага           | `fix/<task_id>`                            | `fix/step-auth-redirect`                  |
| Рефакторинг / docs-only MB | `chore/<task_id>` или `refactor/<task_id>` | `chore/step-update-deps`                  |

**Workflow:**

1. Базовая ветка: `develop` (или `main`, если `develop` отсутствует)
2. `git fetch origin` → `git checkout <base>` → `git pull origin <base>`
3. `git checkout -b <prefix>/<task_id>` (префикс — по типу задачи)
4. Все коммиты задачи остаются в этой ветке до merge через `/git-merge-to` или вручную

**Правила:**

- Не вносить продуктовые изменения в `develop`/`main` при активной задаче Memory Bank
- Имя ветки фиксируется в `memory-bank/tasks.md` при инициализации `/van`
- После `/close-task` при необходимости выполнить merge через `/git-merge-to` (feature → `develop`; с `develop` — в `main`) или вручную (только с явным подтверждением пользователя). **Удаление feature-ветки не выполняется по умолчанию** — можно предложить пользователю; удалять **только после явного подтверждения**

### Удаление feature-веток

На текущем этапе проекта (учебный репозиторий, без продакшен-цикла) feature-ветки **не удаляются автоматически**.

- **Запрещено** без **явного подтверждения** пользователя выполнять `git branch -d`, `git branch -D`, `git push --delete` и аналогичные команды удаления ветки
- **Запрещено** без явного подтверждения вносить **любые изменения в репозиторий** (commit, merge, push, удаление ветки, force push и т.д.)
- После merge можно **предложить** удалить feature-ветку; выполнять удаление только если пользователь явно согласился

## Сообщения коммитов

**Основное правило:** Каждый коммит представляет одно логическое изменение.

**Формат:** Следовать Conventional Commits — `type(scope): описание`

Полные правила оформления сообщений коммитов в `docs/common/git-commit-description.md`.

**Пример атомарных коммитов:**

```bash
# Первый коммит - исправление
git commit -F .git-commit-msg.txt  # fix(auth): исправить обработку expired токена

# Второй коммит - новая фича
git commit -F .git-commit-msg.txt  # feat(profile): добавить просмотр профиля
```

## Rebase вместо Merge (на feature-ветках)

Поддерживать линейную историю на feature-ветках перед мерджем в `develop`.

### Очистка истории перед PR

Перед пушем feature-ветки для PR:

```bash
git fetch origin
git rebase -i origin/develop  # Сквошить WIP коммиты
git push --force-with-lease origin feature/my-feature
```

**ВАЖНО:** Force push только в свои feature-ветки. Никогда не делать force push в `main` или `develop`.

### Merge feature-веток с --no-ff и Conventional Commits

При мердже feature-ветки в `develop` всегда использовать `--no-ff` для сохранения истории. Сообщение merge-коммита должно быть в формате Conventional Commits.

**Процесс:**

```bash
git checkout develop
git pull origin develop

# Создать .git-commit-msg.txt с типом на основе коммитов ветки:
# fix(todolist): исправить валидацию key
#
# - исправить проверку key через console.error
# - обновить задачи для планирования исправления теста

git merge --no-ff fix/test-todolist-key-validation -F .git-commit-msg.txt
rm .git-commit-msg.txt  # или del в Windows

git push origin develop
# Удаление feature-ветки — опционально, только после явного подтверждения пользователя:
# git branch -d fix/test-todolist-key-validation
```

**Определение типа merge-коммита:**

1. Посмотреть коммиты ветки: `git log feature/my-feature --oneline`
2. Определить основной тип (fix, feat, refactor)
3. Сформировать сообщение по Conventional Commits

## Git Hooks с pre-commit

Автоматизировать проверки качества кода до попадания в репозиторий.

**Установка:**

```bash
pip install pre-commit  # или через brew, npm
pre-commit install
pre-commit autoupdate
```

Hooks предотвращают: ошибки линтинга, несогласованность форматирования, случайные коммиты секретов.

**Примечание:** Конфигурация в `.pre-commit-config.yaml` специфична для каждого проекта.

## Гигиена репозитория

### .gitignore

Исключать сгенерированные файлы, зависимости, артефакты сборки и чувствительную информацию:

```
# Операционная система
.DS_Store, Thumbs.db

# Артефакты сборки
/dist/, /build/

# Зависимости
/node_modules/, /venv/, __pycache__/

# Файлы IDE
.idea/, .vscode/

# Секреты
.env, *.env, config.local.js
```

### Git LFS для больших файлов

Не коммитить большие бинарные файлы напрямую. Использовать Git LFS:

```bash
git lfs install
git lfs track "*.psd"
git lfs track "assets/*.mp4"
git add .gitattributes
git add my_large_file.psd
git commit -F .git-commit-msg.txt  # chore(assets): добавить файл через LFS
```

## Разрешение конфликтов

### Частое обновление

Часто подтягивать изменения из базовой ветки для минимизации конфликтов:

```bash
git checkout feature/my-feature
git pull origin develop --rebase
```

### Ручное разрешение конфликтов

Использовать инструмент мерджа IDE или `git mergetool`. Понимать каждое изменение.

```bash
git merge develop  # Git запросит разрешение конфликтов
# Разрешить вручную в IDE или через git mergetool
git add <разрешённые_файлы>
git commit -F .git-commit-msg.txt  # chore: разрешить конфликты
```

## Чеклист работы с Git

- [ ] Создана feature-ветка из develop/main с правильным именем (для задач Memory Bank — при `/van`, см. «Задачи Memory Bank и feature-ветки»)
- [ ] Коммиты атомарные и следуют Conventional Commits
- [ ] Выполнен rebase на develop перед PR
- [ ] Merge с --no-ff и сообщением в формате Conventional Commits
- [ ] Конфликты разрешены вручную
- [ ] Удаление feature-ветки — только по явному подтверждению пользователя (по умолчанию ветку не удалять)
