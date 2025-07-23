# Expo Router

## Что такое Expo Router?

**Expo Router** — это инструмент для организации навигации в приложениях React Native, использующих Expo. Он позволяет создавать маршруты (routes) с помощью структуры файлов и папок, аналогично Next.js.

## Основные возможности

- Автоматическое создание маршрутов на основе структуры файлов.
- Поддержка вложенной навигации.
- Динамические маршруты и параметры.
- Глубокие ссылки (Deep Linking).
- Простая интеграция с **Expo** и **React Navigation**.
- Автоматическое разделение кода и поддержка **React Suspense** для оптимизации загрузки ([Code Splitting и React Suspense](Code%20Splitting%20и%20React%20Suspense.md))

## Установка

```bash
npx create-expo-app my-app
cd my-app
npx expo install expo-router
```

## Структура проекта

В современном Expo Router (версия 2+) используется следующая структура:

```
app/                 # Корневая директория для маршрутизации
  (tabs)/            # Группа маршрутов с нижней навигацией
    _layout.tsx      # Конфигурация TabNavigator
    index.tsx        # Главная вкладка (/)
    explore.tsx      # Вкладка "Поиск" (/explore)
    posts/           # Вложенная группа для постов
      [id].tsx       # Динамический маршрут (/posts/123)
  modal/             # Группа модальных окон
    _layout.tsx      # Конфигурация ModalNavigator
    settings.tsx     # Модальное окно настроек (/modal/settings)
  (auth)/            # Группа экранов авторизации
    login.tsx        # Экран входа (/login)
    signup.tsx       # Экран регистрации (/signup)
  _layout.tsx        # Корневая конфигурация навигации
```

## Специальные файлы и папки

> [!NOTE]
> [Специальные файлы и папки](Специальные%20файлы%20и%20папки.md)

## Группировка маршрутов

> [!NOTE]
> [Группировка маршрутов](Группировка%20маршрутов.md)

Папки в скобках (например, `(tabs)`, `(auth)`) используются для организации маршрутов без влияния на URL-путь. Это позволяет логически группировать связанные маршруты и определять для них общие настройки навигации.

## Типы навигации

> [!NOTE]
> [Типы навигации](Типы%20навигации.md)

- `Stack` - стандартная навигация "стопкой" (экраны накладываются друг на друга)
- `Tabs` - навигация вкладками внизу экрана
- `Drawer` - боковое выдвижное меню
- `Modal` - модальные окна поверх основного контента
- `Group` - группировка маршрутов без визуальной навигации
- `MaterialTopTabs` - вкладки в верхней части экрана
- `MaterialBottomTabs` - вкладки внизу с Material Design
- `BottomSheet` - экраны в виде выдвигающейся снизу панели

Каждый тип навигации настраивается в соответствующем файле `_layout.tsx`:

## Примеры страниц

> [!NOTE]
> [Примеры страниц](Примеры%20страниц.md)

## Способы навигации

> [!NOTE]
> [Способы навигации](Способы%20навигации.md)

1. Использование компонента `Link`
2. Использование хука `useRouter`

## Конфигурация навигации

### Корневой layout (`app/_layout.tsx`)

```tsx
import { Stack } from 'expo-router';

export default function RootLayout() {
	return (
		<Stack>
			<Stack.Screen name='(tabs)' options={{ headerShown: false }} />
			<Stack.Screen name='modal' options={{ presentation: 'modal' }} />
		</Stack>
	);
}
```

### Конфигурация вкладок (`app/(tabs)/_layout.tsx`)

```tsx
import { Tabs } from 'expo-router';
import { Ionicons } from '@expo/vector-icons';

export default function TabLayout() {
	return (
		<Tabs>
			<Tabs.Screen
				name='index'
				options={{
					title: 'Главная',
					tabBarIcon: ({ color }) => (
						<Ionicons name='home' size={24} color={color} />
					),
				}}
			/>
			<Tabs.Screen
				name='explore'
				options={{
					title: 'Поиск',
					tabBarIcon: ({ color }) => (
						<Ionicons name='search' size={24} color={color} />
					),
				}}
			/>
		</Tabs>
	);
}
```

## Полезные ссылки

- [Документация Expo Router (RU)](https://docs.expo.dev/versions/latest/router/introduction/)
- [Примеры Expo Router](https://github.com/expo/router/tree/main/examples)
- [Видео: Expo Router — Быстрый старт](https://www.youtube.com/results?search_query=expo+router)

## Советы

- Используйте вложенные папки для создания вложенной навигации.
- Для параметров маршрута используйте квадратные скобки: `[param].tsx`.
- Для необязательных параметров: `[[param]].tsx`.

## Заключение

Expo Router упрощает навигацию в Expo-приложениях, делая её более декларативной и удобной для масштабирования.
