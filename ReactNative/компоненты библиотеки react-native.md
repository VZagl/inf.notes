# Компоненты библиотеки `react-native`

> "react-native" — это библиотека компонентов с открытым исходным кодом, предназначенная для создания мобильных приложений с использованием JavaScript и React. Она предоставляет набор готовых к использованию компонентов пользовательского интерфейса (UI), таких как `View`, `Text`, `Image`, `ScrollView` и другие, которые абстрагируют нативные элементы платформ Android и iOS. Это позволяет разработчикам создавать кроссплатформенные мобильные приложения с единым кодом, сохраняя при этом нативный внешний вид и производительность.

## 1. `View`

Контейнер для других компонентов, аналог `<div>` в HTML.

```jsx
import { View } from 'react-native';

<View style={{ padding: 10, backgroundColor: 'lightblue' }}>
	{/* другие компоненты */}
</View>;
```

---

## 2. `Text`

Отображение текста.

```jsx
import { Text } from 'react-native';

<Text style={{ fontSize: 18, color: 'navy' }}>Привет, мир!</Text>;
```

---

## 3. `Image`

Отображение изображений.

```jsx
import { Image } from 'react-native';

<Image
	source={{ uri: 'https://reactnative.dev/img/tiny_logo.png' }}
	style={{ width: 40, height: 40 }}
/>;
```

---

## 4. `ScrollView`

Прокручиваемый контейнер.

```jsx
import { ScrollView, Text } from 'react-native';

<ScrollView>
	<Text>Элемент 1</Text>
	<Text>Элемент 2</Text>
	{/* ... */}
</ScrollView>;
```

---

## 5. `FlatList`

Эффективный список для больших данных.

```jsx
import { FlatList, Text } from 'react-native';

<FlatList
	data={[{ key: 'A' }, { key: 'B' }]}
	renderItem={({ item }) => <Text>{item.key}</Text>}
/>;
```

---

## 6. `SectionList`

Список с секциями.

```jsx
import { SectionList, Text } from 'react-native';

<SectionList
	sections={[
		{ title: 'A', data: ['Apple', 'Avocado'] },
		{ title: 'B', data: ['Banana'] },
	]}
	renderItem={({ item }) => <Text>{item}</Text>}
	renderSectionHeader={({ section }) => <Text>{section.title}</Text>}
/>;
```

---

## 7. `TextInput`

Поле ввода текста.

```jsx
import { TextInput } from 'react-native';

<TextInput
	placeholder='Введите текст'
	style={{ borderWidth: 1, padding: 8 }}
/>;
```

---

## 8. `Button`

Кнопка.

```jsx
import { Button } from 'react-native';

<Button title='Нажми меня' onPress={() => alert('Нажато!')} />;
```

---

## 9. `TouchableOpacity`

Область, реагирующая на нажатие с эффектом прозрачности.

```jsx
import { TouchableOpacity, Text } from 'react-native';

<TouchableOpacity onPress={() => alert('Нажато!')}>
	<Text>Коснись меня</Text>
</TouchableOpacity>;
```

---

## 10. `TouchableHighlight`

Область с подсветкой при нажатии.

```jsx
import { TouchableHighlight, Text } from 'react-native';

<TouchableHighlight onPress={() => alert('Нажато!')}>
	<Text>Коснись меня</Text>
</TouchableHighlight>;
```

---

## 11. `SafeAreaView`

Учет безопасных областей экрана (например, вырезы).

```jsx
import { SafeAreaView, Text } from 'react-native';

<SafeAreaView>
	<Text>Контент в безопасной зоне</Text>
</SafeAreaView>;
```

---

## 12. `ActivityIndicator`

Индикатор загрузки.

```jsx
import { ActivityIndicator } from 'react-native';

<ActivityIndicator size='large' color='#0000ff' />;
```

---

## 13. `Switch`

Переключатель (toggle).

```jsx
import { Switch } from 'react-native';

<Switch value={true} onValueChange={(val) => console.log(val)} />;
```

---

## 14. `Modal`

Модальное окно.

```jsx
import { Modal, View, Text, Button } from 'react-native';

<Modal visible={true} transparent={true}>
	<View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
		<Text>Модальное окно</Text>
		<Button title='Закрыть' onPress={() => {}} />
	</View>
</Modal>;
```

---

## 15. `Pressable`

Более современный способ обработки нажатий.

```jsx
import { Pressable, Text } from 'react-native';

<Pressable onPress={() => alert('Pressed!')}>
	<Text>Press me</Text>
</Pressable>;
```

---

## 16. `KeyboardAvoidingView`

Автоматически сдвигает контент при появлении клавиатуры.

```jsx
import { KeyboardAvoidingView, TextInput } from 'react-native';

<KeyboardAvoidingView behavior='padding'>
	<TextInput placeholder='Текст' />
</KeyboardAvoidingView>;
```

---

## 17. `Picker` (deprecated, используйте @react-native-picker/picker)

Выпадающий список.

```jsx
import { Picker } from '@react-native-picker/picker';

<Picker selectedValue='java' onValueChange={(itemValue) => {}}>
	<Picker.Item label='Java' value='java' />
	<Picker.Item label='JavaScript' value='js' />
</Picker>;
```

---

## 18. `StatusBar`

Управление статус-баром устройства.

```jsx
import { StatusBar } from 'react-native';

<StatusBar barStyle='dark-content' backgroundColor='#fff' />;
```

---

## 19. `RefreshControl`

Контроль обновления для ScrollView/FlatList.

```jsx
import { RefreshControl, ScrollView } from 'react-native';

<ScrollView
	refreshControl={<RefreshControl refreshing={false} onRefresh={() => {}} />}
/>;
```

---

## 20. `VirtualizedList`

Базовый компонент для FlatList/SectionList.

```jsx
import { VirtualizedList, Text } from 'react-native';

<VirtualizedList
	data={[]}
	initialNumToRender={4}
	renderItem={({ item }) => <Text>{item.key}</Text>}
	getItemCount={(data) => data.length}
	getItem={(data, index) => data[index]}
/>;
```

---

> Это основные компоненты React Native. Для подробностей см. [официальную документацию](https://reactnative.dev/docs/components-and-apis).
