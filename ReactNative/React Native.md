# React Native

## Что такое React Native?

React Native — это фреймворк для создания мобильных приложений на JavaScript и React. Позволяет разрабатывать приложения для iOS и Android с использованием одного кода.

## Основные особенности

- Использует компоненты React.
- Позволяет писать нативные модули на Java/Kotlin (Android) и Objective-C/Swift (iOS).
- Горячая перезагрузка (Hot Reloading).
- Большое сообщество и множество библиотек.

## Основные компоненты

- `View` — контейнер для других компонентов.
- `Text` — отображение текста.
- `Image` — отображение изображений.
- `ScrollView` — прокручиваемый контейнер.
- `TextInput` — поле ввода.

## Навигация

Для навигации часто используется библиотека `react-navigation`.

## Работа с API

Для сетевых запросов используется `fetch` или сторонние библиотеки, например, `Axios`.

## Стилизация

Стили задаются с помощью объекта `JavaScript`, синтаксис похож на `CSS`, но с `camelCase`.

## Пример кода

```jsx
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

export default function App() {
	return (
		<View style={styles.container}>
			<Text>Hello, React Native!</Text>
		</View>
	);
}

const styles = StyleSheet.create({
	container: {
		flex: 1,
		justifyContent: 'center',
		alignItems: 'center',
	},
});
```

## Итоги

React Native — мощный инструмент для кроссплатформенной мобильной разработки с использованием знакомых принципов React.

## Полезные ссылки

- [Документация React Native](https://reactnative.dev/)
- [Поиск неисправностей](https://reactnative.dev/docs/troubleshooting)
- [React Navigation](https://reactnavigation.org/)
- [Expo](https://expo.dev/) — фреймворк для быстрого старта с React Native
- [React Native Elements](https://reactnativeelements.com/) — UI-библиотека для React Native
- [React Native Paper](https://callstack.github.io/react-native-paper/) — Material Design UI-библиотека
- [React Native Vector Icons](https://oblador.github.io/react-native-vector-icons/) — иконки для React Native
- [React Native Reanimated](https://docs.swmansion.com/react-native-reanimated/) — библиотека для анимаций
- [React Native Gesture Handler](https://docs.swmansion.com/react-native-gesture-handler/) — библиотека для обработки жестов
- [React Native Maps](https://github.com/react-native-maps/react-native-maps) — компонент для отображения карт
- [React Native Web](https://necolas.github.io/react-native-web/) — библиотека для запуска React Native компонентов в браузере
- [React Native Testing Library](https://callstack.github.io/react-native-testing-library/) — библиотека для тестирования компонентов React Native
- [React Native Debugger](https://github.com/jhen0409/react-native-debugger) — инструмент для отладки приложений React Native
- [Flipper](https://fbflipper.com/) — инструмент для отладки мобильных приложений
- [Reactotron](https://github.com/infinitered/reactotron) — инструмент для инспекции и отладки React Native приложений
- [Detox](https://wix.github.io/Detox/) — инструмент для end-to-end тестирования React Native приложений
- [App Center](https://appcenter.ms/) — платформа для CI/CD, тестирования и аналитики мобильных приложений
- [Firebase](https://firebase.google.com/) — облачная платформа для мобильных приложений
- [Sentry](https://sentry.io/) — инструмент для мониторинга ошибок и производительности
- [Crashlytics](https://firebase.google.com/docs/crashlytics) — инструмент для мониторинга сбоев приложений
- [App Store Connect](https://developer.apple.com/app-store-connect/) — платформа для публикации приложений в App Store
- [Google Play Console](https://play.google.com/console) — платформа для публикации приложений в Google Play
- [Fastlane](https://fastlane.tools/) — инструмент для автоматизации сборки и публикации приложений
- [CodePush](https://microsoft.github.io/code-push/) — инструмент для обновления приложений React Native "по воздуху"
- [EAS Build](https://docs.expo.dev/build/introduction/) — инструмент для сборки приложений Expo
- [EAS Update](https://docs.expo.dev/eas-update/introduction/) — инструмент для обновления приложений Expo "по воздуху"
- [EAS Submit](https://docs.expo.dev/eas-submit/introduction/) — инструмент для автоматической отправки приложений в магазины
- [EAS CLI](https://docs.expo.dev/eas-cli/introduction/) — инструмент командной строки для работы с Expo Application Services
- [React Native CLI](https://reactnative.dev/docs/cli) — инструмент командной строки для работы с React Native
- [React Native Community](https://github.com/react-native-community) — официальное сообщество и набор поддерживаемых пакетов для React Native
- [Awesome React Native](https://github.com/jondot/awesome-react-native) — подборка лучших ресурсов, библиотек и инструментов для React Native
- [React Native Showcase](https://reactnative.dev/showcase) — примеры приложений, созданных с помощью React Native
- [React Native Blog](https://reactnative.dev/blog) — официальный блог React Native
