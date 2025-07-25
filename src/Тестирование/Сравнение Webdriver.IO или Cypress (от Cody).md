# WebDriver.IO vs Cypress: Сравнение фреймворков для автоматизации тестирования

## Содержание

- [Содержание](#содержание)
- [Краткое сравнение](#краткое-сравнение)
- [Стоимость использования](#стоимость-использования)
  - [WebDriver.IO - Полностью бесплатно](#webdriverio---полностью-бесплатно)
    - [✅ Бесплатные возможности:](#-бесплатные-возможности)
    - [💰 Дополнительные платные сервисы (опционально):](#-дополнительные-платные-сервисы-опционально)
  - [Cypress - Бесплатно с платными облачными функциями](#cypress---бесплатно-с-платными-облачными-функциями)
    - [✅ Бесплатные возможности:](#-бесплатные-возможности-1)
    - [💰 Cypress Cloud (платные тарифы):](#-cypress-cloud-платные-тарифы)
    - [Бесплатные альтернативы Cypress Cloud:](#бесплатные-альтернативы-cypress-cloud)
- [Установка и настройка](#установка-и-настройка)
  - [WebDriver.IO](#webdriverio)
  - [Cypress](#cypress)
- [Примеры реализации одинаковых тестов](#примеры-реализации-одинаковых-тестов)
  - [1. Простой тест авторизации](#1-простой-тест-авторизации)
    - [WebDriver.IO](#webdriverio-1)
    - [Cypress](#cypress-1)
  - [2. Работа с API и перехват запросов](#2-работа-с-api-и-перехват-запросов)
    - [WebDriver.IO](#webdriverio-2)
    - [Cypress](#cypress-2)
  - [3. Работа с файлами](#3-работа-с-файлами)
    - [WebDriver.IO](#webdriverio-3)
    - [Cypress](#cypress-3)
  - [4. Мобильное тестирование (только WebDriver.IO)](#4-мобильное-тестирование-только-webdriverio)
    - [WebDriver.IO](#webdriverio-4)
- [Конфигурационные файлы](#конфигурационные-файлы)
  - [WebDriver.IO (wdio.conf.js)](#webdriverio-wdioconfjs)
  - [Cypress (cypress.config.js)](#cypress-cypressconfigjs)
- [Преимущества и недостатки](#преимущества-и-недостатки)
  - [WebDriver.IO](#webdriverio-5)
    - [✅ Преимущества:](#-преимущества)
    - [❌ Недостатки:](#-недостатки)
  - [Cypress](#cypress-4)
    - [✅ Преимущества:](#-преимущества-1)
    - [❌ Недостатки:](#-недостатки-1)
- [Когда использовать каждый фреймворк](#когда-использовать-каждый-фреймворк)
  - [Выбирайте WebDriver.IO если:](#выбирайте-webdriverio-если)
  - [Выбирайте Cypress если:](#выбирайте-cypress-если)
- [Полезные ссылки](#полезные-ссылки)
  - [WebDriver.IO](#webdriverio-6)
  - [Cypress](#cypress-5)
  - [Дополнительные ресурсы](#дополнительные-ресурсы)
- [Заключение](#заключение)

---

## Краткое сравнение

| Характеристика              | WebDriver.IO                              | Cypress                         |
| --------------------------- | ----------------------------------------- | ------------------------------- |
| **Архитектура**             | Selenium WebDriver (удаленное выполнение) | Прямое выполнение в браузере    |
| **Поддержка браузеров**     | Chrome, Firefox, Safari, Edge, IE         | Chrome, Firefox, Edge, Electron |
| **Языки программирования**  | JavaScript, TypeScript                    | JavaScript, TypeScript          |
| **Мобильное тестирование**  | ✅ (Appium интеграция)                    | ❌                              |
| **Кроссбраузерность**       | ✅ Отличная                               | ⚠️ Ограниченная                 |
| **Параллельное выполнение** | ✅ Встроенная поддержка                   | ✅ С Cypress Cloud              |
| **Отладка**                 | ⚠️ Сложнее                                | ✅ Отличная                     |
| **Скорость выполнения**     | ⚠️ Медленнее                              | ✅ Быстрее                      |
| **CI/CD интеграция**        | ✅ Отличная                               | ✅ Отличная                     |
| **Стоимость использования** | ✅ Полностью бесплатно                    | ✅ Бесплатно (Cloud - платно)   |

---

## Стоимость использования

### WebDriver.IO - Полностью бесплатно

#### ✅ Бесплатные возможности:

- Все функции фреймворка без ограничений
- Локальное и удаленное выполнение тестов
- Параллельное выполнение
- Интеграция с любыми CI/CD системами
- Поддержка всех браузеров и мобильных платформ
- Все репортеры и плагины

#### 💰 Дополнительные платные сервисы (опционально):

- **Sauce Labs** - облачное выполнение тестов (~$39+/месяц)
- **BrowserStack** - кроссбраузерное тестирование (~$29+/месяц)
- **LambdaTest** - облачная платформа тестирования (~$15+/месяц)

```javascript
// Полностью бесплатное использование
export const config = {
	runner: 'local',
	capabilities: [
		{
			browserName: 'chrome',
		},
	],
	// Все функции доступны бесплатно
};
```

### Cypress - Бесплатно с платными облачными функциями

#### ✅ Бесплатные возможности:

- Cypress Test Runner (все основные функции)
- Локальное выполнение тестов
- Отладка с Time Travel
- Скриншоты и видео записи
- API тестирование и перехват запросов
- CI/CD интеграция

#### 💰 Cypress Cloud (платные тарифы):

| План         | Стоимость   | Возможности                                 |
| ------------ | ----------- | ------------------------------------------- |
| **Free**     | $0          | 500 тестовых запусков/месяц, 3 пользователя |
| **Team**     | ~$75/месяц  | Неограниченные запуски, пользователи        |
| **Business** | ~$300/месяц | + SSO, приоритетная поддержка               |

```bash
# Бесплатное использование
npx cypress run

# Платное - отправка в Cypress Cloud
npx cypress run --record --key=your-record-key
```

#### Бесплатные альтернативы Cypress Cloud:

```bash
# Mochawesome Reporter
npm install --save-dev mochawesome

# Allure Reports
npm install --save-dev @shelex/cypress-allure-plugin
```

---

## Установка и настройка

### WebDriver.IO

```bash
# Создание нового проекта
npm init wdio@latest ./my-test-project

# Или добавление в существующий проект
npm install --save-dev @wdio/cli
npx wdio config
```

### Cypress

```bash
# Установка
npm install --save-dev cypress

# Открытие Test Runner
npx cypress open

# Запуск в headless режиме
npx cypress run
```

---

## Примеры реализации одинаковых тестов

### 1. Простой тест авторизации

#### WebDriver.IO

```javascript
// test/specs/login.e2e.js
describe('Login functionality', () => {
	it('should login with valid credentials', async () => {
		await browser.url('https://example.com/login');

		await $('#username').setValue('testuser');
		await $('#password').setValue('testpass');
		await $('button[type="submit"]').click();

		await expect($('.welcome-message')).toBeDisplayed();
		await expect($('.welcome-message')).toHaveText('Welcome, testuser!');
	});
});
```

#### Cypress

```javascript
// cypress/e2e/login.cy.js
describe('Login functionality', () => {
	it('should login with valid credentials', () => {
		cy.visit('https://example.com/login');

		cy.get('#username').type('testuser');
		cy.get('#password').type('testpass');
		cy.get('button[type="submit"]').click();

		cy.get('.welcome-message').should('be.visible');
		cy.get('.welcome-message').should('contain.text', 'Welcome, testuser!');
	});
});
```

### 2. Работа с API и перехват запросов

#### WebDriver.IO

```javascript
// test/specs/api-integration.e2e.js
describe('API Integration', () => {
	it('should handle API responses', async () => {
		// Настройка mock через внешний инструмент или browser.mock()
		await browser.url('https://example.com/users');

		// Ожидание загрузки данных
		await browser.waitUntil(async () => {
			const users = await $$('.user-item');
			return users.length > 0;
		});

		const userCount = await $$('.user-item').length;
		expect(userCount).toBeGreaterThan(0);
	});
});
```

#### Cypress

```javascript
// cypress/e2e/api-integration.cy.js
describe('API Integration', () => {
	it('should handle API responses', () => {
		// Перехват API запроса
		cy.intercept('GET', '/api/users', { fixture: 'users.json' }).as('getUsers');

		cy.visit('https://example.com/users');

		// Ожидание API запроса
		cy.wait('@getUsers');

		cy.get('.user-item').should('have.length.greaterThan', 0);
	});
});
```

### 3. Работа с файлами

#### WebDriver.IO

```javascript
// test/specs/file-upload.e2e.js
import path from 'path';

describe('File Upload', () => {
	it('should upload a file', async () => {
		await browser.url('https://example.com/upload');

		const filePath = path.join(__dirname, '../fixtures/test-file.pdf');
		const fileInput = await $('#file-input');

		await fileInput.setValue(filePath);
		await $('button[type="submit"]').click();

		await expect($('.success-message')).toBeDisplayed();
	});
});
```

#### Cypress

```javascript
// cypress/e2e/file-upload.cy.js
describe('File Upload', () => {
	it('should upload a file', () => {
		cy.visit('https://example.com/upload');

		cy.get('#file-input').selectFile('cypress/fixtures/test-file.pdf');
		cy.get('button[type="submit"]').click();

		cy.get('.success-message').should('be.visible');
	});
});
```

### 4. Мобильное тестирование (только WebDriver.IO)

#### WebDriver.IO

```javascript
// test/specs/mobile.e2e.js
describe('Mobile App Testing', () => {
	it('should test mobile app', async () => {
		// Конфигурация для мобильного устройства в wdio.conf.js
		await $('~login-button').click(); // ~ для accessibility id

		await $('android=new UiSelector().text("Username")').setValue('testuser');
		await $('ios=.XCUIElementTypeTextField').setValue('testpass');

		await $('~submit-button').click();

		await expect($('~welcome-screen')).toBeDisplayed();
	});
});
```

---

## Конфигурационные файлы

### WebDriver.IO (wdio.conf.js)

```javascript
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
	waitforTimeout: 10000,
	connectionRetryTimeout: 120000,
	connectionRetryCount: 3,
	framework: 'mocha',
	reporters: ['spec', 'allure'],
	mochaOpts: {
		ui: 'bdd',
		timeout: 60000,
	},
};
```

### Cypress (cypress.config.js)

```javascript
import { defineConfig } from 'cypress';

export default defineConfig({
	e2e: {
		baseUrl: 'https://example.com',
		viewportWidth: 1280,
		viewportHeight: 720,
		video: true,
		screenshotOnRunFailure: true,
		defaultCommandTimeout: 10000,
		requestTimeout: 10000,
		responseTimeout: 10000,
		setupNodeEvents(on, config) {
			// Плагины и обработчики событий
		},
	},
});
```

---

## Преимущества и недостатки

### WebDriver.IO

#### ✅ Преимущества:

- Поддержка множества браузеров и мобильных платформ
- Мощная параллелизация из коробки
- Гибкая настройка и расширяемость
- Отличная интеграция с Appium для мобильного тестирования
- Поддержка различных фреймворков (Mocha, Jasmine, Cucumber)
- **Полностью бесплатное использование**

#### ❌ Недостатки:

- Более сложная настройка
- Медленнее выполнение тестов
- Сложнее отладка
- Требует больше ресурсов

### Cypress

#### ✅ Преимущества:

- Простота использования и настройки
- Отличные инструменты отладки
- Быстрое выполнение тестов
- Встроенные возможности для работы с API
- Автоматические скриншоты и видео
- Time-travel debugging
- **Основные функции бесплатны**

#### ❌ Недостатки:

- Ограниченная поддержка браузеров
- Нет поддержки мобильного тестирования
- Ограничения в работе с несколькими вкладками
- Только JavaScript/TypeScript
- **Платные облачные функции для команд**

---

## Когда использовать каждый фреймворк

### Выбирайте WebDriver.IO если:

- Нужна поддержка множества браузеров
- Требуется мобильное тестирование
- Работаете с legacy системами
- Нужна максимальная гибкость настройки
- Команда имеет опыт работы с Selenium
- **Важна полная бесплатность решения**

### Выбирайте Cypress если:

- Разрабатываете современные веб-приложения
- Нужна быстрая разработка тестов
- Важна простота отладки
- Команда состоит из фронтенд-разработчиков
- Не требуется кроссбраузерное тестирование
- **Готовы платить за расширенные облачные функции**

---

## Полезные ссылки

### WebDriver.IO

- [Официальная документация](https://webdriver.io/)
- [Getting Started Guide](https://webdriver.io/docs/gettingstarted)
- [API Reference](https://webdriver.io/docs/api)
- [GitHub Repository](https://github.com/webdriverio/webdriverio)
- [Awesome WebDriver.IO](https://github.com/christian-bromann/awesome-webdriverio)

### Cypress

- [Официальная документация](https://docs.cypress.io/)
- [Getting Started](https://docs.cypress.io/guides/getting-started/installing-cypress)
- [API Reference](https://docs.cypress.io/api/table-of-contents)
- [GitHub Repository](https://github.com/cypress-io/cypress)
- [Cypress Real World App](https://github.com/cypress-io/cypress-realworld-app)
- [Cypress Cloud Pricing](https://www.cypress.io/pricing)

### Дополнительные ресурсы

- [Сравнение инструментов тестирования](https://www.browserstack.com/guide/cypress-vs-selenium)
- [Test Automation University](https://testautomationu.applitools.com/)
- [Playwright как альтернатива](https://playwright.dev/)

---

## Заключение

Оба фреймворка имеют свои сильные стороны:

- **WebDriver.IO** лучше подходит для комплексного тестирования с поддержкой множества платформ и полностью бесплатен
- **Cypress** идеален для быстрой разработки тестов современных веб-приложений, основные функции бесплатны, но расширенные возможности требуют подписки

Выбор зависит от специфики проекта, требований к кроссбраузерности, бюджета и опыта команды.
