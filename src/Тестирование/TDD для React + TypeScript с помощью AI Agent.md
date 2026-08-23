# TDD (Test-Driven Development) для React + TypeScript с помощью AI Agent

## Содержание

- [Содержание](#содержание)
- [1. Что такое TDD с AI Agent?](#1-что-такое-tdd-с-ai-agent)
  - [Цикл TDD с AI:](#цикл-tdd-с-ai)
- [2. Практические преимущества для React + TypeScript](#2-практические-преимущества-для-react--typescript)
- [3. Настройка окружения](#3-настройка-окружения)
  - [3.1 Установка зависимостей](#31-установка-зависимостей)
  - [3.2 Конфигурация Vitest](#32-конфигурация-vitest)
  - [3.3 Setup файл для тестов](#33-setup-файл-для-тестов)
- [4. Практический пример: Разработка формы логина с AI Agent](#4-практический-пример-разработка-формы-логина-с-ai-agent)
  - [4.1 Шаг 1: Описание требований для AI](#41-шаг-1-описание-требований-для-ai)
  - [4.2 Шаг 2: AI генерирует тесты](#42-шаг-2-ai-генерирует-тесты)
  - [4.3 Шаг 3: AI создаёт компонент для прохождения тестов](#43-шаг-3-ai-создаёт-компонент-для-прохождения-тестов)
  - [4.4 Шаг 4: AI предлагает рефакторинг](#44-шаг-4-ai-предлагает-рефакторинг)
- [5. Продвинутые техники TDD с AI](#5-продвинутые-техники-tdd-с-ai)
  - [5.1 Тестирование React хуков](#51-тестирование-react-хуков)
  - [5.2 Тестирование с контекстом](#52-тестирование-с-контекстом)
  - [5.3 Интеграционные тесты](#53-интеграционные-тесты)
- [6. Практические советы и лучшие практики](#6-практические-советы-и-лучшие-практики)
  - [6.1 Эффективные промпты для AI Agent](#61-эффективные-промпты-для-ai-agent)
  - [6.2 Структура проекта для TDD](#62-структура-проекта-для-tdd)
  - [6.3 Команды для ежедневной работы](#63-команды-для-ежедневной-работы)
  - [6.4 Интеграция с CI/CD](#64-интеграция-с-cicd)
  - [6.5 Отладка тестов с AI Agent](#65-отладка-тестов-с-ai-agent)
- [7. Типичные проблемы и решения](#7-типичные-проблемы-и-решения)
  - [7.1 Асинхронные операции](#71-асинхронные-операции)
  - [7.2 Мокинг модулей](#72-мокинг-модулей)
  - [7.3 Тестирование ошибок](#73-тестирование-ошибок)
- [8. Измерение эффективности TDD](#8-измерение-эффективности-tdd)
  - [8.1 Метрики качества](#81-метрики-качества)
  - [8.2 Автоматизация с AI](#82-автоматизация-с-ai)
- [9. Заключение](#9-заключение)

---

## 1. Что такое TDD с AI Agent?

**TDD с AI Agent** — современный подход к разработке, где:

1. **Описываете требования** на естественном языке
2. **AI генерирует тесты** на основе требований
3. **AI создаёт код**, который проходит тесты
4. **AI помогает с рефакторингом** и улучшением архитектуры

### Цикл TDD с AI:

```
Требования → AI создаёт тесты → AI создаёт код → AI рефакторит → Повтор
```

## 2. Практические преимущества для React + TypeScript

- **Автоматическая типизация**: AI создаёт правильные TypeScript типы
- **Покрытие edge cases**: AI предлагает дополнительные тесты
- **Консистентность**: единый стиль кода и тестов
- **Скорость разработки**: мгновенная генерация тестов и кода
- **Обучение**: AI объясняет лучшие практики React

## 3. Настройка окружения

### 3.1 Установка зависимостей

```bash
pnpm add -D vitest @testing-library/react @testing-library/jest-dom jsdom
pnpm add -D @types/react @types/react-dom
```

### 3.2 Конфигурация Vitest

```ts
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
	plugins: [react()],
	test: {
		environment: 'jsdom',
		globals: true,
		setupFiles: ['./src/test/setup.ts'],
		coverage: {
			reporter: ['text', 'html'],
			exclude: ['node_modules/', 'src/test/'],
		},
	},
});
```

### 3.3 Setup файл для тестов

```ts
// src/test/setup.ts
import '@testing-library/jest-dom';
```

## 4. Практический пример: Разработка формы логина с AI Agent

### 4.1 Шаг 1: Описание требований для AI

**Промпт для AI:**

```
Создай тесты для компонента LoginForm на React + TypeScript:
- Поля email и password
- Кнопка Submit
- Валидация: email должен быть корректным, password минимум 6 символов
- При успешной отправке вызывается onSubmit с данными
- При ошибках показываются сообщения
- Состояние loading блокирует форму
```

### 4.2 Шаг 2: AI генерирует тесты

```ts
// LoginForm.test.tsx
import { describe, it, expect, vi } from 'vitest';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import LoginForm from './LoginForm';

interface LoginFormProps {
  onSubmit: (data: { email: string; password: string }) => Promise<void>;
  loading?: boolean;
}

describe('LoginForm', () => {
  const mockSubmit = vi.fn();

  beforeEach(() => {
    mockSubmit.mockReset();
  });

  it('рендерит поля email и password', () => {
    render(<LoginForm onSubmit={mockSubmit} />);

    expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/password/i)).toBeInTheDocument();
    expect(screen.getByRole('button', { name: /войти/i })).toBeInTheDocument();
  });

  it('показывает ошибку при некорректном email', async () => {
    const user = userEvent.setup();
    render(<LoginForm onSubmit={mockSubmit} />);

    await user.type(screen.getByLabelText(/email/i), 'invalid-email');
    await user.click(screen.getByRole('button', { name: /войти/i }));

    expect(screen.getByText(/некорректный email/i)).toBeInTheDocument();
  });

  it('показывает ошибку при коротком пароле', async () => {
    const user = userEvent.setup();
    render(<LoginForm onSubmit={mockSubmit} />);

    await user.type(screen.getByLabelText(/password/i), '123');
    await user.click(screen.getByRole('button', { name: /войти/i }));

    expect(screen.getByText(/пароль должен содержать минимум 6 символов/i)).toBeInTheDocument();
  });

  it('вызывает onSubmit с корректными данными', async () => {
    const user = userEvent.setup();
    mockSubmit.mockResolvedValue(undefined);

    render(<LoginForm onSubmit={mockSubmit} />);

    await user.type(screen.getByLabelText(/email/i), 'test@example.com');
    await user.type(screen.getByLabelText(/password/i), 'password123');
    await user.click(screen.getByRole('button', { name: /войти/i }));

    expect(mockSubmit).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: 'password123'
    });
  });

  it('блокирует форму в состоянии loading', () => {
    render(<LoginForm onSubmit={mockSubmit} loading={true} />);

    expect(screen.getByLabelText(/email/i)).toBeDisabled();
    expect(screen.getByLabelText(/password/i)).toBeDisabled();
    expect(screen.getByRole('button', { name: /загрузка/i })).toBeDisabled();
  });
});
```

### 4.3 Шаг 3: AI создаёт компонент для прохождения тестов

```ts
// LoginForm.tsx
import React, { useState } from 'react';

interface LoginFormData {
  email: string;
  password: string;
}

interface LoginFormProps {
  onSubmit: (data: LoginFormData) => Promise<void>;
  loading?: boolean;
}

const LoginForm: React.FC<LoginFormProps> = ({ onSubmit, loading = false }) => {
  const [formData, setFormData] = useState<LoginFormData>({
    email: '',
    password: ''
  });
  const [errors, setErrors] = useState({});

  const validateEmail = (email: string): boolean => {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email);
  };

  const validateForm = (): boolean => {
    const newErrors: any = {};

    if (!validateEmail(formData.email)) {
      newErrors.email = 'Некорректный email';
    }

    if (formData.password.length < 6) {
      newErrors.password = 'Пароль должен содержать минимум 6 символов';
    }

    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();

    if (validateForm()) {
      await onSubmit(formData);
    }
  };

  const handleChange = (field: keyof LoginFormData) =>
    (e: React.ChangeEvent<HTMLInputElement>) => {
      setFormData(prev => ({ ...prev, [field]: e.target.value }));
      // Очищаем ошибку при изменении поля
      if (errors[field]) {
        setErrors(prev => ({ ...prev, [field]: undefined }));
      }
    };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="email">Email:</label>
        <input
          id="email"
          type="email"
          value={formData.email}
          onChange={handleChange('email')}
          disabled={loading}
        />
        {errors.email && <span className="error">{errors.email}</span>}
      </div>

      <div>
        <label htmlFor="password">Password:</label>
        <input
          id="password"
          type="password"
          value={formData.password}
          onChange={handleChange('password')}
          disabled={loading}
        />
        {errors.password && <span className="error">{errors.password}</span>}
      </div>

      <button type="submit" disabled={loading}>
        {loading ? 'Загрузка...' : 'Войти'}
      </button>
    </form>
  );
};

export default LoginForm;
```

### 4.4 Шаг 4: AI предлагает рефакторинг

**Промпт для AI:**

```
Отрефактори LoginForm для улучшения:
- Вынеси валидацию в отдельный хук
- Добавь типы для ошибок
- Улучши доступность (a11y)
```

**AI создаёт кастомный хук:**

```ts
// useFormValidation.ts
import { useState, useCallback } from 'react';

interface ValidationRule<T> {
  validator: (value: T) => boolean;
  message: string;
}

interface ValidationRules<T> {
  [K in keyof T]?: ValidationRule<T[K]>[];
}

export function useFormValidation<T extends Record<string, any>>(
  initialValues: T,
  rules: ValidationRules<T>
) {
  const [values, setValues] = useState<T>(initialValues);
  const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({});

  const validateField = useCallback((field: keyof T, value: T[keyof T]) => {
    const fieldRules = rules[field];
    if (!fieldRules) return '';

    for (const rule of fieldRules) {
      if (!rule.validator(value)) {
        return rule.message;
      }
    }
    return '';
  }, [rules]);

  const setValue = useCallback((field: keyof T, value: T[keyof T]) => {
    setValues(prev => ({ ...prev, [field]: value }));

    const error = validateField(field, value);
    setErrors(prev => ({ ...prev, [field]: error || undefined }));
  }, [validateField]);

  const validateAll = useCallback(() => {
    const newErrors: Partial<Record<keyof T, string>> = {};
    let hasErrors = false;

    for (const field in values) {
      const error = validateField(field, values[field]);
      if (error) {
        newErrors[field] = error;
        hasErrors = true;
      }
    }

    setErrors(newErrors);
    return !hasErrors;
  }, [values, validateField]);

  return { values, errors, setValue, validateAll };
}
```

**Рефакторенный компонент:**

```ts
// LoginForm.tsx (после рефакторинга)
import React from 'react';
import { useFormValidation } from './useFormValidation';

interface LoginFormData {
  email: string;
  password: string;
}

interface LoginFormProps {
  onSubmit: (data: LoginFormData) => Promise<void>;
  loading?: boolean;
}

const emailValidator = (email: string) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
const passwordValidator = (password: string) => password.length >= 6;

const validationRules = {
  email: [{ validator: emailValidator, message: 'Некорректный email' }],
  password: [{ validator: passwordValidator, message: 'Пароль должен содержать минимум 6 символов' }]
};

const LoginForm: React.FC<LoginFormProps> = ({ onSubmit, loading = false }) => {
  const { values, errors, setValue, validateAll } = useFormValidation(
    { email: '', password: '' },
    validationRules
  );

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();

    if (validateAll()) {
      await onSubmit(values);
    }
  };

  return (
    <form onSubmit={handleSubmit} noValidate>
      <div>
        <label htmlFor="email">Email:</label>
        <input
          id="email"
          type="email"
          value={values.email}
          onChange={(e) => setValue('email', e.target.value)}
          disabled={loading}
          aria-invalid={!!errors.email}
          aria-describedby={errors.email ? 'email-error' : undefined}
        />
        {errors.email && (
          <span id="email-error" className="error" role="alert">
            {errors.email}
          </span>
        )}
      </div>

      <div>
        <label htmlFor="password">Password:</label>
        <input
          id="password"
          type="password"
          value={values.password}
          onChange={(e) => setValue('password', e.target.value)}
          disabled={loading}
          aria-invalid={!!errors.password}
          aria-describedby={errors.password ? 'password-error' : undefined}
        />
        {errors.password && (
          <span id="password-error" className="error" role="alert">
            {errors.password}
          </span>
        )}
      </div>

      <button type="submit" disabled={loading}>
        {loading ? 'Загрузка...' : 'Войти'}
      </button>
    </form>
  );
};

export default LoginForm;
```

## 5. Продвинутые техники TDD с AI

### 5.1 Тестирование React хуков

**Промпт для AI:**

```
Создай тесты для кастомного хука useCounter:
- Начальное значение
- Инкремент/декремент
- Сброс
- Минимальное/максимальное значение
```

**AI генерирует тесты:**

```ts
// useCounter.test.ts
import { renderHook, act } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import { useCounter } from './useCounter';

describe('useCounter', () => {
	it('инициализируется с дефолтным значением 0', () => {
		const { result } = renderHook(() => useCounter());
		expect(result.current.count).toBe(0);
	});

	it('инициализируется с переданным значением', () => {
		const { result } = renderHook(() => useCounter(5));
		expect(result.current.count).toBe(5);
	});

	it('увеличивает счетчик', () => {
		const { result } = renderHook(() => useCounter());

		act(() => {
			result.current.increment();
		});

		expect(result.current.count).toBe(1);
	});

	it('уменьшает счетчик', () => {
		const { result } = renderHook(() => useCounter(5));

		act(() => {
			result.current.decrement();
		});

		expect(result.current.count).toBe(4);
	});

	it('сбрасывает счетчик', () => {
		const { result } = renderHook(() => useCounter(5));

		act(() => {
			result.current.increment();
			result.current.reset();
		});

		expect(result.current.count).toBe(5);
	});

	it('не превышает максимальное значение', () => {
		const { result } = renderHook(() => useCounter(0, { max: 3 }));

		act(() => {
			result.current.increment();
			result.current.increment();
			result.current.increment();
			result.current.increment(); // Попытка превысить max
		});

		expect(result.current.count).toBe(3);
	});

	it('не опускается ниже минимального значения', () => {
		const { result } = renderHook(() => useCounter(2, { min: 0 }));

		act(() => {
			result.current.decrement();
			result.current.decrement();
			result.current.decrement(); // Попытка уйти ниже min
		});

		expect(result.current.count).toBe(0);
	});
});
```

**AI создаёт хук:**

```ts
// useCounter.ts
import { useState, useCallback } from 'react';

interface UseCounterOptions {
	min?: number;
	max?: number;
}

interface UseCounterReturn {
	count: number;
	increment: () => void;
	decrement: () => void;
	reset: () => void;
	setValue: (value: number) => void;
}

export function useCounter(initialValue: number = 0, options: UseCounterOptions = {}): UseCounterReturn {
	const { min, max } = options;
	const [count, setCount] = useState(initialValue);

	const increment = useCallback(() => {
		setCount((prev) => {
			const newValue = prev + 1;
			return max !== undefined ? Math.min(newValue, max) : newValue;
		});
	}, [max]);

	const decrement = useCallback(() => {
		setCount((prev) => {
			const newValue = prev - 1;
			return min !== undefined ? Math.max(newValue, min) : newValue;
		});
	}, [min]);

	const reset = useCallback(() => {
		setCount(initialValue);
	}, [initialValue]);

	const setValue = useCallback(
		(value: number) => {
			let newValue = value;
			if (min !== undefined) newValue = Math.max(newValue, min);
			if (max !== undefined) newValue = Math.min(newValue, max);
			setCount(newValue);
		},
		[min, max],
	);

	return { count, increment, decrement, reset, setValue };
}
```

### 5.2 Тестирование с контекстом

**Промпт для AI:**

```
Создай тесты для ThemeProvider и useTheme хука:
- Предоставляет тему по умолчанию
- Переключает между светлой и темной темой
- Сохраняет выбор в localStorage
```

**AI генерирует комплексные тесты:**

```ts
// ThemeProvider.test.tsx
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { render, screen, act } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { ThemeProvider, useTheme } from './ThemeProvider';

// Мок для localStorage
const localStorageMock = {
  getItem: vi.fn(),
  setItem: vi.fn(),
  removeItem: vi.fn(),
};

Object.defineProperty(window, 'localStorage', {
  value: localStorageMock
});

// Тестовый компонент для проверки хука
const TestComponent = () => {
  const { theme, toggleTheme } = useTheme();

  return (
    <div>
      <span data-testid="current-theme">{theme}</span>
      <button onClick={toggleTheme}>Переключить тему</button>
    </div>
  );
};

describe('ThemeProvider', () => {
  beforeEach(() => {
    vi.clearAllMocks();
  });

  it('предоставляет светлую тему по умолчанию', () => {
    localStorageMock.getItem.mockReturnValue(null);

    render(
      <ThemeProvider>
        <TestComponent />
      </ThemeProvider>
    );

    expect(screen.getByTestId('current-theme')).toHaveTextContent('light');
  });

  it('загружает тему из localStorage', () => {
    localStorageMock.getItem.mockReturnValue('dark');

    render(
      <ThemeProvider>
        <TestComponent />
      </ThemeProvider>
    );

    expect(screen.getByTestId('current-theme')).toHaveTextContent('dark');
  });

  it('переключает тему и сохраняет в localStorage', async () => {
    const user = userEvent.setup();
    localStorageMock.getItem.mockReturnValue('light');

    render(
      <ThemeProvider>
        <TestComponent />
      </ThemeProvider>
    );

    await user.click(screen.getByText('Переключить тему'));

    expect(screen.getByTestId('current-theme')).toHaveTextContent('dark');
    expect(localStorageMock.setItem).toHaveBeenCalledWith('theme', 'dark');
  });
});
```

### 5.3 Интеграционные тесты

**Промпт для AI:**

```
Создай интеграционный тест для TodoApp:
- Добавление задачи
- Отметка как выполненной
- Удаление задачи
- Фильтрация по статусу
```

**AI создаёт интеграционный тест:**

```ts
// TodoApp.integration.test.tsx
import { describe, it, expect } from 'vitest';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import TodoApp from './TodoApp';

describe('TodoApp Integration', () => {
  it('полный цикл работы с задачами', async () => {
    const user = userEvent.setup();
    render(<TodoApp />);

    // Добавляем первую задачу
    await user.type(screen.getByPlaceholderText(/добавить задачу/i), 'Изучить TDD');
    await user.click(screen.getByRole('button', { name: /добавить/i }));

    expect(screen.getByText('Изучить TDD')).toBeInTheDocument();

    // Добавляем вторую задачу
    await user.type(screen.getByPlaceholderText(/добавить задачу/i), 'Написать тесты');
    await user.press('Enter');

    expect(screen.getByText('Написать тесты')).toBeInTheDocument();

    // Отмечаем первую задачу как выполненную
    await user.click(screen.getByRole('checkbox', { name: /изучить tdd/i }));

    expect(screen.getByRole('checkbox', { name: /изучить tdd/i })).toBeChecked();

    // Фильтруем только активные задачи
    await user.click(screen.getByRole('button', { name: /активные/i }));

    expect(screen.getByText('Написать тесты')).toBeInTheDocument();
    expect(screen.queryByText('Изучить TDD')).not.toBeInTheDocument();

    // Фильтруем только выполненные задачи
    await user.click(screen.getByRole('button', { name: /выполненные/i }));

    expect(screen.getByText('Изучить TDD')).toBeInTheDocument();
    expect(screen.queryByText('Написать тесты')).not.toBeInTheDocument();

    // Удаляем выполненную задачу
    await user.click(screen.getByRole('button', { name: /удалить.*изучить tdd/i }));

    expect(screen.queryByText('Изучить TDD')).not.toBeInTheDocument();

    // Показываем все задачи
    await user.click(screen.getByRole('button', { name: /все/i }));

    expect(screen.getByText('Написать тесты')).toBeInTheDocument();
    expect(screen.getByText(/задач: 1/i)).toBeInTheDocument();
  });
});
```

## 6. Практические советы и лучшие практики

### 6.1 Эффективные промпты для AI Agent

**🎯 Хорошие промпты:**

```
✅ "Создай тесты для компонента Button с пропсами variant, size, disabled, onClick"
✅ "Тестируй хук useApi: loading состояние, success, error, retry функциональность"
✅ "Интеграционный тест: форма регистрации с валидацией и отправкой на сервер"
```

**❌ Плохие промпты:**

```
❌ "Создай тесты"
❌ "Сделай компонент"
❌ "Добавь валидацию"
```

### 6.2 Структура проекта для TDD

```
src/
├── components/
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.test.tsx
│   │   ├── Button.stories.tsx
│   │   └── index.ts
│   └── LoginForm/
│       ├── LoginForm.tsx
│       ├── LoginForm.test.tsx
│       └── hooks/
│           ├── useFormValidation.ts
│           └── useFormValidation.test.ts
├── hooks/
│   ├── useCounter.ts
│   ├── useCounter.test.ts
│   ├── useApi.ts
│   └── useApi.test.ts
├── utils/
│   ├── validation.ts
│   ├── validation.test.ts
│   ├── formatters.ts
│   └── formatters.test.ts
└── test/
    ├── setup.ts
    ├── mocks/
    └── helpers/
```

### 6.3 Команды для ежедневной работы

```bash
# Запуск тестов в watch режиме
pnpm test --watch

# Запуск тестов с покрытием
pnpm test --coverage

# Запуск конкретного теста
pnpm test LoginForm

# Запуск в UI режиме
pnpm test --ui
```

### 6.4 Интеграция с CI/CD

```yaml
# .github/workflows/test.yml
name: Tests
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: pnpm install
      - run: pnpm test --coverage
      - uses: codecov/codecov-action@v3
        with:
          file: ./coverage/lcov.info
```

### 6.5 Отладка тестов с AI Agent

**Промпт для отладки:**

```
Тест падает с ошибкой: "Cannot read property 'textContent' of null"
Код теста: [вставить код]
Код компонента: [вставить код]
Помоги найти и исправить проблему
```

**AI анализирует и предлагает решение:**

- Проверяет селекторы в тестах
- Анализирует асинхронные операции
- Предлагает дополнительные waitFor
- Исправляет проблемы с рендерингом

## 7. Типичные проблемы и решения

### 7.1 Асинхронные операции

**Проблема:** Тест падает из-за асинхронных обновлений
**Решение AI предлагает:**

```ts
// ❌ Плохо
it('показывает данные после загрузки', () => {
  render(<UserProfile userId="123" />);
  expect(screen.getByText('John Doe')).toBeInTheDocument();
});

// ✅ Хорошо
it('показывает данные после загрузки', async () => {
  render(<UserProfile userId="123" />);
  expect(await screen.findByText('John Doe')).toBeInTheDocument();
});
```

### 7.2 Мокинг модулей

**Проблема:** Тесты зависят от внешних API
**AI создаёт моки:**

```ts
// __mocks__/api.ts
export const fetchUser = vi.fn().mockResolvedValue({
	id: '123',
	name: 'John Doe',
	email: 'john@example.com',
});

// В тесте
import { fetchUser } from '../api';
vi.mock('../api');

const mockFetchUser = vi.mocked(fetchUser);
```

### 7.3 Тестирование ошибок

**AI генерирует тесты для error boundaries:**

```ts
it('показывает fallback UI при ошибке', () => {
  const ThrowError = () => {
    throw new Error('Test error');
  };

  render(
    <ErrorBoundary>
      <ThrowError />
    </ErrorBoundary>
  );

  expect(screen.getByText(/что-то пошло не так/i)).toBeInTheDocument();
});
```

## 8. Измерение эффективности TDD

### 8.1 Метрики качества

- **Покрытие кода:** > 80%
- **Время выполнения тестов:** < 10 секунд
- **Количество багов в production:** снижение на 40-60%
- **Время рефакторинга:** сокращение на 30-50%

### 8.2 Автоматизация с AI

```ts
// AI может генерировать отчёты о качестве тестов
const testQualityReport = {
	coverage: '85%',
	testSpeed: '8.2s',
	flakyTests: ['LoginForm - timeout'],
	suggestions: [
		'Добавить тесты для error boundaries',
		'Увеличить покрытие utils функций',
		'Оптимизировать медленные тесты',
	],
};
```

## 9. Заключение

TDD с AI Agent революционизирует процесс разработки React + TypeScript приложений:

**Преимущества:**

- 🚀 **Скорость:** Мгновенная генерация тестов и кода
- 🛡️ **Качество:** Высокое покрытие и меньше багов
- 🧠 **Обучение:** AI предлагает лучшие практики
- 🔄 **Рефакторинг:** Безопасные изменения с гарантией

**Рекомендации:**

1. Начинайте с простых компонентов
2. Используйте чёткие промпты для AI
3. Проверяйте сгенерированный код
4. Интегрируйте в CI/CD процесс
5. Измеряйте результаты и улучшайте процесс

TDD + AI Agent = мощная комбинация для создания надёжных приложений! 🎉
