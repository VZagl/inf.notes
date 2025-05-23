# Проблемы и решения при разработке на React Native Expo

## 1. Ограничения Expo Managed Workflow

**Проблема:**  
Expo Managed Workflow не поддерживает нативные модули, которые не входят в стандартный набор Expo.

**Решение:**

Используйте [Expo EAS](https://docs.expo.dev/eas/) для сборки с кастомными нативными модулями

**Примеры неподдерживаемых модулей:**

- Bluetooth-модули (`react-native-ble-plx`)
- Кастомные нативные платежные системы
- Специфичные модули для работы с hardware (`react-native-serial-port`)
- WebRTC модули с нативными зависимостями
- Кастомные push-notification providers

**Поддерживаемые модули в Managed Workflow:**

- Камера (`expo-camera`)
- Геолокация (`expo-location`)
- Уведомления (`expo-notifications`)
- Хранение данных (`expo-secure-store`, `expo-file-system`)
- Сенсоры (`expo-sensors`)
- Аутентификация (`expo-auth-session`)
- Базовые платежи (`expo-in-app-purchases`)

---

## 2. Проблемы с зависимостями и версиями

**Проблема:**  
Некоторые библиотеки могут не поддерживаться текущей версией Expo.

**Решение:**

Проверяйте совместимость библиотек с вашей версией Expo - Используйте `expo install` вместо `npm install` для автоматического выбора совместимых версий

---

## 3. Долгая сборка и обновление приложения

**Проблема:**  
Сборка через Expo Go или EAS может занимать много времени.

**Решение:**

- Используйте локальную сборку (`eas build --local`), если это возможно.
- Оптимизируйте проект, удаляя неиспользуемые зависимости.

---

## 4. Ограничения Expo Go

**Проблема:**  
Expo Go не поддерживает сторонние нативные модули.

**Решение:**

- Для тестирования используйте EAS Build или Bare Workflow.
- Для простых приложений используйте только поддерживаемые Expo API.

---

## 5. Проблемы с push-уведомлениями

**Проблема:**  
Настройка push-уведомлений может быть сложной, особенно для iOS.

**Решение:**

- Следуйте [официальной документации Expo по push-уведомлениям](https://docs.expo.dev/push-notifications/overview/).
- Проверьте настройки сертификатов и разрешений.

---

## 6. Ошибки при публикации и обновлении

**Проблема:**  
Ошибки при публикации через `expo publish` или при обновлении Expo SDK.

**Решение:**

- Перед обновлением делайте резервную копию проекта.
- Читайте [release notes](https://blog.expo.dev/) и следуйте инструкциям по миграции.

---

## 7. Проблемы с производительностью

**Проблема:**  
Expo-приложения могут работать медленнее по сравнению с нативными.

**Решение:**

- Используйте оптимизации: FlatList, мемоизация компонентов, уменьшение количества ререндеров.
- Для критичных задач переходите на Bare Workflow.

---

## 8. Проблемы с отладкой

**Проблема:**  
Иногда сложно отлаживать ошибки, связанные с нативным кодом.

**Решение:**

- Используйте [Expo DevTools](https://docs.expo.dev/workflow/expo-cli/#expo-dev-tools).
- Для Bare Workflow используйте стандартные инструменты React Native.

---

## 9. Необходимость очистки кэша

**Проблема:**  
Иногда после обновления зависимостей, Expo SDK или при возникновении неожиданных ошибок приложение продолжает вести себя некорректно из-за устаревшего кэша.

**Решение:**

1. **Сначала попробуйте базовые методы очистки кэша:**

   ```bash
   npx expo start -c
   npm start -- --reset-cache
   ```

2. **Если проблема сохраняется, очистите кэш npm:**

   ```bash
   npm cache clean --force
   ```

3. **Только если предыдущие шаги не помогли:**

   - Удалите папку `node_modules`
   - Удалите файлы блокировки (`package-lock.json` или `yarn.lock`)
   - Выполните `npm install` или `yarn install`

   > [!warning]
   > Полное удаление зависимостей следует использовать как последнее средство, когда другие методы не помогли.

---

## Полезные ссылки

- [Документация Expo](https://docs.expo.dev/)
- [Expo Community Forums](https://forums.expo.dev/)
- [React Native Troubleshooting](https://reactnative.dev/docs/troubleshooting)
