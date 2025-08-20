> [!NOTE]
> Как в git постоянно работать в ветке "dev" и время от времени коммитить наработки в основную ветку (желательно с тэгом версии)?

> [!TIP]
> Вот последовательность команд для работы с git по описанному workflow:

## Забрать изменения из master в dev и решить конфликты

```bash
# Переключиться на ветку dev (если не находитесь в ней)
git checkout dev

# Забрать последние изменения из удаленного репозитория
git fetch origin

# Слить изменения из master в dev
git merge origin/master

# Если есть конфликты, решите их в редакторе, затем:
git add .
git commit -m "Resolve merge conflicts with master"
```

## Закоммитить наработки в master с тэгом

```bash
# Переключиться на master
git checkout master

# Убедиться, что master актуален
git pull origin master

# Слить изменения из dev в master
git merge dev

# Запушить изменения в master
git push origin master

# Создать тэг версии (например, v1.2.3)
git tag -a v1.2.3 -m "Release version 1.2.3"

# Запушить тэг
git push origin v1.2.3

# Вернуться к работе в dev
git checkout dev
```

## Альтернативный подход с rebase (более чистая история)

```bash
# Находясь в dev, сделать rebase на master
git checkout dev
git fetch origin
git rebase origin/master

# Решить конфликты если есть, затем:
git rebase --continue

# Для коммита в master:
git checkout master
git pull origin master
git merge dev --no-ff  # создаст merge commit
git push origin master

# Создать тэг и вернуться в dev
git tag -a v1.2.3 -m "Release version 1.2.3"
git push origin v1.2.3
git checkout dev
```

## Удобный алиас для автоматизации

Можете создать git алиас для упрощения процесса:

```bash
git config --global alias.release '!f() {
    git checkout master &&
    git pull origin master &&
    git merge dev --no-ff &&
    git push origin master &&
    git tag -a "$1" -m "Release $1" &&
    git push origin "$1" &&
    git checkout dev;
}; f'
```

Использование: `git release v1.2.3`
