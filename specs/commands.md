# specs/commands.md

## 🧩 Назначение

Этот документ описывает **набор команд**, которые GUI может отправлять в Lua-надстройку MyOffice через файл `command.log`.  
Команды задают, какие действия надстройка должна выполнить с документом.

---

## ⚙️ Общие принципы

- Команды передаются в **формате JSON**, одна строка = одна команда.  
- Все команды передаются **односторонне** — от GUI к Lua.  
- Аргументы (`args`) указываются в виде объекта `{}`.  
- Выполнение команд происходит **в порядке их записи** (FIFO).  
- После выполнения всех команд GUI **должен отправить `EXIT`**.

---

## 📄 Структура команды

```json
{"id": 1, "cmd": "SET_CELL_TEXT", "args": {"sheet": 0, "addr": "A1", "text": "Привет"}}
```

| Поле | Тип | Обязательное | Описание |
|------|-----|---------------|-----------|
| `id` | number | да | Уникальный идентификатор команды (для логов и отладки) |
| `cmd` | string | да | Название команды |
| `args` | object | да | Аргументы команды; если не нужны — передаётся `{}` |

---

## 📘 Минимальный набор команд

| Команда | Назначение | Аргументы |
|----------|-------------|-----------|
| **SAVE_AS** | Сохранить документ под новым именем | `path` (string) — полный путь к файлу |
| **SAVE** | Сохранить текущий документ | — |
| **ADD_SHEET** | Добавить новый лист | `name?` (string), `rows?` (int), `cols?` (int) |
| **RENAME_SHEET** | Переименовать лист | `sheet` (number/string), `name` (string) |
| **SET_CELL_TEXT** | Записать текст в ячейку | `sheet` (number/string), `addr` (string), `text` (string) |
| **SET_CELL_NUMBER** | Записать число в ячейку | `sheet` (number/string), `addr` (string), `number` (number) |
| **SET_CELL_FORMULA** | Записать формулу в ячейку | `sheet` (number/string), `addr` (string), `formula` (string) |
| **SET_RANGE_TEXT** | Записать диапазон значений | `sheet` (number/string), `range` (string), `values` (string[][]) |
| **FIND_REPLACE** | Найти и заменить текст | `sheet` (number/string), `range?` (string), `find` (string), `replace` (string) |
| **CREATE_FILTERS_RANGE** | Создать фильтруемый диапазон | `sheet` (number/string), `range` (object — `{row, col, rows, cols}`) |
| **SET_FILTERS** | Применить фильтры | `sheet` (number/string), `filters` (array объектов `{col, values?, cond?}`) |
| **CLEAR_FILTERS** | Очистить фильтры | `sheet` (number/string) |
| **EXIT** | Завершить работу GUI и надстройки | — |

---

## 🧱 Примеры команд

### Добавить лист

```json
{"id": 1, "cmd": "ADD_SHEET", "args": {"name": "Demo"}}
```

### Записать текст в ячейку

```json
{"id": 2, "cmd": "SET_CELL_TEXT", "args": {"sheet": 0, "addr": "A1", "text": "Привет"}}
```

### Найти и заменить текст

```json
{"id": 3, "cmd": "FIND_REPLACE", "args": {"sheet": 0, "find": "TODO", "replace": ""}}
```

### Сохранить документ под новым именем

```json
{"id": 4, "cmd": "SAVE_AS", "args": {"path": "C:/temp/report.xlsx"}}
```

### Завершить работу GUI

```json
{"id": 999, "cmd": "EXIT", "args": {}}
```

---

## 🧩 Расширение набора команд

Этот протокол можно расширять:

- добавляя новые `cmd`, не нарушающие обратную совместимость;
- обновляя `specs/commands.md` с описанием аргументов;
- сохраняя базовую структуру `{ "id": <int>, "cmd": "<NAME>", "args": { ... } }`.

---

## 📚 Где читать дальше

- **[PROTOCOL.md](../PROTOCOL.md)** — описание формата `command.log` и последовательности выполнения.  
- **[ARCHITECTURE.md](../ARCHITECTURE.md)** — общая схема архитектуры GUI ↔ Lua.  
