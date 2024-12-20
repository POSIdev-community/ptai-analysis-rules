# Правила анализа по шаблону (pattern matching)
В данной директории приведены примеры правил для сопоставления по шаблону. 

Они разбиты по языкам программирования, для которых были написаны. Так как одно 
и то же правило может быть использовано для разных языков, каждое такое правило 
было продублировано для всех поддерживаемых языков. Файл 
[all-rules.json](./all-rules.json) содержит имена всех правил в виде json-массива 
объектов с указанием языков, для которых это правило применяется.  

Некоторые примеры правил:
- [Захардкоженный пароль](./rules/Hardcoded%20Password%20PM.pmrls.json)
- [Пустой обработчик исключений для нескольких языков](./rules/Hardcoded%20Password%20PM.pmrls.json)
- [Стэктрейс вызова](./rules/Information%20Exposure%20Through%20an%20Error%20Message.pmrls.json)
- [Пример с регулярным выражением](./rules/Hardcoded%20Token%20with%20Regex.pmrls.json)

## Описание полей правил

- `name` - имя правила
- `dataFormat` - `Regex` или `Dsl` — задаёт формат правила
- `contactInfo` - контакты автора
- `level` - уровень (опасность) уязвимости
- `value` - само правило. Регулярное выражение или код на DSL
- `vulnerabilityType` - тип уязвимости можно указать [встроенный](./all-rules.json) или свой.

Для `Regex`:
- `filenameWildcard` - шаблон имён файлов. Если их несколько, нужно указать их через `;`. Пример: `"*.php;*.java;*.html"`

Для `Dsl`:
- `programmingLanguages` - список языков, поддерживаемых этим правилом.  
Все поддерживаемые языки:
    ```json
    {
      "programmingLanguages": [
        "Php",
        "Java",
        "Html",
        "JavaScript",
        "PlSql",
        "TSql",
        "Aspx",
        "C",
        "CPlusPlus",
        "ObjectiveC",
        "Swift",
        "MySql",
        "Python",
        "CSharp",
        "Go",
        "Kotlin",
        "TypeScript",
        "Ruby",
        "Solidity"
      ]
    }
    ```

## Выдержка из документации по формату правил
Есть два режима:

1. DSL — шаблон задаётся при помощи DSL языка
2. Regex — шаблон задаётся при помощи регулярного выражения

Шаблоны на языке DSL строятся из выражений, которые содержат следующие лексические
элементы:
- идентификаторы;
- ключевые слова;
- спецификаторы;
- литералы.

Идентификаторы (`ID`) представляют собой названия методов, классов, переменных и
различных структур. Идентификатор не должен начинаться с цифры и может содержать только
буквы латинского алфавита, цифры, знаки подчеркивания (`_`) и доллара (`$`).
Ключевые слова (`keywords`) — это набор зарезервированных слов, которые имеют
специфичные значения в конкретном языке программирования (например, обозначение
класса или функции), а также арифметические операторы и знаки препинания.
Спецификаторы представляют собой служебные элементы языка DSL, которыми вы можете
заменить некоторые значения в коде:
- `#` — любое выражение;
- `#*` или `...` — произвольное количество любых аргументов;
- `<[]>` — спецификатор, в котором с помощью регулярных выражений можно задать значения
литералов. Список используемых литералов представлен в таблице ниже.

| Тип         | Описание                                                                                                                                        | Пример                                                                                                                                                                                                                                                                     |
|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| String      | Строка. Должна быть заключена в двойные кавычки. Знак обратной косой черты и двойные кавычки должны быть экранированы обратной косой чертой (\) | "password"                                                                                                                                                                                                                                                                 |
| Number      | Целое, восьмеричное (начиная с 0) или шестнадцатеричное (начиная с 0X) число                                                                    | 12345, 012, 0X123                                                                                                                                                                                                                                                          |
| NumberRange | Диапазон значений данных типа Number                                                                                                            | 10..20 — диапазон, в котором нижняя граница включается, а верхняя нет; <br/>.. — бесконечный диапазон (ограничен множеством значений типа Int64); <br/> 10.. — диапазон, ограниченный снизу, включая границу; <br/> ..20 — диапазон, ограниченный сверху, исключая границу |
| Bool        | Логическое значение                                                                                                                             | true, false                                                                                                                                                                                                                                                                |
| Null        | Отсутствие данных                                                                                                                               | null                                                                                                                                                                                                                                                                       |

Из выражений можно составить два типа шаблонов: поиск присваивания и поиск вызова
функции или метода.

### Поиск присваивания
Общий вид шаблона:

`expression = expression`, где `expression` — это регулярное выражение `<[regexID]>`, идентификатор или литерал.

Например:
- `hardcoded_password = <[".*"]>` — поиск идентификатора `hardcoded_password`, значение которого содержит 
любой строковый литерал;
- `<[^pass]> = <[".*"]>` — поиск любого идентификатора, который начинается с последовательности pass.

**Примечание**. Для записи регулярных выражений нужно использовать 
[синтаксис RE2](https://github.com/google/re2/wiki/Syntax).

### Поиск вызова функции или метода
**Примечание**. Ниже приведено описание шаблонов только для поиска вызова функции или
метода, но не для их объявления.

Общий вид шаблона:

`expression(arg1, arg2, argN)`

В качестве аргументов в круглых скобках вы можете указать:
- `"value"` — строковый литерал;
- `value` — переменную или нестроковый литерал;
- `<["value"]>` — множество строковых литералов;
- `<[value]>` — множество переменных или нестроковых литералов;
- `#` — один аргумент с любым названием;
- `#*` или `...` — любое количество аргументов.

Например:
- `dangerous_func (#*)` — вызов функции с любым количеством аргументов;
- `dangerous_func (#, \"fake value\")` — вызов функции с первым любым аргументом
и вторым обязательным аргументом;
- `dangerous_func (#, \"fake value\", #*)` — вызов функции с первым любым
аргументом, вторым обязательным аргументом и далее любой последовательностью
необязательных аргументов.

Если у метода явно указан объект, необходимо учитывать, что этот метод может быть вызван
как метод объекта, а не свободная функция. Задать обращение к методу можно с помощью
шаблонов вида:

`expression.ID(arg1, arg2, argN)` или `(expression.)?ID(arg1, arg2, argN)`

Например:
- `(#.)?dangerous_func(...)` — вызов метода с любым количеством аргументов, c
указанием необязательного объекта.
