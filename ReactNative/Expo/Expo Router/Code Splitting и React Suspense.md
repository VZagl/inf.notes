# **Expo Router**, автоматическое разделение кода и поддержка **React Suspense** для оптимизации загрузки

## Введение

**Expo Router** — это инструмент для маршрутизации в Expo-проектах на React Native. Он позволяет создавать навигацию на основе файловой структуры, а также поддерживает автоматическое разделение JavaScript-пакета (code splitting) с помощью **React Suspense**.

---

## Автоматическое разделение пакета (Code Splitting)

**Code splitting** — это техника, при которой ваш JavaScript-код разбивается на отдельные части (чанки), которые загружаются только по мере необходимости. Это ускоряет первую загрузку приложения и снижает потребление памяти.

Expo Router автоматически разбивает ваш код на чанки, основываясь на файлах маршрутов. Это значит, что код каждого экрана будет загружаться только тогда, когда пользователь переходит на этот экран.

---

## React Suspense

**React Suspense** — это механизм React, который позволяет "приостанавливать" рендеринг компонента до тех пор, пока не будут загружены необходимые данные или модули.

В Expo Router Suspense используется для отображения индикатора загрузки, пока нужный экран подгружается.

---

## Пример использования

### Структура файлов

```
/app/
	index.js
	profile.js
	settings.js
```

### Пример маршрута

```js
// app/profile.js
import { Text } from 'react-native';

export default function ProfileScreen() {
	return <Text>Профиль пользователя</Text>;
}
```

### Suspense в действии

Expo Router автоматически оборачивает ваши экраны в `<React.Suspense>`. Вам не нужно явно использовать Suspense — всё работает "из коробки".

Если вы хотите настроить индикатор загрузки:

```js
// app/_layout.js
import { Stack } from 'expo-router';
import { ActivityIndicator } from 'react-native';

export default function RootLayout() {
	return (
		<Stack
			screenOptions={{
				// Индикатор загрузки для Suspense
				suspenseFallback: <ActivityIndicator size='large' color='#0000ff' />,
			}}
		/>
	);
}
```

---

## Преимущества

- Быстрая загрузка первого экрана
- Меньше памяти используется на устройстве
- Легко масштабировать приложение

---

## Ссылки

- [Документация Expo Router (RU)](https://docs.expo.dev/versions/latest/router/introduction/)
- [React Suspense (EN)](https://react.dev/reference/react/Suspense)
- [Code Splitting в React (RU)](https://ru.react.dev/learn/code-splitting)

---

## Заключение

Expo Router и React Suspense позволяют создавать быстрые и масштабируемые приложения на React Native, автоматически разделяя код и улучшая пользовательский опыт.
