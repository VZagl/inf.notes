# Тестирование Electron + React приложений на TypeScript

## Содержание

- [Содержание](#содержание)
- [1. Введение](#1-введение)
- [2. Методологии тестирования](#2-методологии-тестирования)
  - [2.1 TDD (Test-Driven Development)](#21-tdd-test-driven-development)
  - [2.2 BDD (Behavior-Driven Development)](#22-bdd-behavior-driven-development)
  - [2.3 Основные типы тестирования](#23-основные-типы-тестирования)
- [3. Обзор инструментов](#3-обзор-инструментов)
  - [3.1 Vitest](#31-vitest)
  - [3.2 Mocha + Chai](#32-mocha--chai)
  - [3.3 Jest](#33-jest)
  - [3.4 React Testing Library](#34-react-testing-library)
  - [3.5 Sinon](#35-sinon)
  - [3.6 Spectron](#36-spectron)
  - [3.7 Playwright / Cypress](#37-playwright--cypress)
  - [3.8 MSW (Mock Service Worker)](#38-msw-mock-service-worker)
  - [3.9 Coverage tools](#39-coverage-tools)
  - [3.10 ESLint для тестов](#310-eslint-для-тестов)
  - [3.11 Mocking Electron/Node.js](#311-mocking-electronnodejs)
  - [3.12 CI/CD интеграция](#312-cicd-интеграция)
- [4. Тестирование React-приложений](#4-тестирование-react-приложений)
- [5. Тестирование Electron-приложений](#5-тестирование-electron-приложений)
- [6. Примеры настройки и тестов](#6-примеры-настройки-и-тестов)
  - [6.1 Настройка Vitest для React + TypeScript](#61-настройка-vitest-для-react--typescript)
  - [6.2 Пример unit-теста React-компонента](#62-пример-unit-теста-react-компонента)
  - [6.3 Пример e2e теста для Electron (Spectron)](#63-пример-e2e-теста-для-electron-spectron)
- [7. Советы по выбору инструментов и организации тестов](#7-советы-по-выбору-инструментов-и-организации-тестов)
- [8. Заключение](#8-заключение)

## 1. Введение

Тестирование — ключевая часть современного процесса разработки. Для приложений на React и Electron с использованием TypeScript существует множество инструментов и подходов, позволяющих обеспечить качество и стабильность кода.

## 2. Методологии тестирования

### 2.1 TDD (Test-Driven Development)

- Разработка через тестирование: сначала пишутся тесты, затем код.
- Этапы: Red (тест падает) → Green (код проходит тест) → Refactor (рефакторинг).
- Преимущества: предотвращение багов, улучшение архитектуры.
- **Пример:**

  ```ts
  // Шаг 1: пишем тест
  import { calculateSum } from './utils';

  test('сумма чисел в массиве', () => {
  	expect(calculateSum([1, 2, 3])).toBe(6);
  });

  // Шаг 2: пишем минимальный код для прохождения теста
  export function calculateSum(numbers: number[]): number {
  	return numbers.reduce((sum, current) => sum + current, 0);
  }

  // Шаг 3: рефакторим код
  export function calculateSum(numbers: number[]): number {
  	if (numbers.length === 0) return 0;
  	return numbers.reduce((sum, current) => sum + current, 0);
  }
  ```

### 2.2 BDD (Behavior-Driven Development)

- Ориентирован на поведение системы с точки зрения пользователя.
- Использует синтаксис Given-When-Then, describe/it.
- **Пример:**

  ```ts
  // Пример BDD теста
  import { render, screen, fireEvent } from '@testing-library/react';
  import Counter from './Counter';

  describe('Счетчик', () => {
  	it('увеличивает значение при клике', () => {
  		// Given: компонент отрендерен
  		render(<Counter />);

  		// When: пользователь кликает на кнопку
  		fireEvent.click(screen.getByText('Увеличить'));

  		// Then: значение увеличивается
  		expect(screen.getByText('1')).toBeInTheDocument();
  	});
  });
  ```

### 2.3 Основные типы тестирования

- **Unit-тесты (модульные):** тестирование отдельных функций или компонентов.
- **Integration-тесты (интеграционные):** проверка взаимодействия между модулями.
- **E2E (end-to-end, сквозные):** тестирование всей системы от начала до конца.
- **Snapshot-тесты:** проверка визуального представления компонентов.
- **Smoke-тесты:** базовая проверка работоспособности.
- **Регрессионные тесты:** проверка, что изменения не сломали существующий функционал.

## 3. Обзор инструментов

### 3.1 Vitest

- Современный быстрый тест-раннер, совместим с Jest API.
- Отлично подходит для TypeScript и Vite-проектов.
- **Пример настройки:**

  ```ts
  // vite.config.ts
  import { defineConfig } from 'vite';
  import react from '@vitejs/plugin-react';

  export default defineConfig({
  	plugins: [react()],
  	test: {
  		environment: 'jsdom',
  		globals: true,
  		coverage: {
  			reporter: ['text', 'html'],
  		},
  	},
  });
  ```

### 3.2 Mocha + Chai

- Классический набор для unit/integration тестов.
- Гибкая настройка, поддержка асинхронных тестов.
- **Пример теста:**

  ```ts
  import { expect } from 'chai';

  describe('Array', () => {
  	it('должен содержать элемент', () => {
  		const arr = [1, 2, 3];
  		expect(arr).to.include(2);
  	});
  });
  ```

### 3.3 Jest

- Де-факто стандарт для React.
- Поддержка снапшотов, мокинга, coverage.
- **Пример снапшот-теста:**

  ```ts
  import renderer from 'react-test-renderer';
  import MyComponent from './MyComponent';

  test('рендер компонента соответствует снапшоту', () => {
  	const tree = renderer.create(<MyComponent />).toJSON();
  	expect(tree).toMatchSnapshot();
  });
  ```

### 3.4 React Testing Library

- Тестирование компонентов через пользовательские сценарии.
- Рекомендуется для React.
- **Пример теста:**

  ```ts
  import { render, screen, fireEvent } from '@testing-library/react';
  import Button from './Button';

  test('клик по кнопке вызывает событие', () => {
  	const handleClick = jest.fn();
  	render(<Button onClick={handleClick} />);
  	fireEvent.click(screen.getByText('Нажми меня'));
  	expect(handleClick).toHaveBeenCalledTimes(1);
  });
  ```

### 3.5 Sinon

- Для создания моков, стабов, шпионов.
- **Пример использования:**

  ```ts
  import sinon from 'sinon';

  describe('Logger', () => {
  	it('должен вызывать метод log', () => {
  		const logSpy = sinon.spy(console, 'log');
  		console.log('Hello, world!');
  		sinon.assert.calledWith(logSpy, 'Hello, world!');
  		logSpy.restore();
  	});
  });
  ```

### 3.6 Spectron

- E2E тестирование Electron (устаревающий, но используется).
- **Пример теста:**

  ```js
  const { Application } = require('spectron');
  const assert = require('assert');

  describe('Electron app', function () {
  	this.timeout(10000);
  	let app;
  	before(() => {
  		app = new Application({
  			path: '/path/to/electron',
  			args: ['.'],
  		});
  		return app.start();
  	});
  	after(() => app && app.stop());

  	it('открывает главное окно', async () => {
  		const count = await app.client.getWindowCount();
  		assert.equal(count, 1);
  	});
  });
  ```

### 3.7 Playwright / Cypress

- Современные инструменты для e2e тестирования UI.
- **Пример теста с Playwright:**

  ```ts
  import { chromium } from 'playwright';

  describe('UI тест', () => {
  	it('должен открыть страницу и проверить заголовок', async () => {
  		const browser = await chromium.launch();
  		const page = await browser.newPage();
  		await page.goto('https://example.com');
  		const title = await page.title();
  		expect(title).toBe('Example Domain');
  		await browser.close();
  	});
  });
  ```

### 3.8 MSW (Mock Service Worker)

- Мокинг HTTP-запросов в тестах.
- **Пример настройки:**

  ```ts
  import { setupServer } from 'msw/node';
  import { rest } from 'msw';

  const server = setupServer(
  	rest.get('/api/user', (req, res, ctx) => {
  		return res(ctx.json({ username: 'admin' }));
  	})
  );

  beforeAll(() => server.listen());
  afterEach(() => server.resetHandlers());
  afterAll(() => server.close());
  ```

### 3.9 Coverage tools

- Istanbul/nyc, встроенные средства покрытия кода.
- **Пример команды:**

  ```bash
  nyc --reporter=html npm test
  ```

### 3.10 ESLint для тестов

- Поддержание качества тестового кода.
- **Пример настройки:**

  ```json
  {
  	"overrides": [
  		{
  			"files": ["*.test.js", "*.spec.js"],
  			"rules": {
  				"no-unused-vars": "off"
  			}
  		}
  	]
  }
  ```

### 3.11 Mocking Electron/Node.js

- Особенности мокинга Electron API, node-модулей.
- **Пример мокинга:**

  ```ts
  jest.mock('electron', () => ({
  	ipcRenderer: {
  		send: jest.fn(),
  		on: jest.fn(),
  	},
  }));
  ```

### 3.12 CI/CD интеграция

- Запуск тестов в CI (GitHub Actions и др.).
- **Пример workflow:**

  ```yaml
  name: CI

  on:
  	push:
  		branches:
  			- main

  jobs:
  	test:
  		runs-on: ubuntu-latest

  		steps:
  			- uses: actions/checkout@v2
  			- uses: actions/setup-node@v2
  				with:
  					node-version: '16'
  			- run: npm install
  			- run: npm test
  ```

## 4. Тестирование React-приложений

- Unit-тесты компонентов (Vitest, Jest, React Testing Library).
- Интеграционные тесты (React Testing Library).
- E2E тесты (Cypress, Playwright).
- Мокинг зависимостей (MSW, vi.mock, jest.mock).
- Снапшот-тесты.

## 5. Тестирование Electron-приложений

- Unit-тесты main/renderer процессов.
- Интеграционные тесты (мокинг Electron API).
- E2E тесты (Spectron, Playwright).
- Особенности работы с node-модулями.

## 6. Примеры настройки и тестов

### 6.1 Настройка Vitest для React + TypeScript

```ts
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { viteStaticCopy } from 'vite-plugin-static-copy';

export default defineConfig({
	plugins: [react(), viteStaticCopy({ ... })],
	test: {
		environment: 'jsdom',
		globals: true,
		coverage: {
			reporter: ['text', 'html'],
		},
	},
});
```

### 6.2 Пример unit-теста React-компонента

```ts
// Counter.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import Counter from './Counter';

test('увеличивает счетчик при клике', () => {
	render(<Counter />);
	fireEvent.click(screen.getByText('Увеличить'));
	expect(screen.getByText('1')).toBeInTheDocument();
});
```

### 6.3 Пример e2e теста для Electron (Spectron)

```js
// main.e2e.js
const { Application } = require('spectron');
const assert = require('assert');

describe('Electron app', function () {
	this.timeout(10000);
	let app;
	before(() => {
		app = new Application({
			path: '/path/to/electron',
			args: ['.'],
		});
		return app.start();
	});
	after(() => app && app.stop());

	it('открывает главное окно', async () => {
		const count = await app.client.getWindowCount();
		assert.equal(count, 1);
	});
});
```

## 7. Советы по выбору инструментов и организации тестов

- Для React: Vitest/Jest + React Testing Library
- Для Electron: unit — Vitest/Mocha, e2e — Spectron/Playwright
- Используйте TDD для критичных модулей
- Покрывайте код тестами с самого начала
- Интегрируйте тесты в CI/CD

## 8. Заключение

Тестирование — неотъемлемая часть разработки. Выбор инструментов зависит от стека, целей и предпочтений команды. Главное — тестировать регулярно и поддерживать тесты в актуальном состоянии.
