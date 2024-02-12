## Information Exposure

Директория для правил, которые находят код, потенциально приводящий к утечке данных 

Список правил:

1. [Information Exposure Through an Error Message](./Information%20Exposure%20Through%20an%20Error%20Message.json)

   Правило для примера оформления. Оно срабатывает при вызове функции с именем `printStackTrace()`
   на произвольной переменной. Так как функция используется для получения стек-трейся исключения,
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
