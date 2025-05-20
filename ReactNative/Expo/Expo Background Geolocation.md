## Вопрос

Как на Expo создать программу геотрекинга с клиентской частью, отображающей накопленные данные, и фоновой службой, которая активируется по таймеру, получает GPS-координаты, записывает их в файл и засыпает до следующего срабатывания?

## Ответ

На Expo можно реализовать подобную программу, но с некоторыми ограничениями.

### Кратко о возможностях Expo

- **Фоновое получение геолокации** - Expo поддерживает фоновое отслеживание местоположения через [TaskManager](https://docs.expo.dev/versions/latest/sdk/task-manager/) и [Location](https://docs.expo.dev/versions/latest/sdk/location/).
- **Запись в файл** - Для записи данных можно использовать [expo-file-system](https://docs.expo.dev/versions/latest/sdk/filesystem/).
- **Ограничения** - Expo Go не поддерживает фоновое выполнение задач, требуется сборка [standalone-приложения](https://docs.expo.dev/classic/building-standalone-apps/).

---

### Пример реализации

1. **Установка зависимостей**

   > Выполнить в терминале

   ```sh
   expo install expo-location expo-task-manager expo-file-system
   expo install expo-sensors
   ```

2. **Регистрация фоновой задачи**

   > Файл: lib/location/task.ts

   ```js
   import * as TaskManager from 'expo-task-manager';
   import * as Location from 'expo-location';
   import { saveLocationToStorage } from '../storage';
   import type { LocationData } from '../../types';

   export const LOCATION_TASK_NAME = 'background-location-task';

   TaskManager.defineTask(LOCATION_TASK_NAME, async ({ data, error }) => {
     if (error) {
       console.error('Ошибка получения локации:', error);
       return;
     }

     if (data) {
       const { locations } = data as { locations: LocationData[] };
       try {
         await saveLocationToStorage(locations);
       } catch (err) {
         console.error('Ошибка сохранения локации:', err);
       }
     }
   });
   ```

3. **Отображение накопленных данных**

   > Файл: lib/storage/locations.ts

   ```js
   import * as FileSystem from 'expo-file-system';
   import type { LocationData } from '../../types';

   const LOCATIONS_FILE = `${FileSystem.documentDirectory}locations.json`;

   export async function saveLocationToStorage(locations: LocationData[]): Promise<void> {
     try {
       const fileContent = await FileSystem.readAsStringAsync(LOCATIONS_FILE).catch(() => '[]');
       const existingLocations = JSON.parse(fileContent);
       const updatedLocations = [...existingLocations, ...locations];

       await FileSystem.writeAsStringAsync(
         LOCATIONS_FILE,
         JSON.stringify(updatedLocations),
         { encoding: FileSystem.EncodingType.UTF8 }
       );
     } catch (error) {
       console.error('Ошибка сохранения в файл:', error);
       throw error;
     }
   }

   export async function readLocations() {
     // ...existing code...
   }
   ```

4. **Экран с отображением данных**

   > Файл: app/(tabs)/tracking.tsx

   ```js
   import { View } from 'react-native';
   import { useEffect, useState } from 'react';
   import { readLocations } from '../../lib/storage';
   import { MotionDetector } from '../../lib/motion/detector';

   export default function TrackingScreen() {
   	const [locations, setLocations] = useState([]);

   	useEffect(() => {
   		const detector = new MotionDetector();
   		detector.start();

   		const loadLocations = async () => {
   			const data = await readLocations();
   			setLocations(data);
   		};

   		loadLocations();

   		return () => {
   			detector.stop();
   		};
   	}, []);

   	return <View>{/* Карта и UI элементы */}</View>;
   }
   ```

5. **Детектор движения**

   > Файл: lib/motion/detector.ts

   ```js
   import { Accelerometer } from 'expo-sensors';
   import { startLocationUpdates, stopLocationUpdates } from '../location';

   const MOTION_THRESHOLD = 0.1;
   const STABLE_TIMEOUT = 30000;

   export class MotionDetector {
     private lastUpdate = 0;
     private lastMovement = 0;
     private isTracking = false;
     private subscription: any = null;

     async start() {
       Accelerometer.setUpdateInterval(50);

       this.subscription = Accelerometer.addListener(data => {
         const { x, y, z } = data;
         const now = Date.now();

         if (now - this.lastUpdate < 50) return;
         this.lastUpdate = now;

         const movement = Math.sqrt(x * x + y * y + z * z);
         if (movement > MOTION_THRESHOLD) {
           this.lastMovement = now;

           if (!this.isTracking) {
             this.isTracking = true;
             startLocationUpdates();
           }
         } else if (this.isTracking && (now - this.lastMovement > STABLE_TIMEOUT)) {
           this.isTracking = false;
           stopLocationUpdates();
         }
       });
     }

     stop() {
       if (this.subscription) {
         this.subscription.remove();
       }
       if (this.isTracking) {
         stopLocationUpdates();
       }
     }
   }
   ```

### Необходимые разрешения и конфигурация

1. **Запрос разрешений**

   > Файл: lib/location/permissions.ts

   ```js
   import * as Location from 'expo-location';

   export async function requestPermissions() {
   	const { status: foregroundStatus } =
   		await Location.requestForegroundPermissionsAsync();
   	const { status: backgroundStatus } =
   		await Location.requestBackgroundPermissionsAsync();

   	return foregroundStatus === 'granted' && backgroundStatus === 'granted';
   }
   ```

2. **Конфигурация app.json**
   > Файл: app.json
   ```json
   {
   	"expo": {
   		"plugins": [
   			[
   				"expo-location",
   				{
   					"locationAlwaysAndWhenInUsePermission": "Allow $(PRODUCT_NAME) to use your location."
   				}
   			],
   			[
   				"expo-sensors",
   				{
   					"motionPermission": "Allow $(PRODUCT_NAME) to access device motion"
   				}
   			]
   		],
   		"android": {
   			"permissions": [
   				"ACCESS_BACKGROUND_LOCATION",
   				"ACCESS_COARSE_LOCATION",
   				"ACCESS_FINE_LOCATION"
   			]
   		}
   	}
   }
   ```

### Обработка ошибок и управление сервисом

1. **Запуск сервиса**

   > Файл: lib/location/index.ts

   ```js
   import * as Location from 'expo-location';
   import { requestPermissions } from './permissions';
   import { LOCATION_TASK_NAME } from './task';

   async function startLocationUpdates() {
   	try {
   		const hasPermissions = await requestPermissions();
   		if (!hasPermissions) {
   			throw new Error('Необходимы разрешения на использование геолокации');
   		}

   		await Location.startLocationUpdatesAsync(LOCATION_TASK_NAME, {
   			accuracy: Location.Accuracy.High,
   			timeInterval: 60000,
   			distanceInterval: 0,
   			showsBackgroundLocationIndicator: true,
   			foregroundService: {
   				notificationTitle: 'Geo Tracking',
   				notificationBody: 'Служба геолокации активна',
   			},
   		});
   	} catch (error) {
   		console.error('Ошибка запуска геолокации:', error);
   	}
   }

   async function stopLocationUpdates() {
   	try {
   		await Location.stopLocationUpdatesAsync(LOCATION_TASK_NAME);
   	} catch (error) {
   		console.error('Ошибка остановки геолокации:', error);
   	}
   }

   export { startLocationUpdates, stopLocationUpdates };
   ```

### Важные замечания

1. **Платформенные особенности**

   - Android: требуется foregroundService для фоновой работы
   - iOS: строгие ограничения на фоновые задачи
   - На всех платформах частое получение координат влияет на батарею

2. **Оптимальные настройки**

   - Для трекинга пешехода: `timeInterval: 10000` (10 секунд)
   - Для автомобиля: `timeInterval: 5000` (5 секунд)
   - Для фонового мониторинга: `timeInterval: 180000` (3 минуты)

3. **Возможные проблемы**

   - Система может отключить геолокацию для экономии батареи
   - Переполнение хранилища данными
   - Задержки получения точных координат

4. **Рекомендации**

   - Периодически очищать старые записи
   - Использовать сжатие данных при долгосрочном хранении
   - Реализовать механизм синхронизации с сервером
   - Добавить систему кэширования для быстрого доступа к последним координатам

5. **После добавления оптимизации с акселерометром**

Теперь приложение будет:

- Отслеживать движение устройства через акселерометр
- Включать GPS только при обнаружении движения
- Отключать GPS после 30 секунд покоя
- Экономить заряд батареи при неподвижном состоянии

### Структура проекта

```text
my-app/
├── app/                     # Маршрутизация и UI компоненты
│   ├── _layout.tsx         # Корневой layout
│   ├── index.tsx           # Главный экран
│   └── (tabs)/
│       └── tracking.tsx    # Экран с картой и данными
├── lib/                    # Бизнес-логика приложения
│   ├── location/
│   │   ├── index.ts       # Основной экспорт
│   │   ├── task.ts        # Фоновая задача
│   │   └── permissions.ts  # Управление разрешениями
│   ├── motion/
│   │   └── detector.ts    # Детектор движения
│   └── storage/
│       ├── index.ts       # Основной экспорт
│       └── locations.ts    # Работа с хранилищем
├── types/                  # Типы TypeScript
│   └── index.ts           # Общие типы
└── app.json               # Конфигурация приложения
```

### Итог

- Expo позволяет реализовать фоновый геотрекинг с записью координат в файл и последующим отображением.
- **Требуется сборка standalone-приложения** - В Expo Go не работает.
- **Ограничения iOS** - На iOS фоновые задачи могут быть ограничены системой.

**Документация**

- [expo-location](https://docs.expo.dev/versions/latest/sdk/location/) - работа с геолокацией
- [expo-task-manager](https://docs.expo.dev/versions/latest/sdk/task-manager/) - фоновые задачи
- [expo-file-system](https://docs.expo.dev/versions/latest/sdk/filesystem/) - работа с файлами
- [expo-sensors](https://docs.expo.dev/versions/latest/sdk/sensors/) - работа с сенсорами
