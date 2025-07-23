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
npx expo start         # Стандартный запуск
npx expo start --tunnel # Запуск через туннель
```

### yarn

```bash
yarn create expo-app my-app
cd my-app
yarn expo start         # Стандартный запуск
yarn expo start --tunnel # Запуск через туннель
```

### pnpm

```bash
pnpm create expo my-app
cd my-app
pnpm expo start         # Стандартный запуск
pnpm expo start --tunnel # Запуск через туннель
```

### О режиме --tunnel

Флаг `--tunnel` создает защищенный туннель между вашим компьютером и устройствами, что позволяет:

- Тестировать приложение на физических устройствах, даже если они не в локальной сети
- Обходить ограничения брандмауэров и корпоративных сетей
- Получать доступ к приложению через интернет с помощью уникального URL

#### Как это работает

1. При запуске с флагом `--tunnel`, Expo создает защищенный туннель через сервисы ngrok
2. Генерируется уникальный URL вида `exp://xx-xxx.exp.direct:XXXXX`
3. Этот URL можно использовать на любом устройстве с установленным Expo Go
4. Весь трафик между устройством и компьютером разработчика шифруется
5. Туннель автоматически пробрасывает порты через NAT и обходит файрволы

> [!NOTE]
> При первом использовании `--tunnel` может потребоваться установка дополнительных зависимостей. Следуйте инструкциям в терминале.

> [!TIP]
> Если у вас возникают проблемы с подключением, попробуйте перезапустить туннель командой `expo start --tunnel --clear`

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
