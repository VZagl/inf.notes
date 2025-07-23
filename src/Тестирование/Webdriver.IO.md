# WebDriver.IO vs Cypress: Подробное сравнение

## Содержание

- [Содержание](#содержание)
- [Обзор фреймворков](#обзор-фреймворков)
  - [WebDriver.IO](#webdriverio)
  - [Cypress](#cypress)
- [Сравнительная таблица](#сравнительная-таблица)
- [Установка и настройка](#установка-и-настройка)
  - [WebDriver.IO](#webdriverio-1)
  - [Cypress](#cypress-1)
- [Примеры реализации одинаковых тестов](#примеры-реализации-одинаковых-тестов)
  - [1. Простой тест авторизации](#1-простой-тест-авторизации)
    - [WebDriver.IO](#webdriverio-2)
    - [Cypress](#cypress-2)
  - [2. Работа с API и перехватом запросов](#2-работа-с-api-и-перехватом-запросов)
    - [WebDriver.IO](#webdriverio-3)
    - [Cypress](#cypress-3)
  - [3. Работа с формами и файлами](#3-работа-с-формами-и-файлами)
    - [WebDriver.IO](#webdriverio-4)
    - [Cypress](#cypress-4)
  - [4. Работа с кастомными командами](#4-работа-с-кастомными-командами)
    - [WebDriver.IO](#webdriverio-5)
    - [Cypress](#cypress-5)
- [Конфигурация для CI/CD](#конфигурация-для-cicd)
  - [WebDriver.IO в GitHub Actions](#webdriverio-в-github-actions)
  - [Cypress в GitHub Actions](#cypress-в-github-actions)
- [Отчеты и интеграции](#отчеты-и-интеграции)
  - [WebDriver.IO с Allure](#webdriverio-с-allure)
  - [Cypress Dashboard](#cypress-dashboard)
- [Производительность и масштабирование](#производительность-и-масштабирование)
  - [WebDriver.IO - Параллельное выполнение](#webdriverio---параллельное-выполнение)
  - [Cypress - Параллельное выполнение](#cypress---параллельное-выполнение)
- [Рекомендации по выбору](#рекомендации-по-выбору)
  - [Выбирайте WebDriver.IO если:](#выбирайте-webdriverio-если)
  - [Выбирайте Cypress если:](#выбирайте-cypress-если)
- [Полезные ссылки](#полезные-ссылки)
  - [WebDriver.IO](#webdriverio-6)
  - [Cypress](#cypress-6)
  - [Сравнения и статьи](#сравнения-и-статьи)
- [Продвинутые примеры использования](#продвинутые-примеры-использования)
  - [5. Работа с базами данных](#5-работа-с-базами-данных)
    - [WebDriver.IO](#webdriverio-7)
    - [Cypress](#cypress-7)
  - [6. Визуальное тестирование](#6-визуальное-тестирование)
    - [WebDriver.IO с Percy](#webdriverio-с-percy)
    - [Cypress с Percy](#cypress-с-percy)
  - [7. Тестирование производительности](#7-тестирование-производительности)
    - [WebDriver.IO с Lighthouse](#webdriverio-с-lighthouse)
    - [Cypress с cypress-audit](#cypress-с-cypress-audit)
  - [8. Мобильное тестирование](#8-мобильное-тестирование)
    - [WebDriver.IO с Appium](#webdriverio-с-appium)
    - [Cypress (только веб-мобильная эмуляция)](#cypress-только-веб-мобильная-эмуляция)
  - [9. Работа с Shadow DOM](#9-работа-с-shadow-dom)
    - [WebDriver.IO](#webdriverio-8)
    - [Cypress](#cypress-8)
  - [10. Работа с WebSockets](#10-работа-с-websockets)
    - [WebDriver.IO](#webdriverio-9)
    - [Cypress](#cypress-9)
- [Интеграция с инструментами разработки](#интеграция-с-инструментами-разработки)
  - [Docker конфигурация](#docker-конфигурация)
    - [WebDriver.IO](#webdriverio-10)
    - [Cypress](#cypress-10)
  - [Отчеты и аналитика](#отчеты-и-аналитика)
    - [WebDriver.IO с кастомным репортером](#webdriverio-с-кастомным-репортером)
    - [Cypress с кастомными командами для отчетности](#cypress-с-кастомными-командами-для-отчетности)
- [Паттерны и лучшие практики](#паттерны-и-лучшие-практики)
  - [Page Object Model](#page-object-model)
    - [WebDriver.IO](#webdriverio-11)
    - [Cypress](#cypress-11)
  - [Использование Page Objects в тестах](#использование-page-objects-в-тестах)
    - [WebDriver.IO](#webdriverio-12)
    - [Cypress](#cypress-12)
  - [Фикстуры и тестовые данные](#фикстуры-и-тестовые-данные)
    - [WebDriver.IO](#webdriverio-13)
    - [Cypress](#cypress-13)
- [Отладка и troubleshooting](#отладка-и-troubleshooting)
  - [WebDriver.IO отладка](#webdriverio-отладка)
  - [Cypress отладка](#cypress-отладка)
  - [Конфигурация для отладки](#конфигурация-для-отладки)
    - [WebDriver.IO debug конфигурация](#webdriverio-debug-конфигурация)
    - [Cypress debug конфигурация](#cypress-debug-конфигурация)
- [Интеграция с системами мониторинга](#интеграция-с-системами-мониторинга)
  - [WebDriver.IO с Grafana/Prometheus](#webdriverio-с-grafanaprometheus)
  - [Cypress с DataDog интеграцией](#cypress-с-datadog-интеграцией)
- [Безопасность в тестировании](#безопасность-в-тестировании)
  - [Управление секретами](#управление-секретами)
    - [WebDriver.IO](#webdriverio-14)
    - [Cypress](#cypress-14)
  - [Тестирование безопасности](#тестирование-безопасности)
    - [WebDriver.IO - SQL Injection тесты](#webdriverio---sql-injection-тесты)
    - [Cypress - Security тесты](#cypress---security-тесты)
- [Accessibility тестирование](#accessibility-тестирование)
  - [WebDriver.IO с axe-core](#webdriverio-с-axe-core)
  - [Cypress с cypress-axe](#cypress-с-cypress-axe)
- [Кроссбраузерное тестирование](#кроссбраузерное-тестирование)
  - [WebDriver.IO с BrowserStack](#webdriverio-с-browserstack)
  - [Cypress с Cypress Dashboard](#cypress-с-cypress-dashboard)
- [Заключение и рекомендации](#заключение-и-рекомендации)
  - [Когда выбирать WebDriver.IO:](#когда-выбирать-webdriverio)
  - [Когда выбирать Cypress:](#когда-выбирать-cypress)
  - [Общие рекомендации:](#общие-рекомендации)
  - [Полезные ресурсы для изучения:](#полезные-ресурсы-для-изучения)
    - [Курсы и обучение:](#курсы-и-обучение)
    - [Сообщества:](#сообщества)
    - [Блоги и статьи:](#блоги-и-статьи)

---

## Обзор фреймворков

### WebDriver.IO

- **Официальный сайт**: https://webdriver.io/
- **GitHub**: https://github.com/webdriverio/webdriverio
- **Тип**: Selenium-based фреймворк
- **Язык**: JavaScript/TypeScript
- **Архитектура**: Работает вне браузера, использует WebDriver протокол

### Cypress

- **Официальный сайт**: https://www.cypress.io/
- **GitHub**: https://github.com/cypress-io/cypress
- **Тип**: Современный E2E фреймворк
- **Язык**: JavaScript/TypeScript
- **Архитектура**: Работает внутри браузера

## Сравнительная таблица

| Критерий                     | WebDriver.IO                      | Cypress                         |
| ---------------------------- | --------------------------------- | ------------------------------- |
| **Поддержка браузеров**      | Chrome, Firefox, Safari, Edge, IE | Chrome, Firefox, Edge, Electron |
| **Мобильное тестирование**   | ✅ (Appium)                       | ❌ (только desktop)             |
| **Параллельное выполнение**  | ✅ Нативная поддержка             | ✅ (Cypress Dashboard - платно) |
| **Отладка**                  | Стандартная                       | ✅ Отличная (Time Travel)       |
| **Скорость выполнения**      | Средняя                           | Высокая                         |
| **Кривая обучения**          | Средняя                           | Низкая                          |
| **Поддержка iframe**         | ✅ Полная                         | ⚠️ Ограниченная                 |
| **Работа с вкладками**       | ✅ Полная                         | ❌ Не поддерживается            |
| **Real-time reload**         | ❌                                | ✅                              |
| **Автоматические скриншоты** | Настраиваемые                     | ✅ Автоматические               |

## Установка и настройка

### WebDriver.IO

```bash
npm init wdio@latest ./
```

**wdio.conf.js**

```js
export const config = {
	runner: 'local',
	specs: ['./test/specs/**/*.js'],
	maxInstances: 10,
	capabilities: [
		{
			browserName: 'chrome',
			'goog:chromeOptions': {
				args: ['--headless'],
			},
		},
	],
	logLevel: 'info',
	bail: 0,
	baseUrl: 'http://localhost:3000',
	waitforTimeout: 10000,
	connectionRetryTimeout: 120000,
	connectionRetryCount: 3,
	framework: 'mocha',
	reporters: ['spec'],
	mochaOpts: {
		ui: 'bdd',
		timeout: 60000,
	},
};
```

### Cypress

```bash
npm install cypress --save-dev
```

**cypress.config.js**

```javascript
import { defineConfig } from 'cypress';

export default defineConfig({
	e2e: {
		baseUrl: 'http://localhost:3000',
		viewportWidth: 1280,
		viewportHeight: 720,
		video: true,
		screenshotOnRunFailure: true,
		defaultCommandTimeout: 10000,
		requestTimeout: 10000,
		responseTimeout: 10000,
		setupNodeEvents(on, config) {
			// implement node event listeners here
		},
	},
});
```

## Примеры реализации одинаковых тестов

### 1. Простой тест авторизации

#### WebDriver.IO

**test/specs/login.spec.js**

```js
describe('Login functionality', () => {
	it('should login with valid credentials', async () => {
		await browser.url('/login');

		await $('#username').setValue('testuser');
		await $('#password').setValue('testpass');
		await $('#login-button').click();

		await expect($('.welcome-message')).toBeDisplayed();
		await expect($('.welcome-message')).toHaveText('Welcome, testuser!');
	});

	it('should show error for invalid credentials', async () => {
		await browser.url('/login');

		await $('#username').setValue('invalid');
		await $('#password').setValue('invalid');
		await $('#login-button').click();

		await expect($('.error-message')).toBeDisplayed();
		await expect($('.error-message')).toHaveText('Invalid credentials');
	});
});
```

#### Cypress

**cypress/e2e/login.cy.js**

```js
describe('Login functionality', () => {
	it('should login with valid credentials', () => {
		cy.visit('/login');

		cy.get('#username').type('testuser');
		cy.get('#password').type('testpass');
		cy.get('#login-button').click();

		cy.get('.welcome-message').should('be.visible');
		cy.get('.welcome-message').should('contain.text', 'Welcome, testuser!');
	});

	it('should show error for invalid credentials', () => {
		cy.visit('/login');

		cy.get('#username').type('invalid');
		cy.get('#password').type('invalid');
		cy.get('#login-button').click();

		cy.get('.error-message').should('be.visible');
		cy.get('.error-message').should('contain.text', 'Invalid credentials');
	});
});
```

### 2. Работа с API и перехватом запросов

#### WebDriver.IO

**test/specs/api-mock.spec.js**

```js
describe('API mocking', () => {
	it('should mock API response', async () => {
		// Настройка мока через browser.mock()
		const mock = await browser.mock('\*\*/api/users', {
			method: 'GET',
		});

		mock.respond([
			{ id: 1, name: 'John Doe', email: 'john@example.com' },
			{ id: 2, name: 'Jane Smith', email: 'jane@example.com' },
		]);

		await browser.url('/users');

		await expect($$('.user-item')).toHaveLength(2);
		await expect($('.user-item:first-child .name')).toHaveText('John Doe');
	});
});
```

#### Cypress

**cypress/e2e/api-mock.cy.js**

```js
describe('API mocking', () => {
	it('should mock API response', () => {
		// Перехват запроса
		cy.intercept('GET', '\*\*/api/users', {
			statusCode: 200,
			body: [
				{ id: 1, name: 'John Doe', email: 'john@example.com' },
				{ id: 2, name: 'Jane Smith', email: 'jane@example.com' },
			],
		}).as('getUsers');

		cy.visit('/users');

		cy.wait('@getUsers');
		cy.get('.user-item').should('have.length', 2);
		cy.get('.user-item:first-child .name').should('contain.text', 'John Doe');
	});
});
```

### 3. Работа с формами и файлами

#### WebDriver.IO

**test/specs/form-upload.spec.js**

```js
describe('File upload', () => {
	it('should upload file successfully', async () => {
		await browser.url('/upload');

		const filePath = path.join(__dirname, '../fixtures/test-file.pdf');
		const remoteFilePath = await browser.uploadFile(filePath);

		await $('#file-input').setValue(remoteFilePath);
		await $('#upload-button').click();

		await browser.waitUntil(
			async () => {
				return await $('.upload-success').isDisplayed();
			},
			{
				timeout: 10000,
				timeoutMsg: 'Upload success message not displayed',
			}
		);

		await expect($('.upload-success')).toHaveText('File uploaded successfully');
	});
});
```

#### Cypress

**cypress/e2e/form-upload.cy.js**

```js
describe('File upload', () => {
	it('should upload file successfully', () => {
		cy.visit('/upload');

		cy.get('#file-input').selectFile('cypress/fixtures/test-file.pdf');
		cy.get('#upload-button').click();

		cy.get('.upload-success', { timeout: 10000 })
			.should('be.visible')
			.and('contain.text', 'File uploaded successfully');
	});
});
```

### 4. Работа с кастомными командами

#### WebDriver.IO

**test/support/commands.js**

```js
// Кастомная команда для логина
browser.addCommand('loginAs', async function (username, password) {
	await this.url('/login');
	await $('#username').setValue(username);
	await $('#password').setValue(password);
	await $('#login-button').click();
	await $('.welcome-message').waitForDisplayed();
});

// Использование
describe('Dashboard tests', () => {
	it('should display user dashboard', async () => {
		await browser.loginAs('testuser', 'testpass');
		await browser.url('/dashboard');

		await expect($('.dashboard-title')).toHaveText('User Dashboard');
	});
});
```

#### Cypress

**cypress/support/commands.js**

```js
// Кастомная команда для логина
Cypress.Commands.add('loginAs', (username, password) => {
	cy.visit('/login');
	cy.get('#username').type(username);
	cy.get('#password').type(password);
	cy.get('#login-button').click();
	cy.get('.welcome-message').should('be.visible');
});

// Использование в тесте
describe('Dashboard tests', () => {
	it('should display user dashboard', () => {
		cy.loginAs('testuser', 'testpass');
		cy.visit('/dashboard');

		cy.get('.dashboard-title').should('contain.text', 'User Dashboard');
	});
});
```

## Конфигурация для CI/CD

### WebDriver.IO в GitHub Actions

**.github/workflows/wdio-tests.yml**

```yaml
name: WebDriver.IO Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm ci

      - name: Run WebDriver.IO tests
        run: npm run test:wdio

      - name: Upload test results
        uses: actions/upload-artifact@v3
        if: always()
        with:
          name: wdio-results
          path: ./test-results/
```

### Cypress в GitHub Actions

**.github/workflows/cypress-tests.yml**

```yaml
name: Cypress Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Cypress run
        uses: cypress-io/github-action@v5
        with:
          build: npm run build
          start: npm start
          wait-on: 'http://localhost:3000'

      - name: Upload screenshots
        uses: actions/upload-artifact@v3
        if: failure()
        with:
          name: cypress-screenshots
          path: cypress/screenshots
```

## Отчеты и интеграции

### WebDriver.IO с Allure

**wdio.conf.js**

```js
export const config = {
	// ...
	reporters: [
		'spec',
		[
			'allure',
			{
				outputDir: 'allure-results',
				disableWebdriverStepsReporting: true,
				disableWebdriverScreenshotsReporting: true,
			},
		],
	],
	// ...
};
```

### Cypress Dashboard

**cypress.config.js**

```js
export default defineConfig({
	projectId: 'your-project-id',
	e2e: {
		// конфигурация
	},
});
```

## Производительность и масштабирование

### WebDriver.IO - Параллельное выполнение

**wdio.conf.js**

```js
export const config = {
	maxInstances: 5,
	capabilities: [
		{
			browserName: 'chrome',
			maxInstances: 2,
		},
		{
			browserName: 'firefox',
			maxInstances: 2,
		},
	],
	// ...
};
```

### Cypress - Параллельное выполнение

# С Cypress Dashboard (платно)

`npx cypress run --record --parallel`

# Локально с cypress-parallel

```bash
npm install cypress-parallel --save-dev
npx cypress-parallel -s cy:run -t 4 -d cypress/e2e
```

## Рекомендации по выбору

### Выбирайте WebDriver.IO если:

- Нужна поддержка мобильных приложений (Appium)
- Требуется тестирование в Internet Explorer
- Необходима работа с несколькими вкладками браузера
- Важна полная поддержка iframe
- Команда уже знакома с Selenium

### Выбирайте Cypress если:

- Команда новичок в E2E тестировании
- Важна скорость разработки тестов
- Нужны отличные инструменты отладки
- Тестируете только веб-приложения
- Важен real-time reload при разработке тестов

## Полезные ссылки

### WebDriver.IO

- [Официальная документация](https://webdriver.io/docs/gettingstarted)
- [API Reference](https://webdriver.io/docs/api)
- [Awesome WebDriver.IO](https://github.com/christian-bromann/awesome-webdriverio)
- [WebDriver.IO Examples](https://github.com/webdriverio/webdriverio/tree/main/examples)

### Cypress

- [Официальная документация](https://docs.cypress.io/)
- [Best Practices](https://docs.cypress.io/guides/references/best-practices)
- [Cypress Real World App](https://github.com/cypress-io/cypress-realworld-app)
- [Cypress Examples](https://github.com/cypress-io/cypress-example-recipes)

### Сравнения и статьи

- [WebDriver.IO vs Cypress: A Detailed Comparison](https://www.browserstack.com/guide/webdriverio-vs-cypress)
- [End-to-End Testing Framework Comparison](https://blog.logrocket.com/end-to-end-testing-framework-comparison/)
- [Cypress vs WebDriver.IO vs Playwright](https://medium.com/@mwaseemzakir/cypress-vs-webdriver-io-vs-playwright-e2e-showdown-630f1cce4e90)

## Продвинутые примеры использования

### 5. Работа с базами данных

#### WebDriver.IO

:test/specs/database.spec.js

```js
import mysql from 'mysql2/promise';

describe('Database integration', () => {
	let connection;

	before(async () => {
		connection = await mysql.createConnection({
			host: 'localhost',
			user: 'testuser',
			password: 'testpass',
			database: 'testdb',
		});
	});

	after(async () => {
		await connection.end();
	});

	it('should verify user data in database', async () => {
		// Создаем пользователя через UI
		await browser.url('/register');
		await $('#username').setValue('newuser');
		await $('#email').setValue('newuser@example.com');
		await $('#password').setValue('password123');
		await $('#register-button').click();

		await expect($('.success-message')).toBeDisplayed();

		// Проверяем в базе данных
		const [rows] = await connection.execute(
			'SELECT * FROM users WHERE username = ?',
			['newuser']
		);

		expect(rows).toHaveLength(1);
		expect(rows[0].email).toBe('newuser@example.com');
	});
});
```

#### Cypress

:cypress/e2e/database.cy.js

```js
describe('Database integration', () => {
	it('should verify user data in database', () => {
		// Создаем пользователя через UI
		cy.visit('/register');
		cy.get('#username').type('newuser');
		cy.get('#email').type('newuser@example.com');
		cy.get('#password').type('password123');
		cy.get('#register-button').click();

		cy.get('.success-message').should('be.visible');

		// Проверяем в базе данных через task
		cy.task('queryDb', {
			query: 'SELECT * FROM users WHERE username = ?',
			values: ['newuser'],
		}).then((result) => {
			expect(result).to.have.length(1);
			expect(result[0].email).to.equal('newuser@example.com');
		});
	});
});
```

:cypress/support/e2e.js

```js
// Настройка database task
import mysql from 'mysql2/promise';

const queryTestDb = async (query, values = []) => {
	const connection = await mysql.createConnection({
		host: 'localhost',
		user: 'testuser',
		password: 'testpass',
		database: 'testdb',
	});

	const [rows] = await connection.execute(query, values);
	await connection.end();
	return rows;
};

// В cypress.config.js
export default defineConfig({
	e2e: {
		setupNodeEvents(on, config) {
			on('task', {
				queryDb: ({ query, values }) => queryTestDb(query, values),
			});
		},
	},
});
```

### 6. Визуальное тестирование

#### WebDriver.IO с Percy

:test/specs/visual.spec.js

```js
describe('Visual testing', () => {
	it('should match homepage design', async () => {
		await browser.url('/');

		// Ждем загрузки всех элементов
		await $('.hero-section').waitForDisplayed();
		await browser.pause(1000); // Ждем анимации

		// Делаем скриншот с Percy
		await browser.percySnapshot('Homepage');
	});

	it('should match responsive design', async () => {
		await browser.setWindowSize(375, 667); // iPhone размер
		await browser.url('/');

		await $('.mobile-menu').waitForDisplayed();
		await browser.percySnapshot('Homepage Mobile');
	});
});
```

**wdio.conf.js**

```js
export const config = {
	services: [
		[
			'percy',
			{
				// Percy конфигурация
			},
		],
	],
	// ...
};
```

#### Cypress с Percy

:cypress/e2e/visual.cy.js

```js
describe('Visual testing', () => {
	it('should match homepage design', () => {
		cy.visit('/');

		// Ждем загрузки всех элементов
		cy.get('.hero-section').should('be.visible');
		cy.wait(1000); // Ждем анимации

		// Делаем скриншот с Percy
		cy.percySnapshot('Homepage');
	});

	it('should match responsive design', () => {
		cy.viewport(375, 667); // iPhone размер
		cy.visit('/');

		cy.get('.mobile-menu').should('be.visible');
		cy.percySnapshot('Homepage Mobile');
	});
});
```

### 7. Тестирование производительности

#### WebDriver.IO с Lighthouse

:test/specs/performance.spec.js

```js
describe('Performance testing', () => {
	it('should meet performance benchmarks', async () => {
		await browser.url('/');

		// Запускаем Lighthouse аудит
		const result = await browser.emulateDevice('iPhone X');
		const audit = await browser.audit(['performance']);

		// Проверяем метрики
		expect(audit.performance).toBeGreaterThan(90);
		expect(audit.lcp).toBeLessThan(2500); // Largest Contentful Paint
		expect(audit.fid).toBeLessThan(100); // First Input Delay
	});
});
```

#### Cypress с cypress-audit

:cypress/e2e/performance.cy.js

```js
describe('Performance testing', () => {
	it('should meet performance benchmarks', () => {
		cy.visit('/');

		// Lighthouse аудит
		cy.lighthouse({
			performance: 90,
			accessibility: 90,
			'best-practices': 90,
			seo: 90,
		});

		// Проверка Core Web Vitals
		cy.get('body').then(() => {
			cy.window().then((win) => {
				// Измеряем LCP
				new win.PerformanceObserver((list) => {
					const entries = list.getEntries();
					const lcp = entries[entries.length - 1];
					expect(lcp.startTime).to.be.lessThan(2500);
				}).observe({ entryTypes: ['largest-contentful-paint'] });
			});
		});
	});
});
```

### 8. Мобильное тестирование

#### WebDriver.IO с Appium

:test/specs/mobile.spec.js

```js
describe('Mobile app testing', () => {
	it('should login on mobile app', async () => {
		// Для Android
		await $('~username-input').setValue('testuser');
		await $('~password-input').setValue('testpass');
		await $('~login-button').click();

		await expect($('~welcome-message')).toBeDisplayed();
	});

	it('should handle gestures', async () => {
		const element = await $('~swipeable-element');

		// Свайп влево
		await element.touchAction([
			{ action: 'press', x: 200, y: 200 },
			{ action: 'wait', ms: 100 },
			{ action: 'moveTo', x: 50, y: 200 },
			{ action: 'release' },
		]);

		await expect($('~next-screen')).toBeDisplayed();
	});
});
```

:wdio.mobile.conf.js

```js
export const config = {
	port: 4723,
	services: ['appium'],
	capabilities: [
		{
			platformName: 'Android',
			'appium:deviceName': 'Android Emulator',
			'appium:app': './app/android/app-debug.apk',
			'appium:automationName': 'UiAutomator2',
		},
	],
	// ...
};
```

#### Cypress (только веб-мобильная эмуляция)

:cypress/e2e/mobile-web.cy.js

```js
describe('Mobile web testing', () => {
	beforeEach(() => {
		cy.viewport('iphone-x');
	});

	it('should work on mobile viewport', () => {
		cy.visit('/');

		// Проверяем мобильное меню
		cy.get('.hamburger-menu').should('be.visible');
		cy.get('.desktop-menu').should('not.be.visible');

		// Тестируем touch события
		cy.get('.swipeable-carousel')
			.trigger('touchstart', { touches: [{ clientX: 200, clientY: 200 }] })
			.trigger('touchmove', { touches: [{ clientX: 50, clientY: 200 }] })
			.trigger('touchend');

		cy.get('.carousel-item.active').should('contain', 'Second slide');
	});
});
```

### 9. Работа с Shadow DOM

#### WebDriver.IO

:test/specs/shadow-dom.spec.js

```js
describe('Shadow DOM testing', () => {
	it('should interact with shadow DOM elements', async () => {
		await browser.url('/shadow-dom-page');

		// Получаем shadow root
		const shadowHost = await $('my-custom-element');
		const shadowRoot = await shadowHost.shadow$('input');

		await shadowRoot.setValue('test input');

		const shadowButton = await shadowHost.shadow$('button');
		await shadowButton.click();

		const result = await shadowHost.shadow$('.result');
		await expect(result).toHaveText('test input');
	});
});
```

#### Cypress

:cypress/e2e/shadow-dom.cy.js

```js
describe('Shadow DOM testing', () => {
	it('should interact with shadow DOM elements', () => {
		cy.visit('/shadow-dom-page');

		// Cypress поддерживает shadow DOM из коробки
		cy.get('my-custom-element').shadow().find('input').type('test input');

		cy.get('my-custom-element').shadow().find('button').click();

		cy.get('my-custom-element')
			.shadow()
			.find('.result')
			.should('contain.text', 'test input');
	});
});
```

### 10. Работа с WebSockets

#### WebDriver.IO

:test/specs/websocket.spec.js

```js
describe('WebSocket testing', () => {
	it('should handle real-time updates', async () => {
		await browser.url('/chat');

		// Отправляем сообщение
		await $('#message-input').setValue('Hello WebSocket!');
		await $('#send-button').click();

		// Ждем появления сообщения в чате
		await browser.waitUntil(
			async () => {
				const messages = await $$('.message');
				return messages.length > 0;
			},
			{
				timeout: 5000,
				timeoutMsg: 'Message not received via WebSocket',
			}
		);

		const lastMessage = await $('.message:last-child .text');
		await expect(lastMessage).toHaveText('Hello WebSocket!');
	});
});
```

#### Cypress

:cypress/e2e/websocket.cy.js

```js
describe('WebSocket testing', () => {
	it('should handle real-time updates', () => {
		cy.visit('/chat');

		// Отправляем сообщение
		cy.get('#message-input').type('Hello WebSocket!');
		cy.get('#send-button').click();

		// Ждем появления сообщения в чате
		cy.get('.message', { timeout: 5000 }).should('have.length.at.least', 1);
		cy.get('.message:last-child .text').should(
			'contain.text',
			'Hello WebSocket!'
		);
	});

	it('should mock WebSocket connection', () => {
		cy.visit('/chat', {
			onBeforeLoad: (win) => {
				// Мокаем WebSocket
				cy.stub(win, 'WebSocket').callsFake((url) => {
					const mockWs = {
						send: cy.stub(),
						close: cy.stub(),
						addEventListener: cy.stub(),
					};

					// Симулируем получение сообщения
					setTimeout(() => {
						const event = new win.MessageEvent('message', {
							data: JSON.stringify({ text: 'Mocked message' }),
						});
						mockWs.onmessage(event);
					}, 100);

					return mockWs;
				});
			},
		});

		cy.get('.message').should('contain.text', 'Mocked message');
	});
});
```

## Интеграция с инструментами разработки

### Docker конфигурация

#### WebDriver.IO

**Dockerfile.wdio**

```dockerfile
FROM node:18-alpine

WORKDIR /app

# Установка Chrome
RUN apk add --no-cache \
    chromium \
    nss \
    freetype \
    freetype-dev \
    harfbuzz \
    ca-certificates \
    ttf-freefont

# Установка зависимостей
COPY package*.json ./
RUN npm ci

COPY . .

# Запуск тестов
CMD ["npm", "run", "test:wdio:headless"]
```

**docker-compose.yml**

```yaml
version: '3.8'
services:
  selenium-hub:
    image: selenium/hub:4.0.0
    ports:
      - '4444:4444'

  chrome:
    image: selenium/node-chrome:4.0.0
    depends_on:
      - selenium-hub
    environment:
      - HUB_HOST=selenium-hub

  wdio-tests:
    build: .
    depends_on:
      - selenium-hub
      - chrome
    environment:
      - SELENIUM_HUB_URL=http://selenium-hub:4444/wd/hub
```

#### Cypress

:Dockerfile.cypress

```
FROM cypress/included:12.0.0

WORKDIR /app

COPY package\*.json ./
RUN npm ci

COPY . .

CMD ["npx", "cypress", "run"]
```

### Отчеты и аналитика

#### WebDriver.IO с кастомным репортером

javascript:test/reporters/custom-reporter.js

```js
import WDIOReporter from '@wdio/reporter';

export default class CustomReporter extends WDIOReporter {
	constructor(options) {
		super(options);
		this.results = [];
	}

	onTestPass(test) {
		this.results.push({
			title: test.title,
			status: 'passed',
			duration: test.duration,
			timestamp: new Date().toISOString(),
		});
	}

	onTestFail(test) {
		this.results.push({
			title: test.title,
			status: 'failed',
			duration: test.duration,
			error: test.error.message,
			timestamp: new Date().toISOString(),
		});
	}

	onRunnerEnd() {
		// Отправляем результаты в внешнюю систему
		this.sendToAnalytics(this.results);

		// Генерируем кастомный отчет
		this.generateCustomReport(this.results);
	}

	sendToAnalytics(results) {
		// Интеграция с системой аналитики
		fetch('https://analytics.company.com/test-results', {
			method: 'POST',
			headers: { 'Content-Type': 'application/json' },
			body: JSON.stringify(results),
		});
	}

	generateCustomReport(results) {
		const report = {
			summary: {
				total: results.length,
				passed: results.filter((r) => r.status === 'passed').length,
				failed: results.filter((r) => r.status === 'failed').length,
				duration: results.reduce((sum, r) => sum + r.duration, 0),
			},
			details: results,
		};

		require('fs').writeFileSync(
			'./test-results/custom-report.json',
			JSON.stringify(report, null, 2)
		);
	}
}
```

#### Cypress с кастомными командами для отчетности

**cypress/support/commands.js**

```js
// Команда для логирования действий пользователя
Cypress.Commands.add('logUserAction', (action, details) => {
	cy.task('logAction', {
		action,
		details,
		timestamp: new Date().toISOString(),
		url: window.location.href,
	});
});

// Команда для измерения производительности
Cypress.Commands.add('measurePerformance', (label) => {
	cy.window().then((win) => {
		win.performance.mark(`${label}-start`);

		return cy.wrap(null).then(() => {
			win.performance.mark(`${label}-end`);
			win.performance.measure(label, `${label}-start`, `${label}-end`);

			const measure = win.performance.getEntriesByName(label)[0];
			cy.task('recordPerformance', {
				label,
				duration: measure.duration,
				timestamp: new Date().toISOString(),
			});
		});
	});
});

// Использование в тестах
describe('User actions tracking', () => {
	it('should track user journey', () => {
		cy.visit('/login');
		cy.logUserAction('visit_login_page', { page: '/login' });

		cy.measurePerformance('login_process');

		cy.get('#username').type('testuser');
		cy.logUserAction('enter_username', { username: 'testuser' });

		cy.get('#password').type('password');
		cy.logUserAction('enter_password', { passwordLength: 8 });

		cy.get('#login-button').click();
		cy.logUserAction('click_login_button');

		cy.get('.welcome-message').should('be.visible');
		cy.measurePerformance('login_process');
		cy.logUserAction('successful_login', { redirectTo: '/dashboard' });
	});
});
```

**cypress.config.js**

```js
export default defineConfig({
	e2e: {
		setupNodeEvents(on, config) {
			const actions = [];
			const performance = [];

			on('task', {
				logAction(actionData) {
					actions.push(actionData);
					console.log('User Action:', actionData);
					return null;
				},

				recordPerformance(perfData) {
					performance.push(perfData);
					console.log('Performance:', perfData);
					return null;
				},

				generateReport() {
					const report = {
						userActions: actions,
						performance: performance,
						generatedAt: new Date().toISOString(),
					};

					require('fs').writeFileSync(
						'./cypress/reports/user-journey.json',
						JSON.stringify(report, null, 2)
					);

					return report;
				},
			});
		},
	},
});
```

## Паттерны и лучшие практики

### Page Object Model

#### WebDriver.IO

javascript:test/pageobjects/login.page.js

```js
import Page from './page.js';

class LoginPage extends Page {
	get usernameInput() {
		return $('#username');
	}
	get passwordInput() {
		return $('#password');
	}
	get loginButton() {
		return $('#login-button');
	}
	get errorMessage() {
		return $('.error-message');
	}
	get welcomeMessage() {
		return $('.welcome-message');
	}

	async login(username, password) {
		await this.usernameInput.setValue(username);
		await this.passwordInput.setValue(password);
		await this.loginButton.click();
	}

	async waitForLoginResult() {
		await browser.waitUntil(
			async () => {
				const welcomeDisplayed = await this.welcomeMessage.isDisplayed();
				const errorDisplayed = await this.errorMessage.isDisplayed();
				return welcomeDisplayed || errorDisplayed;
			},
			{
				timeout: 10000,
				timeoutMsg: 'Login result not displayed',
			}
		);
	}

	async isLoginSuccessful() {
		return await this.welcomeMessage.isDisplayed();
	}

	async getErrorMessage() {
		return await this.errorMessage.getText();
	}

	open() {
		return super.open('/login');
	}
}

export default new LoginPage();
```

javascript:test/pageobjects/page.js

```js
export default class Page {
	open(path) {
		return browser.url(path);
	}

	async waitForPageLoad() {
		await browser.waitUntil(async () => {
			const readyState = await browser.execute(() => document.readyState);
			return readyState === 'complete';
		});
	}

	async takeScreenshot(name) {
		await browser.saveScreenshot(`./screenshots/${name}.png`);
	}
}
```

#### Cypress

javascript:cypress/support/pages/LoginPage.js

```js
class LoginPage {
	get usernameInput() {
		return cy.get('#username');
	}
	get passwordInput() {
		return cy.get('#password');
	}
	get loginButton() {
		return cy.get('#login-button');
	}
	get errorMessage() {
		return cy.get('.error-message');
	}
	get welcomeMessage() {
		return cy.get('.welcome-message');
	}

	visit() {
		cy.visit('/login');
		return this;
	}

	login(username, password) {
		this.usernameInput.type(username);
		this.passwordInput.type(password);
		this.loginButton.click();
		return this;
	}

	waitForLoginResult() {
		// В Cypress автоматическое ожидание
		cy.get('.welcome-message, .error-message', { timeout: 10000 }).should(
			'be.visible'
		);
		return this;
	}

	verifyLoginSuccess() {
		this.welcomeMessage.should('be.visible');
		return this;
	}

	verifyLoginError(expectedMessage) {
		this.errorMessage.should('be.visible').and('contain.text', expectedMessage);
		return this;
	}

	takeScreenshot(name) {
		cy.screenshot(name);
		return this;
	}
}

export default new LoginPage();
```

### Использование Page Objects в тестах

#### WebDriver.IO

javascript:test/specs/login-with-po.spec.js

```js
import LoginPage from '../pageobjects/login.page.js';
import DashboardPage from '../pageobjects/dashboard.page.js';

describe('Login functionality with Page Objects', () => {
	beforeEach(async () => {
		await LoginPage.open();
	});

	it('should login with valid credentials', async () => {
		await LoginPage.login('testuser', 'testpass');
		await LoginPage.waitForLoginResult();

		expect(await LoginPage.isLoginSuccessful()).toBe(true);

		// Переходим на dashboard
		await DashboardPage.waitForPageLoad();
		expect(await DashboardPage.getWelcomeText()).toContain('testuser');
	});

	it('should show error for invalid credentials', async () => {
		await LoginPage.login('invalid', 'invalid');
		await LoginPage.waitForLoginResult();

		expect(await LoginPage.isLoginSuccessful()).toBe(false);
		expect(await LoginPage.getErrorMessage()).toBe('Invalid credentials');
	});
});
```

#### Cypress

javascript:cypress/e2e/login-with-po.cy.js

```js
import LoginPage from '../support/pages/LoginPage';
import DashboardPage from '../support/pages/DashboardPage';

describe('Login functionality with Page Objects', () => {
	it('should login with valid credentials', () => {
		LoginPage.visit().login('testuser', 'testpass').verifyLoginSuccess();

		DashboardPage.verifyWelcomeMessage('testuser');
	});

	it('should show error for invalid credentials', () => {
		LoginPage.visit()
			.login('invalid', 'invalid')
			.verifyLoginError('Invalid credentials');
	});
});
```

### Фикстуры и тестовые данные

#### WebDriver.IO

javascript:test/fixtures/users.js

```js
export const testUsers = {
	admin: {
		username: 'admin',
		password: 'admin123',
		role: 'administrator',
		permissions: ['read', 'write', 'delete'],
	},
	user: {
		username: 'testuser',
		password: 'user123',
		role: 'user',
		permissions: ['read'],
	},
	guest: {
		username: 'guest',
		password: 'guest123',
		role: 'guest',
		permissions: [],
	},
};

export const testData = {
	products: [
		{ id: 1, name: 'Laptop', price: 999.99, category: 'Electronics' },
		{ id: 2, name: 'Book', price: 19.99, category: 'Education' },
	],
	orders: [{ id: 1, userId: 1, productId: 1, quantity: 1, status: 'pending' }],
};
```

javascript: test / specs / data - driven.spec.js;

```js
import { testUsers, testData } from '../fixtures/users.js';
import LoginPage from '../pageobjects/login.page.js';

describe('Data-driven testing', () => {
	Object.entries(testUsers).forEach(([userType, userData]) => {
		it(`should login as ${userType}`, async () => {
			await LoginPage.open();
			await LoginPage.login(userData.username, userData.password);
			await LoginPage.waitForLoginResult();

			expect(await LoginPage.isLoginSuccessful()).toBe(true);
		});
	});

	testData.products.forEach((product) => {
		it(`should display product: ${product.name}`, async () => {
			await browser.url('/products');

			const productElement = await $(`.product[data-id="${product.id}"]`);
			await expect(productElement).toBeDisplayed();

			const productName = await productElement.$('.name');
			await expect(productName).toHaveText(product.name);
		});
	});
});
```

#### Cypress

json:cypress/fixtures/users.json

```json
{
	"admin": {
		"username": "admin",
		"password": "admin123",
		"role": "administrator",
		"permissions": ["read", "write", "delete"]
	},
	"user": {
		"username": "testuser",
		"password": "user123",
		"role": "user",
		"permissions": ["read"]
	},
	"guest": {
		"username": "guest",
		"password": "guest123",
		"role": "guest",
		"permissions": []
	}
}
```

javascript:cypress/e2e/data-driven.cy.js

```js
import LoginPage from '../support/pages/LoginPage';

describe('Data-driven testing', () => {
	let users;

	before(() => {
		cy.fixture('users').then((data) => {
			users = data;
		});
	});

	it('should login with different user types', () => {
		Object.entries(users).forEach(([userType, userData]) => {
			cy.log(`Testing login for ${userType}`);

			LoginPage.visit()
				.login(userData.username, userData.password)
				.verifyLoginSuccess();

			// Logout для следующей итерации
			cy.get('#logout-button').click();
		});
	});

	it('should test products from fixture', () => {
		cy.fixture('products').then((products) => {
			cy.visit('/products');

			products.forEach((product) => {
				cy.get(`.product[data-id="${product.id}"]`)
					.should('be.visible')
					.within(() => {
						cy.get('.name').should('contain.text', product.name);
						cy.get('.price').should('contain.text', product.price);
					});
			});
		});
	});
});
```

## Отладка и troubleshooting

### WebDriver.IO отладка

javascript:test/specs/debug-example.spec.js

```js
describe('Debugging example', () => {
	it('should debug step by step', async () => {
		await browser.url('/login');

		// Пауза для ручной проверки
		await browser.pause(2000);

		// Отладочная информация
		console.log('Current URL:', await browser.getUrl());
		console.log('Page title:', await browser.getTitle());

		// Проверка элемента
		const usernameInput = await $('#username');
		console.log('Username input exists:', await usernameInput.isExisting());
		console.log('Username input displayed:', await usernameInput.isDisplayed());

		// Скриншот для отладки
		await browser.saveScreenshot('./debug-screenshot.png');

		// Выполнение JavaScript в браузере
		const result = await browser.execute(() => {
			return {
				userAgent: navigator.userAgent,
				viewport: {
					width: window.innerWidth,
					height: window.innerHeight,
				},
				readyState: document.readyState,
			};
		});
		console.log('Browser info:', result);

		// Отладочная точка останова (только в режиме отладки)
		await browser.debug();
	});
});
```

### Cypress отладка

javascript:cypress/e2e/debug-example.cy.js

```js
describe('Debugging example', () => {
	it('should debug step by step', () => {
		cy.visit('/login');

		// Пауза для ручной проверки
		cy.wait(2000);

		// Отладочная информация
		cy.url().then((url) => {
			cy.log(`Current URL: ${url}`);
		});

		cy.title().then((title) => {
			cy.log(`Page title: ${title}`);
		});

		// Проверка элемента с отладкой
		cy.get('#username').then(($el) => {
			cy.log(`Username input exists: ${$el.length > 0}`);
			cy.log(`Username input visible: ${$el.is(':visible')}`);
		});

		// Скриншот для отладки
		cy.screenshot('debug-screenshot');

		// Выполнение JavaScript в браузере
		cy.window().then((win) => {
			const info = {
				userAgent: win.navigator.userAgent,
				viewport: {
					width: win.innerWidth,
					height: win.innerHeight,
				},
				readyState: win.document.readyState,
			};
			cy.log('Browser info:', info);
		});

		// Отладочная точка останова
		cy.debug();

		// Использование debugger в коде
		cy.get('#username').then(() => {
			debugger; // Остановка в DevTools
		});
	});
});
```

### Конфигурация для отладки

#### WebDriver.IO debug конфигурация

javascript:wdio.debug.conf.js

```js
import { config as baseConfig } from './wdio.conf.js';

export const config = {
	...baseConfig,

	// Запуск только одного теста за раз для отладки
	maxInstances: 1,

	// Отключаем headless режим
	capabilities: [
		{
			browserName: 'chrome',
			'goog:chromeOptions': {
				args: [
					'--disable-web-security',
					'--disable-features=VizDisplayCompositor',
				],
			},
		},
	],

	// Увеличиваем таймауты
	waitforTimeout: 30000,
	connectionRetryTimeout: 300000,

	// Подробные логи
	logLevel: 'debug',

	// Сохранение скриншотов при ошибках
	afterTest: async function (
		test,
		context,
		{ error, result, duration, passed, retries }
	) {
		if (!passed) {
			await browser.saveScreenshot(`./screenshots/error-${test.title}.png`);
		}
	},
};
```

#### Cypress debug конфигурация

javascript:cypress.debug.config.js

```js
import { defineConfig } from 'cypress';

export default defineConfig({
	e2e: {
		// Отключаем видео для ускорения отладки
		video: false,

		// Увеличиваем таймауты
		defaultCommandTimeout: 30000,
		requestTimeout: 30000,
		responseTimeout: 30000,

		// Замедляем выполнение команд
		slowTestThreshold: 10000,

		// Автоматические скриншоты при ошибках
		screenshotOnRunFailure: true,

		// Открываем DevTools автоматически
		chromeWebSecurity: false,

		setupNodeEvents(on, config) {
			// Логирование всех событий
			on('task', {
				log(message) {
					console.log(message);
					return null;
				},
			});

			// Обработка ошибок
			on('task', {
				failed: require('cypress-failed-log/src/failed')(),
			});
		},
	},
});
```

## Интеграция с системами мониторинга

### WebDriver.IO с Grafana/Prometheus

javascript:test/utils/metrics.js

```js
import client from 'prom-client';

// Создаем метрики
const testDuration = new client.Histogram({
	name: 'test_duration_seconds',
	help: 'Duration of test execution',
	labelNames: ['test_name', 'status', 'browser'],
});

const testCounter = new client.Counter({
	name: 'tests_total',
	help: 'Total number of tests executed',
	labelNames: ['status', 'browser'],
});

export class MetricsReporter {
	constructor() {
		this.startTime = null;
	}

	startTest(testName) {
		this.startTime = Date.now();
		this.testName = testName;
	}

	endTest(status, browser = 'chrome') {
		if (this.startTime) {
			const duration = (Date.now() - this.startTime) / 1000;

			testDuration.labels(this.testName, status, browser).observe(duration);

			testCounter.labels(status, browser).inc();
		}
	}

	async pushMetrics() {
		const gateway = new client.Pushgateway('http://pushgateway:9091');
		await gateway.pushAdd({ jobName: 'e2e-tests' });
	}
}
```

javascript:test/specs/monitored-test.spec.js

```js
import { MetricsReporter } from '../utils/metrics.js';

describe('Monitored tests', () => {
	let metrics;

	beforeEach(() => {
		metrics = new MetricsReporter();
	});

	afterEach(async () => {
		await metrics.pushMetrics();
	});

	it('should track test performance', async () => {
		metrics.startTest('login_test');

		try {
			await browser.url('/login');
			await $('#username').setValue('testuser');
			await $('#password').setValue('testpass');
			await $('#login-button').click();

			await expect($('.welcome-message')).toBeDisplayed();

			metrics.endTest('passed');
		} catch (error) {
			metrics.endTest('failed');
			throw error;
		}
	});
});
```

### Cypress с DataDog интеграцией

javascript:cypress/support/datadog.js

```js
// Отправка метрик в DataDog
export class DataDogReporter {
	constructor() {
		this.apiKey = Cypress.env('DATADOG_API_KEY');
		this.baseUrl = 'https://api.datadoghq.com/api/v1';
	}

	async sendMetric(metricName, value, tags = []) {
		const metric = {
			series: [
				{
					metric: metricName,
					points: [[Math.floor(Date.now() / 1000), value]],
					tags: tags,
					host: 'cypress-runner',
				},
			],
		};

		await fetch(`${this.baseUrl}/series?api_key=${this.apiKey}`, {
			method: 'POST',
			headers: { 'Content-Type': 'application/json' },
			body: JSON.stringify(metric),
		});
	}

	async sendEvent(title, text, tags = []) {
		const event = {
			title,
			text,
			tags,
			alert_type: 'info',
		};

		await fetch(`${this.baseUrl}/events?api_key=${this.apiKey}`, {
			method: 'POST',
			headers: { 'Content-Type': 'application/json' },
			body: JSON.stringify(event),
		});
	}
}
```

javascript:cypress/e2e/monitored-test.cy.js

```js
import { DataDogReporter } from '../support/datadog';

describe('Monitored tests', () => {
	let reporter;
	let startTime;

	beforeEach(() => {
		reporter = new DataDogReporter();
		startTime = Date.now();
	});

	afterEach(() => {
		const duration = Date.now() - startTime;
		const testName = Cypress.currentTest.title;
		const status = Cypress.currentTest.state;

		cy.task('sendMetric', {
			name: 'cypress.test.duration',
			value: duration,
			tags: [`test:${testName}`, `status:${status}`],
		});
	});

	it('should track test performance', () => {
		cy.visit('/login');

		// Измеряем время загрузки страницы
		cy.window().then((win) => {
			const loadTime =
				win.performance.timing.loadEventEnd -
				win.performance.timing.navigationStart;

			cy.task('sendMetric', {
				name: 'cypress.page.load_time',
				value: loadTime,
				tags: ['page:login'],
			});
		});

		cy.get('#username').type('testuser');
		cy.get('#password').type('testpass');
		cy.get('#login-button').click();

		cy.get('.welcome-message').should('be.visible');

		// Отправляем событие об успешном логине
		cy.task('sendEvent', {
			title: 'Successful Login Test',
			text: 'User successfully logged in during E2E test',
			tags: ['environment:test', 'team:qa'],
		});
	});
});
```

## Безопасность в тестировании

### Управление секретами

#### WebDriver.IO

javascript:test/utils/secrets.js

```js
import { SecretsManager } from 'aws-sdk';

export class SecretManager {
	constructor() {
		this.client = new SecretsManager({
			region: process.env.AWS_REGION || 'us-east-1',
		});
	}

	async getSecret(secretName) {
		try {
			const result = await this.client
				.getSecretValue({
					SecretId: secretName,
				})
				.promise();

			return JSON.parse(result.SecretString);
		} catch (error) {
			console.error('Error retrieving secret:', error);
			throw error;
		}
	}
}

// Использование в тестах
export async function getTestCredentials() {
	const secretManager = new SecretManager();

	if (process.env.NODE_ENV === 'production') {
		return await secretManager.getSecret('test-credentials');
	} else {
		return {
			username: process.env.TEST_USERNAME || 'testuser',
			password: process.env.TEST_PASSWORD || 'testpass',
		};
	}
}
```

javascript:test/specs/secure-login.spec.js

```js
import { getTestCredentials } from '../utils/secrets.js';
import LoginPage from '../pageobjects/login.page.js';

describe('Secure login tests', () => {
	let credentials;

	before(async () => {
		credentials = await getTestCredentials();
	});

	it('should login with secure credentials', async () => {
		await LoginPage.open();
		await LoginPage.login(credentials.username, credentials.password);
		await LoginPage.waitForLoginResult();

		expect(await LoginPage.isLoginSuccessful()).toBe(true);
	});

	afterEach(async () => {
		// Очищаем чувствительные данные из памяти
		if (credentials) {
			Object.keys(credentials).forEach((key) => {
				credentials[key] = null;
			});
		}
	});
});
```

#### Cypress

javascript:cypress/support/secrets.js

```js
// Cypress плагин для работы с секретами
export const getSecureCredentials = () => {
	// В production используем переменные окружения
	if (Cypress.env('NODE_ENV') === 'production') {
		return {
			username: Cypress.env('SECURE_USERNAME'),
			password: Cypress.env('SECURE_PASSWORD'),
		};
	}

	// В разработке используем локальные переменные
	return {
		username: Cypress.env('TEST_USERNAME') || 'testuser',
		password: Cypress.env('TEST_PASSWORD') || 'testpass',
	};
};

// Команда для безопасного ввода пароля
Cypress.Commands.add(
	'secureType',
	{ prevSubject: 'element' },
	(subject, text) => {
		// Отключаем логирование для паролей
		cy.wrap(subject, { log: false }).type(text, { log: false });
	}
);
```

javascript:cypress/e2e/secure-login.cy.js

```js
import { getSecureCredentials } from '../support/secrets';

describe('Secure login tests', () => {
	it('should login with secure credentials', () => {
		const credentials = getSecureCredentials();

		cy.visit('/login');

		cy.get('#username').type(credentials.username);
		cy.get('#password').secureType(credentials.password); // Не логируется
		cy.get('#login-button').click();

		cy.get('.welcome-message').should('be.visible');
	});
});
```

### Тестирование безопасности

#### WebDriver.IO - SQL Injection тесты

javascript:test/specs/security.spec.js

```js
describe('Security testing', () => {
	const sqlInjectionPayloads = [
		"' OR '1'='1",
		"'; DROP TABLE users; --",
		"' UNION SELECT * FROM users --",
		"admin'--",
		"' OR 1=1#",
	];

	sqlInjectionPayloads.forEach((payload) => {
		it(`should prevent SQL injection: ${payload}`, async () => {
			await browser.url('/login');

			await $('#username').setValue(payload);
			await $('#password').setValue('anypassword');
			await $('#login-button').click();

			// Проверяем, что атака не прошла
			await expect($('.error-message')).toBeDisplayed();
			await expect($('.welcome-message')).not.toBeDisplayed();

			// Проверяем, что нет признаков успешной атаки
			const pageSource = await browser.getPageSource();
			expect(pageSource).not.toContain('users');
			expect(pageSource).not.toContain('admin');
		});
	});

	const xssPayloads = [
		'<script>alert("XSS")</script>',
		'<img src="x" onerror="alert(1)">',
		'javascript:alert("XSS")',
		'<svg onload="alert(1)">',
	];

	xssPayloads.forEach((payload) => {
		it(`should prevent XSS: ${payload}`, async () => {
			await browser.url('/search');

			await $('#search-input').setValue(payload);
			await $('#search-button').click();

			// Проверяем, что скрипт не выполнился
			const alerts = await browser.getAlertText().catch(() => null);
			expect(alerts).toBeNull();

			// Проверяем, что payload экранирован
			const searchResults = await $('.search-results');
			const innerHTML = await searchResults.getHTML();
			expect(innerHTML).not.toContain('<script>');
			expect(innerHTML).not.toContain('onerror=');
		});
	});
});
```

#### Cypress - Security тесты

javascript:cypress/e2e/security.cy.js

```js
describe('Security testing', () => {
	const sqlInjectionPayloads = [
		"' OR '1'='1",
		"'; DROP TABLE users; --",
		"' UNION SELECT * FROM users --",
		"admin'--",
		"' OR 1=1#",
	];

	sqlInjectionPayloads.forEach((payload) => {
		it(`should prevent SQL injection: ${payload}`, () => {
			cy.visit('/login');

			cy.get('#username').type(payload);
			cy.get('#password').type('anypassword');
			cy.get('#login-button').click();

			// Проверяем, что атака не прошла
			cy.get('.error-message').should('be.visible');
			cy.get('.welcome-message').should('not.exist');

			// Проверяем отсутствие признаков успешной атаки
			cy.get('body').should('not.contain', 'users');
			cy.get('body').should('not.contain', 'admin');
		});
	});

	const xssPayloads = [
		'<script>alert("XSS")</script>',
		'<img src="x" onerror="alert(1)">',
		'javascript:alert("XSS")',
		'<svg onload="alert(1)">',
	];

	xssPayloads.forEach((payload) => {
		it(`should prevent XSS: ${payload}`, () => {
			cy.visit('/search');

			cy.get('#search-input').type(payload);
			cy.get('#search-button').click();

			// Проверяем, что скрипт не выполнился
			cy.window().then((win) => {
				// Мокаем alert для проверки
				cy.stub(win, 'alert').as('windowAlert');
			});

			cy.get('@windowAlert').should('not.have.been.called');

			// Проверяем экранирование
			cy.get('.search-results').then(($el) => {
				expect($el.html()).to.not.contain('<script>');
				expect($el.html()).to.not.contain('onerror=');
			});
		});
	});

	it('should have proper CSP headers', () => {
		cy.request('/').then((response) => {
			expect(response.headers).to.have.property('content-security-policy');

			const csp = response.headers['content-security-policy'];
			expect(csp).to.contain("default-src 'self'");
			expect(csp).to.contain("script-src 'self'");
		});
	});

	it('should have secure cookies', () => {
		cy.visit('/login');
		cy.get('#username').type('testuser');
		cy.get('#password').type('testpass');
		cy.get('#login-button').click();

		cy.getCookies().then((cookies) => {
			const sessionCookie = cookies.find((c) => c.name === 'session');
			expect(sessionCookie).to.have.property('secure', true);
			expect(sessionCookie).to.have.property('httpOnly', true);
			expect(sessionCookie).to.have.property('sameSite', 'strict');
		});
	});
});
```

## Accessibility тестирование

### WebDriver.IO с axe-core

javascript:test/specs/accessibility.spec.js

```js
import AxeBuilder from '@axe-core/webdriverio';

describe('Accessibility testing', () => {
	it('should have no accessibility violations on homepage', async () => {
		await browser.url('/');

		const results = await new AxeBuilder({ driver: browser })
			.withTags(['wcag2a', 'wcag2aa'])
			.analyze();

		expect(results.violations).toHaveLength(0);
	});

	it('should be keyboard navigable', async () => {
		await browser.url('/login');

		// Тестируем навигацию по Tab
		await browser.keys(['Tab']);
		let activeElement = await browser.getActiveElement();
		expect(await activeElement.getAttribute('id')).toBe('username');

		await browser.keys(['Tab']);
		activeElement = await browser.getActiveElement();
		expect(await activeElement.getAttribute('id')).toBe('password');

		await browser.keys(['Tab']);
		activeElement = await browser.getActiveElement();
		expect(await activeElement.getAttribute('id')).toBe('login-button');

		// Тестируем активацию Enter
		await browser.keys(['Enter']);
		await expect($('.error-message')).toBeDisplayed();
	});

	it('should have proper ARIA labels', async () => {
		await browser.url('/form');

		const usernameInput = await $('#username');
		expect(await usernameInput.getAttribute('aria-label')).toBe('Username');

		const passwordInput = await $('#password');
		expect(await passwordInput.getAttribute('aria-label')).toBe('Password');

		const submitButton = await $('#submit');
		expect(await submitButton.getAttribute('aria-describedby')).toBe(
			'submit-help'
		);
	});

	it('should support screen readers', async () => {
		await browser.url('/dashboard');

		// Проверяем заголовки для навигации
		const headings = await $$('h1, h2, h3, h4, h5, h6');
		expect(headings.length).toBeGreaterThan(0);

		// Проверяем alt текст для изображений
		const images = await $$('img');
		for (const img of images) {
			const alt = await img.getAttribute('alt');
			expect(alt).not.toBe('');
		}

		// Проверяем роли для интерактивных элементов
		const buttons = await $$('button');
		for (const button of buttons) {
			const role = await button.getAttribute('role');
			expect(role === 'button' || role === null).toBe(true);
		}
	});
});
```

### Cypress с cypress-axe

javascript:cypress/e2e/accessibility.cy.js

```js
describe('Accessibility testing', () => {
	beforeEach(() => {
		cy.visit('/');
		cy.injectAxe();
	});

	it('should have no accessibility violations on homepage', () => {
		cy.checkA11y();
	});

	it('should have no violations with specific rules', () => {
		cy.checkA11y(null, {
			tags: ['wcag2a', 'wcag2aa'],
			rules: {
				'color-contrast': { enabled: true },
				'keyboard-navigation': { enabled: true },
			},
		});
	});

	it('should be keyboard navigable', () => {
		cy.visit('/login');

		// Тестируем навигацию по Tab
		cy.get('body').tab();
		cy.focused().should('have.id', 'username');

		cy.focused().tab();
		cy.focused().should('have.id', 'password');

		cy.focused().tab();
		cy.focused().should('have.id', 'login-button');

		// Тестируем активацию Enter
		cy.focused().type('{enter}');
		cy.get('.error-message').should('be.visible');
	});

	it('should have proper focus management', () => {
		cy.visit('/modal-page');

		cy.get('#open-modal').click();

		// Фокус должен переместиться в модальное окно
		cy.focused().should('have.class', 'modal-close-button');

		// Tab должен циклически перемещаться внутри модального окна
		cy.focused().tab();
		cy.focused().should('have.class', 'modal-action-button');

		cy.focused().tab();
		cy.focused().should('have.class', 'modal-close-button');

		// Закрытие модального окна должно вернуть фокус
		cy.get('.modal-close-button').click();
		cy.focused().should('have.id', 'open-modal');
	});

	it('should announce dynamic content changes', () => {
		cy.visit('/dynamic-content');

		// Проверяем наличие aria-live регионов
		cy.get('[aria-live="polite"]').should('exist');
		cy.get('[aria-live="assertive"]').should('exist');

		// Тестируем объявление изменений
		cy.get('#load-content').click();

		cy.get('[aria-live="polite"]').should(
			'contain.text',
			'Content loaded successfully'
		);
	});

	it('should have proper form labels and validation', () => {
		cy.visit('/contact-form');

		// Проверяем связь label с input
		cy.get('label[for="name"]').should('exist');
		cy.get('#name').should('have.attr', 'aria-labelledby');

		// Проверяем сообщения об ошибках
		cy.get('#submit').click();

		cy.get('#name-error')
			.should('be.visible')
			.and('have.attr', 'role', 'alert');

		cy.get('#name')
			.should('have.attr', 'aria-describedby', 'name-error')
			.and('have.attr', 'aria-invalid', 'true');
	});
});
```

## Кроссбраузерное тестирование

### WebDriver.IO с BrowserStack

javascript:wdio.browserstack.conf.js

```js
export const config = {
	user: process.env.BROWSERSTACK_USERNAME,
	key: process.env.BROWSERSTACK_ACCESS_KEY,

	specs: ['./test/specs/**/*.js'],

	capabilities: [
		{
			'bstack:options': {
				os: 'Windows',
				osVersion: '10',
				projectName: 'E2E Tests',
				buildName: 'Build 1.0',
				sessionName: 'Chrome Test',
			},
			browserName: 'Chrome',
			browserVersion: 'latest',
		},
		{
			'bstack:options': {
				os: 'OS X',
				osVersion: 'Big Sur',
				projectName: 'E2E Tests',
				buildName: 'Build 1.0',
				sessionName: 'Safari Test',
			},
			browserName: 'Safari',
			browserVersion: 'latest',
		},
		{
			'bstack:options': {
				os: 'Windows',
				osVersion: '10',
				projectName: 'E2E Tests',
				buildName: 'Build 1.0',
				sessionName: 'Firefox Test',
			},
			browserName: 'Firefox',
			browserVersion: 'latest',
		},
	],

	services: [
		[
			'browserstack',
			{
				browserstackLocal: true,
				opts: {
					forceLocal: true,
				},
			},
		],
	],

	// Специфичные настройки для разных браузеров
	beforeSession: function (config, capabilities, specs) {
		if (capabilities.browserName === 'Safari') {
			// Специальные настройки для Safari
			capabilities['safari.options'] = {
				technologyPreview: false,
			};
		}

		if (capabilities.browserName === 'Internet Explorer') {
			// Настройки для IE
			capabilities['se:ieOptions'] = {
				ignoreProtectedModeSettings: true,
				ignoreZoomSetting: true,
			};
		}
	},
};
```

### Cypress с Cypress Dashboard

**cypress.config.js**

```js
export default defineConfig({
	projectId: 'your-project-id',

	e2e: {
		baseUrl: 'http://localhost:3000',

		// Конфигурация для разных браузеров
		setupNodeEvents(on, config) {
			// Настройки для Chrome
			if (config.browser.name === 'chrome') {
				config.chromeWebSecurity = false;
				config.defaultCommandTimeout = 8000;
			}

			// Настройки для Firefox
			if (config.browser.name === 'firefox') {
				config.defaultCommandTimeout = 10000;
				config.requestTimeout = 8000;
			}

			// Настройки для Edge
			if (config.browser.name === 'edge') {
				config.defaultCommandTimeout = 12000;
			}

			return config;
		},
	},
});
```

```bash
# Запуск тестов в разных браузерах
npx cypress run --browser chrome
npx cypress run --browser firefox
npx cypress run --browser edge
```

## Заключение и рекомендации

### Когда выбирать WebDriver.IO:

1. **Мобильное тестирование** - полная поддержка Appium
2. **Legacy браузеры** - поддержка Internet Explorer
3. **Сложные сценарии** - работа с несколькими вкладками, iframe
4. **Существующая Selenium инфраструктура** - легкая миграция
5. **Большие команды** - гибкая конфигурация и масштабирование

### Когда выбирать Cypress:

1. **Быстрый старт** - простота изучения и настройки
2. **Отладка** - превосходные инструменты разработчика
3. **Современные веб-приложения** - отличная поддержка SPA
4. **Команды новичков** - низкий порог входа
5. **Активная разработка тестов** - hot reload и real-time feedback

### Общие рекомендации:

1. **Начинайте с простого** - выберите инструмент, который быстрее освоит команда
2. **Инвестируйте в инфраструктуру** - CI/CD, отчеты, мониторинг
3. **Следуйте лучшим практикам** - Page Object Model, data-driven тесты
4. **Автоматизируйте постепенно** - начните с критичных пользовательских сценариев
5. **Поддерживайте тесты** - регулярно обновляйте и рефакторьте

### Полезные ресурсы для изучения:

#### Курсы и обучение:

- [Test Automation University](https://testautomationu.applitools.com/)
- [Cypress Real World Testing](https://learn.cypress.io/)
- [WebDriver.IO Tutorials](https://webdriver.io/docs/gettingstarted)

#### Сообщества:

- [WebDriver.IO Discord](https://discord.webdriver.io/)
- [Cypress Discord](https://discord.gg/cypress)
- [Ministry of Testing](https://www.ministryoftesting.com/)

#### Блоги и статьи:

- [Cypress Blog](https://www.cypress.io/blog/)
- [WebDriver.IO Blog](https://webdriver.io/blog/)
- [Awesome Testing](https://github.com/TheJambo/awesome-testing)
