# Что такое Deno?

**Deno** — это современная среда выполнения для JavaScript и TypeScript, созданная Райаном Далем (автором Node.js). Deno разработан с учетом современных стандартов безопасности, удобства и поддержки последних возможностей языка.

## Основные особенности Deno

- **Поддержка TypeScript "из коробки"**  
  Deno может выполнять файлы TypeScript без необходимости предварительной компиляции или настройки дополнительных инструментов.

- **Безопасность по умолчанию**  
  Скрипты Deno по умолчанию не имеют доступа к файловой системе, сети, среде окружения и другим критичным ресурсам. Для предоставления доступа используются специальные флаги (`--allow-read`, `--allow-net` и др.).

- **Единый исполняемый файл**  
  Deno распространяется как один бинарный файл, не требует установки package manager (npm/yarn) или дополнительных зависимостей.

- **Современный API, основанный на стандартах веба**  
  Многие API в Deno совместимы с браузерными стандартами (например, `fetch`, `WebWorker`, `TextEncoder`/`TextDecoder`).

- **Встроенный менеджер зависимостей через URL**  
  Модули импортируются напрямую по URL, что упрощает управление зависимостями и избавляет от необходимости использовать `package.json`.

- **Встроенные инструменты**  
  В Deno есть встроенные инструменты для форматирования кода (`deno fmt`), тестирования (`deno test`), сборки документации (`deno doc`), а также статического анализа (`deno lint`).

## Пример запуска скрипта

```sh
deno run --allow-read script.ts
```

## Пример импорта модуля

```ts
import { serve } from 'https://deno.land/std@0.203.0/http/server.ts';
```

## Преимущества Deno по сравнению с Node.js

- Более строгая модель безопасности
- Нет необходимости в менеджере пакетов
- Современные стандарты и поддержка TypeScript
- Простота установки и обновления

## Недостатки и ограничения

- Меньшая экосистема по сравнению с Node.js
- Некоторые npm-пакеты могут быть недоступны или несовместимы
- Новизна платформы и меньшее количество обучающих материалов

Deno — альтернатива Node.js, ориентированная на безопасность, простоту и современные технологии. Он подходит для разработки современных серверных приложений, скриптов и инструментов.
