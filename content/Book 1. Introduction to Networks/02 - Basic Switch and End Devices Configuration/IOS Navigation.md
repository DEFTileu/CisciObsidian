#book1
## 2.2.1 Основные режимы команд Cisco IOS

В целях безопасности Cisco IOS разделяет доступ на **два основных режима**.

---

### Сравнение режимов

|Режим|Что можно делать|Символ в строке|Пример|
|---|---|---|---|
|**User EXEC Mode**|Только базовый мониторинг, **нельзя** менять конфигурацию|`>`|`Switch>` / `Router>`|
|**Privileged EXEC Mode**|Все команды: мониторинг, настройка, управление|`#`|`Switch#` / `Router#`|

---

### Запомни главное

**`>`** — ты в режиме пользователя (только смотреть) **`#`** — ты в привилегированном режиме (полный доступ)

---

### Аналогия

> Это как в Linux: обычный пользователь vs `sudo`. User EXEC — обычный юзер, Privileged EXEC — root.

Из Privileged EXEC Mode можно войти в ещё более глубокие режимы настройки (например, **Global Configuration Mode**), но туда нельзя попасть напрямую — только через `#`.

---

## 2.2.2 Режим конфигурации и суб-режимы

---

### Иерархия режимов Cisco IOS

```
Router>                    ← User EXEC
Router#                    ← Privileged EXEC
Router(config)#            ← Global Configuration
Router(config-line)#       ← Line Configuration (суб-режим)
Router(config-if)#         ← Interface Configuration (суб-режим)
```

---

### Global Configuration Mode

- Вход: из Privileged EXEC командой `configure terminal`
- Изменения здесь влияют на **всё устройство целиком**
- Промпт: `Switch(config)#`

---

### Два основных суб-режима

|Суб-режим|Для чего|Промпт|
|---|---|---|
|**Line Configuration**|Настройка доступа: Console, SSH, Telnet, AUX|`Switch(config-line)#`|
|**Interface Configuration**|Настройка портов коммутатора или сетевых интерфейсов роутера|`Switch(config-if)#`|

---

### Как читать промпт

Промпт всегда состоит из двух частей:

`Switch(config-if)#`

- `Switch` — имя устройства
- `(config-if)` — текущий режим
- `#` — ты в привилегированном уровне доступа

> **Вывод:** по промпту всегда можно определить, где ты сейчас находишься и что можешь настраивать.

---

## 2.2.4 Навигация между режимами IOS

### Шпаргалка по командам

|Действие|Команда|
|---|---|
|User EXEC → Privileged EXEC|`enable`|
|Privileged EXEC → User EXEC|`disable`|
|Privileged EXEC → Global Config|`configure terminal`|
|На один уровень вверх|`exit`|
|Из любого места → Privileged EXEC|`end` или `Ctrl+Z`|
|Из одного суб-режима в другой|просто ввести команду нового режима|

---

### Примеры из материала

**Войти в line-режим и выйти обратно:**

```
Switch(config)# line console 0
Switch(config-line)# exit
Switch(config)#
```

**Прыжок сразу в Privileged EXEC из суб-режима:**

```
Switch(config-line)# end
Switch#
```

**Переключиться между суб-режимами напрямую:**

```
Switch(config-line)# interface FastEthernet 0/1
Switch(config-if)#
```

> Не нужно делать `exit` — просто вводишь команду нового режима.

---

### Полная картина навигации

```
Switch>
  ↕ enable / disable
Switch#
  ↕ configure terminal / exit (или end/Ctrl+Z)
Switch(config)#
  ↕ line console 0 / exit
Switch(config-line)#
  ↕ interface Fa0/1 / exit
Switch(config-if)#
```

> **Запомни:** `exit` — шаг назад, `end` / `Ctrl+Z` — сразу на уровень `Switch#`.

#computernetworks 
