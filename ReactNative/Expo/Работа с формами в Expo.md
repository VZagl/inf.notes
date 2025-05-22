# Работа с формами в Expo

## 1. Формы без плагинов

### Основные принципы

В Expo (React Native) формы можно реализовать с помощью стандартных компонентов: `TextInput`, `Button`, `Switch`, `Picker` и управления состоянием через `useState`.

### Пример

```jsx
import React, { useState } from 'react';
import { View, TextInput, Button, Text } from 'react-native';

export default function SimpleForm() {
	const [name, setName] = useState('');
	const [submitted, setSubmitted] = useState(false);

	const handleSubmit = () => {
		setSubmitted(true);
	};

	return (
		<View>
			<TextInput
				placeholder='Введите имя'
				value={name}
				onChangeText={setName}
				style={{ borderWidth: 1, marginBottom: 10, padding: 5 }}
			/>
			<Button title='Отправить' onPress={handleSubmit} />
			{submitted && <Text>Привет, {name}!</Text>}
		</View>
	);
}
```

- [Документация TextInput](https://reactnative.dev/docs/textinput)
- [Документация Button](https://reactnative.dev/docs/button)

---

## 2. Формы с плагинами

### Популярные библиотеки

- [Formik](https://formik.org/)
- [React Hook Form](https://react-hook-form.com/)
- [Yup](https://github.com/jquense/yup) (валидация)

### Пример с Formik и Yup

```jsx
import React from 'react';
import { View, TextInput, Button, Text, TouchableOpacity } from 'react-native';
import { Formik } from 'formik';
import * as Yup from 'yup';
import { Checkbox } from 'expo-elements';

const validationSchema = Yup.object().shape({
	name: Yup.string()
		.min(2, 'Имя должно содержать минимум 2 символа')
		.required('Обязательное поле'),
	email: Yup.string().email('Некорректный email').required('Обязательное поле'),
	password: Yup.string()
		.min(6, 'Пароль должен содержать минимум 6 символов')
		.required('Обязательное поле'),
	confirmPassword: Yup.string()
		.oneOf([Yup.ref('password'), null], 'Пароли должны совпадать')
		.required('Обязательное поле'),
	agreement: Yup.boolean()
		.oneOf([true], 'Необходимо принять правила')
		.required('Обязательное поле'),
});

export default function FormikForm() {
	return (
		<Formik
			initialValues={{
				name: '',
				email: '',
				password: '',
				confirmPassword: '',
				agreement: false,
			}}
			validationSchema={validationSchema}
			onSubmit={(values) => alert(JSON.stringify(values))}
		>
			{({
				handleChange,
				handleBlur,
				handleSubmit,
				values,
				errors,
				touched,
				setFieldValue,
			}) => (
				<View style={{ padding: 20 }}>
					<TextInput
						placeholder='Имя'
						onChangeText={handleChange('name')}
						onBlur={handleBlur('name')}
						value={values.name}
						style={styles.input}
					/>
					{touched.name && errors.name && (
						<Text style={styles.errorText}>{errors.name}</Text>
					)}

					<TextInput
						placeholder='Email'
						onChangeText={handleChange('email')}
						onBlur={handleBlur('email')}
						value={values.email}
						keyboardType='email-address'
						style={styles.input}
					/>
					{touched.email && errors.email && (
						<Text style={styles.errorText}>{errors.email}</Text>
					)}

					<TextInput
						placeholder='Пароль'
						onChangeText={handleChange('password')}
						onBlur={handleBlur('password')}
						value={values.password}
						secureTextEntry
						style={styles.input}
					/>
					{touched.password && errors.password && (
						<Text style={styles.errorText}>{errors.password}</Text>
					)}

					<TextInput
						placeholder='Подтвердите пароль'
						onChangeText={handleChange('confirmPassword')}
						onBlur={handleBlur('confirmPassword')}
						value={values.confirmPassword}
						secureTextEntry
						style={styles.input}
					/>
					{touched.confirmPassword && errors.confirmPassword && (
						<Text style={styles.errorText}>{errors.confirmPassword}</Text>
					)}

					<View style={styles.checkboxContainer}>
						<Checkbox
							value={values.agreement}
							onValueChange={(value) => setFieldValue('agreement', value)}
						/>
						<View
							style={{ flexDirection: 'row', flexWrap: 'wrap', marginLeft: 8 }}
						>
							<Text>Я согласен с </Text>
							<TouchableOpacity
								onPress={() => {
									/* навигация к правилам */
								}}
							>
								<Text style={styles.link}>правилами</Text>
							</TouchableOpacity>
						</View>
					</View>
					{touched.agreement && errors.agreement && (
						<Text style={styles.errorText}>{errors.agreement}</Text>
					)}

					<Button onPress={handleSubmit} title='Отправить' />
				</View>
			)}
		</Formik>
	);
}

const styles = {
	input: {
		borderWidth: 1,
		borderColor: '#ddd',
		padding: 10,
		marginBottom: 10,
		borderRadius: 5,
	},
	errorText: {
		color: 'red',
		fontSize: 12,
		marginBottom: 10,
	},
	checkboxContainer: {
		flexDirection: 'row',
		alignItems: 'center',
		marginBottom: 15,
	},
	link: {
		color: 'blue',
		textDecorationLine: 'underline',
	},
};
```

> [!NOTE]
> Для работы чекбокса необходимо установить пакет `expo-elements` или использовать другую реализацию чекбокса по вашему выбору.

- [Formik + React Native](https://formik.org/docs/examples/react-native)
- [React Hook Form + React Native](https://react-hook-form.com/get-started#ReactNative)
- [Yup](https://github.com/jquense/yup)

---

## 3. Советы

- Для простых форм достаточно стейта и стандартных компонентов.
- Для сложных форм используйте Formik или React Hook Form.
- Для валидации удобно использовать Yup.

---

## 4. Полезные ссылки

- [React Native Forms Guide](https://reactnative.dev/docs/handling-text-input)
- [Expo Docs](https://docs.expo.dev/)
- [React Native Examples](https://reactnative.dev/docs/tutorial)
