## Information Exposure

Директория для правил, которые находят код, потенциально приводящий к утечке данных 

Список правил:

1. [Information Exposure Through an Error Message](./Information%20Exposure%20Through%20an%20Error%20Message.json)

   Правило для примера оформления. Оно срабатывает при вызове функции с именем `printStackTrace()`
   на произвольной переменной. Так как функция используется для получения стек-трейса исключения,
   ее неосторожное использование может привести к утечкам чувствительной информации такой как структура
   кода. Правило сработает на следующем примере кода:

    ```kotlin
    fun handler() {
        try {
            throwException()
        } catch (e: Exception) {
            e.printStackTrace()
        }
    } 
   ```
2. [Использование атрибута Referrerpolicy unsafe-url](./Использование%20атрибута%20Referrerpolicy%20unsafe-url.json)

   `Referrerpolicy` – атрибут указывающий какой заголовок `Referer` необходимо отправлять при получении ресурса фрейма. При указании `unsafe-url` заголовок `Referer` будет включать источник и путь (но не fragment, password, или username). Это значение небезопасно, так как оно приводит к утечке источников и путей от ресурсов, защищенных с помощью TLS к небезопасным источникам. Применение `unsafe-url` в коде обязательно должно быть проанализировано.
   
   [<iframe>: The Inline Frame element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe#unsafe-url)
