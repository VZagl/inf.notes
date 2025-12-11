# Конвенции именования

- Переменные: camelCase
- Константы: UPPER_CASE
- interface: i_camelCase
- enum: e_camelCase
- Функции: camelCase
- Классы: PascalCase
- Компоненты: PascalCase

## Именование файлов

- **Обычные файлы: kebab-case** (например: `user-service.ts`, `weather-api.ts`)
  - Совместимость с ОС и URL
  - Лучшая читаемость
  - Стандарт веб-экосистемы
- **React компоненты: PascalCase** (например: `UserProfile.tsx`, `WeatherCard.tsx`)
  - Соответствует именам компонентов в коде
  - Стандарт React экосистемы

## Идентификаторы

- **В структурах данных (JSON/интерфейсы):**

  - Уникальный идентификатор: `id`
  - Ссылка на другую сущность: `id_сущность` (например: `id_country`, `id_city`)

- **В коде (переменные/параметры):**
  - Соответствующие переменные: `idСущность` в camelCase (например: `idCountry`, `idCity`)

Пример:

```typescript
// В интерфейсе
interface i_city {
  id: string;
  id_country: string;
}

// В функциях
function getCity(idCity: string, idCountry: string) { ... }
```
