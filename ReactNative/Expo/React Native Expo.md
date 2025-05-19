# React Native Expo

## Что такое Expo?

- **Expo** — это набор инструментов и сервисов для быстрого создания приложений на React Native.
- Позволяет запускать, тестировать и публиковать приложения без необходимости настраивать нативную среду разработки.

## Основные возможности Expo

- Быстрый старт без Xcode/Android Studio.
- Expo Go — приложение для тестирования на устройстве.
- Поддержка "горячей перезагрузки" (Hot Reload).
- Простая публикация и обновление приложений.

## Установка и запуск

Вы можете использовать любой из популярных пакетных менеджеров:

### npm

```bash
npx create-expo-app my-app
cd my-app
npx expo start
```

### yarn

```bash
yarn create expo-app my-app
cd my-app
yarn expo start
```

### pnpm

```bash
pnpm create expo my-app
cd my-app
pnpm expo start
```

## Структура проекта

- `app/` — директория для файлов маршрутизации (при использовании Expo Router)
- `assets/` — папка для изображений и других ресурсов
- `package.json` — зависимости проекта
- `app.json` — конфигурация Expo
- `babel.config.js` — настройки Babel
- `tsconfig.json` — настройки TypeScript (если используется)

## Использование компонентов

```jsx
import { Text, View } from 'react-native';

export default function App() {
	return (
		<View>
			<Text>Привет, Expo!</Text>
		</View>
	);
}
```

## Работа с библиотеками Expo

- Expo предоставляет множество готовых модулей: камера, геолокация, push-уведомления и др.
- Пример установки:

  **npm:**

  ```bash
  npx expo install expo-camera
  ```

  **yarn:**

  ```bash
  yarn expo install expo-camera
  ```

  **pnpm:**

  ```bash
  pnpm expo install expo-camera
  ```

## Публикация приложения

- EAS (Expo Application Services) используется для сборки и публикации:
  ```bash
  pnpm add -g eas-cli
  eas build  # Сборка приложения
  eas update # Публикация OTA-обновлений
  eas submit # Отправка в App Store/Google Play
  ```

## Плюсы и минусы Expo

**Плюсы:**

- Быстрый старт и простота.
- Много встроенных API.
- Кроссплатформенность.

**Минусы:**

- Некоторые нативные модули могут потребовать Development Build
- Размер базового приложения немного больше чистого React Native
- Необходимость использования EAS для продвинутых сценариев сборки

## Полезные ссылки

- [Документация Expo](https://docs.expo.dev/)
  - [Expo Router](https://docs.expo.dev/router/introduction/)
  - [Expo SDK](https://docs.expo.dev/versions/latest/sdk/expo/)
  - [EAS](https://docs.expo.dev/eas/)
  - [Development Builds](https://docs.expo.dev/development/introduction/)
- [От Web к Native с React](https://habr.com/ru/companies/timeweb/articles/833904/)
