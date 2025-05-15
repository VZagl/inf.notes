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
npm install -g expo-cli
npx create-expo-app my-app
cd my-app
npx expo start
```

### yarn

```bash
yarn global add expo-cli
yarn create expo-app my-app
cd my-app
yarn expo start
```

### pnpm

```bash
pnpm add -g expo-cli
pnpm create expo my-app
cd my-app
pnpm expo start
```

## Структура проекта

- `App.js` — основной файл приложения.
- `assets/` — папка для изображений и других ресурсов.
- `package.json` — зависимости проекта.

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

- `expo publish` — публикация обновлений через Expo.
- `expo build` — сборка standalone-приложения для iOS/Android.

## Плюсы и минусы Expo

**Плюсы:**

- Быстрый старт и простота.
- Много встроенных API.
- Кроссплатформенность.

**Минусы:**

- Ограничения по нативным модулям (можно решить через EAS или "eject").
- Размер приложения может быть больше.

## Полезные ссылки

- [Документация Expo](https://docs.expo.dev/)
- [От Web к Native с React](https://habr.com/ru/companies/timeweb/articles/833904/)
