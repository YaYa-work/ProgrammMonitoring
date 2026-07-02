# Руководство по созданию приложения для мониторинга сервера

## Введение

Данное руководство содержит полную информацию о создании приложения для мониторинга компьютера под управлением Windows 11. Приложение разработано с использованием Electron и Node.js, позволяет отслеживать системные ресурсы, управлять компьютером и контролировать подключенные устройства.

## Содержание

1. Подготовка окружения
2. Структура проекта
3. Код приложения
4. Сборка и запуск
5. Функциональность
6. Уровни безопасности

## Подготовка окружения

### Установка Node.js

Node.js необходим для работы с npm и выполнения JavaScript вне браузера.

Для установки необходимо:
1. Перейти на официальный сайт https://nodejs.org
2. Скачать LTS версию для Windows
3. Запустить установщик и следовать инструкциям
4. После установки проверить выполнив в командной строке:

```bash
node --version
npm --version
```

# Создание проекта

Создание папки для проекта и инициализация npm:

```bash
mkdir ServerMonitor
cd ServerMonitor
npm init -y
```

# Руководство по созданию приложения для мониторинга сервера

## Содержание

1. Установка зависимостей
2. Структура проекта
3. Код приложения
4. Сборка и запуск
5. Функциональность
6. Уровни безопасности

## Установка зависимостей

Установка необходимых пакетов:

```bash
npm install electron systeminformation
```

# Описание зависимостей

- **electron**  
  Фреймворк для создания десктопных приложений на базе веб-технологий (HTML, CSS, JavaScript).

- **systeminformation**  
  Библиотека для получения системной информации о компьютере и его компонентах.

# Структура проекта ServerMonitor

| Файл/Папка         | Описание                                   |
|---------------------|--------------------------------------------|
| **main.js**        | Основной файл приложения                   |
| **index.html**     | Интерфейс пользователя                     |
| **package.json**   | Конфигурационный файл проекта              |
| **node_modules/**  | Каталог с установленными пакетами          |

# Код приложения

## Файл `package.json`

Этот файл содержит конфигурацию проекта и список зависимостей.

```json
{
  "name": "monitor",
  "version": "1.0.0",
  "main": "main.js",
  "scripts": {
    "start": "electron ."
  },
  "dependencies": {
    "electron": "^43.0.0",
    "systeminformation": "^5.21.0"
  }
}

# Описание полей `package.json`

- **name**: Название проекта  
- **version**: Версия приложения  
- **main**: Точка входа в приложение (основной файл)  
- **scripts**: Команды для запуска и управления проектом  
- **dependencies**: Используемые пакеты и библиотеки

# Описание файла `main.js`

Основной файл, который управляет приложением:  
- Собирает системные данные  
- Обрабатывает команды и события приложения  
- Инициализирует окно приложения и взаимодействие с системной информацией

```javascript
// Подключение необходимых модулей
const { app, BrowserWindow, ipcMain, globalShortcut } = require('electron');
const si = require('systeminformation');
const { exec } = require('child_process');
const os = require('os');
```

## Объяснение импортов

- **app** — управляет жизненным циклом приложения  
- **BrowserWindow** — создает и управляет окнами приложения  
- **ipcMain** — обеспечивает обмен данными между главным процессом и рендерером  
- **globalShortcut** — регистрирует глобальные горячие клавиши для вызова функций вне фокуса окна

- **si** — библиотека для получения системной информации  
- **exec** — выполняет системные команды через командную строку или shell  
- **os** — предоставляет сведения об операционной системе

---

## Пример кода на JavaScript

```javascript
let mainWindow;
let isWindowVisible = false;
```

## Объяснение переменных

- **mainWindow** — переменная, которая содержит ссылку на главное окно приложения. Используется для управления окном, его открытия, закрытия и обновления.

- **isWindowVisible** — логическая переменная (флаг), показывающая, отображается ли сейчас окно. Обычно используется для контроля состояния видимости окна.

```
function createWindow() {
    mainWindow = new BrowserWindow({
        width: 1200,
        height: 800,
        webPreferences: {
            nodeIntegration: true,
            contextIsolation: false
        },
        autoHideMenuBar: true,
        show: false,
        backgroundColor: '#0a0e17'
    });

    mainWindow.loadFile('index.html');
    mainWindow.setTitle('Мониторинг сервера');
    
    mainWindow.on('close', (event) => {
        if (!app.isQuiting) {
            event.preventDefault();
            mainWindow.hide();
            isWindowVisible = false;
        }
    });
}
```

# Объяснение функции createWindow

## Обзор
Функция `createWindow` создает главное окно приложения Electron для системы мониторинга сервера.

## Параметры окна

### Размеры
- **Ширина:** 1200 пикселей
- **Высота:** 800 пикселей

### WebPreferences (Настройки веб-движка)
- **nodeIntegration: true** - разрешает использование Node.js API непосредственно в интерфейсе приложения. Позволяет работать с файловой системой, процессами и другими системными функциями.
- **contextIsolation: false** - отключает изоляцию контекста. Это означает, что скрипты в интерфейсе имеют прямой доступ к Node.js, что упрощает разработку, но может быть менее безопасно.

### Настройки интерфейса
- **autoHideMenuBar: true** - автоматически скрывает панель меню, создавая более чистый и минималистичный интерфейс.
- **show: false** - окно не отображается до момента полной загрузки всех ресурсов, что предотвращает мерцание пустого окна.
- **backgroundColor: '#0a0e17'** - устанавливает темно-синий фон окна (#0a0e17), что создает атмосферу "космической" тематики для мониторинга.

## Методы

### Загрузка контента
```javascript
mainWindow.loadFile('index.html');

```javascript
function toggleWindow() {
    if (mainWindow) {
        if (mainWindow.isVisible()) {
            mainWindow.hide();
            isWindowVisible = false;
        } else {
            mainWindow.show();
            mainWindow.focus();
            isWindowVisible = true;
        }
    }
}
```

## Объяснение функции toggleWindow:

**Назначение:** Переключает видимость окна (показывает или скрывает)

**Логика работы:**

1. **Проверка существования окна**
   - `if (mainWindow)` — убеждается, что объект окна существует

2. **Проверка видимости**
   - `if (mainWindow.isVisible())` — определяет текущее состояние окна

3. **Если окно видимо** — скрывает его:
   - `mainWindow.hide()` — скрывает окно
   - `isWindowVisible = false` — обновляет флаг состояния

4. **Если окно скрыто** — показывает и активирует его:
   - `mainWindow.show()` — показывает окно
   - `mainWindow.focus()` — активирует окно (ставит на передний план)
   - `isWindowVisible = true` — обновляет флаг состояния

```javascript
function registerShortcuts() {
    const shortcut = 'Ctrl+Shift+Z';
    const ret = globalShortcut.register(shortcut, () => {
        toggleWindow();
    });

    if (ret) {
        console.log('Глобальная клавиша зарегистрирована');
    }

    const closeShortcut = 'Ctrl+Shift+X';
    globalShortcut.register(closeShortcut, () => {
        app.quit();
    });
}
```

## Объяснение функции registerShortcuts:

### Назначение
Регистрирует глобальные горячие клавиши для управления приложением.

### Горячие клавиши

| Комбинация | Действие | Описание |
|------------|----------|----------|
| **Ctrl+Shift+Z** | `toggleWindow()` | Показывает/скрывает главное окно |
| **Ctrl+Shift+X** | `app.quit()` | Завершает работу приложения |

### Логика работы

#### 1. Регистрация первой комбинации (Ctrl+Shift+Z)
```javascript
const shortcut = 'Ctrl+Shift+Z';
const ret = globalShortcut.register(shortcut, () => {
    toggleWindow();
});
```
- Сохраняет комбинацию в переменную `shortcut`
- Регистрирует глобальный хоткей
- При нажатии вызывается функция `toggleWindow()`
- `ret` возвращает `true` при успешной регистрации

#### 2. Проверка успешности регистрации
```javascript
if (ret) {
    console.log('Глобальная клавиша зарегистрирована');
}
```
- Выводит сообщение в консоль для отладки
- Помогает убедиться, что хоткей активирован

#### 3. Регистрация второй комбинации (Ctrl+Shift+X)
```javascript
const closeShortcut = 'Ctrl+Shift+X';
globalShortcut.register(closeShortcut, () => {
    app.quit();
});
```
- Регистрирует комбинацию для закрытия приложения
- При нажатии вызывает `app.quit()` — полное завершение

### Особенности
-  **Глобальные** — работают даже когда приложение в фоне
-  **Быстрый доступ** — управление без мыши
-  **Безопасное завершение** — Ctrl+Shift+X для экстренного выхода

### Важно
Не забудьте очищать регистрацию при завершении:
```javascript
app.on('will-quit', () => {
    globalShortcut.unregisterAll();
});
```

```javascript
async function getSystemData() {
    try {
        const [cpu, mem, disk, osInfo, time, processes, network] = await Promise.all([
            si.cpu(),
            si.mem(),
            si.diskLayout(),
            si.osInfo(),
            si.time(),
            si.processes(),
            si.networkStats()
        ]);

        return {
            cpu: {
                brand: cpu.brand || 'Unknown',
                cores: cpu.cores || 0
            },
            memory: {
                total: (mem.total / 1024 / 1024 / 1024).toFixed(1),
                used: (mem.used / 1024 / 1024 / 1024).toFixed(1),
                usage: ((mem.used / mem.total) * 100).toFixed(1)
            },
            disk: disk.slice(0, 2).map(d => ({
                device: d.device || 'Disk',
                size: (d.size / 1024 / 1024 / 1024).toFixed(1)
            })),
            os: {
                hostname: osInfo.hostname || 'Unknown',
                distro: osInfo.distro || 'Windows'
            },
            uptime: {
                uptime: time.uptime || 0
            },
            processes: processes.list ? processes.list.slice(0, 5).map(p => ({
                name: p.name || 'Unknown',
                cpu: p.cpu || 0,
                mem: p.mem || 0
            })) : []
        };
    } catch (error) {
        console.error('Ошибка сбора данных:', error);
        return null;
    }
}
```

## Объяснение функции getSystemData:

### Назначение
Асинхронно собирает системную информацию для отображения в интерфейсе мониторинга.

### Архитектура

#### 1. Параллельный сбор данных
```javascript
const [cpu, mem, disk, osInfo, time, processes, network] = await Promise.all([
    si.cpu(),          // Информация о процессоре
    si.mem(),           // Информация о памяти
    si.diskLayout(),    // Информация о дисках
    si.osInfo(),        // Информация об ОС
    si.time(),          // Время работы системы
    si.processes(),     // Список процессов
    si.networkStats()   // Сетевая статистика
]);
```
- Использует `Promise.all` для параллельного выполнения запросов
- Ускоряет сбор данных за счет одновременных вызовов
- Ожидает завершения всех промисов

### Форматирование данных

| Категория | Поля | Форматирование |
|-----------|------|----------------|
| **CPU** | `brand`, `cores` | Значения по умолчанию если отсутствуют |
| **Память** | `total`, `used`, `usage` | Преобразование байт → ГБ (с 1 знаком после запятой) |
| **Диски** | `device`, `size` | Первые 2 диска, размер в ГБ |
| **ОС** | `hostname`, `distro` | Значения по умолчанию |
| **Время работы** | `uptime` | В секундах |
| **Процессы** | `name`, `cpu`, `mem` | Топ-5 процессов по использованию ресурсов |

### Преобразование данных

#### Память (байты → ГБ)
```javascript
total: (mem.total / 1024 / 1024 / 1024).toFixed(1)
```
- Делит на `1024³` для перевода в ГБ
- Округляет до 1 знака после запятой

#### Проценты использования
```javascript
usage: ((mem.used / mem.total) * 100).toFixed(1)
```
- Вычисляет процент использования памяти
- Округляет до 1 знака

#### Топ-5 процессов
```javascript
processes.list.slice(0, 5).map(p => ({
    name: p.name || 'Unknown',
    cpu: p.cpu || 0,
    mem: p.mem || 0
}))
```
- Берет только первые 5 процессов
- Добавляет значения по умолчанию для безопасности

### Обработка ошибок
```javascript
catch (error) {
    console.error('Ошибка сбора данных:', error);
    return null;
}
```
- Перехватывает любые ошибки
- Выводит в консоль
- Возвращает `null` для проверки в вызывающем коде

### Возвращаемый объект
```javascript
{
    cpu: { brand, cores },
    memory: { total, used, usage },
    disk: [ { device, size } ],
    os: { hostname, distro },
    uptime: { uptime },
    processes: [ { name, cpu, mem } ]
}
```

### Использование
```javascript
// Периодический сбор данных
setInterval(async () => {
    const data = await getSystemData();
    if (data) {
        updateUI(data);
    }
}, 5000);
```

```javascript
// Периодический сбор данных каждые 3 секунды
setInterval(async () => {
    if (mainWindow && mainWindow.isVisible()) {
        const data = await getSystemData();
        if (data) {
            mainWindow.webContents.send('system-data', data);
        }
    }
}, 3000);
```

## Объяснение интервала:

### Назначение
Автоматически собирает системные данные и отправляет их в интерфейс для обновления мониторинга.

### Логика работы

| Шаг | Действие | Описание |
|-----|----------|----------|
| 1 | `setInterval(..., 3000)` | Запускает интервал каждые 3 секунды |
| 2 | `if (mainWindow && mainWindow.isVisible())` | Проверяет существование и видимость окна |
| 3 | `await getSystemData()` | Асинхронно собирает системные данные |
| 4 | `if (data)` | Проверяет, что данные получены успешно |
| 5 | `mainWindow.webContents.send('system-data', data)` | Отправляет данные в интерфейс через IPC |

### Оптимизация
- Сбор данных происходит **только когда окно видимо**
- Экономит ресурсы системы, не выполняя запросы в фоне
- Данные отправляются через IPC канал `'system-data'`

---

```javascript
// Обработчик запроса данных из интерфейса
ipcMain.handle('get-system-data', async () => {
    return await getSystemData();
});
```

## Объяснение обработчика:

### Назначение
Обрабатывает запросы из интерфейса на получение системных данных.

### Особенности
- Использует `ipcMain.handle()` для обработки асинхронных запросов
- Вызывает `getSystemData()` для сбора информации
- Возвращает актуальные данные в интерфейс

### Использование в интерфейсе
```javascript
// В renderer процессе
const data = await ipcRenderer.invoke('get-system-data');
```

---

```javascript
// Обработчик выключения компьютера
ipcMain.handle('shutdown-computer', () => {
    if (os.platform() === 'win32') {
        exec('shutdown /s /t 10 /c "Выключение через 10 секунд"');
    }
    return { success: true };
});
```

## Объяснение обработчика shutdown-computer:

### Назначение
Выключает компьютер с задержкой 10 секунд.

### Параметры команды

| Параметр | Значение | Описание |
|----------|----------|----------|
| `/s` | Выключение | Команда завершения работы |
| `/t 10` | Задержка 10 сек | Время до выключения |
| `/c` | Сообщение | Показывает текст пользователю |

### Особенности
-  Проверяет ОС (`os.platform() === 'win32'`)
-  Только для Windows
-  Отображает сообщение пользователю
-  Дает время сохранить работу

---

```javascript
// Обработчик перезагрузки компьютера
ipcMain.handle('restart-computer', () => {
    if (os.platform() === 'win32') {
        exec('shutdown /r /t 10 /c "Перезагрузка через 10 секунд"');
    }
    return { success: true };
});
```

## Объяснение обработчика restart-computer:

### Назначение
Перезагружает компьютер с задержкой 10 секунд.

### Параметры команды

| Параметр | Значение | Описание |
|----------|----------|----------|
| `/r` | Перезагрузка | Команда перезапуска системы |
| `/t 10` | Задержка 10 сек | Время до перезагрузки |
| `/c` | Сообщение | Показывает текст пользователю |

### Отличия от выключения
- `/r` вместо `/s` — перезагрузка вместо выключения
- Остальные параметры идентичны

---

```javascript
// Обработчик перед завершением приложения
app.on('before-quit', () => {
    app.isQuiting = true;
});
```

## Объяснение обработчика before-quit:

### Назначение
Устанавливает флаг `isQuiting` перед завершением приложения.

### Использование
```javascript
// В функции createWindow()
mainWindow.on('close', (event) => {
    if (!app.isQuiting) {
        event.preventDefault();
        mainWindow.hide();
        isWindowVisible = false;
    }
});
```

### Логика работы
1. При завершении приложения вызывается `before-quit`
2. Устанавливается `app.isQuiting = true`
3. В обработчике `close` окна проверяется этот флаг
4. Если `isQuiting === true` — окно закрывается полностью
5. Если `isQuiting === false` — окно только скрывается

### Важность
- Позволяет корректно различать:
  - **Закрытие окна** → скрыть в трей
  - **Завершение приложения** → закрыть полностью

## Полный цикл работы

1. **Интервал** собирает данные каждые 3 секунды
2. При запросе из интерфейса используется **обработчик** `get-system-data`
3. Управление системой через **shutdown-computer** и **restart-computer**
4. **before-quit** обеспечивает правильное завершение приложения
```

```javascript
// Обработчик перед выходом из приложения
app.on('before-quit', () => {
    app.isQuiting = true;
});
```

## Объяснение обработчика before-quit:

### Назначение
Устанавливает флаг перед выходом из приложения, позволяя корректно завершить работу.

### Логика работы
- Срабатывает **до** начала завершения приложения
- Устанавливает `app.isQuiting = true`
- Используется в обработчике `close` окна для определения:
  - Если `isQuiting === true` → окно **закрывается полностью**
  - Если `isQuiting === false` → окно **скрывается в трей**

### Пример использования
```javascript
mainWindow.on('close', (event) => {
    if (!app.isQuiting) {
        event.preventDefault();
        mainWindow.hide();
        isWindowVisible = false;
    }
});
```

---

```javascript
// Очистка горячих клавиш при завершении
app.on('will-quit', () => {
    globalShortcut.unregisterAll();
});
```

## Объяснение обработчика will-quit:

### Назначение
Очищает все зарегистрированные горячие клавиши перед завершением приложения.

### Почему это важно?
- Предотвращает **конфликты** с другими приложениями
- Освобождает **системные ресурсы**
- Избегает **ошибок** при повторном запуске
- Соблюдает **правила** операционной системы

### Альтернативный вариант
```javascript
// Очистка конкретных клавиш
app.on('will-quit', () => {
    globalShortcut.unregister('Ctrl+Shift+Z');
    globalShortcut.unregister('Ctrl+Shift+X');
});
```

---

```javascript
// Запуск приложения
app.whenReady().then(() => {
    createWindow();
    registerShortcuts();
    
    setTimeout(() => {
        mainWindow.show();
        isWindowVisible = true;
    }, 500);
});
```

## Объяснение запуска приложения:

### Последовательность действий

| Шаг | Действие | Задержка | Описание |
|-----|----------|----------|----------|
| 1 | `createWindow()` | 0 мс | Создает главное окно (скрытое) |
| 2 | `registerShortcuts()` | 0 мс | Регистрирует горячие клавиши |
| 3 | `setTimeout()` | 500 мс | Задержка для подготовки интерфейса |
| 4 | `mainWindow.show()` | 500 мс | Показывает окно |
| 5 | `isWindowVisible = true` | 500 мс | Обновляет флаг видимости |

### Почему задержка 500 мс?
-  Дает время на **загрузку ресурсов**
-  Позволяет **проинициализировать** интерфейс
-  Предотвращает **мерцание** пустого окна
-  Обеспечивает **плавный** старт приложения

---

```javascript
// Обработчик закрытия всех окон
app.on('window-all-closed', () => {
    if (process.platform !== 'darwin') {
        app.quit();
    }
});
```

## Объяснение обработчика window-all-closed:

### Назначение
Закрывает приложение при закрытии всех окон (кроме macOS).

### Поведение на разных платформах

| Платформа | Действие | Причина |
|-----------|----------|---------|
| **Windows** | `app.quit()` | Завершает приложение полностью |
| **Linux** | `app.quit()` | Завершает приложение полностью |
| **macOS** | **Ничего** | Приложения обычно остаются активными |

### Особенности macOS
- На macOS приложения **продолжают работать** даже без окон
- Переключение через `Cmd+Tab` или Dock
- Соответствует **стандартам** Apple

### Альтернативное поведение
```javascript
// Для всех платформ (включая macOS)
app.on('window-all-closed', () => {
    app.quit();
});
```

---

## Полный жизненный цикл приложения

```mermaid
graph TD
    A[Запуск] --> B[whenReady]
    B --> C[createWindow]
    B --> D[registerShortcuts]
    C --> E[Задержка 500ms]
    E --> F[Показать окно]
    F --> G[Приложение работает]
    G --> H{Закрытие окна?}
    H -->|isQuiting=false| I[Скрыть в трей]
    H -->|isQuiting=true| J[Закрыть окно]
    I --> G
    J --> K{Все окна закрыты?}
    K -->|Да, Windows/Linux| L[app.quit]
    K -->|Да, macOS| M[Остаться активным]
    L --> N[will-quit]
    N --> O[unregisterAll]
    O --> P[Завершение]
```

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Мониторинг сервера</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        /* ===== СБРОС СТИЛЕЙ ===== */
        * { 
            margin: 0; 
            padding: 0; 
            box-sizing: border-box; 
            font-family: 'Segoe UI', system-ui, sans-serif; 
        }

        /* ===== ОСНОВНЫЕ СТИЛИ ===== */
        body {
            background: #0a0e17;
            padding: 15px;
            min-height: 100vh;
            color: #e0e8f0;
        }

        /* ===== ГЛАВНЫЙ КОНТЕЙНЕР ===== */
        .app-container {
            max-width: 1400px;
            margin: 0 auto;
            background: #131c2b;
            border-radius: 16px;
            padding: 20px;
            border: 1px solid #1e2d42;
        }

        /* ===== ШАПКА ===== */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            padding-bottom: 12px;
            border-bottom: 2px solid #1e2d42;
            flex-wrap: wrap;
            gap: 10px;
        }

        .header h1 {
            font-size: 20px;
            font-weight: 600;
            color: #e0e8f0;
        }

        .header h1 i {
            color: #4caf50;
            margin-right: 10px;
        }

        .header-time {
            font-size: 14px;
            color: #8899aa;
        }

        /* ===== СЕТКА КАРТОЧЕК ===== */
        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 16px;
            margin-bottom: 20px;
        }

        /* ===== КАРТОЧКИ ===== */
        .card {
            background: #0d1520;
            border-radius: 12px;
            padding: 16px;
            border: 1px solid #1e2d42;
        }

        .card-title {
            font-size: 13px;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            color: #8899aa;
            margin-bottom: 12px;
        }

        .card-title i {
            margin-right: 8px;
            color: #4caf50;
        }

        /* ===== СТРОКИ РЕСУРСОВ ===== */
        .resource-item {
            display: flex;
            justify-content: space-between;
            padding: 6px 0;
            border-bottom: 1px solid #1e2d42;
            font-size: 13px;
        }

        .resource-item:last-child {
            border-bottom: none;
        }

        .resource-label {
            color: #8899aa;
        }

        .resource-value {
            font-weight: 500;
        }

        /* ===== ПРОГРЕСС-БАРЫ ===== */
        .progress-bar {
            width: 100%;
            height: 4px;
            background: #0d1520;
            border-radius: 3px;
            margin-top: 3px;
            overflow: hidden;
        }

        .progress-fill {
            height: 100%;
            border-radius: 3px;
            transition: width 0.5s ease;
            width: 0%;
        }

        .fill-green { background: #4caf50; }
        .fill-yellow { background: #ffc107; }
        .fill-red { background: #ef5350; }

        /* ===== КНОПКИ УПРАВЛЕНИЯ ===== */
        .controls {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            margin-top: 10px;
        }

        .btn {
            padding: 8px 16px;
            border: none;
            border-radius: 8px;
            font-size: 13px;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.3s ease;
            display: inline-flex;
            align-items: center;
            gap: 8px;
        }

        .btn-danger {
            background: #c62828;
            color: white;
        }

        .btn-danger:hover {
            background: #b71c1c;
            transform: scale(1.02);
        }

        .btn-warning {
            background: #e65100;
            color: white;
        }

        .btn-warning:hover {
            background: #bf360c;
            transform: scale(1.02);
        }

        .btn-primary {
            background: #1565c0;
            color: white;
        }

        .btn-primary:hover {
            background: #0d47a1;
            transform: scale(1.02);
        }

        .btn-success {
            background: #2e7d32;
            color: white;
        }

        .btn-success:hover {
            background: #1b5e20;
            transform: scale(1.02);
        }

        /* ===== ТАБЛИЦА ПРОЦЕССОВ ===== */
        .process-table {
            width: 100%;
            font-size: 12px;
            border-collapse: collapse;
        }

        .process-table th {
            text-align: left;
            padding: 6px 8px;
            color: #8899aa;
            font-weight: 600;
            border-bottom: 1px solid #1e2d42;
        }

        .process-table td {
            padding: 6px 8px;
            border-bottom: 1px solid #0d1520;
        }

        /* ===== ПРОЦЕНТЫ ДЛЯ ПРОЦЕССОВ ===== */
        .process-cpu {
            color: #4caf50;
        }
        .process-mem {
            color: #42a5f5;
        }

        /* ===== АДАПТИВНОСТЬ ===== */
        @media (max-width: 600px) {
            .grid {
                grid-template-columns: 1fr;
            }
            .header {
                flex-direction: column;
                align-items: flex-start;
            }
            .controls {
                flex-direction: column;
                width: 100%;
            }
            .btn {
                width: 100%;
                justify-content: center;
            }
        }
    </style>
</head>
<body>
    <!-- ===== КОНТЕЙНЕР ПРИЛОЖЕНИЯ ===== -->
    <div class="app-container">
        
        <!-- ===== ШАПКА ===== -->
        <div class="header">
            <h1>
                <i class="fas fa-server"></i>
                Мониторинг сервера
            </h1>
            <div class="header-time">
                <i class="fas fa-clock"></i>
                <span id="uptime">Загрузка...</span>
            </div>
        </div>

        <!-- ===== СЕТКА КАРТОЧЕК ===== -->
        <div class="grid">
            
            <!-- ===== КАРТОЧКА ПРОЦЕССОРА ===== -->
            <div class="card">
                <div class="card-title">
                    <i class="fas fa-microchip"></i>
                    Процессор
                </div>
                <div class="resource-item">
                    <span class="resource-label">Модель</span>
                    <span class="resource-value" id="cpu-brand">Загрузка...</span>
                </div>
                <div class="resource-item">
                    <span class="resource-label">Ядра</span>
                    <span class="resource-value" id="cpu-cores">0</span>
                </div>
                <div class="resource-item">
                    <span class="resource-label">Загрузка</span>
                    <span class="resource-value" id="cpu-usage">0%</span>
                </div>
                <div class="progress-bar">
                    <div class="progress-fill fill-green" id="cpu-progress" style="width: 0%;"></div>
                </div>
            </div>

            <!-- ===== КАРТОЧКА ПАМЯТИ ===== -->
            <div class="card">
                <div class="card-title">
                    <i class="fas fa-memory"></i>
                    Память
                </div>
                <div class="resource-item">
                    <span class="resource-label">Всего</span>
                    <span class="resource-value" id="mem-total">0 ГБ</span>
                </div>
                <div class="resource-item">
                    <span class="resource-label">Использовано</span>
                    <span class="resource-value" id="mem-used">0 ГБ</span>
                </div>
                <div class="resource-item">
                    <span class="resource-label">Загрузка</span>
                    <span class="resource-value" id="mem-usage">0%</span>
                </div>
                <div class="progress-bar">
                    <div class="progress-fill fill-green" id="mem-progress" style="width: 0%;"></div>
                </div>
            </div>

            <!-- ===== КАРТОЧКА ДИСКА ===== -->
            <div class="card">
                <div class="card-title">
                    <i class="fas fa-hdd"></i>
                    Диски
                </div>
                <div id="disk-info">
                    <div class="resource-item">
                        <span class="resource-label">Загрузка...</span>
                    </div>
                </div>
            </div>

            <!-- ===== КАРТОЧКА СИСТЕМЫ ===== -->
            <div class="card">
                <div class="card-title">
                    <i class="fas fa-desktop"></i>
                    Система
                </div>
                <div class="resource-item">
                    <span class="resource-label">Хост</span>
                    <span class="resource-value" id="hostname">Загрузка...</span>
                </div>
                <div class="resource-item">
                    <span class="resource-label">ОС</span>
                    <span class="resource-value" id="os-distro">Загрузка...</span>
                </div>
                <div class="resource-item">
                    <span class="resource-label">Аптайм</span>
                    <span class="resource-value" id="uptime-display">0с</span>
                </div>
            </div>

        </div>

        <!-- ===== КАРТОЧКА ПРОЦЕССОВ (полная ширина) ===== -->
        <div class="card" style="margin-bottom: 16px;">
            <div class="card-title">
                <i class="fas fa-tasks"></i>
                Топ процессов
            </div>
            <div style="overflow-x: auto;">
                <table class="process-table">
                    <thead>
                        <tr>
                            <th>Процесс</th>
                            <th style="text-align: right;">CPU</th>
                            <th style="text-align: right;">Память</th>
                        </tr>
                    </thead>
                    <tbody id="process-list">
                        <tr>
                            <td colspan="3" style="text-align: center; color: #8899aa;">Загрузка...</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>

        <!-- ===== КНОПКИ УПРАВЛЕНИЯ ===== -->
        <div class="controls">
            <button class="btn btn-success" id="btn-update">
                <i class="fas fa-sync-alt"></i> Обновить
            </button>
            <button class="btn btn-danger" id="btn-shutdown">
                <i class="fas fa-power-off"></i> Выключить
            </button>
            <button class="btn btn-warning" id="btn-restart">
                <i class="fas fa-redo"></i> Перезагрузить
            </button>
            <button class="btn btn-primary" id="btn-toggle">
                <i class="fas fa-eye"></i> Показать/Скрыть
            </button>
        </div>

    </div>

    <!-- ===== JAVASCRIPT ===== -->
    <script>
        // Подключение IPC
        const { ipcRenderer } = require('electron');

        // ===== ЭЛЕМЕНТЫ ИНТЕРФЕЙСА =====
        const elements = {
            cpuBrand: document.getElementById('cpu-brand'),
            cpuCores: document.getElementById('cpu-cores'),
            cpuUsage: document.getElementById('cpu-usage'),
            cpuProgress: document.getElementById('cpu-progress'),
            
            memTotal: document.getElementById('mem-total'),
            memUsed: document.getElementById('mem-used'),
            memUsage: document.getElementById('mem-usage'),
            memProgress: document.getElementById('mem-progress'),
            
            diskInfo: document.getElementById('disk-info'),
            hostname: document.getElementById('hostname'),
            osDistro: document.getElementById('os-distro'),
            uptimeDisplay: document.getElementById('uptime-display'),
            uptime: document.getElementById('uptime'),
            
            processList: document.getElementById('process-list')
        };

        // ===== ФУНКЦИЯ ОБНОВЛЕНИЯ ИНТЕРФЕЙСА =====
        function updateUI(data) {
            if (!data) return;

            // ---- CPU ----
            elements.cpuBrand.textContent = data.cpu.brand;
            elements.cpuCores.textContent = data.cpu.cores;
            const cpuUsage = data.memory.usage || 0;
            elements.cpuUsage.textContent = cpuUsage + '%';
            elements.cpuProgress.style.width = cpuUsage + '%';
            updateProgressColor(elements.cpuProgress, cpuUsage);

            // ---- ПАМЯТЬ ----
            elements.memTotal.textContent = data.memory.total + ' ГБ';
            elements.memUsed.textContent = data.memory.used + ' ГБ';
            const memUsage = data.memory.usage || 0;
            elements.memUsage.textContent = memUsage + '%';
            elements.memProgress.style.width = memUsage + '%';
            updateProgressColor(elements.memProgress, memUsage);

            // ---- ДИСКИ ----
            if (data.disk && data.disk.length > 0) {
                elements.diskInfo.innerHTML = data.disk.map(d => `
                    <div class="resource-item">
                        <span class="resource-label">${d.device}</span>
                        <span class="resource-value">${d.size} ГБ</span>
                    </div>
                `).join('');
            }

            // ---- СИСТЕМА ----
            elements.hostname.textContent = data.os.hostname;
            elements.osDistro.textContent = data.os.distro;
            
            // Форматирование аптайма
            const uptime = data.uptime.uptime || 0;
            elements.uptimeDisplay.textContent = formatUptime(uptime);
            elements.uptime.textContent = formatUptime(uptime);

            // ---- ПРОЦЕССЫ ----
            if (data.processes && data.processes.length > 0) {
                elements.processList.innerHTML = data.processes.map(p => `
                    <tr>
                        <td>${p.name}</td>
                        <td style="text-align: right;" class="process-cpu">${p.cpu.toFixed(1)}%</td>
                        <td style="text-align: right;" class="process-mem">${p.mem.toFixed(1)}%</td>
                    </tr>
                `).join('');
            } else {
                elements.processList.innerHTML = `
                    <tr>
                        <td colspan="3" style="text-align: center; color: #8899aa;">Нет данных</td>
                    </tr>
                `;
            }
        }

        // ===== ФУНКЦИЯ ОБНОВЛЕНИЯ ЦВЕТА ПРОГРЕСС-БАРА =====
        function updateProgressColor(element, value) {
            element.className = 'progress-fill';
            if (value < 60) {
                element.classList.add('fill-green');
            } else if (value < 80) {
                element.classList.add('fill-yellow');
            } else {
                element.classList.add('fill-red');
            }
        }

        // ===== ФУНКЦИЯ ФОРМАТИРОВАНИЯ АПТАЙМА =====
        function formatUptime(seconds) {
            const days = Math.floor(seconds / 86400);
            const hours = Math.floor((seconds % 86400) / 3600);
            const minutes = Math.floor((seconds % 3600) / 60);
            const secs = Math.floor(seconds % 60);
            
            if (days > 0) {
                return `${days}д ${hours}ч ${minutes}м`;
            } else if (hours > 0) {
                return `${hours}ч ${minutes}м ${secs}с`;
            } else if (minutes > 0) {
                return `${minutes}м ${secs}с`;
            } else {
                return `${secs}с`;
            }
        }

        // ===== ЗАПРОС ДАННЫХ ПРИ ЗАГРУЗКЕ =====
        async function fetchData() {
            try {
                const data = await ipcRenderer.invoke('get-system-data');
                if (data) {
                    updateUI(data);
                }
            } catch (error) {
                console.error('Ошибка получения данных:', error);
            }
        }

        // ===== ОБРАБОТЧИКИ КНОПОК =====
        document.getElementById('btn-update').addEventListener('click', fetchData);
        
        document.getElementById('btn-shutdown').addEventListener('click', async () => {
            if (confirm('Вы действительно хотите выключить компьютер?')) {
                await ipcRenderer.invoke('shutdown-computer');
            }
        });
        
        document.getElementById('btn-restart').addEventListener('click', async () => {
            if (confirm('Вы действительно хотите перезагрузить компьютер?')) {
                await ipcRenderer.invoke('restart-computer');
            }
        });
        
        document.getElementById('btn-toggle').addEventListener('click', () => {
            ipcRenderer.invoke('toggle-window');
        });

        // ===== ПОЛУЧЕНИЕ ДАННЫХ ИЗ MAIN ПРОЦЕССА =====
        ipcRenderer.on('system-data', (event, data) => {
            updateUI(data);
        });

        // ===== ЗАПУСК ПРИ ЗАГРУЗКЕ =====
        fetchData();

        // ===== ОБНОВЛЕНИЕ КАЖДЫЕ 3 СЕКУНДЫ (ДУБЛИРУЕТ ИНТЕРВАЛ) =====
        setInterval(fetchData, 3000);
    </script>
</body>
</html>
```

## Полное объяснение интерфейса:

### Структура HTML

| Элемент | Описание |
|---------|----------|
| `.app-container` | Главный контейнер приложения |
| `.header` | Шапка с заголовком и временем |
| `.grid` | Сетка из 4 карточек (CPU, память, диски, система) |
| `.card` | Карточки с данными |
| `.process-table` | Таблица с топ-5 процессами |
| `.controls` | Кнопки управления |

### Основные функции JavaScript

| Функция | Назначение |
|---------|------------|
| `updateUI(data)` | Обновляет интерфейс новыми данными |
| `updateProgressColor()` | Меняет цвет прогресс-бара (зеленый/желтый/красный) |
| `formatUptime()` | Форматирует время работы системы |
| `fetchData()` | Запрашивает данные через IPC |

### IPC Каналы

| Канал | Назначение |
|-------|------------|
| `get-system-data` | Запрос системных данных |
| `shutdown-computer` | Выключение компьютера |
| `restart-computer` | Перезагрузка компьютера |
| `toggle-window` | Показать/скрыть окно |
| `system-data` | Получение данных из main процесса |

### Цветовая индикация

| Цвет | Значение | Диапазон |
|------|----------|----------|
|  Зеленый | Низкая нагрузка | 0-60% |
|  Желтый | Средняя нагрузка | 60-80% |
|  Красный | Высокая нагрузка | 80-100% |

